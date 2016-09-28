<properties
   pageTitle="Como utilizar a Cache de Redis do Azure com o Java | Microsoft Azure"
    description="Introdução à Cache de Redis do Azure com o Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/24/2016"
    ms.author="sdanie"/>


# Como utilizar a Cache de Redis do Azure com o Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

A Cache de Redis do Azure permite-lhe aceder a uma cache de Redis dedicada e gerida pela Microsoft. Pode aceder a uma cache a partir de qualquer aplicação dentro do Microsoft Azure.

Este tópico mostra-lhe como começar a utilizar a Cache de Redis do Azure com o Java.

## Pré-requisitos

[Jedis](https://github.com/xetorthio/jedis) – Cliente de Java para Redis

Este tutorial utiliza o Jedis, mas pode utilizar qualquer cliente de Java listado em [http://redis.io/clients](http://redis.io/clients).

## Criar uma cache de Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Obter as chaves de acesso e o nome de anfitrião

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Ativar o ponto final não SSL

Alguns clientes Redis não suportam o SSL e, por predefinição, a [porta não SSL está desativada para as novas instâncias da Cache de Redis do Azure](cache-configure.md#access-ports). No momento, o cliente [Jedis](https://github.com/xetorthio/jedis) não suporta SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## Adicionar um elemento à cache e recuperá-lo

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


## Passos seguintes

- [Ativar o diagnóstico da cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para poder [monitorizar](https://msdn.microsoft.com/library/azure/dn763945.aspx) o estado de funcionamento da cache.
- Leia a [Documentação Redis](http://redis.io/documentation) oficial.




<!--HONumber=Sep16_HO3-->


