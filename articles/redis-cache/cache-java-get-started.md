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
ms.date: 08/31/2017
ms.author: sdanie
ms.openlocfilehash: b433eecb0424db85b616c40c5f0cdfc88692cef1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
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

## <a name="connect-to-the-cache-securely-using-ssl"></a>Ligar à cache de forma segura através de SSL
As versões mais recentes de [jedis](https://github.com/xetorthio/jedis) fornecem suporte para ligar à Cache de Redis do Azure através de SSL. O seguinte exemplo mostra como ligar a Cache de Redis do Azure com o ponto final de SSL de 6380. Substitua `<name>` pelo nome da sua cache e `<key>` pela sua chave primária ou secundária, conforme descrito na secção anterior, [Obter as chaves de acesso e o nome de anfitrião](#retrieve-the-host-name-and-access-keys).

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */

> [!NOTE]
> A porta não SSL está desativada para novas instâncias da Cache de Redis do Azure. Se estiver a utilizar um cliente diferente que não suporta SSL, consulte [How to enable the non-SSL port (Como ativar a porta não SSL)](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar um elemento à cache e recuperá-lo
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Passos seguintes
* [Ativar o diagnóstico da cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para poder [monitorizar](https://msdn.microsoft.com/library/azure/dn763945.aspx) o estado de funcionamento da cache.
* Leia a [Documentação Redis](http://redis.io/documentation) oficial.
* Veja [how to configure a Spring Initializr app to use Redis Cache](cache-java-spring-boot-initializer-with-redis-cache.md) (Como configurar uma aplicação Spring Initializr para utilizar a Cache de Redis).