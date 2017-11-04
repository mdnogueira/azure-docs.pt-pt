---
title: Configurar o Cofre de chaves do Azure para VMs com Linux | Microsoft Docs
description: "Como configurar o Cofre de chaves para utilização com uma máquina virtual do Azure Resource Manager com o CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: 2cc9b4c978e9a4deb0c8443c4b0f9e301a7cf492
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Como configurar o Cofre de chaves para máquinas virtuais com o 2.0 CLI do Azure

Na pilha do Azure Resource Manager, os segredos/certificados são modelados como recursos que são fornecidos pelo Cofre de chaves. Para saber mais sobre o Cofre de chaves do Azure, consulte o artigo [que é o Cofre de chaves do Azure?](../../key-vault/key-vault-whatis.md) Para que o Cofre de chaves ser utilizado com VMs do Azure Resource Manager, o *EnabledForDeployment* propriedade no Cofre de chaves tem de ser definida como true. Este artigo mostra como configurar o Cofre de chaves para utilização com máquinas virtuais do Azure (VMs) com o 2.0 CLI do Azure. Também pode efetuar estes passos com a [CLI 1.0 do Azure](key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para executar estes passos, terá da versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

## <a name="create-a-key-vault"></a>Criar um Key Vault
Criar um cofre de chaves e atribuir a política de implementação com [az keyvault criar](/cli/azure/keyvault#create). O exemplo seguinte cria um cofre de chaves com o nome `myKeyVault` no `myResourceGroup` grupo de recursos:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Atualizar um cofre de chaves para utilização com VMs
Conjunto a política de implementação numa chave do cofre com [atualização de keyvault az](/cli/azure/keyvault#update). O seguinte procedimento atualiza o Cofre de chaves com o nome `myKeyVault` no `myResourceGroup` grupo de recursos:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Utilizar modelos para configurar o Cofre de chaves
Quando utiliza um modelo, tem de definir o `enabledForDeployment` propriedade `true` para a chave de cofre recurso da seguinte forma:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Para outras opções que pode configurar quando criar um cofre de chaves utilizando os modelos, consulte [criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
