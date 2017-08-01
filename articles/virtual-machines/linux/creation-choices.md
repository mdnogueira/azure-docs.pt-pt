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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b2f93579eb1c7a69d0dbd1b0ef112aed9b2168c3
ms.contentlocale: pt-pt
ms.lasthandoff: 05/31/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Diferentes formas de criar uma VM com Linux
No Azure, tem a flexibilidade de criar máquinas virtuais (VMs) do Linux com ferramentas e fluxos de trabalho com os quais se sente confortável. Este artigo resume estas diferenças e mostra exemplos para criar as suas VMs do Linux. incluindo a CLI 2.0 do Azure. Também pode ver opções de criação, incluindo a [CLI 1.0 do Azure](creation-choices-nodejs.md).

A [CLI 2.0 do Azure](/cli/azure/install-az-cli2) está disponível para todas as plataformas através de um pacote npm, pacotes fornecidos pela distro ou contentor Docker. Instale a compilação mais adequada para o seu ambiente e inicie sessão através de uma conta do Azure com [az login](/cli/azure/#login)

* [Criar uma VM do Linux com a CLI 2.0 do Azure](quick-create-cli.md)
  
  * Crie um grupo de recursos com [az group create](/cli/azure/group#create) denominado *myResourceGroup*: 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * Crie uma VM com [az vm create](/cli/azure/vm#create) denominada *myVM* ao utilizar a mais recente imagem *UbuntuLTS* e efetue a gestão de chaves SSH, caso ainda não existam em *~/.ssh*:

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Criar uma VM do Linux com um modelo do Azure](create-ssh-secured-vm-from-template.md)
  
  * O exemplo seguinte utiliza [az group deployment create](/cli/azure/group/deployment#create) para criar uma VM a partir de um modelo armazenado no GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Criar uma VM do Linux e personalizar com cloud-init](tutorial-automate-vm-deployment.md)

* [Criar uma aplicação com balanceamento de carga e elevada disponibilidade em várias VMs do Linux](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) permite-lhe criar rapidamente uma VM, uma vez que não existe nada para instalar no seu sistema. Utilize o Portal do Azure para criar a VM:

* [Criar uma VM do Linux através do portal do Azure](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>Opções de imagem e sistema operativo
Quando cria uma VM; escolhe uma imagem com base no sistema operativo que pretende executar. O Azure e os respetivos parceiros oferecem várias imagens, algumas das quais incluem aplicações e ferramentas pré-instaladas. Ou então, carregue uma das suas próprias imagens (veja [a secção seguinte](#use-your-own-image)).

### <a name="azure-images"></a>Imagens do Azure
Utilize os comandos [az vm image](/cli/azure/vm/image) para ver o que está disponível por publicador, versão distro e compilações.

Listar publicadores disponíveis:

```azurecli
az vm image list-publishers --location eastus
```

Listar produtos disponíveis (ofertas) para um determinado publicador:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

Listar SKUs disponíveis (versões distro) de uma determinada oferta:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

Listar todas as imagens disponíveis para uma determinada versão:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

Para obter mais exemplos da navegação e utilização de imagens disponíveis, veja [Navigate and select Azure virtual machine images with the Azure CLI (Navegar e selecionar imagens de máquinas virtuais do Azure com a CLI do Azure)](cli-ps-findimage.md).

O comando [az vm create](/cli/azure/vm#create) tem aliases que pode utilizar para aceder rapidamente as distros mais comuns e às respetivas versões mais recentes. Muitas vezes, é mais rápido utilizar aliases do que especificar o publicador, a oferta, o SKU e a versão sempre que cria uma VM:

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
Se precisar de personalizações específicas, pode utilizar uma imagem com base numa VM do Azure existente ao capturar essa VM. Também pode carregar uma imagem criada no local. Para obter mais informações sobre distros suportados e como utilizar as suas imagens, consulte os artigos seguintes:

* [Distribuições aprovadas pelo Azure](endorsed-distros.md)
* [Informações sobre distribuições não aprovadas](create-upload-generic.md)
* [Como criar uma imagem a partir de uma VM do Azure existente](tutorial-custom-images.md).
  
  * Comandos de exemplo de início rápido para criar uma imagem a partir de uma VM do Azure existente:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>Passos seguintes
* Crie uma VM do Linux com a [CLI](quick-create-cli.md), a partir do [portal](quick-create-portal.md) ou através de um [modelo do Azure Resource Manager](../windows/cli-deploy-templates.md).
* Depois de criar uma VM do Linux, [saiba mais sobre os discos e o armazenamento do Azure](tutorial-manage-disks.md).
* Passos rápidos para [repor uma palavra-passe ou chaves SSH e gerir utilizadores](using-vmaccess-extension.md).

