---
title: Como configurar o clustering de Redis para uma Cache de Redis do Azure Premium | Microsoft Docs
description: "Saiba como criar e gerir Redis clustering para o escalão Premium instâncias de Cache de Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: 86a4a605dbb3b11924c14ff42238009742f72898
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-redis-cache"></a>Como configurar o clustering de Redis para uma Cache de Redis do Azure Premium
Cache de Redis do Azure tem ofertas de cache diferente, que fornecem flexibilidade na escolha de funcionalidades, incluindo funcionalidades do escalão Premium, tais como clustering, persistência e suporte da virtual network e tamanho da cache. Este artigo descreve como configurar o clustering numa instância de Cache de Redis do Azure premium.

Para obter informações sobre outras funcionalidades de cache premium, consulte [introdução para o escalão Premium do Azure Redis Cache](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>O que é o Cluster de Redis?
Cache de Redis do Azure oferece o cluster de Redis como [implementado no Redis](http://redis.io/topics/cluster-tutorial). Com o Cluster de Redis, obtenha as seguintes vantagens: 

* A capacidade de dividir automaticamente o conjunto de dados entre vários nós. 
* A capacidade de continuar as operações quando um subconjunto de nós estão a ocorrer falhas ou não conseguem comunicar com o restante do cluster. 
* Débito mais: débito de forma linear aumenta como aumentar o número de partições horizontais. 
* Tamanho da memória mais: aumenta de forma linear como aumentar o número de partições horizontais.  

Para obter mais informações sobre o tamanho, débito e largura de banda com premium caches, consulte [que tamanho e a oferta da Cache de Redis devo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

No Azure, o cluster de Redis é fornecido como um modelo de primário/réplica em que cada partição horizontal tem um par de primário/réplica com replicação em que a replicação é gerida pelo serviço de Cache de Redis do Azure. 

## <a name="clustering"></a>Clustering
Clustering está ativado no **nova Cache de Redis** painel durante a criação da cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Clustering está configurado no **Cluster de Redis** painel.

![Clustering][redis-cache-clustering]

Pode ter até 10 shards no cluster. Clique em **ativado** e deslize o controlo de deslize ou escreva um número entre 1 e 10 para **contagem de partições horizontais** e clique em **OK**.

Cada partição horizontal é um par de cache primário/réplica gerido pelo Azure e o tamanho total da cache é calculado multiplicando o número de partições horizontais pelo tamanho da cache selecionado no escalão de preço. 

![Clustering][redis-cache-clustering-selected]

Assim que for criada a cache de ligar ao mesmo e utilizado apenas como uma cache agrupado e Redis distribui os dados em toda as shards de Cache. Se for diagnóstico [ativada](cache-how-to-monitor.md#enable-cache-diagnostics), métricas são capturadas em separado para cada partição horizontal e pode ser [visualizadas](cache-how-to-monitor.md) no painel da Cache de Redis. 

> [!NOTE]
> 
> Existem algumas diferenças menores necessárias na sua aplicação de cliente quando o cluster está configurado. Para obter mais informações, consulte [é necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Para o código de exemplo sobre como trabalhar com clustering com o cliente stackexchange. redis, veja o [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) parte a [Olá, mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplo.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Alterar o tamanho do cluster em execução premium cache
Para alterar o tamanho do cluster em execução cache premium com clustering ativada, clique em **tamanho do Cluster de Redis** do **menu recursos**.

> [!NOTE]
> Enquanto o escalão Premium de Cache de Redis do Azure foi lançado para disponibilidade geral, a funcionalidade de tamanho de Cluster de Redis está atualmente em pré-visualização.
> 
> 

![Tamanho do cluster de redis][redis-cache-redis-cluster-size]

Para alterar o tamanho do cluster, utilize o controlo de deslize ou escreva um número entre 1 e 10 no **contagem de partições horizontais** caixa de texto e clique em **OK** para guardar.

> [!NOTE]
> Dimensionamento de um cluster é executado o [MIGRAR](https://redis.io/commands/migrate) comando, que é um comando dispendiosas, por isso, para um impacto mínimo, considere executar esta operação durante o horário de pico. Durante o processo de migração, verá um pico de pedidos de carga do servidor. Dimensionamento de um cluster é uma longa execução processo e a quantidade de tempo que demora depende o número de chaves e o tamanho dos valores associados a essas chaves.
> 
> 

## <a name="clustering-faq"></a>FAQ sobre o clustering
A lista seguinte contém as respostas a perguntas mais comuns sobre o clustering de Cache de Redis do Azure.

* [É necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Como as chaves são distribuídas num cluster?](#how-are-keys-distributed-in-a-cluster)
* [O que é o maior tamanho da cache que pode criar?](#what-is-the-largest-cache-size-i-can-create)
* [Todos os clientes de Redis suportam clustering?](#do-all-redis-clients-support-clustering)
* [Como ligar ao meu cache quando o cluster está ativado?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Posso diretamente ligar para os shards individuais da minha cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Pode configurar clustering para uma cache criada anteriormente?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Pode configurar clustering para uma cache de básica ou padrão?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Posso utilizar o clustering com os fornecedores de estado de sessão do ASP.NET de Redis e a cache de saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Posso estou obtendo mover exceções ao utilizar o stackexchange. redis e clustering, o que devo fazer?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>É necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar clustering?
* Quando clustering estiver ativado, apenas base de dados 0 está disponível. Se a aplicação de cliente utiliza várias bases de dados e tentar ler ou escrever uma base de dados diferente de 0, a seguinte exceção é emitida. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Para obter mais informações, consulte [especificação de Cluster de Redis - subconjunto implementado](http://redis.io/topics/cluster-spec#implemented-subset).
* Se estiver a utilizar [stackexchange. redis](https://www.nuget.org/packages/StackExchange.Redis/), tem de utilizar 1.0.481 ou posterior. Ligar à cache com o mesmo [pontos finais, portas e as chaves](cache-configure.md#properties) que utilizar ao ligar a uma cache que não tenha clustering ativada. A única diferença é que todas as leituras e escritas tem de ser efetuadas a base de dados 0.
  
  * Outros clientes podem ter requisitos diferentes. Consulte [todos os clientes de Redis suportam clustering?](#do-all-redis-clients-support-clustering)
* Se a sua aplicação utiliza várias operações de chaves em lotes para um comando único, todas as chaves devem estar localizadas no ID de partição horizontal mesmo. Para localizar as chaves no ID de partição horizontal mesmo, consulte [como são chaves distribuídas num cluster?](#how-are-keys-distributed-in-a-cluster)
* Se estiver a utilizar o fornecedor de estado de sessão do ASP.NET de Redis tem de utilizar 2.0.1 ou superior. Consulte [posso utilizar o clustering com os fornecedores de estado de sessão do ASP.NET de Redis e a cache de saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Como as chaves são distribuídas num cluster?
Pelo Redis [modelo de distribuição de chaves](http://redis.io/topics/cluster-spec#keys-distribution-model) documentação: O espaço de chave é dividido em 16384 ranhuras. Cada chave está protegido por hash e atribuído a um destes ranhuras, que são distribuídas por nós do cluster. Pode configurar a que parte da chave é protegido por hash para se certificar de que a várias chaves estão localizadas no ID de partição horizontal mesmo utilizando tags de hash.

* Chaves com uma etiqueta de hash - se de qualquer parte da chave é colocado entre `{` e `}`, apenas que parte da chave é protegido por hash para o objetivo de determinar a ranhura de hash de uma chave. Por exemplo, as seguintes chaves de 3 deverá estar localizadas no ID de partição horizontal mesmo: `{key}1`, `{key}2`, e `{key}3` , uma vez que apenas o `key` parte do nome é protegido por hash. Para obter uma lista completa de especificações de etiqueta de hash de chaves, consulte [etiquetas de hash de chaves](http://redis.io/topics/cluster-spec#keys-hash-tags).
* As chaves sem uma tag de hash - o nome completo da chave é utilizada para codificar. Isto resulta numa distribuição estatisticamente mesmo em shards da cache.

Para melhor desempenho e débito, recomendamos que distribuir uniformemente as chaves. Se estiver a utilizar chaves com uma etiqueta de hash responsabilidade da aplicação para garantir que as chaves são distribuídos uniformemente.

Para obter mais informações, consulte [modelo de distribuição de chaves](http://redis.io/topics/cluster-spec#keys-distribution-model), [fragmentação de dados do Cluster de Redis](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), e [etiquetas de hash de chaves](http://redis.io/topics/cluster-spec#keys-hash-tags).

Para o código de exemplo sobre como trabalhar com clustering e localizar as chaves de partição horizontal mesmo com o cliente stackexchange. redis, veja o [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) parte a [Olá, mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplo.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>O que é o maior tamanho da cache que pode criar?
O maior tamanho da cache premium é 53 GB. Pode criar até 10 shards dando-lhe um tamanho máximo de 530 GB. Se precisar de um tamanho maior pode [pedir mais](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obter mais informações, consulte [preços da Cache de Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Todos os clientes de Redis suportam clustering?
À hora presente nem todos os clientes suportam o Redis clustering. Stackexchange. redis é aquele que suporta para o mesmo. Para obter mais informações sobre outros clientes, consulte o [reproduzir com o cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) secção o [tutorial do cluster de Redis](http://redis.io/topics/cluster-tutorial).

> [!NOTE]
> Se estiver a utilizar o stackexchange. redis como o cliente, certifique-se de que está a utilizar a versão mais recente do [stackexchange. redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou posterior para clustering funcione corretamente. Se tiver quaisquer problemas com exceções de movimentação, consulte [mover exceções](#move-exceptions) para obter mais informações.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Como ligar ao meu cache quando o cluster está ativado?
Pode ligar à sua cache com o mesmo [pontos finais](cache-configure.md#properties), [portas](cache-configure.md#properties), e [chaves](cache-configure.md#access-keys) que utilizar ao ligar a uma cache que não tenha clustering ativada. Redis gere o clustering do back-end, pelo que não tem geri-lo a partir do seu cliente.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Posso diretamente ligar para os shards individuais da minha cache?
Isto não é oficialmente suportado. Com que consiga aceder tal, cada partição horizontal é composta por um par de cache primário/réplica, coletivamente conhecido como uma instância da cache. Pode ligar a estes instâncias de cache utilizando o utilitário de redis-cli no [instável](http://redis.io/download) ramo do repositório de Redis no GitHub. Esta versão implementa o suporte básico quando começar a utilizar o `-c` mudar. Para obter mais informações consulte [reproduzir com o cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) no [http://redis.io](http://redis.io) no [tutorial do cluster de Redis](http://redis.io/topics/cluster-tutorial).

Para não ssl, utilize os seguintes comandos.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Para ssl, substitua `1300N` com `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Pode configurar clustering para uma cache criada anteriormente?
Atualmente só pode ativar clustering quando criar uma cache. Pode alterar o tamanho do cluster depois da cache é criada, mas não é possível adicionar clustering para uma cache de premium ou remova o clustering de uma cache premium depois de é criada a cache. Uma cache de premium com clustering ativada e o ID de partição horizontal apenas um é diferente de uma cache de premium o mesmo tamanho com sem clustering.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Pode configurar clustering para uma cache de básica ou padrão?
Clustering só está disponível para premium caches.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Posso utilizar o clustering com os fornecedores de estado de sessão do ASP.NET de Redis e a cache de saída?
* **Fornecedor de Cache de saída de redis** -sem necessidade de alterações.
* **Fornecedor de estado de sessão de redis** - para utilizar o clustering, terá de utilizar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou de uma exceção é emitida. Esta é uma alteração inovadora; Para obter mais informações consulte [v2.0.0 interrompendo detalhes de alteração](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Posso estou obtendo mover exceções ao utilizar o stackexchange. redis e clustering, o que devo fazer?
Se estiver a utilizar o stackexchange. redis e receber `MOVE` exceções ao utilizar o clustering, certifique-se de que está a utilizar [stackexchange. redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) ou posterior. Para obter instruções sobre como configurar as aplicações de .NET para utilizar stackexchange. redis, consulte [configurar os clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Passos seguintes
Saiba como utilizar as funcionalidades de cache do mais premium.

* [Introdução ao escalão Premium de Cache de Redis do Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







