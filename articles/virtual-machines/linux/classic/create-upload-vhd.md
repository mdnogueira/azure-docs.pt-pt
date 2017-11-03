---
title: Criar e carregar um VHD do Linux no Azure | Microsoft Docs
description: "Criar e carregar um Azure disco rígido virtual (VHD) que contém o sistema de operativo Linux utilizando o modelo de implementação clássica"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
ms.openlocfilehash: 23c30c954875598ce3e01db137b0ef8cda9779f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Criar e Carregar um Disco Rígido Virtual que Contenha o Sistema Operativo Linux
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Também pode [carregar uma imagem de disco personalizada com o Azure Resource Manager](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Este artigo mostra como criar e carregar um disco rígido virtual (VHD), pelo que pode utilizar como a sua própria imagem para criar máquinas virtuais no Azure. Saiba como preparar o sistema operativo, pelo que pode ser utilizado para criar várias máquinas virtuais com base nessa imagem. 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem os seguintes itens:

* **Sistema de operativo Linux instalado num ficheiro. vhd** -tiver instalado um [distribuição Linux aprovadas pelo Azure](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consulte [informações para distribuições não aprovadas](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) para um disco virtual no o formato VHD. Existem várias ferramentas para criar uma VM e os VHD:
  * Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), tendo cuidado para utilizar o VHD como o formato de imagem. Se necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) utilizando `qemu-img convert`.
  * Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O formato VHDX mais recente não é suportado no Azure. Quando cria uma VM, especifique o VHD como o formato. Se for necessário, pode converter discos VHDX VHD utilizando [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, o Azure não suporta o carregamento de VHDs dinâmicos, por isso terá de converter essas discos VHDs estáticos antes de carregar. Pode utilizar ferramentas como [utilitários de VHD do Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) converter discos dinâmicos durante o processo de carregamento para o Azure.

* **Interface de linha de comandos do Azure** -instale a versão mais recente [Interface de linha de comandos do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) para carregar o VHD.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Passo 1: Preparar a imagem a ser carregado
Azure suporta várias distribuições em Linux (consulte [distribuições aprovadas pelo](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os artigos seguintes descrevem como preparar as diversas distribuições de Linux que são suportadas no Azure. Depois de concluir os passos nos seguintes guias, volte aqui depois de ter um ficheiro VHD que está pronto para carregar para o Azure:

* **[Distribuições baseado em centOS](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Outras - distribuições não aprovadas](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> A plataforma do Azure que SLA se aplica a máquinas virtuais com o SO Linux apenas quando um das distribuições endorsed é utilizado com a configuração detalhes conforme especificado em versões suportadas em [Linux em Azure-Endorsed distribuições](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Todas as distribuições do Linux na galeria da imagem do Azure são distribuições aprovadas com a configuração necessária.
> 
> 

Consulte também o  **[Linux instalação notas](../create-upload-generic.md#general-linux-installation-notes)**  para dicas mais gerais sobre preparar imagens de Linux para o Azure.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Passo 2: Preparar a ligação para o Azure
Certifique-se de que está a utilizar a CLI do Azure no modelo de implementação clássica (`azure config mode asm`), em seguida, inicie sessão na sua conta:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Passo 3: Carregar a imagem para o Azure
Necessita de uma conta de armazenamento para carregar o ficheiro VHD para. Pode optar por escolher uma conta de armazenamento existente ou [criar um novo](../../../storage/common/storage-create-storage-account.md).

Utilize a CLI do Azure para carregar a imagem utilizando o seguinte comando:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

No exemplo anterior:

* **BlobStorageURL** é o URL para a conta de armazenamento que pretende utilizar
* **YourImagesFolder** é o contentor no armazenamento de BLOBs de onde pretende armazenar as imagens
* **VHDName** é a etiqueta que é apresentado no portal para identificar o disco rígido virtual.
* **PathToVHDFile** é o caminho completo e nome do ficheiro. vhd no seu computador.

O comando seguinte mostra um exemplo concluído:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Passo 4: Criar uma VM a partir da imagem
Criar uma VM utilizando `azure vm create` da mesma forma como uma VM regular. Especifique o nome que deu a sua imagem no passo anterior. No exemplo seguinte, utilizamos o **myImage** nome de imagem especificado no passo anterior:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Para criar os seus próprios VMs, forneça o seu próprio nome de utilizador + palavra-passe, localização, nome DNS e o nome da imagem.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte [referência da CLI do Azure para o modelo de implementação clássico do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
