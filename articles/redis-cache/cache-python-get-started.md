<properties
    pageTitle="Como utilizar a Cache de Redis do Azure com o Python | Microsoft Azure"
    description="Introdução à Cache de Redis do Azure com o Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="05/31/2016"
    ms.author="sdanie"/>

# Como utilizar a Cache de Redis do Azure com o Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este tópico mostra-lhe como começar com a Cache de Redis do Azure com o Python.


## Pré-requisitos

Instale o [redis-py](https://github.com/andymccurdy/redis-py).


## Criar uma cache de Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Obter as chaves de acesso e o nome de anfitrião

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Ativar o ponto final não SSL

Alguns clientes Redis não suportam o SSL e, por predefinição, a [porta não SSL está desativada para as novas instâncias da Cache de Redis do Azure](cache-configure.md#access-ports). No momento, o cliente [redis-py](https://github.com/andymccurdy/redis-py) não suporta SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Adicionar um elemento à cache e recuperá-lo


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Substitua `<name>` pelo nome da sua cache `key` e pela sua chave de acesso.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png



<!--HONumber=Jun16_HO2-->


