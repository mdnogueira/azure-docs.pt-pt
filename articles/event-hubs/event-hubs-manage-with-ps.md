---
title: Utilizar o PowerShell para gerir os recursos de Event Hubs do Azure | Microsoft Docs
description: "Utilizar o módulo do PowerShell para criar e gerir os Event Hubs"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 2b49c01153b1104612e6ebf9c88566fc40d1f635
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Utilizar o PowerShell para gerir os recursos de Event Hubs

Microsoft Azure PowerShell é um ambiente de script que pode utilizar para controlar e automatizar a implementação e gestão de serviços do Azure. Este artigo descreve como utilizar o [módulo PowerShell do Gestor de recursos de Hubs de eventos](/powershell/module/azurerm.eventhub) aprovisionar e gerir as entidades de Event Hubs (espaços de nomes, os hubs de eventos individuais e grupos de consumidores) utilizando uma consola local do Azure PowerShell ou script.

Também pode gerir recursos de Event Hubs através de modelos Azure Resource Manager. Para obter mais informações, consulte o artigo [criar um espaço de nomes de Event Hubs com o grupo de concentrador hub- and consumidor de eventos através de um modelo Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, irá precisar do seguinte:

* Uma subscrição do Azure. Para obter mais informações sobre a obtenção de uma subscrição, consulte [opções de compra][purchase options], [ofertas de membros][member offers], ou [conta gratuita][free account].
* Um computador com o Azure PowerShell. Para obter instruções, consulte [introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps).
* Uma compreensão geral de scripts do PowerShell, os pacotes de NuGet e o .NET Framework.

## <a name="get-started"></a>Introdução

O primeiro passo consiste em utilizar o PowerShell para iniciar sessão na sua conta do Azure e subscrição do Azure. Siga as instruções em [introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps) para iniciar sessão sua conta do Azure, em seguida, obter e aceder aos recursos na sua subscrição do Azure.

## <a name="provision-an-event-hubs-namespace"></a>Aprovisionar um espaço de nomes de Event Hubs

Ao trabalhar com espaços de nomes de Event Hubs, pode utilizar o [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [remover AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace), e [conjunto AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace) cmdlets.

Este exemplo cria algumas variáveis locais do script; `$Namespace` e `$Location`.

* `$Namespace`é o nome do espaço de nomes dos Event Hubs com a qual podemos pretende trabalhar.
* `$Location`identifica o Centro de dados na qual iremos aprovisionará o espaço de nomes.
* `$CurrentNamespace`armazena o espaço de nomes de referência que vamos obter (ou crie).

Um script real, `$Namespace` e `$Location` podem ser transmitidos como parâmetros.

Esta parte do script faz o seguinte:

1. A tentar obter um espaço de nomes de Event Hubs com o nome especificado.
2. Se o espaço de nomes for encontrado, relatórios que foi encontrado.
3. Se não for encontrado o espaço de nomes, cria o espaço de nomes e, em seguida, obtém o espaço de nomes criado recentemente.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Para criar um hub de eventos, efetue uma verificação de espaço de nomes utilizando o script na secção anterior. Em seguida, utilize o [New-AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) cmdlet para criar o hub de eventos:

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Para criar um grupo de consumidores dentro de um hub de eventos, execute o hub de espaço de nomes e eventos verifica a utilizar os scripts na secção anterior. Em seguida, utilize o [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) cmdlet para criar o grupo de consumidores dentro do hub de eventos. Por exemplo:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Metadados de utilizador do conjunto

Depois de executar os scripts nas secções anteriores, pode utilizar o [conjunto AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) cmdlet para atualizar as propriedades de um grupo de consumidores, como no exemplo seguinte:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Remover o hub de eventos

Para remover os event hubs que criou, pode utilizar o `Remove-*` cmdlets, como no exemplo seguinte:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Passos seguintes

- Consulte a documentação de módulo do PowerShell do Gestor de recursos de Hubs de eventos concluída [aqui](/powershell/module/azurerm.eventhub). Esta página apresenta uma lista de todos os cmdlets disponíveis.
- Para obter informações sobre como utilizar os modelos Azure Resource Manager, consulte o artigo [criar um espaço de nomes de Event Hubs com o grupo de concentrador hub- and consumidor de eventos através de um modelo Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Informações sobre [bibliotecas de gestão de .NET de Hubs de eventos](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
