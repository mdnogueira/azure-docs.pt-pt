---
title: Implementar o modelo do Azure com o SAS token e o CLI do Azure | Microsoft Docs
description: "Utilize o Azure Resource Manager e a CLI do Azure para implementar recursos no Azure a partir de um modelo que está protegido com o SAS token."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 22387aadd8f53a65efb76a29a9403c46a2c25954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Implementar o modelo do Resource Manager privada com o SAS token e o CLI do Azure

Quando o modelo reside numa conta do storage, pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) de acesso partilhado durante a implementação. Este tópico explica como utilizar o Azure PowerShell com modelos do Resource Manager para fornecer um token SAS durante a implementação. 

## <a name="add-private-template-to-storage-account"></a>Adicionar modelo privado para a conta de armazenamento

Pode adicionar os modelos para uma conta de armazenamento e a ligação aos mesmos durante a implementação com um token SAS.

> [!IMPORTANT]
> Ao seguir os passos abaixo, o blob que contém o modelo está acessível para apenas o proprietário da conta. No entanto, quando cria um token SAS para o blob, o blob está acessível a qualquer pessoa com esse URI. Se outro utilizador intercetar o URI, esse utilizador é capaz de aceder ao modelo. A utilização de um token SAS é uma boa forma de limitar o acesso aos seus modelos, mas não deve incluir dados confidenciais, como palavras-passe diretamente no modelo.
> 
> 

O exemplo a seguir configura um contentor de conta de armazenamento privada e carrega um modelo:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Forneça o SAS token durante a implementação
Para implementar um modelo privado numa conta do storage, gerar um token SAS e incluí-la no URI para o modelo. Defina a hora de expiração para permitir tempo suficiente para concluir a implementação.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Para obter um exemplo de utilização de um token SAS com modelos ligados, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução à implementação de modelos, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy-cli.md).
* Para um script de exemplo completo que implementa um modelo, consulte [script de modelo de implementação Resource Manager](resource-manager-samples-cli-deploy.md)
* Para definir os parâmetros de modelo, consulte o artigo [criação de modelos](resource-group-authoring-templates.md#parameters).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).
