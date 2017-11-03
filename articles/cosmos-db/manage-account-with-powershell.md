---
title: "Automatização do Azure Cosmos DB - gestão com o Powershell | Microsoft Docs"
description: Utilize o Azure Powershell gerir as contas de base de dados do Azure Cosmos.
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: 25c543528119410dff0684845a713dcb0d6151d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Criar uma conta de base de dados do Azure Cosmos através do PowerShell

O guia seguinte descreve os comandos para automatizar a gestão das suas contas de base de dados de base de dados do Azure Cosmos com o Azure Powershell. Também inclui comandos para gerir chaves de conta e prioridades de ativação pós-falha no [contas de base de dados de multirregião][scaling-globally]. Atualizar a sua conta de base de dados permite-lhe modificar as políticas de consistência e adicionar/remover regiões. Para a gestão de plataforma da sua conta de base de dados do Azure Cosmos, pode utilizar tanto [CLI do Azure](cli-samples.md), a [API de REST do fornecedor de recursos][rp-rest-api], ou o [portal do Azure](create-documentdb-dotnet.md#create-account).

## <a name="getting-started"></a>Introdução

Siga as instruções em [como instalar e configurar o Azure PowerShell] [ powershell-install-configure] para instalar e iniciar sessão sua conta do Azure Resource Manager no Powershell.

### <a name="notes"></a>Notas

* Se gostaria de executar os seguintes comandos sem necessidade de confirmação do utilizador, acrescentar o `-Force` sinalizador para o comando.
* Os seguintes comandos são síncronos.

## <a id="create-documentdb-account-powershell"></a>Criar uma conta do Azure Cosmos DB

Este comando permite-lhe criar uma conta de base de dados de base de dados do Azure Cosmos. Configurar a sua nova conta de base de dados como a região único ou [multirregião] [ scaling-globally] com uma determinada [política consistência](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>`O nome de localização da região de escrita da conta de base de dados. Esta localização é necessário ter um valor de prioridade de ativação pós-falha de 0. É necessário exatamente uma região de escrita por conta de base de dados.
* `<read-region-location>`O nome de localização da região de leitura da conta de base de dados. Esta localização é necessário ter um valor de prioridade de ativação pós-falha de maior que 0. Pode existir mais do que um regiões leitura por conta de base de dados.
* `<ip-range-filter>`Especifica o conjunto de endereços IP ou intervalos de endereços IP no formato CIDR ser incluído como uma lista de permitidos do cliente IPs de uma conta de base de dados específica. Endereços/intervalos IP tem de ser vírgula separados e não pode conter quaisquer espaços. Para obter mais informações, consulte [suporte de Firewall de base de dados do Azure Cosmos](firewall-support.md)
* `<default-consistency-level>`O nível de consistência predefinida da conta de base de dados do Azure Cosmos. Para obter mais informações, consulte [níveis de consistência na base de dados do Azure Cosmos](consistency-levels.md).
* `<max-interval>`Quando utilizado com consistência de consistência vinculada, este valor representa o período de tempo de vinculada (em segundos) tolerated. Intervalo aceite para este valor é 1-100.
* `<max-staleness-prefix>`Quando utilizado com consistência de consistência vinculada, este valor representa o número de pedidos obsoletos tolerated. Intervalo aceite para este valor é 1 – 2 147 483 647.
* `<resource-group-name>`O nome do [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<resource-group-location>`A localização do grupo de recursos do Azure para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<database-account-name>`O nome da conta de base de dados do Azure Cosmos DB a ser criado. Letras minúsculas, números, só pode utilizar o '-' carateres e tem de ter entre 3 e 50 carateres.

Exemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Notas
* O exemplo anterior cria uma conta de base de dados com duas regiões. Também é possível criar uma conta de base de dados com uma região (que é designada como a região de escrita e de ter um valor de prioridade de ativação pós-falha de 0) ou mais de duas regiões. Para obter mais informações, consulte [contas de base de dados de multirregião][scaling-globally].
* As localizações tem de ser regiões na qual a base de dados do Azure Cosmos estiver geralmente disponível. A atual lista de regiões é fornecida no [página regiões do Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a>Atualizar uma conta de base de dados do DocumentDB

Este comando permite-lhe atualizar as propriedades da conta de base de dados Azure Cosmos DB. Isto inclui a política de consistência e as localizações que a conta de base de dados existe.

> [!NOTE]
> Este comando permite-lhe adicionar e remover regiões, mas não lhe permite modificar as prioridades de ativação pós-falha. Para modificar as prioridades de ativação pós-falha, consulte [abaixo](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>`O nome de localização da região de escrita da conta de base de dados. Esta localização é necessário ter um valor de prioridade de ativação pós-falha de 0. É necessário exatamente uma região de escrita por conta de base de dados.
* `<read-region-location>`O nome de localização da região de leitura da conta de base de dados. Esta localização é necessário ter um valor de prioridade de ativação pós-falha de maior que 0. Pode existir mais do que um regiões leitura por conta de base de dados.
* `<default-consistency-level>`O nível de consistência predefinida da conta de base de dados do Azure Cosmos. Para obter mais informações, consulte [níveis de consistência na base de dados do Azure Cosmos](consistency-levels.md).
* `<ip-range-filter>`Especifica o conjunto de endereços IP ou intervalos de endereços IP no formato CIDR ser incluído como uma lista de permitidos do cliente IPs de uma conta de base de dados específica. Endereços/intervalos IP tem de ser vírgula separados e não pode conter quaisquer espaços. Para obter mais informações, consulte [suporte de Firewall de base de dados do Azure Cosmos](firewall-support.md)
* `<max-interval>`Quando utilizado com consistência de consistência vinculada, este valor representa o período de tempo de vinculada (em segundos) tolerated. Intervalo aceite para este valor é 1-100.
* `<max-staleness-prefix>`Quando utilizado com consistência de consistência vinculada, este valor representa o número de pedidos obsoletos tolerated. Intervalo aceite para este valor é 1 – 2 147 483 647.
* `<resource-group-name>`O nome do [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<resource-group-location>`A localização do grupo de recursos do Azure para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<database-account-name>`O nome da conta de base de dados do Azure Cosmos DB ser atualizados.

Exemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a>Eliminar uma conta de base de dados do DocumentDB

Este comando permite-lhe eliminar uma conta de base de dados de base de dados do Azure Cosmos existente.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>`O nome do [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<database-account-name>`O nome da conta de base de dados de base de dados do Azure Cosmos a eliminar.

Exemplo:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a>Obter propriedades de uma conta de base de dados do DocumentDB

Este comando permite-lhe obter as propriedades de uma conta de base de dados de base de dados do Azure Cosmos existente.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>`O nome do [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<database-account-name>`O nome da conta de base de dados do Azure Cosmos DB.

Exemplo:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a>Etiquetas de atualização de uma conta de base de dados do Azure Cosmos DB

O exemplo a seguir descreve como definir [etiquetas de recursos do Azure] [ azure-resource-tags] para a base de dados do Azure Cosmos conta de base de dados.

> [!NOTE]
> Este comando pode ser combinado com os comandos create ou atualização, acrescentando o `-Tags` sinalizador com o parâmetro correspondente.

Exemplo:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a>Chaves de conta de lista

Quando cria uma conta de base de dados do Azure Cosmos, o serviço gera duas chaves de acesso principal que podem ser utilizadas para autenticação quando a conta de base de dados do Azure Cosmos é acedida. Ao fornecer duas chaves de acesso, base de dados do Azure Cosmos permite-lhe voltar a gerar as chaves sem qualquer interrupção à sua conta de base de dados do Azure Cosmos. As chaves de só de leitura para a autenticação de operações só de leitura também estão disponíveis. Existem duas chaves de leitura / escrita (principais e secundários) e duas chaves de só de leitura (principais e secundários).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>`O nome do [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<database-account-name>`O nome da conta de base de dados do Azure Cosmos DB.

Exemplo:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a>Cadeias de ligação de lista

Para contas do MongoDB, a cadeia de ligação para ligar a aplicação do MongoDB para a conta de base de dados pode ser obtida utilizando o seguinte comando.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>`O nome do [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<database-account-name>`O nome da conta de base de dados do Azure Cosmos DB.

Exemplo:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a>Regenerar a chave de conta

Deverá alterar as chaves de acesso à sua conta de base de dados do Azure Cosmos periodicamente para ajudar a manter as suas ligações mais segura. São atribuídas duas chaves de acesso que lhe permite manter as ligações para a conta de base de dados do Azure Cosmos utilizando uma chave de acesso enquanto volta a gerar a chave de acesso.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>`O nome do [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<database-account-name>`O nome da conta de base de dados do Azure Cosmos DB.
* `<key-kind>`Um dos quatro tipos de chaves: ["Primário" | " Secundária "|" PrimaryReadonly "|" SecondaryReadonly"] que gostaria de voltar a gerar.

Exemplo:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a>Modificar a prioridade de ativação pós-falha de uma conta de base de dados do Azure Cosmos DB

Para contas de base de dados de multirregião, pode alterar a prioridade de ativação pós-falha das várias regiões que a conta de base de dados de base de dados do Azure Cosmos existe na. Para obter mais informações sobre a ativação pós-falha na sua conta de base de dados de BD do Cosmos do Azure, consulte [distribuir dados globalmente com a base de dados do Azure Cosmos][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>`O nome de localização da região de escrita da conta de base de dados. Esta localização é necessário ter um valor de prioridade de ativação pós-falha de 0. É necessário exatamente uma região de escrita por conta de base de dados.
* `<read-region-location>`O nome de localização da região de leitura da conta de base de dados. Esta localização é necessário ter um valor de prioridade de ativação pós-falha de maior que 0. Pode existir mais do que um regiões leitura por conta de base de dados.
* `<resource-group-name>`O nome do [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados de base de dados do Azure Cosmos pertence.
* `<database-account-name>`O nome da conta de base de dados do Azure Cosmos DB.

Exemplo:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Passos seguintes

* Para ligar através do .NET, consulte [ligar e consultar com .NET](create-documentdb-dotnet.md).
* Para ligar através do .NET Core, consulte [ligar e consultar com .NET Core](create-documentdb-dotnet-core.md).
* Para estabelecer ligação com o Node.js, consulte [ligar e consultar com o Node.js e uma aplicação do MongoDB](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/
