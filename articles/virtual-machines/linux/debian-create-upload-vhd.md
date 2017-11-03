---
title: Preparar um VHD Debian do Linux no Azure | Microsoft Docs
description: "Saiba como criar Debian 7 & 8 ficheiros VHD para a implementação no Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 63970d162c12984d6476bf0b9fc4ab70160eccdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar um VHD Debian para o Azure
## <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já tem instalado um sistema operativo Debian Linux de um ficheiro. ISO transferido a partir de [site Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd; Hyper-V é apenas um exemplo. Para obter instruções sobre como utilizar o Hyper-V, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Notas de instalação
* Consulte também [geral notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para mais sugestões no preparar Linux para o Azure.
* O formato VHDX mais recente não é suportado no Azure. Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o **convert-vhd** cmdlet.
* Ao instalar o sistema Linux é recomendado que utilize partições padrão em vez de LVM (muitas vezes, a predefinição para instalações muitos). Evitará LVM nome entra em conflito com VMs Clonadas, particularmente se um disco de SO alguma vez precisar de ser ligado a outra VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizado em discos de dados se preferencial.
* Não configure uma partição de comutação no disco do SO. O agente Linux do Azure pode ser configurado para criar um ficheiro de comutação no disco de recursos temporário. Podem encontrar mais informações sobre esta nos passos abaixo.
* Todos os VHDs têm de ter tamanhos que estão em múltiplos de 1 MB.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Utilize a gerir o Azure para criar Debian VHDs
Existem ferramentas disponíveis para a geração de VHDs Debian do Azure, tal como o [azure-gerir](https://github.com/credativ/azure-manage) scripts de [credativ](http://www.credativ.com/). Esta é a abordagem recomendada versus criar uma imagem a partir do zero. Por exemplo, para criar um VHD de 8 Debian, execute os seguintes comandos para transferir o azure gerir (e as dependências) e execute o script de azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparar manualmente uma VHD Debian
1. No Gestor de Hyper-V, selecione a máquina virtual.
2. Clique em **Connect** para abrir uma janela da consola da máquina virtual.
3. Comente a linha de **CD-ROM de deb** no `/etc/apt/source.list` se configurou a VM em relação a um ficheiro ISO.
4. Editar o `/etc/default/grub` de ficheiros e modificar o **GRUB_CMDLINE_LINUX** parâmetro da seguinte forma para incluir parâmetros de kernel adicionais para o Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. Reconstrua o grub e execute:
   
        # sudo update-grub
6. Adicione repositórios do Azure do Debian ao /etc/apt/sources.list para Debian 7 ou 8:
   
    **Debian 7. x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8. x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Instale o agente Linux do Azure:
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Para Debian 7, é necessário executar o kernel do 3.16 do repositório wheezy backports. Em primeiro lugar, crie um ficheiro chamado /etc/apt/preferences.d/linux.pref com o seguinte conteúdo:
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    Em seguida, execute "sudo apt get instalação linux-imagem-amd64" para instalar o novo kernel.
3. Desaprovisionar a máquina virtual e prepará-la para o aprovisionamento no Azure e execute:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Clique em **ação** -> Encerrar para baixo no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.

## <a name="next-steps"></a>Passos seguintes
Agora, está pronto a utilizar o seu disco rígido virtual Debian para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, consulte os passos 2 e 3 na [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

