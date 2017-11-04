---
title: Como dimensionar a Cache de Redis do Azure | Microsoft Docs
description: "Saiba como dimensionar as instâncias de Cache de Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: sdanie
ms.openlocfilehash: 91b3580491a1e3504a3891b66606a9bd18c0638f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-azure-redis-cache"></a>Como dimensionar a Cache de Redis do Azure
Cache de Redis do Azure tem ofertas de cache diferente, que fornecem flexibilidade na escolha de funcionalidades e o tamanho da cache. Depois de criar uma cache, pode dimensionar o tamanho e o escalão de preço da cache se alteram os requisitos da sua aplicação. Este artigo mostra como dimensionar a sua cache no portal do Azure e utilizando ferramentas como o Azure PowerShell e da CLI do Azure.

## <a name="when-to-scale"></a>Quando dimensionar
Pode utilizar o [monitorização](cache-how-to-monitor.md) funcionalidades de Cache de Redis do Azure para monitorizar o estado de funcionamento e desempenho da cache e ajudar a determinar quando a cache de dimensionar. 

Pode monitorizar as métricas seguintes para ajudar a determinar se precisar de dimensionar.

* Carga de servidor de redis
* Utilização de memória
* Largura de banda de rede
* Utilização da CPU

Se determinar que a cache já não está a cumprir os requisitos da sua aplicação, pode dimensionar a uma cache superior ou inferior escalão que é mais adequada para a sua aplicação de preço. Para obter mais informações sobre como determinar qual cache escalão utilizar de preço, consulte [que tamanho e a oferta da Cache de Redis devo utilizar](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Dimensionar uma cache
Dimensionar a sua cache [navegue para a cache](cache-configure.md#configure-redis-cache-settings) no [portal do Azure](https://portal.azure.com) e clique em **escala** do **menu recursos**.

![Escala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecione o escalão de preço pretendido do **selecione o escalão de preço** painel e clique em **selecione**.

![Escalão de preço][redis-cache-pricing-tier-blade]


Pode dimensionar para um escalão de preço diferente com as seguintes restrições:

* Não é possível aumentar a partir de um escalão de preço superior para um escalão de preço inferior.
  * Não é possível dimensionar desde um **Premium** colocar em cache até um **padrão** ou um **básico** cache.
  * Não é possível dimensionar desde um **padrão** colocar em cache até um **básico** cache.
* Pode dimensionar desde um **básico** colocar em cache para um **padrão** cache, mas não é possível alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode efetuar uma operação de dimensionamento subsequente para o tamanho pretendido.
* Não é possível dimensionar desde um **básico** cache diretamente para um **Premium** cache. Tem de dimensionar de **básico** para **padrão** numa operação de dimensionamento e, em seguida, **padrão** para **Premium** numa operação de dimensionamento subsequente.
* Não é possível aumentar a partir de um tamanho maior baixo até o **C0 (250 MB)** tamanho.
 
Enquanto a cache está a dimensionar o escalão de preço nova, uma **dimensionamento** estado é apresentado no **a Cache de Redis** painel.

![Dimensionamento][redis-cache-scaling]

Quando o dimensionamento é concluída, o estado é alterado de **dimensionamento** para **executar**.

## <a name="how-to-automate-a-scaling-operation"></a>Como automatizar uma operação de dimensionamento
Para além de dimensionar as instâncias de cache no portal do Azure, pode dimensionar utilizando cmdlets do PowerShell, CLI do Azure e ao utilizar o Microsoft Azure Management bibliotecas (MAML). 

* [Dimensionamento com o PowerShell](#scale-using-powershell)
* [Escala utilizando a CLI do Azure](#scale-using-azure-cli)
* [Escala utilizando MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Dimensionamento com o PowerShell
Pode dimensionar as instâncias de Cache de Redis do Azure com o PowerShell, utilizando o [conjunto AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) cmdlet quando o `Size`, `Sku`, ou `ShardCount` propriedades são modificadas. O exemplo seguinte mostra como dimensionar uma cache com o nome `myCache` para uma cache de 2,5 GB. 

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Para obter mais informações sobre como aumentar com o PowerShell, consulte [dimensionar uma cache de Redis através do Powershell](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Escala utilizando a CLI do Azure
Dimensionar as instâncias de Cache de Redis do Azure utilizando a CLI do Azure, chame o `azure rediscache set` de comandos e transmitir as alterações de configuração pretendida que inclua um novo tamanho, o sku ou o tamanho do cluster, dependendo da operação de dimensionamento pretendido.

Para obter mais informações sobre dimensionamento com o CLI do Azure, consulte [alterar as definições de uma Cache de Redis existente](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Escala utilizando MAML
Dimensionar a sua Cache de Redis do Azure instâncias utilizando o [bibliotecas de gestão do Azure (MAML) da Microsoft](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), chamar o `IRedisOperations.CreateOrUpdate` método e passar o novo tamanho para o `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Para obter mais informações, consulte o [gerir Cache de Redis com MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) exemplo.

## <a name="scaling-faq"></a>FAQ sobre o dimensionamento
A lista seguinte contém as respostas a perguntas mais comuns sobre o dimensionamento de Cache de Redis do Azure.

* [Posso Dimensionar para ou dentro de uma cache Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Depois de dimensionamento, é necessário que alterar as minhas chaves de acesso ou o nome da cache?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Como funciona o dimensionamento?](#how-does-scaling-work)
* [Irá perder dados da minha cache durante dimensionamento?](#will-i-lose-data-from-my-cache-during-scaling)
* [Os meus bases de dados personalizadas é definir afetados durante o dimensionamento?](#is-my-custom-databases-setting-affected-during-scaling)
* [A minha cache estará disponível durante dimensionamento?](#will-my-cache-be-available-during-scaling)
* [Operações que não são suportadas](#operations-that-are-not-supported)
* [Quanto dimensionamento necessário?](#how-long-does-scaling-take)
* [Como saber quando o dimensionamento é concluída?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Posso Dimensionar para ou dentro de uma cache Premium?
* Não é possível dimensionar desde um **Premium** colocar em cache até um **básico** ou **padrão** escalão de preço.
* Pode dimensionar a partir de um **Premium** cache escalão para outro de preço.
* Não é possível dimensionar desde um **básico** cache diretamente para um **Premium** cache. Primeiro tem de dimensionar de **básico** para **padrão** numa operação de dimensionamento e, em seguida, **padrão** para **Premium** um subsequentes no operação de dimensionamento.
* Se tiver ativado o clustering quando criou o **Premium** cache, pode [alterar o tamanho do cluster](cache-how-to-premium-clustering.md#cluster-size). Se a sua cache foi criada sem clustering ativado, não é possível configurar o clustering numa altura posterior.
  
  Para obter mais informações, veja [Como configurar o clustering de uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Depois de dimensionamento, é necessário que alterar as minhas chaves de acesso ou o nome da cache?
Não, o nome da cache e as chaves são iguais durante uma operação de dimensionamento.

### <a name="how-does-scaling-work"></a>Como funciona o dimensionamento?
* Quando um **básico** cache é dimensionada para um tamanho diferente, é encerrado e uma nova cache é aprovisionada com o tamanho de novo. Durante este período, a cache não está disponível e todos os dados na cache é perdido.
* Quando um **básico** cache é dimensionada para um **padrão** cache, uma cache de réplica é aprovisionada e os dados são copiados da cache principal para a cache de réplica. A cache permanece disponível durante o processo de dimensionamento.
* Quando um **padrão** cache é dimensionada para um tamanho diferente ou para um **Premium** cache, uma das réplicas é encerrado e novamente aprovisionado para o novo tamanho e os dados transferidos e, em seguida, de outra réplica efetua uma ativação pós-falha antes de ser aprovisionada novamente, semelhante ao processo que ocorre durante uma falha de um de nós de cache.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Irá perder dados da minha cache durante dimensionamento?
* Quando um **básico** cache é dimensionada para um tamanho de nova, todos os dados são perdidas e a cache não está disponível durante a operação de dimensionamento.
* Quando um **básico** cache é dimensionada para um **padrão** cache, os dados na cache, normalmente, é preservada.
* Quando um **padrão** cache é dimensionada para uma maior tamanho ou camada, ou um **Premium** cache é dimensionada para um tamanho maior, todos os dados normalmente é preservada. Quando o dimensionamento um **padrão** ou **Premium** cache para um tamanho mais pequeno, os dados poderão perder-se consoante a quantidade de dados estiver na cache relacionados com o novo tamanho quando é escalado. Se os dados se tenha perdidos quando o dimensionamento para baixo, as chaves são expulso utilizando o [allkeys lru](http://redis.io/topics/lru-cache) política de expulsão. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Os meus bases de dados personalizadas é definir afetados durante o dimensionamento?
Alguns escalões de preços têm diferentes [limites de bases de dados](cache-configure.md#databases), por isso, existem algumas considerações quando o dimensionamento inativo se tiver configurado um valor personalizado para o `databases` definição durante a criação da cache.

* Quando o dimensionamento para um escalão de preço um inferior `databases` limite que a camada atual:
  * Se estiver a utilizar o número predefinido de `databases` que é 16 para todos os escalões de preços, nenhum dado é perdido.
  * Se estiver a utilizar um número personalizado de `databases` que fica dentro dos limites para a camada à qual a são escalar, isto `databases` definição é mantida e não se tenha perdido nenhum dado.
  * Se estiver a utilizar um número personalizado de `databases` que excede os limites da camada de novo, o `databases` definição é lowered os limites de nova camada e todos os dados nas bases de dados removidas é perdido.
* Quando o dimensionamento para um escalão de preço com o mesmo nível ou superior `databases` limite que a camada atual sua `databases` definição é mantida e não se tenha perdido nenhum dado.

Tenha em atenção que enquanto caches Standard e Premium têm um SLA de 99,9% de disponibilidade, não há nenhum SLA para a perda de dados.

### <a name="will-my-cache-be-available-during-scaling"></a>A minha cache estará disponível durante dimensionamento?
* **Standard** e **Premium** caches permanecem disponíveis durante a operação de dimensionamento.
* **Básico** caches estão offline durante um tamanho diferente das operações de dimensionamento, mas permanecem disponíveis quando o dimensionamento do **básico** para **padrão**.

### <a name="operations-that-are-not-supported"></a>Operações que não são suportadas
* Não é possível aumentar a partir de um escalão de preço superior para um escalão de preço inferior.
  * Não é possível dimensionar desde um **Premium** colocar em cache até um **padrão** ou um **básico** cache.
  * Não é possível dimensionar desde um **padrão** colocar em cache até um **básico** cache.
* Pode dimensionar desde um **básico** colocar em cache para um **padrão** cache, mas não é possível alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode efetuar uma operação de dimensionamento subsequente para o tamanho pretendido.
* Não é possível dimensionar desde um **básico** cache diretamente para um **Premium** cache. Primeiro tem de dimensionar de **básico** para **padrão** numa operação de dimensionamento e, em seguida, **padrão** para **Premium** um subsequentes no operação de dimensionamento.
* Não é possível aumentar a partir de um tamanho maior baixo até o **C0 (250 MB)** tamanho.

Se uma operação de dimensionamento falhar, o serviço irá tentar efetuar a reverter a operação e a cache irá reverter para o tamanho original.

### <a name="how-long-does-scaling-take"></a>Quanto dimensionamento necessário?
Dimensionamento demora cerca de 20 minutos, dependendo da quantidade de dados estiver na cache.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Como saber quando o dimensionamento é concluída?
No portal do Azure pode ver que a operação de dimensionamento em curso. Quando o dimensionamento é concluída, o estado da cache é alterado para **executar**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



