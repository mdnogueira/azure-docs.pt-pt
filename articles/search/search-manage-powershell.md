---
title: Gerir a Azure Search com scripts do Powershell | Microsoft Docs
description: "Gerir o serviço da Azure Search com scripts do PowerShell. Criar ou atualizar um serviço da Azure Search e gerir chaves de administração de pesquisa do Azure"
services: search
documentationcenter: 
author: seansaleh
manager: mblythe
editor: 
tags: azure-resource-manager
ms.assetid: 9b3dc1f2-3619-4235-ba1f-d2d6f5c45dd5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: powershell
ms.date: 08/15/2016
ms.author: seasa
ms.openlocfilehash: aa51c846efef12461ec382274199bc049c42aaa3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Gerir o serviço de pesquisa do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Este tópico descreve os comandos do PowerShell para efetuar muitas das tarefas de gestão para os serviços de pesquisa do Azure. Iremos irá guiá-se a criação de um serviço de pesquisa, dimensionamento-lo e gerir as respetivas chaves de API.
Estes comandos paralela as opções de gestão disponíveis no [API de REST de gestão do Azure Search](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Pré-requisitos
* Tem de ter o Azure PowerShell 1.0 ou superior. Para obter instruções, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* Tem de ter sessão iniciada sua subscrição do Azure no PowerShell, conforme descrito abaixo.

Em primeiro lugar, tem de iniciar sessão no Azure com este comando:

    Login-AzureRmAccount

Especifique o endereço de e-mail da sua conta do Azure e a palavra-passe na caixa de diálogo de início de sessão da Microsoft Azure.

Em alternativa, pode [início de sessão forma não interativa com um principal de serviço](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Se tiver várias subscrições do Azure, tem de definir a sua subscrição do Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a subscrição, execute o seguinte comando. No exemplo seguinte, o nome da subscrição é `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Os comandos para o ajudar a começar a utilizar
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>Passos Seguintes
Agora que criou o seu serviço, pode efetuar os passos seguintes: criar um [índice](search-what-is-an-index.md), [consultar um índice](search-query-overview.md)e, finalmente, criar e gerir a sua própria aplicação de pesquisa que utiliza a Azure Search.

* [Criar um índice da Azure Search no portal do Azure](search-create-index-portal.md)
* [Consultar um índice da Azure Search utilizando o Explorador de pesquisa no portal do Azure](search-explorer.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Como utilizar a pesquisa do Azure no .NET](search-howto-dotnet-sdk.md)
* [Analisar o tráfego de pesquisa do Azure](search-traffic-analytics.md)

