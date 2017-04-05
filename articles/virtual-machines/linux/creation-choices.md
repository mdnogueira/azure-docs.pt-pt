---
title: Diferentes formas de criar uma VM do Linux no Azure| Microsoft Azure
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
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Diferentes formas de criar uma VM com Linux
No Azure, tem a flexibilidade de criar máquinas virtuais (VMs) do Linux com ferramentas e fluxos de trabalho com os quais se sente confortável. Este artigo resume estas diferenças e mostra exemplos para criar as suas VMs do Linux. incluindo a CLI 2.0 do Azure. Também pode ver opções de criação, incluindo a [CLI 1.0 do Azure](creation-choices-nodejs.md).

A [CLI 2.0 do Azure](/cli/azure/install-az-cli2) está disponível para todas as plataformas através de um pacote npm, pacotes fornecidos pela distro ou contentor Docker. Instale a compilação mais adequada para o seu ambiente e inicie sessão através de uma conta do Azure com [az login](/cli/azure/#login)

Os exemplos seguintes utilizam a CLI 2.0 do Azure. Leia cada artigo para obter mais detalhes sobre os comandos apresentados. Também pode encontrar exemplos das opções de criação do Linux através do [Azure CLI 1.0](creation-choices-nodejs.md).

* [Criar uma VM com Linux através da CLI do Azure 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Este exemplo utiliza [az group create](/cli/azure/group#create) para criar um grupo de recursos denominado `myResourceGroup`: 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * Este exemplo utiliza [az vm create](/cli/azure/vm#create) para criar uma VM denominada `myVM` com a imagem Debian mais recente com Managed Disks do Azure e uma chave pública denominada `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Se pretender utilizar discos não geridos, adicione o sinalizador `--use-unmanaged-disks` no comando acima. É criada uma conta de armazenamento para si. Para obter mais informações, veja [Azure Managed Disks overview (Descrição geral dos Managed Disks do Azure)](../../storage/storage-managed-disks-overview.md).

* [Criar uma VM do Linux protegida com um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo seguinte utiliza [az group deployment create](/cli/azure/group/deployment#create) para criar uma VM através de um modelo armazenado no GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Criar um ambiente completo do Linux com a CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Inclui a criação de um balanceador de carga e várias VMs num conjunto de disponibilidade.

* [Adicionar um disco a uma VM do Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo seguinte utiliza [az vm disk attach-new](/cli/azure/vm/disk#attach-new) para adicionar um disco gerido de 50 GB a uma VM existente denominada `myVM`:
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) permite-lhe criar rapidamente uma VM, uma vez que não existe nada para instalar no seu sistema. Utilize o Portal do Azure para criar a VM:

* [Criar uma VM do Linux através do portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Anexar um disco através do portal do Azure](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Opções de imagem e sistema operativo
Quando cria uma VM; escolhe uma imagem com base no sistema operativo que pretende executar. O Azure e os respetivos parceiros oferecem várias imagens, algumas das quais incluem aplicações e ferramentas pré-instaladas. Ou então, carregue uma das suas próprias imagens (veja [a secção seguinte](#use-your-own-image)).

### <a name="azure-images"></a>Imagens do Azure
Utilize os comandos [az vm image](/cli/azure/vm/image) para ver o que está disponível por publicador, versão distro e compilações.

Listar publicadores disponíveis:

```azurecli
az vm image list-publishers --location WestUS
```

Listar produtos disponíveis (ofertas) para um determinado publicador:

```azurecli
az vm image list-offers --publisher Canonical --location WestUS
```

Listar SKUs disponíveis (versões distro) de uma determinada oferta:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

Listar todas as imagens disponíveis para uma determinada versão:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Para obter mais exemplos da navegação e utilização de imagens disponíveis, veja [Navigate and select Azure virtual machine images with the Azure CLI (Navegar e selecionar imagens de máquinas virtuais do Azure com a CLI do Azure)](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O comando **az vm create** tem aliases que pode utilizar para aceder rapidamente as distros mais comuns e às respetivas versões mais recentes. Muitas vezes, é mais rápido utilizar aliases do que especificar o publicador, a oferta, o SKU e a versão sempre que cria uma VM:

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

* [Distribuições aprovadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Informações sobre distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Como capturar uma máquina virtual com Linux como um modelo do Resource Manager](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandos de exemplo **az vm** de início rápido para capturar uma VM existente com discos não geridos:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Passos seguintes
* Crie uma VM do Linux a partir do [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) com a [CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou um [modelo do Azure Resource Manager](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de criar uma VM do Linux, [adicione um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Passos rápidos para [repor uma palavra-passe ou chaves SSH e gerir utilizadores](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

