---
title: Adicionar imagens de Linux a pilha do Azure
description: Saiba como adicionar imagens de Linux com pilha do Azure.
services: azure-stack
documentationcenter: 
author: anjayajodha
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
ms.openlocfilehash: a8763c01cba4e5a9eaa3b7842b627d6eb9661a95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-linux-images-to-azure-stack"></a>Adicionar imagens de Linux a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure* 

Pode implementar máquinas virtuais Linux no Azure pilha ao adicionar uma imagem baseado em Linux no Azure Marketplace pilha. É a forma mais fácil para adicionar uma imagem de Linux a pilha do Azure através da gestão do Marketplace.

## <a name="marketplace-management"></a>Gestão do Marketplace

Para transferir as imagens de Linux no Azure Marketplace, utilize os procedimentos no seguinte artigo. Selecione as imagens de Linux que pretende oferecem aos utilizadores na sua pilha do Azure.

[Transferir itens do marketplace do Azure para Azure pilha](azure-stack-download-azure-marketplace-item.md).

## <a name="download-an-image"></a>Transferir uma imagem

Pode transferir e extrair imagens de Linux compatível com pilha do Azure com as seguintes ligações:


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. Extrair a imagem do VHD, se necessário e [adicionar a imagem para o mercado](azure-stack-add-vm-image.md). Certifique-se de que o `OSType` parâmetro está definido como `Linux`.
2. Depois de adicionar a imagem do Marketplace, é criado um item do Marketplace e os utilizadores podem implementar uma máquina virtual Linux.

## <a name="prepare-your-own-image"></a>Preparar a sua própria imagem

Preparar a sua própria imagem Linux utilizando um dos seguintes instruções:
   
   * [Distribuições baseado em centOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Transfira e instale o [agente Linux do Azure](https://github.com/Azure/WALinuxAgent/).
   
    O agente Linux do Azure versão 2.1.3 ou superior, é necessário para aprovisionar a VM do Linux na pilha do Azure. Muitas das distribuições listadas anteriormente já incluem esta versão do agente ou superior como um pacote na respetiva repositórios (normalmente designado `WALinuxAgent` ou `walinuxagent`). No entanto, se a versão do pacote de agente do Azure é inferior a 2.1.3 (por exemplo, 2.0.18 ou inferior), em seguida, tem de instalar o agente manualmente. É possível determinar a versão instalada do nome do pacote ou executando `/usr/sbin/waagent -version` na VM.
   
    Siga as instruções abaixo para instalar manualmente - o agente Linux do Azure
   
   a. Em primeiro lugar, transfira o agente Linux do Azure mais recente do [GitHub](https://github.com/Azure/WALinuxAgent/releases), exemplo:
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   b. Descompacte o agente do Azure:
     
            # tar -vzxf v2.2.16.tar.gz
   c. Instalar o python setuptools
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d. Instale o agente do Azure:
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     Os sistemas com o Python 2. x e Python 3 instalado do lado do lado a lado poderão ter de executar o seguinte comando:
     
         sudo python3 setup.py install --register-service
     Para obter mais informações, consulte o agente Linux do Azure [Leia-me](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
2. [Adicionar a imagem para o mercado](azure-stack-add-vm-image.md). Certifique-se de que o `OSType` parâmetro está definido como `Linux`.
3. Depois de adicionar a imagem do Marketplace, é criado um item do Marketplace e os utilizadores podem implementar uma máquina virtual Linux.

## <a name="next-steps"></a>Passos seguintes
[Descrição geral da oferta de serviços na pilha do Azure](azure-stack-offer-services-overview.md)

