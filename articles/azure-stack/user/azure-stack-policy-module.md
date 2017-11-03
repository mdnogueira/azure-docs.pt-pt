---
title: "Utilizar o módulo de política de pilha do Azure | Microsoft Docs"
description: "Saiba como restringir uma subscrição do Azure se comporte como uma subscrição de pilha do Azure"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e505c52a5e1897d5626ee2cacce9fa3eff12fbbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gestão da política do Azure utilizando o módulo de política de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

O módulo de política de pilha do Azure permite-lhe configurar uma subscrição do Azure com o controlo de versões do mesmo e a disponibilidade do serviço como a pilha do Azure.  O módulo utiliza a **New-AzureRMPolicyAssignment** cmdlet para criar uma política do Azure, o que limita a tipos de recursos e serviços disponíveis numa subscrição.  Depois de concluído, pode utilizar a sua subscrição do Azure para desenvolver aplicações direcionadas para a pilha do Azure.  

## <a name="install-the-module"></a>Instalar o módulo
1. Instale a versão necessária do módulo do AzureRM PowerShell, conforme descrito no passo 1 de [instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).   
2. [Transferir as ferramentas de pilha do Azure a partir do GitHub](azure-stack-powershell-download.md)  
3. [Configurar o PowerShell para utilização com o Azure Stack](azure-stack-powershell-configure-user.md)

4. Importe o módulo de AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>Aplicar a política de subscrição
O seguinte comando pode ser utilizado para aplicar uma política de Azure pilha predefinida contra a sua subscrição do Azure. Antes de executar, substitua *nome da subscrição do Azure* com a sua subscrição do Azure.

```PowerShell
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzureStackRmPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
$rgName = 'AzureStack'
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Aplicar a política a um grupo de recursos
Poderá aplicar políticas de um método mais granular.  Por exemplo, poderá ter outros recursos em execução na mesma subscrição.  Pode definir o âmbito da aplicação de política para um grupo de recurso específico, o que lhe permite testar as suas aplicações para a pilha do Azure através de recursos do Azure. Antes de executar, substitua *nome da subscrição do Azure* com o nome da sua subscrição do Azure.

```PowerShell
$resourceGroupName = ‘myRG01’
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzureStackRmPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Política em ação
Depois de implementar a política do Azure, receber um erro ao tentar implementar um recurso que proibida pela política.  

![Resultado da falha de implementação de recursos devido a restrição de política](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Passos seguintes
[Implementar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)

[Implementar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)

[Implementar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
