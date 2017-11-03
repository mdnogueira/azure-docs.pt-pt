---
title: "Configurar software RAID numa máquina virtual com Linux | Microsoft Docs"
description: Saiba como utilizar mdadm para configurar o RAID em Linux no Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: timlt
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.openlocfilehash: 12f540a700fbf85e579e8aadc9f6def039299ff7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-software-raid-on-linux"></a>Configurar o RAID de Software no Linux
É um cenário comum para utilizar o software RAID em máquinas virtuais do Linux no Azure para apresentar vários discos de dados anexados como um único dispositivo RAID. Normalmente, isto pode ser utilizado para melhorar o desempenho e permitir a obtenção de débito melhorado em comparação comparado a utilização de apenas um único disco.

## <a name="attaching-data-disks"></a>Anexar os discos de dados
Dois ou mais discos de dados vazio são necessárias para configurar um dispositivo RAID.  É o principal motivo para a criação de um dispositivo RAID para melhorar o desempenho do disco e/s.  Com base nas suas necessidades de e/s, pode optar por ligar os discos que estão armazenados no nosso armazenamento Standard, com até 500 e/s/ps por nossa armazenamento Premium ou disco com até 5000 e/s por ps por disco. Este artigo não entrar em detalhe sobre como aprovisionar e anexe discos de dados para uma máquina virtual Linux.  Consulte o artigo do Microsoft Azure [anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter instruções detalhadas sobre como ligar um disco de dados vazia para uma máquina virtual do Linux no Azure.

## <a name="install-the-mdadm-utility"></a>Instalar o utilitário de mdadm
* **Ubuntu**
```bash
sudo apt-get update
sudo apt-get install mdadm
```

* **CentOS & Oracle Linux**
```bash
sudo yum install mdadm
```

* **SLES e openSUSE**
```bash  
zypper install mdadm
```

## <a name="create-the-disk-partitions"></a>Criar as partições de disco
Neste exemplo, vamos criar uma partição de disco individual no /dev/sdc. A nova partição de disco será chamada /dev/sdc1.

1. Iniciar `fdisk` para começar a criar partições

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

2. Prima 'n' na linha de comandos para criar um  **n** partição ew:

    ```bash
    Command (m for help): n
    ```

3. Em seguida, prima p para criar um **p**partição o:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Prima '1' para selecionar o número de partição 1:

    ```bash
    Partition number (1-4): 1
    ```

5. Selecione o ponto de partida da nova partição ou prima `<enter>` para aceitar a predefinição para colocar a partição no início do espaço livre na unidade:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Selecione o tamanho da partição, por exemplo, digite +10G para criar uma partição 10 gigabyte. Em alternativa, prima `<enter>` criar uma partição única que abrange a unidade completa:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. Em seguida, altere o ID e **t**ype da partição do ID de predefinição '83' (Linux) para o ID 'df' (Linux automática de raid):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Por fim, a tabela de partições de escrita para a unidade e sair fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Criar a matriz RAID
1. Irá de exemplo seguintes "stripe" (nível RAID 0) três partições localizadas em três discos de dados individual (sdc1 sdd1, sde1).  Depois de executar este comando um novo dispositivo RAID chamado **/dev/md127** é criado. Tenha também em atenção que se estes dados discos foi anteriormente parte de outra matriz RAID desativado poderá ser necessário adicionar o `--force` parâmetro para o `mdadm` comando:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. Criar o sistema de ficheiros no dispositivo RAID novo
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE e Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** - ativar boot.md e criar mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Reiniciar o computador poderá ser necessário depois de efetuar estas alterações em sistemas SUSE. Este passo é *não* necessário em SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de ficheiros para /etc/fstab
> [!IMPORTANT]
> Incorretamente editar o ficheiro de /etc/fstab pode resultar num sistema de arranque. Se não souber, consulte a documentação de distribuição para obter informações sobre como editar corretamente este ficheiro. Também é recomendável que uma cópia de segurança do ficheiro /etc/fstab foi criada antes de editar.

1. Crie o ponto de montagem pretendida para o novo sistema de ficheiros, por exemplo:

    ```bash
    sudo mkdir /data
    ```
2. Ao editar /etc/fstab, o **UUID** deve ser utilizado para referenciar o sistema de ficheiros em vez do nome do dispositivo.  Utilize o `blkid` utilitário para determinar o UUID para o novo sistema de ficheiros:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. Abra /etc/fstab num editor de texto e adicione uma entrada para o novo sistema de ficheiros, por exemplo:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Ou no **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Em seguida, guarde e feche /etc/fstab.

4. Se a entrada de fstab etc/está correta de teste:

    ```bash  
    sudo mount -a
    ```

    Se este comando resulta numa mensagem de erro, verifique a sintaxe no ficheiro /etc/fstab.
   
    Executar novamente o `mount` comando para garantir que o sistema de ficheiros está montado:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Opcional) Parâmetros de arranque FailSafe
   
    **configuração de fstab**
   
    Muitas distribuições incluem um o `nobootwait` ou `nofail` parâmetros que podem ser adicionados para o ficheiro etc/fstab de montagem. Estes parâmetros permitem para falhas ao montar um sistema de ficheiros específica e permitir que o sistema de Linux continuar a arrancar mesmo se for não é possível montar corretamente o sistema de ficheiros RAID. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
   
    Exemplo (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parâmetros de arranque do Linux**
   
    Para além dos parâmetros acima, o parâmetro de kernel "`bootdegraded=true`" pode permitir que o sistema de arranque, mesmo que o RAID é interpretada como exemplo danificado ou degradado, para se uma unidade dados inadvertidamente for removida da máquina virtual. Por predefinição Isto pode resultar também um não arranque do sistema.
   
    Consulte a documentação do seu distribuição sobre como editar corretamente os parâmetros de kernel. Por exemplo, em muitas distribuições (CentOS, Oracle Linux, SLES 11) estes parâmetros podem ser adicionados manualmente para o "`/boot/grub/menu.lst`" ficheiro.  No Ubuntu este parâmetro pode ser adicionado para o `GRUB_CMDLINE_LINUX_DEFAULT` variável no "/ predefinido/etc/grub".


## <a name="trimunmap-support"></a>Suporte de cortar/UNMAP
Alguns kernels Linux suportam operações de cortar/UNMAP para eliminar os blocos no disco. Estas operações são principalmente útil para armazenamento standard para informar o Azure eliminado páginas já não são válido e pode ser eliminada. Rejeitar páginas pode guardar o custo, se criar ficheiros grandes e, em seguida, elimine-os.

> [!NOTE]
> RAID não pode emitir comandos de eliminação se o tamanho do segmento para a matriz estiver definido como inferior à predefinição de (512KB). Isto acontece porque a granularidade unmap no anfitrião também é 512KB. Se alterar o tamanho do segmento da matriz através da mdadm `--chunk=` parâmetro, em seguida, cortar/unmap pedidos podem ser ignoradas pelo kernel.

Existem duas formas de ativar a limitação suportam na sua VM com Linux. Normalmente, consulte a distribuição para a abordagem recomendada:

- Utilize o `discard` montar opção na `/etc/fstab`, por exemplo:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos o `discard` opção pode ter implicações de desempenho. Em alternativa, pode executar o `fstrim` manualmente de comando na linha de comandos ou adicioná-lo à sua crontab regularmente a executar:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
