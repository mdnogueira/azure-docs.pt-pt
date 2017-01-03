---
title: Diferentes formas para criar uma VM com Linux | Microsoft Docs
description: "Conheça as diferentes formas de criar uma máquina virtual do Linux no Azure, incluindo ligações para ferramentas e tutoriais para cada método."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 8835427415e8e01e851796eaf323bce7d1918c8c
ms.openlocfilehash: 8c7ea2e7131f69bc43f2e82b816efdfbda59e85d


---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Diferentes formas de criar uma máquina virtual do Linux no Azure
No Azure, tem a flexibilidade de criar máquinas virtuais (VMs) do Linux com ferramentas e fluxos de trabalho com os quais se sente confortável. Este artigo resume estas diferenças e mostra exemplos para criar as suas VMs do Linux.

## <a name="azure-cli"></a>CLI do Azure
A CLI do Azure está disponível para todas as plataformas através de um pacote npm, pacotes fornecidos pela distro ou contentor Docker. Pode ler mais sobre [como instalar e configurar a CLI do Azure](../xplat-cli-install.md). Os tutoriais seguintes fornecem exemplos sobre como utilizar a CLI do Azure. Leia cada artigo para obter mais detalhes sobre os comandos de início rápido da CLI mostrados:

* [Criar uma VM do Linux a partir da CLI do Azure para desenvolvimento e teste](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo seguinte cria uma VM CoreOS com uma chave pública com o nome `azure_id_rsa.pub`:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Criar uma VM do Linux protegida com um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo seguinte cria uma VM ao utilizar um modelo armazenado no GitHub:
    
    ```azurecli
    azure group create --name myResourceGroup --location WestUS 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Criar um ambiente completo do Linux com a CLI do Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Inclui a criação de um balanceador de carga e várias VMs num conjunto de disponibilidade.
* [Adicionar um disco a uma VM do Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo seguinte adiciona um disco de 5 GB a uma VM existente com o nome `TestVM`:
    
    ```azurecli
    azure vm disk attach-new --resource-group myResourceGroup  --vm-name myVM \
      --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) permite-lhe criar rapidamente uma VM, uma vez que não existe nada para instalar no seu sistema. Utilize o Portal do Azure para criar a VM:

* [Criar uma VM do Linux através do portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Anexar um disco através do portal do Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Opções de imagem e sistema operativo
Quando cria uma VM; escolhe uma imagem com base no sistema operativo que pretende executar. O Azure e os respetivos parceiros oferecem várias imagens, algumas das quais incluem aplicações e ferramentas pré-instaladas. Ou então, carregue uma das suas próprias imagens (veja [a secção seguinte](#use-your-own-image)).

### <a name="azure-images"></a>Imagens do Azure
Utilize os comandos da CLI `azure vm image` para ver o que está disponível por publicador, versão distro e compilações.

Liste os publicadores disponíveis da seguinte forma:

```azurecli
azure vm image list-publishers --location WestUS
```

Liste os produtos disponíveis (ofertas) de um determinado publicador da seguinte forma:

```azurecli
azure vm image list-offers --location WestUS --publisher Canonical
```

Liste os SKUs disponíveis (versões distro) de uma determinada oferta da seguinte forma:

```azurecli
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Liste todas as imagens disponíveis para uma determinada versão da seguinte forma:

```azurecli
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Para obter mais exemplos da navegação e utilização de imagens disponíveis, veja [Navigate and select Azure virtual machine images with the Azure CLI (Navegar e selecionar imagens de máquinas virtuais do Azure com a CLI do Azure)](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Os comandos `azure vm quick-create` e `azure vm create` também têm aliases que pode utilizar para aceder rapidamente a distros mais comuns e às respetivas versões mais recentes. Muitas vezes, é mais rápido utilizar aliases do que especificar o publicador, a oferta, o SKU e a versão sempre que cria uma VM:

| Alias | Publicador | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |
| Debian |credativ |Debian |8 |mais recente |
| openSUSE |SUSE |openSUSE |13.2 |mais recente |
| RHEL |Redhat |RHEL |7.2 |mais recente |
| SLES |SLES |SLES |12-SP1 |mais recente |
| UbuntuLTS |Canónico |UbuntuServer |14.04.4-LTS |mais recente |

### <a name="use-your-own-image"></a>Utilizar a sua própria imagem
Se precisar de personalizações específicas, pode utilizar uma imagem com base numa VM do Azure existente ao *capturar* essa VM. Também pode carregar uma imagem criada no local. Para obter mais informações sobre distros suportados e como utilizar as suas imagens, consulte os artigos seguintes:

* [Distribuições aprovadas pelo Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Informações sobre distribuições não aprovadas](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Carregar e criar uma VM Linux a partir da imagem de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Como capturar uma máquina virtual com Linux como um modelo do Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandos de exemplo de início rápido para capturar uma VM existente:
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Passos seguintes
* Crie uma VM do Linux a partir do [portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) com a [CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou um [modelo do Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de criar uma VM do Linux, [adicione um disco de dados](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Passos rápidos para [repor uma palavra-passe ou chaves SSH e gerir utilizadores](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Dec16_HO5-->


