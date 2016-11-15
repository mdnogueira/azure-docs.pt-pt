---
title: Como utilizar a Cache de Redis do Azure com o Java | Microsoft Docs
description: "Introdução à Cache de Redis do Azure com o Java"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/24/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 907f75dc02bff7e25712a564410c1974e22f0d99


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Como utilizar a Cache de Redis do Azure com o Java
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

A Cache de Redis do Azure permite-lhe aceder a uma cache de Redis dedicada e gerida pela Microsoft. Pode aceder a uma cache a partir de qualquer aplicação dentro do Microsoft Azure.

Este tópico mostra-lhe como começar a utilizar a Cache de Redis do Azure com o Java.

## <a name="prerequisites"></a>Pré-requisitos
[Jedis](https://github.com/xetorthio/jedis) – Cliente de Java para Redis

Este tutorial utiliza o Jedis, mas pode utilizar qualquer cliente de Java listado em [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Criar uma cache de Redis no Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Obter as chaves de acesso e o nome de anfitrião
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="enable-the-nonssl-endpoint"></a>Ativar o ponto final não SSL
Alguns clientes Redis não suportam o SSL e, por predefinição, a [porta não SSL está desativada para as novas instâncias da Cache de Redis do Azure](cache-configure.md#access-ports). No momento, o cliente [Jedis](https://github.com/xetorthio/jedis) não suporta SSL. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar um elemento à cache e recuperá-lo
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
         jedis.set("foo", "bar");
         String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Passos seguintes
* [Ativar o diagnóstico da cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para poder [monitorizar](https://msdn.microsoft.com/library/azure/dn763945.aspx) o estado de funcionamento da cache.
* Leia a [Documentação Redis](http://redis.io/documentation) oficial.




<!--HONumber=Nov16_HO2-->


