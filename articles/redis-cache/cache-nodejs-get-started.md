---
title: Como utilizar a Cache de Redis do Azure com o Node.js | Microsoft Docs
description: "Introdução à Cache de Redis do Azure com o Node.js e o node_redis."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
ms.openlocfilehash: f2c448af24e180db58f3ef3d39e90036dda3f7eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Como utilizar a Cache de Redis do Azure com o Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [python](cache-python-get-started.md)
> 
> 

A Cache de Redis do Azure permite-lhe aceder a uma cache de Redis segura, dedicada e gerida pela Microsoft. Pode aceder a uma cache a partir de qualquer aplicação dentro do Microsoft Azure.

Este tópico mostra-lhe como começar com a Cache de Redis do Azure com o Node.js. 

## <a name="prerequisites"></a>Pré-requisitos
Instale o [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial utiliza o [node_redis](https://github.com/mranney/node_redis). Para obter exemplos de utilização de outros clientes Node.js, consulte a documentação individual para os clientes Node.js listados em [Clientes Node.js Redis](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Criar uma cache de Redis no Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Obter as chaves de acesso e o nome de anfitrião
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Ligar à cache de forma segura através de SSL
As versões mais recentes do [node_redis](https://github.com/mranney/node_redis) fornecem suporte para ligar a Cache de Redis do Azure através de SSL. O seguinte exemplo mostra como ligar a Cache de Redis do Azure com o ponto final de SSL de 6380. Substitua `<name>` pelo nome da sua cache e `<key>` pela sua chave primária ou secundária, conforme descrito na secção anterior, [Obter as chaves de acesso e o nome de anfitrião](#retrieve-the-host-name-and-access-keys).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> A porta não SSL está desativada para novas instâncias da Cache de Redis do Azure. Se estiver a utilizar um cliente diferente que não suporta SSL, consulte [How to enable the non-SSL port (Como ativar a porta não SSL)](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar um elemento à cache e recuperá-lo
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


## <a name="next-steps"></a>Passos seguintes
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache.
* Leia a [Documentação Redis](http://redis.io/documentation) oficial.

