---
title: Criar e carregar um VHD de Linux Ubuntu no Azure
description: "Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contenha um sistema de operativo Ubuntu Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 4496b34ff88ca1e08cc74788ae09d787d4399eaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparar uma máquina virtual do Ubuntu para o Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Imagens de nuvem Ubuntu oficiais
Ubuntu agora publica oficial do Azure VHDs para transferência em [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Se precisar de criar a sua própria imagem Ubuntu especializada para o Azure, em vez disso que utilize o procedimento manual abaixo se recomenda para começar a utilizar estas conhecido trabalhar VHDs e personalizar conforme necessário. As versões mais recentes de imagem sempre podem ser encontradas nas seguintes localizações:

* Ubuntu 12.04/preciso: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já tem instalado um sistema de operativo Ubuntu Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização, tais como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do Ubuntu**

* Consulte também [geral notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para mais sugestões no preparar Linux para o Azure.
* O formato VHDX não é suportado no Azure, apenas **fixo VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux é recomendado que utilize partições padrão em vez de LVM (muitas vezes, a predefinição para instalações muitos). Evitará LVM nome entra em conflito com VMs Clonadas, particularmente se um disco de SO alguma vez precisar de ser ligado a outra VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizado em discos de dados se preferencial.
* Não configure uma partição de comutação no disco do SO. O agente Linux pode ser configurado para criar um ficheiro de comutação no disco de recursos temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.
* Todos os VHDs têm de ter tamanhos que estão em múltiplos de 1 MB.

## <a name="manual-steps"></a>Passos manuais
> [!NOTE]
> Antes de tentar criar a sua imagem personalizada do Ubuntu para o Azure, considere utilizar as imagens previamente concebidas e testadas do [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) em vez disso.
> 
> 

1. No painel central do Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Connect** para abrir a janela para a máquina virtual.

3. Substitua os atuais repositórios na imagem a utilizar repos do Azure do Ubuntu. Os passos variam ligeiramente dependendo da versão Ubuntu.
   
    Antes de editar `/etc/apt/sources.list`, é recomendado que faça uma cópia de segurança:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. As imagens de Ubuntu Azure estão agora a seguir a *ativação hardware* kernel (HWE). Atualize o sistema operativo para o kernel mais recente, executando os seguintes comandos:

    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **Consulte também:**
    - [https://Wiki.ubuntu.com/kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://Wiki.ubuntu.com/kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Modifique a linha de arranque de kernel para Grub incluir parâmetros de kernel adicionais para o Azure. Para fazer este open `/etc/default/grub` num editor de texto, localizar a variável chamada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicioná-la, se necessário) e editá-lo para incluir os seguintes parâmetros:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Guarde e feche este ficheiro e, em seguida, execute `sudo update-grub`. Isto irá garantir que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar o suporte técnico do Azure com problemas de depuração.

6. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Normalmente, esta é a predefinição.

7. Instale o agente Linux do Azure:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    O `walinuxagent` pacote pode remover o `NetworkManager` e `NetworkManager-gnome` pacotes e, se estiverem instalados.

8. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.

## <a name="next-steps"></a>Passos seguintes
Agora, está pronto a utilizar o seu disco rígido virtual Ubuntu Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, consulte os passos 2 e 3 na [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="references"></a>Referências
Ubuntu kernel de ativação (HWE) de hardware:

* [http://blog.utlemming.org/2015/01/ubuntu-1404-Azure-Images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
* [http://blog.utlemming.org/2015/02/1204-Azure-cloud-Images-Now-using-Hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

