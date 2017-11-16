---
title: "Exemplo de json de política do Azure - NSG x em cada sub-rede | Microsoft Docs"
description: "Esta política de exemplo de json requer que um grupo de segurança de rede específica é utilizado com todas as sub-redes virtuais."
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 5e7ef65686f20f291ba17498654a91fc212c5337
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="nsg-x-on-every-subnet"></a>NSG x em cada sub-rede

Esta política requer que um grupo de segurança de rede específica é utilizado com todas as sub-redes virtuais. Especifique o ID do grupo de segurança de rede a utilizar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

[!code-json[main](../../../policy-templates/samples/Network/enforce-nsg-on-subnet/azurepolicy.json "NSG X on every subnet")]

Pode implementar este modelo utilizando o [portal do Azure](#deploy-with-the-portal), com [PowerShell](#deploy-with-powershell) ou com o [CLI do Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementar com o portal

[![Implementar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fenforce-nsg-on-subnet%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implementar com o PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "enforce-nsg-on-subnet" -DisplayName "NSG X on every subnet" -description "This policy enforces a specific NSG on every subnet" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-subnet/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-subnet/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -nsgId <NSG Id> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implementação de PowerShell

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Implementar com o CLI do Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'enforce-nsg-on-subnet' --display-name 'NSG X on every subnet' --description 'This policy enforces a specific NSG on every subnet' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-subnet/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/enforce-nsg-on-subnet/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "enforce-nsg-on-subnet"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implementação da CLI do Azure

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

- Amostras de modelo de política do Azure adicionais são [modelos de política do Azure](../json-samples.md).
