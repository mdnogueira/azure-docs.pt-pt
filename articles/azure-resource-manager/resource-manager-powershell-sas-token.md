---
title: Implementar o modelo do Azure com o SAS token e o PowerShell | Microsoft Docs
description: "Utilize o Azure Resource Manager e o Azure PowerShell para implementar recursos no Azure a partir de um modelo que está protegido com o SAS token."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 1e3cea027b599e2b1af1ced0fdf14e2cc8a0db82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Implementar o modelo do Resource Manager privada com o SAS token e o Azure PowerShell

Quando o modelo reside numa conta do storage, pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) de acesso partilhado durante a implementação. Este tópico explica como utilizar o Azure PowerShell com modelos do Resource Manager para fornecer um token SAS durante a implementação. 

## <a name="add-private-template-to-storage-account"></a>Adicionar modelo privado para a conta de armazenamento

Pode adicionar os modelos para uma conta de armazenamento e a ligação aos mesmos durante a implementação com um token SAS.

> [!IMPORTANT]
> Ao seguir os passos abaixo, o blob que contém o modelo está acessível para apenas o proprietário da conta. No entanto, quando cria um token SAS para o blob, o blob está acessível a qualquer pessoa com esse URI. Se outro utilizador intercetar o URI, esse utilizador é capaz de aceder ao modelo. A utilização de um token SAS é uma boa forma de limitar o acesso aos seus modelos, mas não deve incluir dados confidenciais, como palavras-passe diretamente no modelo.
> 
> 

O exemplo a seguir configura um contentor de conta de armazenamento privada e carrega um modelo:
   
```powershell
# create a storage account for templates
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Forneça o SAS token durante a implementação
Para implementar um modelo privado numa conta do storage, gerar um token SAS e incluí-la no URI para o modelo. Defina a hora de expiração para permitir tempo suficiente para concluir a implementação.
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Para obter um exemplo de utilização de um token SAS com modelos ligados, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Passos seguintes
* Para uma introdução à implementação de modelos, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md).
* Para um script de exemplo completo que implementa um modelo, consulte [script de modelo de implementação Resource Manager](resource-manager-samples-powershell-deploy.md)
* Para definir os parâmetros de modelo, consulte o artigo [criação de modelos](resource-group-authoring-templates.md#parameters).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

