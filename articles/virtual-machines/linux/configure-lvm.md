---
title: "Configurar LVM numa máquina virtual com Linux | Microsoft Docs"
description: Saiba como configurar LVM no Linux no Azure.
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: timlt
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 7926627aaa3f0da935131f491d927ab5cb4b35c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurar LVM numa VM com Linux no Azure
Este documento irá discutir como configurar o Gestor de lógica de Volume (LVM) na sua máquina virtual do Azure. Apesar de ser exequível a configurar LVM em qualquer disco ligado à máquina virtual, por predefinição a maioria das imagens da nuvem não terá LVM configurado no disco do SO. Isto serve para evitar problemas com grupos de volume duplicados se o disco do SO é alguma vez ligado a outra VM, do mesmo distribuição e do tipo, ou seja, durante um cenário de recuperação. Por isso, recomenda-se apenas ao utilizar LVM nos discos de dados.

## <a name="linear-vs-striped-logical-volumes"></a>Linear vs volumes repartidos de lógicos
LVM pode ser utilizado para combinar um número de discos físicos para um único volume de armazenamento. Por predefinição LVM normalmente criará lineares volumes lógicas, o que significa que o armazenamento físico é concatenado em conjunto. Neste caso, as operações de leitura/escrita serão normalmente apenas enviadas para um único disco. Em contrapartida, iremos também pode criar volumes repartidos de lógicos onde leituras e escritas são distribuídas por vários discos contidos no grupo de volume (ou seja, semelhante a RAID0). Por motivos de desempenho, é provável deverá stripe os volumes lógicos para que as leituras e escritas utilizam todos os seus discos de dados anexados.

Este documento descreve como combinar vários discos de dados num grupo de único volume e, em seguida, crie um volume lógico repartido. Os passos abaixo são um pouco generalizados para trabalhar com a maioria das distribuições. Na maioria dos casos os utilitários e fluxos de trabalho para gerir LVM no Azure não são fundamentalmente diferentes noutros ambientes. Normalmente, também consulte o fornecedor do Linux para documentação e melhores práticas para utilizar LVM com a distribuição específica.

## <a name="attaching-data-disks"></a>Anexar os discos de dados
Normalmente, um irá querer começar a utilizar duas ou mais discos de dados vazio quando utilizar LVM. Com base nas suas necessidades de e/s, pode optar por ligar os discos que estão armazenados no nosso armazenamento Standard, com até 500 e/s/ps por nossa armazenamento Premium ou disco com até 5000 e/s por ps por disco. Este artigo não irá entrar em detalhe sobre como aprovisionar e anexe discos de dados para uma máquina virtual Linux. Consulte o artigo do Microsoft Azure [anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter instruções detalhadas sobre como ligar um disco de dados vazia para uma máquina virtual do Linux no Azure.

## <a name="install-the-lvm-utilities"></a>Instale os utilitários LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 e openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    No SLES11 tem também de editar `/etc/sysconfig/lvm` e defina `LVM_ACTIVATED_ON_DISCOVERED` "ativar":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Configurar LVM
Neste guia iremos assumir ter ligado três discos de dados, que irá denominamos `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Tenha em atenção que estas podem nem sempre ser os mesmos nomes de caminho no VM. Pode executar '`sudo fdisk -l`' ou um comando semelhante para listar os discos disponíveis.

1. Prepare os volumes físicos:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Crie um grupo de volume. Neste exemplo estamos a ligar para o grupo de volume `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Crie os volumes lógico. O comando abaixo, irá criar um único volume lógico denominado `data-lv01` span o grupo de volume completo, mas tenha em atenção que também é possível criar vários volumes lógicos no grupo de volume.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formate o volume lógico

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Com a utilização de SLES11 `-t ext3` em vez de ext4. SLES11 só suporta acesso só de leitura aos sistemas de ficheiros instalados ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de ficheiros para /etc/fstab
> [!IMPORTANT]
> Editar incorretamente o `/etc/fstab` ficheiros poderia resultar num sistema de arranque. Se não souber, consulte a documentação de distribuição para obter informações sobre como editar corretamente este ficheiro. Também é recomendável que uma cópia de segurança a `/etc/fstab` ficheiro é criado antes de editar.

1. Crie o ponto de montagem pretendida para o novo sistema de ficheiros, por exemplo:

    ```bash  
    sudo mkdir /data
    ```

2. Localizar o caminho do volume lógica

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Abra `/etc/fstab` num editor de texto e adicione uma entrada para o novo sistema de ficheiros, por exemplo:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Em seguida, guarde e feche `/etc/fstab`.

4. Testar que o `/etc/fstab` entrada está correta:

    ```bash    
    sudo mount -a
    ```

    Se este comando resulta numa mensagem de erro Verifique a sintaxe `/etc/fstab` ficheiro.
   
    Executar novamente o `mount` comando para garantir que o sistema de ficheiros está montado:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Opcional) Parâmetros de arranque FailSafe no`/etc/fstab`
   
    Muitas distribuições incluem um o `nobootwait` ou `nofail` montar os parâmetros que podem ser adicionados à `/etc/fstab` ficheiro. Estes parâmetros permitem para falhas ao montar um sistema de ficheiros específica e permitir que o sistema de Linux continuar a arrancar mesmo se for não é possível montar corretamente o sistema de ficheiros RAID. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
   
    Exemplo (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Suporte de cortar/UNMAP
Alguns kernels Linux suportam operações de cortar/UNMAP para eliminar os blocos no disco. Estas operações são principalmente útil para armazenamento standard para informar o Azure eliminado páginas já não são válido e pode ser eliminada. Rejeitar páginas pode guardar o custo, se criar ficheiros grandes e, em seguida, elimine-os.

Existem duas formas de ativar a limitação suportam na sua VM com Linux. Normalmente, consulte a distribuição para a abordagem recomendada:

- Utilize o `discard` montar opção na `/etc/fstab`, por exemplo:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos o `discard` opção pode ter implicações de desempenho. Em alternativa, pode executar o `fstrim` manualmente de comando na linha de comandos ou adicioná-lo à sua crontab regularmente a executar:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
