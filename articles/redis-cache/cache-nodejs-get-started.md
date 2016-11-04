---
title: Como utilizar a Cache de Redis do Azure com o Node.js | Microsoft Docs
description: Introdução à Cache de Redis do Azure com o Node.js e o node_redis.
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: v-lincan

ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/16/2016
ms.author: sdanie

---
# Como utilizar a Cache de Redis do Azure com o Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

A Cache de Redis do Azure permite-lhe aceder a uma cache de Redis segura, dedicada e gerida pela Microsoft. Pode aceder a uma cache a partir de qualquer aplicação dentro do Microsoft Azure.

Este tópico mostra-lhe como começar com a Cache de Redis do Azure com o Node.js. Para obter outro exemplo de utilização da Cache de Redis do Azure com o Node.js, veja [Build a Node.js Chat Application with Socket.IO on an Azure Website (Criar uma Aplicação de Chat do Node.js com Socket.IO num Site do Azure)](../app-service-web/web-sites-nodejs-chat-app-socketio.md).

## Pré-requisitos
Instale o [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial utiliza o [node_redis](https://github.com/mranney/node_redis). Para obter exemplos de utilização de outros clientes Node.js, consulte a documentação individual para os clientes Node.js listados em [Clientes Node.js Redis](http://redis.io/clients#nodejs).

## Criar uma cache de Redis no Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Obter as chaves de acesso e o nome de anfitrião
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Ligar à cache de forma segura através de SSL
As versões mais recentes do [node_redis](https://github.com/mranney/node_redis) fornecem suporte para ligar a Cache de Redis do Azure através de SSL. O seguinte exemplo mostra como ligar a Cache de Redis do Azure com o ponto final de SSL de 6380. Substitua `<name>` pelo nome da sua cache e `<key>` pela sua chave primária ou secundária, conforme descrito na secção anterior, [Obter as chaves de acesso e o nome de anfitrião](#retrieve-the-host-name-and-access-keys).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## Adicionar um elemento à cache e recuperá-lo
O seguinte exemplo mostra como ligar a uma instância da Cache de Redis do Azure, bem como armazenar e recuperar um item da cache. Para obter mais exemplos de utilização de Redis com o cliente [node_redis](https://github.com/mranney/node_redis), aceda a [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Saída:

    OK
    value


## Passos seguintes
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache.
* Leia a [Documentação Redis](http://redis.io/documentation) oficial.

<!--HONumber=ago16_HO4-->


