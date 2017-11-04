---
title: Gerir a Cache de Redis do Azure com o Azure PowerShell | Microsoft Docs
description: Saiba como efetuar tarefas administrativas para a Cache de Redis do Azure com o Azure PowerShell.
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: sdanie
ms.openlocfilehash: 0a5c95eab3fd01f611fc049e80c5c506857e0b81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Gerir a Cache de Redis do Azure com o Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [CLI do Azure](cache-manage-cli.md)
> 
> 

Este tópico mostra como realizar tarefas comuns, tais como de criar, atualizar e dimensionar as instâncias de Cache de Redis do Azure, como voltar a gerar chaves de acesso e como visualizar informações sobre as caches. Para obter uma lista completa dos cmdlets do PowerShell de Cache de Redis do Azure, consulte [cmdlets de Cache de Redis do Azure](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Para obter mais informações sobre o modelo de implementação clássica, consulte [vs. de implementação clássica do Azure Resource Manager: compreender o estado dos seus recursos e modelos de implementação](../azure-resource-manager/resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Pré-requisitos
Se já tiver instalado o Azure PowerShell, tem de ter o Azure PowerShell versão 1.0.0 ou posterior. Pode verificar a versão do Azure PowerShell que tenha instalado com este comando na linha de comandos do Azure PowerShell.

    Get-Module azure | format-table version


Em primeiro lugar, tem de iniciar sessão no Azure com este comando.

    Login-AzureRmAccount

Especifique o endereço de e-mail da sua conta do Azure e a palavra-passe no diálogo de início de sessão do Microsoft Azure.

Em seguida, se tiver várias subscrições do Azure, tem de definir a sua subscrição do Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a subscrição, execute o seguinte comando. No exemplo seguinte, o nome da subscrição é `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Antes de poder utilizar o Windows PowerShell com o Azure Resource Manager, precisa do seguinte:

* O Windows PowerShell, versão 3.0 ou 4.0. Para encontrar a versão do Windows PowerShell, escreva:`$PSVersionTable` e verifique o valor de `PSVersion` é 3.0 ou 4.0. Para instalar uma versão compatível, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Para obter ajuda detalhada para qualquer cmdlet que visualiza neste tutorial, utilize o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o `New-AzureRmRedisCache` cmdlet, tipo:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Como ligar a outras nuvens
Por predefinição do Azure é ambiente `AzureCloud`, que representa a instância global de nuvem do Azure. Para ligar a uma instância diferente, utilize o `Add-AzureRmAccount` comando com o `-Environment` ou -`EnvironmentName` comutador de linha de comandos com o ambiente pretendido ou o nome do ambiente.

Para obter uma lista dos ambientes disponíveis, execute o `Get-AzureRmEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Para estabelecer a ligação para a nuvem do Azure Government
Para ligar para a nuvem do Azure Government, utilize um dos seguintes comandos.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Para criar uma cache na nuvem do Azure Government, utilize uma das seguintes localizações.

* Governo dos E.u.a. Virginia
* Governo dos E.u.a. Iowa

Para obter mais informações sobre a nuvem do Azure Government, consulte [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) e [guia para programadores do Microsoft Azure Government](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Para estabelecer a ligação para a nuvem do Azure China
Para ligar para a nuvem do Azure China, utilize um dos seguintes comandos.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Para criar uma cache na nuvem do Azure China, utilize uma das seguintes localizações.

* Leste da China
* China Norte

Para obter mais informações sobre a nuvem do Azure China, consulte [AzureChinaCloud para o Azure operado pela 21Vianet na China](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Para ligar ao Microsoft Azure Alemanha
Para ligar ao Microsoft Azure Datacenters, utilize um dos seguintes comandos.

    Add-AzureRMAccount -EnvironmentName AzureGermanCloud


ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureGermanCloud)

Para criar uma cache na Alemanha do Microsoft Azure, utilize uma das seguintes localizações.

* Alemanha Central
* Alemanha Nordeste

Para obter mais informações sobre o Microsoft Azure Datacenters, consulte [Datacenters do Microsoft Azure](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Propriedades utilizadas para o PowerShell Cache de Redis do Azure
A tabela seguinte contém as propriedades e descrições de parâmetros frequentemente utilizadas quando criar e gerir as instâncias de Cache de Redis do Azure com o Azure PowerShell.

| Parâmetro | Descrição | Predefinição |
| --- | --- | --- |
| Nome |Nome da cache | |
| Localização |Localização da cache | |
| resourceGroupName |Nome do grupo de recursos no qual pretende criar a cache | |
| Tamanho |O tamanho da cache. Os valores válidos são: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1GB |
| ShardCount |O número de partições horizontais para criar quando criar uma cache premium com clustering ativada. Os valores válidos são: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Especifica o SKU da cache. Os valores válidos são: básico, Standard, Premium |Standard |
| RedisConfiguration |Especifica as definições de configuração de Redis. Para obter detalhes sobre cada definição, consulte o seguinte [RedisConfiguration propriedades](#redisconfiguration-properties) tabela. | |
| EnableNonSslPort |Indica se a porta não SSL está ativada. |Falso |
| MaxMemoryPolicy |Este parâmetro foi preterido - utilizar em vez disso, RedisConfiguration. | |
| StaticIP |Quando a alojar a cache numa VNET, especifica um endereço IP exclusivo na sub-rede para a cache. Se não for indicado, um valor é escolhido por si da sub-rede. | |
| Subrede |Quando a alojar a cache numa VNET, especifica o nome da sub-rede na qual pretende implementar a cache. | |
| VirtualNetwork |Quando a alojar a cache numa VNET, especifica o ID de recurso da VNET no qual pretende implementar a cache. | |
| KeyType |Especifica que a chave de acesso para voltar a gerar quando renovar teclas de acesso. Os valores válidos são: principal, secundária | |

### <a name="redisconfiguration-properties"></a>Propriedades de RedisConfiguration
| Propriedade | Descrição | Escalões de preço |
| --- | --- | --- |
| capacidade de cópia de segurança de RDB |Se [persistência de dados de Redis](cache-how-to-premium-persistence.md) está ativado |Apenas Premium |
| ligação-cadeia de RDB-armazenamento |A cadeia de ligação para a conta de armazenamento para [persistência de dados de Redis](cache-how-to-premium-persistence.md) |Apenas Premium |
| frequência de cópia de segurança RDB |A frequência de cópia de segurança para [persistência de dados de Redis](cache-how-to-premium-persistence.md) |Apenas Premium |
| maxmemory-reservados |Configura o [memória reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para processos de cache não |Standard e Premium |
| política de maxmemory |Configura o [política de expulsão](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para a cache |Todos os escalões de preços |
| notificar-keyspace-eventos |Configura [notificações keyspace](cache-configure.md#keyspace-notifications-advanced-settings) |Standard e Premium |
| ziplist-entradas de hash-máx. |Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| ziplist-valor de hash-máx. |Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| Set-máximo intset entradas |Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| ziplist-entradas de zset-máx. |Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| zset--ziplist-o valor máximo |Configura [otimização de memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| bases de dados |Configura o número de bases de dados. Esta propriedade pode ser configurada apenas durante a criação de cache. |Standard e Premium |

## <a name="to-create-a-redis-cache"></a>Para criar uma Cache de Redis
Novas instâncias de Cache de Redis do Azure são criadas utilizando o [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet.

> [!IMPORTANT]
> Na primeira vez que criar uma cache de Redis numa subscrição utilizando o portal do Azure, o portal regista o `Microsoft.Cache` espaço de nomes para essa subscrição. Se tentar criar a cache de Redis primeiro numa subscrição utilizando o PowerShell, primeiro tem de registar esse espaço de nomes utilizando o seguinte comando; cmdlets, caso contrário, tais como `New-AzureRmRedisCache` e `Get-AzureRmRedisCache` falhar.
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `New-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new redis cache.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.


    PARAMETERS
        -Name <String>
            Name of the redis cache to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.

        -Location <String>
            Location in which to create the redis cache.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para criar uma cache com os parâmetros predefinidos, execute o seguinte comando.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, e `Location` são os parâmetros necessários, mas as restantes são opcionais e têm valores predefinidos. Executar o comando anterior cria uma instância da Cache de Redis de Azure de SKU Standard com o nome especificado, a localização e o grupo de recursos, o que é de 1 GB de tamanho com a porta não SSL desativada.

Para criar uma cache de premium, especifique um tamanho de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (GB de 26-260 GB), ou P4 (53 GB - 530 GB). Para ativar o clustering, especifique uma contagem de partições horizontais utilizando o `ShardCount` parâmetro. O exemplo seguinte cria uma cache de premium P1 com 3 shards. Uma cache de premium P1 6 GB no tamanho, e uma vez que especificamos três shards o tamanho total é 18 GB (3, 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para o `RedisConfiguration` parâmetro, coloque os valores no interior `{}` como chave/valor como pares `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. O exemplo seguinte cria uma cache de 1 GB padrão com `allkeys-random` notificações de política e keyspace do maxmemory configuradas com `KEA`. Para obter mais informações, consulte [notificações Keyspace (definições avançadas)](cache-configure.md#keyspace-notifications-advanced-settings) e [políticas memória](cache-configure.md#memory-policies).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Para configurar as bases de dados definição durante a criação da cache
O `databases` definição pode ser configurada apenas durante a criação da cache. O exemplo seguinte cria um premium P3 (26 GB) de cache com 48 bases de dados utilizando o [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Para mais informações sobre o `databases` propriedade, consulte [configuração do servidor de Cache de Redis do Azure predefinido](cache-configure.md#default-redis-server-configuration). Para obter mais informações sobre como criar uma cache utilizando o [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet, consulte anterior [para criar uma Cache de Redis](#to-create-a-redis-cache) secção.

## <a name="to-update-a-redis-cache"></a>A atualizar a cache de Redis
Instâncias de Cache de Redis do Azure são atualizadas utilizando o [conjunto AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) cmdlet.

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `Set-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set redis cache updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.

    PARAMETERS
        -Name <String>
            Name of the redis cache to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

O `Set-AzureRmRedisCache` cmdlet pode ser utilizado para atualizar as propriedades, tais como `Size`, `Sku`, `EnableNonSslPort`e o `RedisConfiguration` valores. 

O seguinte comando atualiza a política de maxmemory para a Cache de Redis com o nome myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-a-redis-cache"></a>Dimensionar uma cache de Redis
`Set-AzureRmRedisCache`pode ser utilizada para dimensionar uma cache de Redis do Azure instância quando o `Size`, `Sku`, ou `ShardCount` propriedades são modificadas. 

> [!NOTE]
> Dimensionamento uma cache com o PowerShell está sujeita a mesma limites e diretrizes como dimensionar uma cache do portal do Azure. Pode dimensionar para um escalão de preço diferente com as seguintes restrições.
> 
> * Não é possível aumentar a partir de um escalão de preço superior para um escalão de preço inferior.
> * Não é possível dimensionar desde um **Premium** colocar em cache até um **padrão** ou um **básico** cache.
> * Não é possível dimensionar desde um **padrão** colocar em cache até um **básico** cache.
> * Pode dimensionar desde um **básico** colocar em cache para um **padrão** cache, mas não é possível alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode efetuar uma operação de dimensionamento subsequente para o tamanho pretendido.
> * Não é possível dimensionar desde um **básico** cache diretamente para um **Premium** cache. Tem de dimensionar de **básico** para **padrão** numa operação de dimensionamento e, em seguida, **padrão** para **Premium** numa operação de dimensionamento subsequente.
> * Não é possível aumentar a partir de um tamanho maior baixo até o **C0 (250 MB)** tamanho.
> 
> Para obter mais informações, consulte [como Cache de Redis do Azure de escala](cache-how-to-scale.md).
> 
> 

O exemplo seguinte mostra como dimensionar uma cache com o nome `myCache` para uma cache de 2,5 GB. Tenha em atenção que este comando funciona para num nível básico ou uma cache de padrão.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Depois deste comando é emitido, o estado da cache é devolvido (semelhante ao chamar `Get-AzureRmRedisCache`). Tenha em atenção que o `ProvisioningState` é `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Quando a operação de dimensionamento estiver concluída, o `ProvisioningState` alterações `Succeeded`. Se precisar de efetuar uma operação de dimensionamento subsequente, tais como alteração de básico para Standard e, em seguida, alterar o tamanho, tem de aguardar até que a operação anterior seja concluída ou receber um erro semelhante ao seguinte.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Para obter informações sobre uma cache de Redis
Pode obter informações sobre como utilizar uma cache de [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) cmdlet.

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `Get-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para devolver informações sobre todas as caches na subscrição atual, execute `Get-AzureRmRedisCache` sem quaisquer parâmetros.

    Get-AzureRmRedisCache

Para devolver informações sobre todas as caches num grupo de recursos específico, execute `Get-AzureRmRedisCache` com o `ResourceGroupName` parâmetro.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Para devolver informações sobre uma cache específica, execute `Get-AzureRmRedisCache` com o `Name` parâmetro contendo o nome da cache e o `ResourceGroupName` parâmetro com o grupo de recursos que contém a cache.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Para obter as chaves de acesso para uma cache de Redis
Para obter as chaves de acesso para a sua cache, pode utilizar o [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) cmdlet.

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `Get-AzureRmRedisCacheKey`, execute o seguinte comando.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified redis cache.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para obter as chaves para a sua cache, chame o `Get-AzureRmRedisCacheKey` cmdlet e atribua o nome cache, o nome do grupo de recursos que contém a cache.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Para voltar a gerar chaves de acesso para a sua cache de Redis
Para voltar a gerar as chaves de acesso para a sua cache, pode utilizar o [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) cmdlet.

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `New-AzureRmRedisCacheKey`, execute o seguinte comando.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of a redis cache.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para voltar a gerar a chave primária ou secundária para a sua cache, chame o `New-AzureRmRedisCacheKey` cmdlet e passar o nome e grupo de recursos e especifique `Primary` ou `Secondary` para o `KeyType` parâmetro. No exemplo seguinte, a chave de acesso secundária para uma cache é novamente gerada.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Para eliminar uma cache de Redis
Para eliminar uma cache de Redis, utilize o [remover AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) cmdlet.

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `Remove-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove redis cache if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.

    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

No exemplo seguinte, a cache com o nome `myCache` é removido.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Para importar uma cache de Redis
Pode importar dados para uma instância de Cache de Redis do Azure utilizando o `Import-AzureRmRedisCache` cmdlet.

> [!IMPORTANT]
> Importar/exportar só está disponível para [escalão premium](cache-premium-tier-intro.md) coloca em cache. Para obter mais informações sobre a importação/exportação, consulte [importar e exportar dados na Cache de Redis do Azure](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `Import-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Redis Cache.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


O seguinte comando importa dados de blob especificado pelo uri de SAS para a Cache de Redis do Azure.

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Para exportar uma cache de Redis
Pode exportar dados de uma instância de Cache de Redis do Azure utilizando o `Export-AzureRmRedisCache` cmdlet.

> [!IMPORTANT]
> Importar/exportar só está disponível para [escalão premium](cache-premium-tier-intro.md) coloca em cache. Para obter mais informações sobre a importação/exportação, consulte [importar e exportar dados na Cache de Redis do Azure](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `Export-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


O seguinte comando exporta dados a partir de uma instância da Cache de Redis do Azure para o contentor especificado pelo uri de SAS.

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Para reiniciar uma cache de Redis
Pode reiniciar a instância de Cache de Redis do Azure utilizando o `Reset-AzureRmRedisCache` cmdlet.

> [!IMPORTANT]
> Reiniciar o computador está disponível apenas para [escalão premium](cache-premium-tier-intro.md) coloca em cache. Para obter mais informações sobre a cache a ser reiniciado, consulte [Cache administração - reinício](cache-administration.md#reboot).
> 
> 

Para ver uma lista dos parâmetros disponíveis e as respetivas descrições para `Reset-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


O seguinte comando reinicia ambos os nós da cache especificado.

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como utilizar o Windows PowerShell com o Azure, consulte os seguintes recursos:

* [Documentação de cmdlet de Cache de Redis do Azure no MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
* [Cmdlets do Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkID=394765): Saiba como utilizar os cmdlets no módulo do Azure Resource Manager.
* [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): Saiba como criar e gerir grupos de recursos no portal do Azure.
* [Blogue do Azure](http://blogs.msdn.com/windowsazure): saber mais sobre as novas funcionalidades no Azure.
* [Blogue do Windows PowerShell](http://blogs.msdn.com/powershell): saber mais sobre as novas funcionalidades no Windows PowerShell.
* ["Hei, responsável pelo script!" Blogue](http://blogs.technet.com/b/heyscriptingguy/): obter sugestões de mundo real e truques da Comunidade do Windows PowerShell.

