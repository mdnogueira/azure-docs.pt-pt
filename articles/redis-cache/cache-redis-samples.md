---
title: Exemplos de Cache de Redis do Azure | Microsoft Docs
description: Saiba como utilizar a Cache de Redis do Azure
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: 7841fcf0b5f4dcb409abf8bfb804c2e03dad6d3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-redis-cache-samples"></a>Exemplos de Cache de Redis do Azure
Este tópico fornece uma lista de amostras a Cache de Redis do Azure, que abrangem os cenários, como ligar a uma cache, ler e escrever os dados para e de uma cache e utilizando os fornecedores de Cache de Redis do ASP.NET. Alguns exemplos são projetos transferíveis, e algumas fornecem orientações passo a passo e incluam fragmentos de código, mas não ligar a um projeto disponível para transferência.

## <a name="hello-world-samples"></a>Amostras do Olá mundo
Os exemplos nesta secção mostram as noções básicas de ligar a uma instância da Cache de Redis do Azure e ler e escrever dados para a cache utilizando uma variedade de idiomas e os clientes de Redis.

O [Olá mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplo mostra como realizar várias operações de cache a utilizar o [stackexchange. redis](https://github.com/StackExchange/StackExchange.Redis) cliente do .NET.

Este exemplo mostra como:

* Utilizar várias opções de ligação
* Ler e escrever os objetos de e para a cache utilizando operações síncronas e assíncronas
* Utilizar comandos de Redis MGET/MSET para devolver valores de chaves especificados
* Efetuar operações transacionais Redis
* Trabalhar com conjuntos de ordenado e listas de Redis
* Armazenar objetos .NET utilizando JsonConvert serializers
* Utilize conjuntos de Redis para implementar a etiquetagem
* Trabalhar com o Cluster de Redis

Para obter mais informações, consulte o [stackexchange. redis](https://github.com/StackExchange/StackExchange.Redis) consulte a documentação no github e para cenários de utilização mais o [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) testes de unidade.

[Como utilizar a Cache de Redis do Azure com o Python](cache-python-get-started.md) mostra como começar a utilizar com a Cache de Redis do Azure com o Python e o [redis-py](https://github.com/andymccurdy/redis-py) cliente.

[Trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) mostra-lhe uma forma de serializar objetos .NET, para que possa escrevê-las em e lê-los a partir de uma instância da Cache de Redis do Azure. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Utilizar a Cache de Redis como uma Backplane de ampliação para SignalR de ASP.NET
O [utilizar a Cache de Redis como uma Backplane para ASP.NET SignalR de ampliação](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) exemplo demonstra como pode utilizar a Cache de Redis do Azure como um backplane SignalR. Para obter mais informações sobre backplane, consulte [SignalR Scaleout com Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Exemplo de consulta do cliente de Cache de redis
Este exemplo demonstra compara desempenho entre a aceder aos dados numa cache e aceder aos dados do armazenamento de persistência. Este exemplo tem dois projetos.

* [Demonstração como Cache de Redis pode melhorar o desempenho ao colocar em cache dados](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [A base de dados e a Cache do seed de demonstração de](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Estado da sessão ASP.NET e a cache de saída
O [utilizar Cache de Redis do Azure para armazenar ASP.NET SessionState e OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) exemplo demonstra como utilizar a Cache de Redis do Azure para armazenar a sessão ASP.NET e Cache de saída com os fornecedores SessionState e OutputCache para Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Gerir a Cache de Redis do Azure com MAML
O [gerir Azure Redis Cache com bibliotecas de gestão do Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) exemplo demonstra como pode utilizar bibliotecas de gestão do Azure para gerir - (criar / atualizar / eliminar) sua Cache. 

## <a name="custom-monitoring-sample"></a>Personalizar monitorização de exemplo
O [dados de monitorização da Cache de Redis acesso](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) exemplo demonstra como pode aceder a dados de monitorização para a Cache de Redis do Azure fora do Portal do Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Um clone de estilo Twitter escrito utilizando o PHP e de Redis
O [Retwis](https://github.com/SyntaxC4-MSFT/retwis) exemplo é o Redis Olá, mundo. É um clone de redes sociais Twitter-estilo mínimo escrito utilizando Redis e o PHP utilizando o [Predis](https://github.com/nrk/predis) cliente. O código de origem foi concebido para ser muito simples e ao mesmo tempo para mostrar diferentes estruturas de dados de Redis.

## <a name="bandwidth-monitor"></a>Monitor de largura de banda
O [monitor de largura de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) exemplo permite-lhe monitorizar a largura de banda utilizada no cliente. Para medir a largura de banda, execute o exemplo na máquina do cliente de cache, efetuar chamadas à cache e observe a largura de banda comunicada pelo exemplo de monitor de largura de banda.

