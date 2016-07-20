<properties 
    pageTitle="Como Utilizar a Cache de Redis do Azure | Microsoft Azure" 
    description="Saiba como melhorar o desempenho das aplicações do Azure com a Cache de Redis do Azure" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="05/31/2016" 
    ms.author="sdanie"/>

# Como Utilizar a Cache de Redis do Azure

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este guia mostra como começar a utilizar a **Cache de Redis do Azure**. A Cache de Redis do Microsoft Azure baseia-se na popular Cache de Redis open source. Permite-lhe aceder a uma cache de Redis segura, dedicada e gerida pela Microsoft. Pode aceder a uma cache criada com a Cache de Redis do Azure a partir de qualquer aplicação dentro do Microsoft Azure.

A Cache de Redis do Microsoft Azure está disponível nos seguintes escalões:

-   **Básico** – Único nó. Vários tamanhos até 53 GB.
-   **Standard** – Dois nós Primário/Réplica. Vários tamanhos até 53 GB. SLA de 99,9%.
-   **Premium** – Dois nós Primário/Réplica com um máximo de 10 shards. Vários tamanhos que variam entre 6 GB e 530 GB (contacte-nos para obter mais tamanhos). Todas as funcionalidades do escalão Standard e mais, incluindo o suporte para o [Cluster de Redis](cache-how-to-premium-clustering.md), a [Persistência de Redis](cache-how-to-premium-persistence.md) e a [Azure Virtual Network](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada escalão difere em termos de funcionalidades e preços. Para obter informações sobre os preços, veja [Detalhes dos Preços da Cache][].

Este guia mostra como utilizar o cliente [StackExchange.Redis][] com o código C\#. Os cenários abrangidos incluem a **criação e configuração de uma cache**, a **configuração de clientes de cache** e a **adição e remoção de objetos da cache**. Para obter mais informações sobre como utilizar a Cache de Redis do Azure, veja a secção [Passos Seguintes][]. Para obter um tutorial passo-a-passo para criar uma aplicação Web ASP.NET MVC com a Cache de Redis, veja [Como criar uma aplicação Web com a Cache de Redis](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## Introdução à Cache de Redis do Azure 

É fácil começar a trabalhar com a Cache de Redis do Azure. Para começar, aprovisione e configure uma cache. Em seguida, configure os clientes de cache para que possam aceder à cache. Assim que tiver configurado os clientes de cache, poderá começar a trabalhar com eles.

-   [Criar a cache][]
-   [Configurar os clientes de cache][]

<a name="create-cache"></a>
## Criar uma cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### Para aceder à cache depois de a criar

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Para obter mais informações sobre como configurar a cache, veja [How to configure Azure Redis Cache (Como configurar a Cache de Redis do Azure)](cache-configure.md).

<a name="NuGet"></a>
## Configurar os clientes de cache

Pode aceder a uma cache criada com a Cache de Redis do Azure a partir de qualquer aplicação do Azure. As aplicações .NET desenvolvidas no Visual Studio podem utilizar o cliente de cache **StackExchange.Redis**, que poderá ser configurado com um pacote NuGet que simplifica a configuração das aplicações cliente de cache. 

>[AZURE.NOTE] Para obter mais informações, veja a página do GitHub [StackExchange.Redis][] e a [Documentação do cliente de cache StackExchange.Redis][].

Para configurar uma aplicação cliente no Visual Studio com o pacote NuGet StackExchange.Redis, clique com o botão direito do rato no projeto em **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**. 

![Gerir pacotes NuGet][NuGetMenu]

Introduza **StackExchange.Redis** ou **StackExchange.Redis.StrongName** na caixa de texto de pesquisa, selecione a versão pretendida apresentada nos resultados e clique em **Instalar**.

>[AZURE.NOTE] Se preferir utilizar uma versão da biblioteca de clientes **StackExchange.Redis** com nome seguro, escolha **StackExchange.Redis.StrongName**; caso contrário, escolha **StackExchange.Redis**.

![Pacote NuGet StackExchange.Redis][StackExchangeNuget]

O pacote NuGet transfere e adiciona as referências de assemblagem necessárias para a sua aplicação cliente aceder à Cache de Redis do Azure com o cliente de cache StackExchange.Redis.

Depois de ter configurado o projeto do cliente para a colocação em cache, pode utilizar as técnicas descritas nas secções seguintes para trabalhar com a cache.

<a name="working-with-caches"></a>
## Trabalhar com Caches

Os passos desta secção descrevem como realizar tarefas comuns com a Cache.

-   [Ligar à cache][]
-   [Adicionar e obter objetos da cache][]
-   [Trabalhar com objetos .NET na cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Ligar à cache

Para poder trabalhar programaticamente com uma cache, precisa de uma referência para a cache. Adicione o seguinte na parte superior de qualquer ficheiro a partir do qual pretende utilizar o cliente StackExchange.Redis para aceder a uma Cache de Redis do Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] O cliente StackExchange.Redis requer o .NET Framework 4 ou superior.

A ligação à Cache de Redis do Azure é gerida pela classe `ConnectionMultiplexer`. Esta classe foi concebida para ser partilhada e reutilizada em toda a aplicação cliente e não precisa de ser criada para cada operação. 

Para ligar a uma Cache de Redis do Azure e ser devolvida uma instância de um `ConnectionMultiplexer` ligado, chame o método `Connect` estático e passe o ponto final e a chave da cache, como no exemplo seguinte. Utilize a chave gerada do Portal do Azure como o parâmetro de palavra-passe.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Aviso: nunca guarde as credenciais no código fonte. Para manter simples este exemplo, as credenciais são apresentadas no código fonte. Veja [How Application Strings and Connection Strings Work (Como funcionam as Cadeias da Aplicação e as Cadeias de Ligação)][] para obter informações sobre como guardar as credenciais.

Se não pretender utilizar o protocolo SSL, defina `ssl=false` ou omita o parâmetro `ssl`.

>[AZURE.NOTE] Por predefinição, a porta não SSL está desativada para as novas caches. Para obter instruções sobre como ativar a porta não SSL, veja [Access Ports (Portas de Acesso)](cache-configure.md#access-ports).

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

Para obter mais informações sobre as opções avançadas de configuração de ligação, veja o [Modelo de configuração StackExchange.Redis][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

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

Agora que sabe como ligar a uma instância da Cache de Redis do Azure e como devolver uma referência à base de dados da cache, vejamos como trabalhar com a cache.

<a name="add-object"></a>
## Adicionar e obter objetos da cache

Pode armazenar e obter itens numa cache com os métodos `StringSet` e `StringGet`.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

O Redis armazena grande parte dos dados como cadeias de Redis. No entanto, estas cadeias podem conter vários tipos de dados, incluindo dados binários serializados, que podem ser utilizados ao armazenar objetos .NET na cache.

Quando chamar `StringGet`, o objeto será devolvido, se existir. Caso contrário, será devolvido `null`. Neste caso, pode obter o valor da fonte de dados pretendida e armazená-lo na cache para uma utilização posterior. Este processo é conhecido como o padrão do lado da cache.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Para especificar a expiração de um item na cache, utilize o parâmetro `TimeSpan` de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Trabalhar com objetos .NET na cache

A Cache de Redis do Azure pode colocar em cache objetos .NET, bem como tipos de dados primitivos. Mas para ser colocado em cache, o objeto .NET tem de ser serializado. Este procedimento é da responsabilidade do programador da aplicação, podendo o programador escolher o serializador pretendido.

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
## Passos Seguintes

Agora que aprendeu as noções básicas, siga estas ligações para saber mais sobre a Cache de Redis do Azure.

-   Consulte os fornecedores do ASP.NET para a Cache de Redis do Azure.
    -   [Fornecedor de Estado da Sessão de Redis do Azure](cache-aspnet-session-state-provider.md)
    -   [Fornecedor de Cache de Saída do ASP.NET da Cache de Redis do Azure](cache-aspnet-output-cache-provider.md)
-   [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no Portal do Azure e [transferi-las e revê-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) com as ferramentas da sua escolha.
-   Veja a [Documentação do cliente de cache StackExchange.Redis][].
    -   Pode aceder à Cache de Redis do Azure a partir de muitos clientes de Redis e linguagens de desenvolvimento. Para obter mais informações, veja [http://redis.io/clients][].
    -   Também pode utilizar a Cache de Redis do Azure com serviços como o Redsmin. Para obter mais informações, veja [How to retrieve an Azure Redis connection string and use it with Redsmin (Como obter uma cadeia de ligação Redis do Azure e utilizá-la com o Redsmin)][].
-   Veja a documentação [Redis][] e obtenha mais informações sobre os [Tipos de dados de Redis][] e [uma introdução de quinze minutos aos tipos de dados de Redis][].



<!-- INTRA-TOPIC LINKS -->
[Passos Seguintes]: #next-steps
[Introdução à Cache de Redis do Azure (Vídeo)]: #video
[O que é a Cache de Redis do Azure?]: #what-is
[Criar uma Cache do Azure]: #create-cache
[Que tipo de colocação em cache é o mais adequado para mim?]: #choosing-cache
[Preparar o Projeto do Visual Studio para Utilizar a Colocação em Cache do Azure]: #prepare-vs
[Configurar a Aplicação a Utilizar para a Colocação em Cache]: #configure-app
[Introdução à Cache de Redis do Azure ]: #getting-started-cache-service
[Criar a cache]: #create-cache
[Configurar a cache]: #enable-caching
[Configurar os clientes de cache]: #NuGet
[Trabalhar com Caches]: #working-with-caches
[Ligar à cache]: #connect-to-cache
[Adicionar e obter objetos da cache]: #add-object
[Especificar a expiração de um objeto na cache]: #specify-expiration
[Armazenar o estado da sessão do ASP.NET na cache]: #store-session

  
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
[Desenvolver noutras linguagens da Cache de Redis do Azure]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin (Como obter uma cadeia de ligação Redis do Azure e utilizá-la com o Redsmin)]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Fornecedor de Estado da Sessão de Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
[Como: Configurar um Cliente de Cache através de Programação]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Fornecedor de Estado da Sessão da Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320835
[Cache do Azure AppFabric: Estado da Sessão da Colocação em Cache]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Fornecedor de Cache de Saída da Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320837
[Colocação em Cache Partilhada do Azure]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Blogue da Equipa]: http://blogs.msdn.com/b/windowsazure/
[Colocação em Cache do Azure]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Como Configurar os Tamanhos das Máquinas Virtuais]: http://go.microsoft.com/fwlink/?LinkId=164387
[Considerações sobre o Planeamento da Capacidade de Colocação em Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320167
[Colocação em Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=252658
[Como: Definir de Forma Declarativa a Capacidade de Colocação em Cache de uma Página ASP.NET]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[Como: Definir a Capacidade de Colocação em Cache de uma Página Através de Programação]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configurar uma cache na Cache de Redis do Azure]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuração StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Trabalhar com objetos .NET na cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[Instalação do Gestor de Pacotes NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalhes dos Preços da Cache]: http://www.windowsazure.com/pricing/details/cache/
[Portal do Azure]: https://portal.azure.com/

[Descrição Geral da Cache de Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[Cache de Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrar para a Cache de Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=317347
[Exemplos de Cache de Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=320840
[Utilizar Grupos de recursos para gerir os recursos do Azure]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentação do cliente de cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Tipos de dados de Redis]: http://redis.io/topics/data-types
[uma introdução de quinze minutos aos tipos de dados de Redis]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work (Como funcionam as Cadeias da Aplicação e as Cadeias de Ligação)]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/





<!--HONumber=Jun16_HO2-->


