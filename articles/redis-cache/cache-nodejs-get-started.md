<properties
    pageTitle="Como utilizar a Cache de Redis do Azure com o Node.js | Microsoft Azure"
    description="Introdução à Cache de Redis do Azure com o Node.js e o node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="05/31/2016"
    ms.author="sdanie"/>

# Como utilizar a Cache de Redis do Azure com o Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

A Cache de Redis do Azure permite-lhe aceder a uma cache de Redis segura, dedicada e gerida pela Microsoft. Pode aceder a uma cache a partir de qualquer aplicação dentro do Microsoft Azure.

Este tópico mostra-lhe como começar com a Cache de Redis do Azure com o Node.js. Para obter outro exemplo de utilização da Cache de Redis do Azure com o Node.js, veja [Build a Node.js Chat Application with Socket.IO on an Azure Website (Criar uma Aplicação de Chat do Node.js com Socket.IO num Site do Azure)](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Pré-requisitos

Instale o [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial utiliza o [node_redis](https://github.com/mranney/node_redis), mas pode utilizar qualquer cliente do Node.js listado em [http://redis.io/clients](http://redis.io/clients).

## Criar uma cache de Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Obter as chaves de acesso e o nome de anfitrião

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Ativar o ponto final não SSL

Alguns clientes Redis não suportam o SSL e, por predefinição, a [porta não SSL está desativada para as novas instâncias da Cache de Redis do Azure](cache-configure.md#access-ports). No momento, o cliente [node_redos](https://github.com/mranney/node_redis) não suporta SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Adicionar um elemento à cache e recuperá-lo

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

- [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache.
- Leia a [Documentação Redis](http://redis.io/documentation) oficial.






<!--HONumber=Jun16_HO2-->


