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
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 44c46fff9ccf9c7dba9ee380faf5f8213b58e3c3
ms.openlocfilehash: 4397d84ef4d97bdee387777a193ec0b969f2d5e1


---
# <a name="different-ways-to-create-a-linux-vm-including-azure-cli-20-preview"></a>Diferentes formas de criar uma VM do Linux incluindo o Azure CLI 2.0 (Pré-visualização)
No Azure, tem a flexibilidade de criar máquinas virtuais (VMs) do Linux com ferramentas e fluxos de trabalho com os quais se sente confortável. Este artigo resume estas diferenças e mostra exemplos para criar as suas VMs do Linux.

## <a name="azure-cli"></a>CLI do Azure

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- Azure CLI 1.0 – CLI para os modelos de implementação de gestão clássica e de recursos
- [Azure CLI 2.0 (Pré-visualização)](../xplat-cli-install.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos

O Azure CLI 2.0 (Pré-visualização) está disponível para todas as plataformas através de um pacote npm, pacotes fornecidos pela distro ou contentor Docker. Certifique-se de que tem sessão iniciada, utilizando **az login**.

Os tutoriais seguintes fornecem exemplos sobre a utilização do Azure CLI 2.0 (Pré-visualização). Leia cada artigo para obter mais detalhes sobre os comandos apresentados:

* [Criar uma VM do Linux com o Azure CLI 2.0 (Pré-visualização)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Este exemplo cria um grupo de recursos com o nome myResourceGroup: 
    
    ```azurecli
    az group create -n myResourceGroup -l westus
    ```

  * Este exemplo cria uma VM no novo grupo de recursos com a imagem Debian mais recente com uma chave pública denominada `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mydns \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

* [Criar uma VM do Linux protegida com um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo seguinte cria uma VM ao utilizar um modelo armazenado no GitHub:
    
    ```azurecli
    az group deployment create -g myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Criar um ambiente completo do Linux com a CLI do Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Inclui a criação de um balanceador de carga e várias VMs num conjunto de disponibilidade.

* [Adicionar um disco a uma VM do Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo seguinte adiciona um disco de 5 GB a uma VM existente com o nome `myVM`:
    
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://myStorage.blob.core.windows.net/vhds/myDataDisk1.vhd
    ```

## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) permite-lhe criar rapidamente uma VM, uma vez que não existe nada para instalar no seu sistema. Utilize o Portal do Azure para criar a VM:

* [Criar uma VM do Linux através do portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Anexar um disco através do portal do Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Opções de imagem e sistema operativo
Quando cria uma VM; escolhe uma imagem com base no sistema operativo que pretende executar. O Azure e os respetivos parceiros oferecem várias imagens, algumas das quais incluem aplicações e ferramentas pré-instaladas. Ou então, carregue uma das suas próprias imagens (veja [a secção seguinte](#use-your-own-image)).

### <a name="azure-images"></a>Imagens do Azure
Utilize os comandos da CLI `az vm image` para ver o que está disponível por publicador, versão distro e compilações.

Listar publicadores disponíveis:

```azurecli
az vm image list-publishers -l WestUS
```

Listar produtos disponíveis (ofertas) para um determinado publicador:

```azurecli
az vm image list-offers --publisher-name Canonical -l WestUS
```

Listar SKUs disponíveis (versões distro) de uma determinada oferta:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer -l WestUS
```

Listar todas as imagens disponíveis para uma determinada versão:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS -l WestUS
```

Para obter mais exemplos da navegação e utilização de imagens disponíveis, veja [Navigate and select Azure virtual machine images with the Azure CLI (Navegar e selecionar imagens de máquinas virtuais do Azure com a CLI do Azure)](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O comando `az vm create` tem aliases que pode utilizar para aceder rapidamente a distros mais comuns e às respetivas versões mais recentes. Muitas vezes, é mais rápido utilizar aliases do que especificar o publicador, a oferta, o SKU e a versão sempre que cria uma VM:

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
* [Como capturar uma máquina virtual com Linux como um modelo do Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandos de exemplo de início rápido para capturar uma VM existente:
    
    ```azurecli
    az vm deallocate -g myResourceGroup -n myVM
    az vm generalize -g myResourceGroup -n myVM
    az vm capture -g myResourceGroup -n myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Passos seguintes
* Crie uma VM do Linux a partir do [portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) com a [CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou um [modelo do Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de criar uma VM do Linux, [adicione um disco de dados](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Passos rápidos para [repor uma palavra-passe ou chaves SSH e gerir utilizadores](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->


