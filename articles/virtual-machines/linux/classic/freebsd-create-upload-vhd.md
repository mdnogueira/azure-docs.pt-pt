---
title: Criar e carregar uma imagem de FreeBSD VM | Microsoft Docs
description: "Saiba como criar e carregar um disco rígido virtual (VHD) que contém o sistema de operativo FreeBSD para criar uma máquina virtual do Azure"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Criar e carregar um VHD de FreeBSD para o Azure
Este artigo mostra como criar e carregar um disco rígido virtual (VHD) que contém o sistema de operativo FreeBSD. Depois de carregá-lo, pode utilizá-lo como a sua própria imagem para criar uma máquina virtual (VM) no Azure.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para obter informações sobre como carregar um VHD com o modelo do Resource Manager, consulte [aqui](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem os seguintes itens:

* **Uma subscrição do Azure**– se não tiver uma conta, pode criar um em apenas alguns minutos. Se tiver uma subscrição do MSDN, consulte [crédito do Azure mensal para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **Ferramentas do Azure PowerShell**– o Azure PowerShell módulo tem de ser instalado e configurado para utilizar a sua subscrição do Azure. Para transferir o módulo, consulte [Azure transfere](https://azure.microsoft.com/downloads/). Está disponível um tutorial que descreve como instalar e configurar o módulo. Utilize o [Azure transfere](https://azure.microsoft.com/downloads/) cmdlet para carregar o VHD.
* **Sistema de operativo FreeBSD instalado num ficheiro. vhd**– suportado FreeBSD instalado um sistema operativo tem de ser um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd. Por exemplo, pode utilizar uma solução de virtualização, tais como o Hyper-V para criar o ficheiro. vhd e instalar o sistema operativo. Para obter instruções sobre como instalar e utilizar o Hyper-V, consulte [instalar o Hyper-V e criar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O formato VHDX mais recente não é suportado no Azure. Converter disco em formato VHD utilizando o Gestor de Hyper-V ou o cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Além disso, não há um [tutorial no MSDN sobre como utilizar FreeBSD com Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Esta tarefa inclui os seguintes quatro passos:

## <a name="step-1-prepare-the-image-for-upload"></a>Passo 1: Preparar a imagem para carregamento
Na máquina virtual em que instalou o sistema de operativo FreeBSD, conclua os procedimentos seguintes:

1. Ative o DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Ative SSH.

    Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque. Por predefinição está ativada após a instalação a partir do disco de FreeBSD. 
3. Configure uma consola de série.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Instale o sudo.

    A conta de raiz está desativada no Azure. Isto significa que terá de utilizar o sudo de um utilizador sem privilégios para executar comandos com privilégios elevados.

        # pkg install sudo
   
5. Pré-requisitos para o agente do Azure.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Instale o agente do Azure.

    A versão mais recente do agente do Azure pode sempre ser encontrada no [github](https://github.com/Azure/WALinuxAgent/releases). A versão 2.0.10 + oficialmente suporta FreeBSD 10 & 10.1 e a versão 2.1.4 + (incluindo 2.2.x) suporta oficialmente FreeBSD 10.2 e versões posteriores.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0, vamos utilizar 2.0.16 como um exemplo:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1, vamos utilizar 2.1.4 como um exemplo:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Depois de instalar o agente do Azure, é boa ideia Certifique-se de que está em execução:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Desaprovisionar o sistema.

    O sistema para limpá-lo e torná-lo adequado para reprovisioning retirar o aprovisionamento. O comando seguinte também elimina a última conta de utilizador aprovisionado e os dados associados:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Agora pode desligar a VM.

## <a name="step-2-prepare-the-connection-to-azure"></a>Passo 2: Preparar a ligação para o Azure
Certifique-se de que está a utilizar a CLI do Azure no modelo de implementação clássica (`azure config mode asm`), em seguida, inicie sessão na sua conta:

```azurecli
azure login
```


<a id="upload"> </a>


## <a name="step-3-upload-the-vhd-file"></a>Passo 3: Carregar o ficheiro. vhd

Necessita de uma conta de armazenamento para carregar o ficheiro VHD para. Pode optar por escolher uma conta de armazenamento existente ou [criar um novo](../../../storage/common/storage-create-storage-account.md).

Ao carregar o ficheiro. vhd, pode colocá-lo em qualquer lugar no seu armazenamento de Blobs. Seguem-se alguns termos que será utilizado ao carregar o ficheiro:

* **BlobStorageURL** é o URL para a conta de armazenamento que criou no passo 2.
* **YourImagesFolder** é o contentor no armazenamento de BLOBs de onde pretende armazenar as imagens.
* **VHDName** é a etiqueta que aparece no portal clássico do Azure para identificar o disco rígido virtual.
* **PathToVHDFile** é o caminho completo e nome do ficheiro. vhd.

A partir da janela do PowerShell do Azure que utilizou no passo anterior, escreva:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>Passo 4: Criar uma VM com o ficheiro. vhd carregado
Depois de carregar o ficheiro. vhd, pode adicioná-lo como uma imagem para a lista de imagens personalizadas que estão associados com a sua subscrição e crie uma máquina virtual com esta imagem personalizada.

1. A partir da janela do PowerShell do Azure que utilizou no passo anterior, escreva:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Utilize o Linux como o tipo de SO. A versão atual do PowerShell do Azure aceita apenas "Linux" ou "Windows" como um parâmetro.
   >
   >
2. Depois de concluir os passos anteriores, a nova imagem está listada quando escolhe o **imagens** separador no portal clássico do Azure.  

    ![Escolha uma imagem](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Crie uma máquina virtual a partir da galeria. Esta nova imagem está agora disponível em **as minhas imagens**.
4. Selecione a imagem de novo. Em seguida, seguir as instruções para configurar um nome de anfitrião, a palavra-passe, a chave SSH e assim sucessivamente.

    ![Imagem personalizada](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. Depois de concluir o aprovisionamento, verá a VM FreeBSD em execução no Azure.

    ![Imagem de FreeBSD no azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
