---
title: Como Utilizar a Cache de Redis do Azure | Microsoft Docs
description: "Saiba como melhorar o desempenho das aplicações do Azure com a Cache de Redis do Azure"
services: redis-cache,app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/27/2017
ms.author: sdanie
ms.openlocfilehash: 3dfc026490093523446650c510dbebdd660e8b6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-redis-cache"></a>Como Utilizar a Cache de Redis do Azure
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Este guia mostra como começar a utilizar a **Cache de Redis do Azure**. A Cache de Redis do Microsoft Azure baseia-se na popular Cache de Redis open source. Permite-lhe aceder a uma cache de Redis segura, dedicada e gerida pela Microsoft. Pode aceder a uma cache criada com a Cache de Redis do Azure a partir de qualquer aplicação dentro do Microsoft Azure.

A Cache de Redis do Microsoft Azure está disponível nos seguintes escalões:

* **Básico** – Único nó. Vários tamanhos até 53 GB.
* **Standard** – Dois nós Primário/Réplica. Vários tamanhos até 53 GB. SLA de 99,9%.
* **Premium** – Dois nós Primário/Réplica com um máximo de 10 shards. Vários tamanhos, de 6 GB a 530 GB. Todas as funcionalidades do escalão Standard e mais, incluindo o suporte para o [Cluster de Redis](cache-how-to-premium-clustering.md), a [Persistência de Redis](cache-how-to-premium-persistence.md) e a [Azure Virtual Network](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada escalão difere em termos de funcionalidades e preços. Para obter informações sobre os preços, veja [Cache Pricing Details (Detalhes dos Preços da Cache)][Cache Pricing Details].

Este guia mostra como utilizar o cliente [StackExchange.Redis][StackExchange.Redis] com o código C\#. Os cenários abrangidos incluem a **criação e configuração de uma cache**, a **configuração de clientes de cache** e a **adição e remoção de objetos da cache**. Para obter mais informações sobre como utilizar a Cache de Redis do Azure, veja [Next Steps (Passos Seguintes)][Next Steps]. Para obter um tutorial passo-a-passo para criar uma aplicação Web ASP.NET MVC com a Cache de Redis, veja [Como criar uma aplicação Web com a Cache de Redis](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>Introdução à Cache de Redis do Azure 
É fácil começar a trabalhar com a Cache de Redis do Azure. Para começar, aprovisione e configure uma cache. Em seguida, configure os clientes de cache para que possam aceder à cache. Assim que tiver configurado os clientes de cache, poderá começar a trabalhar com eles.

* [Criar a cache][Create the cache]
* [Configurar os clientes de cache][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Criar uma cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Para aceder à cache depois de a criar
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Para obter mais informações sobre como configurar a cache, veja [How to configure Azure Redis Cache (Como configurar a Cache de Redis do Azure)](cache-configure.md).

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Configurar os clientes de cache
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Depois de ter configurado o projeto do cliente para a colocação em cache, pode utilizar as técnicas descritas nas secções seguintes para trabalhar com a cache.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Trabalhar com Caches
Os passos desta secção descrevem como realizar tarefas comuns com a Cache.

* [Ligar à cache][Connect to the cache]
* [Adicionar e obter objetos da cache][Add and retrieve objects from the cache]
* [Trabalhar com objetos .NET na cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>Ligar à cache
Para trabalhar programaticamente com uma cache, precisa de uma referência para a cache. Adicione o seguinte na parte superior de qualquer ficheiro a partir do qual pretende utilizar o cliente StackExchange.Redis para aceder a uma Cache de Redis do Azure.

    using StackExchange.Redis;

> [!NOTE]
> O cliente StackExchange.Redis requer o .NET Framework 4 ou superior.
> 
> 

A ligação à Cache de Redis do Azure é gerida pela classe `ConnectionMultiplexer`. Esta classe deve ser partilhada e reutilizada em toda a aplicação cliente e não precisa de ser criada para cada operação. 

Para ligar a uma Cache de Redis do Azure e ser devolvida uma instância de um `ConnectionMultiplexer` ligado, chame o método `Connect` estático e passe o ponto final e a chave da cache. Utilize a chave gerada do portal do Azure como o parâmetro de palavra-passe.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> Aviso: nunca guarde as credenciais no código fonte. Para manter simples este exemplo, as credenciais são apresentadas no código fonte. Veja [How Application Strings and Connection Strings Work (Como funcionam as Cadeias da Aplicação e as Cadeias de Ligação)][How Application Strings and Connection Strings Work] para obter informações sobre como guardar as credenciais.
> 
> 

Se não pretender utilizar o protocolo SSL, defina `ssl=false` ou omita o parâmetro `ssl`.

> [!NOTE]
> Por predefinição, a porta não SSL está desativada para as novas caches. Para obter instruções sobre como ativar a porta não SSL, veja [Access Ports (Portas de Acesso)](cache-configure.md#access-ports).
> 
> 

Uma abordagem para partilhar uma instância `ConnectionMultiplexer` na sua aplicação consiste em ter uma propriedade estática que devolva uma instância ligada, tal como no seguinte exemplo. Esta abordagem fornece uma forma segura para os threads de modo a inicializar apenas uma única instância `ConnectionMultiplexer` ligada. Nestes exemplos, `abortConnect` está definido como falso, o que significa que a chamada terá êxito mesmo se não for estabelecida uma ligação à Cache de Redis do Azure. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Para obter mais informações sobre as opções avançadas de configuração de ligação, veja o [Modelo de configuração StackExchange.Redis][StackExchange.Redis configuration model].

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Assim que a ligação for estabelecida, é devolvida uma referência à base de dados da cache de Redis, chamando o método `ConnectionMultiplexer.GetDatabase`. O objeto devolvido do método `GetDatabase` é um objeto pass-through simples e não precisa de ser armazenado.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Os caches de Redis do Azure têm um número configurável de bases de dados (predefinição de 16) que pode ser utilizado para separar logicamente os dados dentro de uma cache de Redis. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration).

Agora que sabe como ligar a uma instância da Cache de Redis do Azure e como devolver uma referência à base de dados da cache, vejamos como trabalhar com a cache.

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>Adicionar e obter objetos da cache
Pode armazenar e obter itens numa cache com os métodos `StringSet` e `StringGet`.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

O Redis armazena grande parte dos dados como cadeias de Redis. No entanto, estas cadeias podem conter vários tipos de dados, incluindo dados binários serializados, que podem ser utilizados ao armazenar objetos .NET na cache.

Quando chamar `StringGet`, o objeto será devolvido, se existir. Caso contrário, será devolvido `null`. Se `null` for devolvido, pode obter o valor da fonte de dados pretendida e armazená-lo na cache para uma utilização posterior. Este padrão de utilização é conhecido como o padrão do lado da cache.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Também pode utilizar `RedisValue`, conforme mostrado no exemplo seguinte. `RedisValue` tem operadores implícitos para trabalhar com tipos de dados integrais e pode ser útil se `null` for um valor esperado para um item em cache.


    RedisValue value = cache.StringGet("key1");
    if (!value.HasValue)
    {
        value = GetValueFromDataSource();
        cache.StringSet("key1", value);
    }


Para especificar a expiração de um item na cache, utilize o parâmetro `TimeSpan` de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET na cache
A Cache de Redis do Azure pode colocar em cache objetos .NET e tipos de dados primitivos, mas para ser colocado em cache, o objeto .NET tem de ser serializado. Esta serialização do objeto .NET é da responsabilidade do programador da aplicação, podendo o programador escolher o serializador pretendido.

Uma forma simples de serializar objetos passa por utilizar os métodos de serialização `JsonConvert` no [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) e serializar de e para o JSON. O exemplo que se segue mostra os objetos obter e definir utilizando uma instância do objeto `Employee`.

    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas, siga estas ligações para saber mais sobre a Cache de Redis do Azure.

* Consulte os fornecedores do ASP.NET para a Cache de Redis do Azure.
  * [Fornecedor de Estado da Sessão de Redis do Azure](cache-aspnet-session-state-provider.md)
  * [Fornecedor de Cache de Saída do ASP.NET da Cache de Redis do Azure](cache-aspnet-output-cache-provider.md)
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no portal do Azure e [transferi-las e revê-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) com as ferramentas da sua escolha.
* Veja a [Documentação do cliente de cache StackExchange.Redis][StackExchange.Redis cache client documentation].
  * Pode aceder à Cache de Redis do Azure a partir de muitos clientes de Redis e linguagens de desenvolvimento. Para obter mais informações, veja [http://redis.io/clients][http://redis.io/clients].
* A Cache de Redis do Azure também pode ser utilizada com serviços e ferramentas de terceiros, tais como Redsmin e Redis Desktop Manager.
  * Para obter mais informações sobre Redsmin, veja [How to retrieve an Azure Redis connection string and use it with Redsmin (Como obter uma cadeia de ligação Redis do Azure e utilizá-la com o Redsmin)][How to retrieve an Azure Redis connection string and use it with Redsmin].
  * Aceda e inspecione os seus dados na Cache de Redis do Azure através de uma GUI com o [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
* Veja a documentação [Redis][redis] e obtenha mais informações sobre os [Tipos de dados de Redis][redis data types] e [uma introdução de quinze minutos aos tipos de dados de Redis][a fifteen minute introduction to Redis data types].

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[Connect to the cache]: #connect-to-cache
[Add and retrieve objects from the cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session


<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png







<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis configuration model]: https://stackexchange.github.io/StackExchange.Redis/Configuration

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Azure portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis data types]: http://redis.io/topics/data-types
[a fifteen minute introduction to Redis data types]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


