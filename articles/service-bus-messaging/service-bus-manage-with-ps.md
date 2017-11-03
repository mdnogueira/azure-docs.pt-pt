---
title: Utilizar o PowerShell para gerir recursos do Azure Service Bus | Microsoft Docs
description: "Utilizar o módulo do PowerShell para criar e gerir recursos do Service Bus"
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 1520cea72069be5d7bb078a3770d61fade26b853
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Utilize o PowerShell para gerir recursos do Service Bus

Microsoft Azure PowerShell é um ambiente de script que pode utilizar para controlar e automatizar a implementação e gestão de serviços do Azure. Este artigo descreve como utilizar o [módulo PowerShell do Gestor de recursos do Service Bus](/powershell/module/azurerm.servicebus) aprovisionar e gerir entidades do Service Bus (espaços de nomes, filas, tópicos e subscrições) utilizando uma consola local do Azure PowerShell ou script.

Também pode gerir entidades do Service Bus através de modelos Azure Resource Manager. Para obter mais informações, consulte o artigo [recursos de criar o Service Bus através de modelos Azure Resource Manager](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, terá os seguintes pré-requisitos:

* Uma subscrição do Azure. Para obter mais informações sobre a obtenção de uma subscrição, consulte [opções de compra][purchase options], [ofertas de membros][member offers], ou [conta gratuita][free account].
* Um computador com o Azure PowerShell. Para obter instruções, consulte [introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps).
* Uma compreensão geral de scripts do PowerShell, os pacotes de NuGet e o .NET Framework.

## <a name="get-started"></a>Introdução

O primeiro passo consiste em utilizar o PowerShell para iniciar sessão na sua conta do Azure e subscrição do Azure. Siga as instruções em [introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps) para iniciar sessão sua conta do Azure e obter e aceder aos recursos na sua subscrição do Azure.

## <a name="provision-a-service-bus-namespace"></a>Aprovisionar um espaço de nomes do Service Bus

Ao trabalhar com espaços de nomes de barramento de serviço, pode utilizar o [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [remover AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace), e [conjunto AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace) cmdlets.

Este exemplo cria algumas variáveis locais do script; `$Namespace` e `$Location`.

* `$Namespace`é o nome do espaço de nomes de barramento de serviço com o qual podemos pretende trabalhar.
* `$Location`identifica o Centro de dados na qual iremos aprovisionar o espaço de nomes.
* `$CurrentNamespace`armazena o espaço de nomes de referência que vamos obter (ou crie).

Um script real, `$Namespace` e `$Location` podem ser transmitidos como parâmetros.

Esta parte do script faz o seguinte:

1. A tentar obter um espaço de nomes do Service Bus com o nome especificado.
2. Se o espaço de nomes for encontrado, relatórios que foi encontrado.
3. Se não for encontrado o espaço de nomes, cria o espaço de nomes e, em seguida, obtém o espaço de nomes criado recentemente.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Criar uma regra de autorização de espaço de nomes

O exemplo seguinte mostra como gerir regras de autorização de espaço de nomes utilizando o [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [conjunto AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule), e [remover AzureRmServiceBusNamespaceAuthorizationRule cmdlets](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusNamespaceKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
}
```

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila ou um tópico, execute uma verificação de espaço de nomes utilizando o script na secção anterior. Em seguida, crie a fila:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modificar as propriedades da fila

Depois de executar o script na secção anterior, pode utilizar o [conjunto AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) cmdlet para atualizar as propriedades de uma fila, como no exemplo seguinte:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Aprovisionamento outras entidades do Service Bus

Pode utilizar o [módulo PowerShell do barramento de serviço](/powershell/module/azurerm.servicebus) para aprovisionar outras entidades, como os tópicos e subscrições. Estes cmdlets são sintaticamente semelhantes para os cmdlets de criação de fila demonstrados na secção anterior.

## <a name="next-steps"></a>Passos seguintes

- Consulte a documentação de módulo do PowerShell do Gestor de recursos de barramento de serviço completa [aqui](/powershell/module/azurerm.servicebus). Esta página apresenta uma lista de todos os cmdlets disponíveis.
- Para obter informações sobre como utilizar os modelos Azure Resource Manager, consulte o artigo [recursos de criar o Service Bus através de modelos Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informações sobre [bibliotecas de gestão do Service Bus .NET](service-bus-management-libraries.md).

Existem algumas formas alternativas para gerir as entidades do Service Bus, conforme descrito nestas publicações no blogue:

* [Como criar filas do Service Bus, tópicos e subscrições através de um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Como criar um espaço de nomes de barramento de serviço e um Hub de eventos utilizando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Scripts do PowerShell de Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
