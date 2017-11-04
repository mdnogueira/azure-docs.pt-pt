---
title: "Migrar aplicações de serviço de Cache gerida para Redis - Azure | Microsoft Docs"
description: "Saiba como migrar as aplicações de serviço de Cache gerida e Cache de função para a Cache de Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/30/2017
ms.author: sdanie
ms.openlocfilehash: 0fbfb945c66926794721f2ce8cc183dac51ecb27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrar do serviço de Cache gerida para Cache de Redis do Azure
Migrar as aplicações que utilizam o serviço de Cache gerida do Azure para a Cache de Redis do Azure pode ser conseguido com alterações mínimas à sua aplicação, consoante as funcionalidades do serviço de Cache gerida utilizadas pela sua aplicação de colocação em cache. Enquanto as APIs são não exatamente os mesmos são semelhantes e grande parte do seu código existente que utiliza o serviço de Cache gerida para aceder a uma cache pode ser reutilizada com alterações mínimas. Este tópico mostra como efetuar a configuração necessária e alterações de aplicação para migrar as suas aplicações de serviço de Cache gerida para utilizar a Cache de Redis do Azure e mostra como algumas das funcionalidades de Cache de Redis do Azure podem ser utilizadas para implementar a funcionalidade de uma cache de serviço de Cache gerida.

>[!NOTE]
>Serviço de Cache e não geridas Cache com função foram [extinto](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 de Novembro de 2016. Se tiver quaisquer implementações de Cache de função que pretende migrar para a Cache de Redis do Azure, pode seguir os passos neste artigo.

## <a name="migration-steps"></a>Passos de migração
Os seguintes passos são necessários para migrar uma aplicação de serviço de Cache gerida para utilizar a Cache de Redis do Azure.

* Funcionalidades do serviço de Cache gerida do mapa para a Cache de Redis do Azure
* Escolha uma oferta de Cache
* Criar uma Cache
* Configurar os clientes de Cache
  * Remover a configuração do serviço de Cache gerida
  * Configurar um cliente de cache com o pacote NuGet do stackexchange. redis
* Migre código do serviço de Cache gerida
  * Ligar à cache utilizando a classe de ConnectionMultiplexer
  * Tipos de dados primitivos de acesso na cache
  * Trabalhar com objetos .NET na cache
* Migrar o estado da sessão ASP.NET e a cache de saída para a Cache de Redis do Azure 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Funcionalidades do serviço de Cache gerida do mapa para a Cache de Redis do Azure
Serviço de Cache gerida do Azure e de Cache de Redis do Azure são semelhantes, mas implementam algumas das respetivas funcionalidades de formas diferentes. Esta secção descreve algumas das diferenças e fornece orientações para implementar as funcionalidades do serviço de Cache gerida na Cache de Redis do Azure.

| Funcionalidade do serviço de Cache gerida | Suporte do serviço de Cache gerido | Suporte de Cache de Redis do Azure |
| --- | --- | --- |
| Caches nomeados |Uma cache de predefinição está configurada e na cache de Standard e Premium ofertas, até nove adicionais com o nome caches podem ser configuradas se assim o desejar. |Caches de Redis do Azure têm um número configurável de bases de dados (predefinição de 16) que podem ser utilizados para implementar uma funcionalidade semelhante para caches com nome. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration). |
| Elevada Disponibilidade |Fornece elevada disponibilidade para os itens na cache nas ofertas de cache Standard e Premium. Se os itens são perdidos devido a uma falha, cópias de segurança dos itens na cache ainda estão disponíveis. Escritas para a cache secundária são efetuadas de forma síncrona. |Elevada disponibilidade está disponível nas ofertas de cache Standard e Premium, que dois nós primário/réplica configuração (cada partição horizontal cache Premium têm um par de primário/réplica). Operações de escrita para a réplica que são efetuadas no modo assíncrono. Para obter mais informações, consulte [preços da Cache de Redis do Azure](https://azure.microsoft.com/pricing/details/cache/). |
| Notificações |Permite aos clientes receber notificações assíncronas quando ocorrerem diversas operações de cache numa cache com nome. |As aplicações de cliente podem utilizar Redis pub/sub ou [notificações Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) para alcançar uma funcionalidade semelhante para enviar notificações. |
| Cache local |Armazena uma cópia de objetos em cache localmente no cliente para acesso muito rápido. |Seriam necessário aplicações cliente implementar esta funcionalidade a utilizar um dicionário ou estrutura de dados semelhantes. |
| Política de expulsão |Nenhum ou LRU. A política predefinida é LRU. |Cache de Redis do Azure suporta as seguintes políticas de expulsão: VOLÁTIL lru, allkeys lru, volátil aleatórias, allkeys-aleatórias, volátil-ttl, noeviction. A política predefinida é lru volátil. Para obter mais informações, consulte [configuração do servidor de predefinição Redis](cache-configure.md#default-redis-server-configuration). |
| Política de expiração |A política de expiração de predefinida é absoluto e o intervalo de expiração de predefinido é dez minutos. As políticas a deslizante e nunca também estão disponíveis. |Por predefinição itens na cache não expira, mas uma expiração pode ser configurada numa base por escrita utilizando as sobrecargas de conjunto de cache. Para obter mais informações, consulte [adicionar e obter objetos da cache](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache). |
| Regiões e etiquetagem |Regiões são os subgrupos para itens em cache. Regiões também suportam a anotação de itens em cache com cadeias descritivas adicionais chamadas etiquetas. As regiões para suportar a capacidade para efetuar operações de pesquisa em qualquer itens marcados nessa região. Todos os itens numa região estão localizados dentro de um único nó de cluster de cache. |Uma cache de Redis é composta por um único nó (exceto se o cluster de Redis está ativado) para que o conceito de regiões do serviço de Cache gerida não se aplica. Redis suporta procura e operações de carateres universais ao obter as chaves para que etiquetas descritivas podem ser incorporadas os nomes de chaves e utilizadas para obter os itens mais tarde. Para obter um exemplo de implementação de uma solução de etiquetagem Redis a utilizar, consulte [implementar cache etiquetagem com Redis](http://stackify.com/implementing-cache-tagging-redis/). |
| Serialização |Cache gerida suporta NetDataContractSerializer, BinaryFormatter e a utilização de serializers personalizados. A predefinição é NetDataContractSerializer. |É da responsabilidade de aplicação de cliente para serializar objetos .NET antes de colocá-las para a cache, com a opção do serializador até o programador da aplicação cliente. Para obter mais informações e código de exemplo, consulte [trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulador de cache |Cache gerida fornece um emulador de local cache. |Cache de Redis do Azure não tem um emulador, mas pode [executar localmente a compilação de MSOpenTech de redis server.exe](cache-faq.md#cache-emulator) para fornecer uma experiência de emulador. |

## <a name="choose-a-cache-offering"></a>Escolha uma oferta de Cache
A Cache de Redis do Microsoft Azure está disponível nos seguintes escalões:

* **Básico** – Único nó. Vários tamanhos até 53 GB.
* **Standard** – Dois nós Primário/Réplica. Vários tamanhos até 53 GB. SLA de 99,9%.
* **Premium** – Dois nós Primário/Réplica com um máximo de 10 shards. Vários tamanhos, de 6 GB a 530 GB. Todas as funcionalidades do escalão Standard e mais, incluindo o suporte para o [Cluster de Redis](cache-how-to-premium-clustering.md), a [Persistência de Redis](cache-how-to-premium-persistence.md) e a [Azure Virtual Network](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada escalão difere em termos de funcionalidades e preços. As funcionalidades são abordadas mais tarde neste guia e para obter mais informações sobre preços, consulte [detalhes dos preços da Cache](https://azure.microsoft.com/pricing/details/cache/).

Um ponto de partida para a migração é escolher o tamanho que corresponde ao tamanho da cache, serviço de Cache gerida anterior e, em seguida, aumentar ou reduzir verticalmente dependendo dos requisitos da sua aplicação. Para obter mais orientações sobre como escolher a oferta de Cache de Redis do Azure à direita, consulte [que tamanho e a oferta da Cache de Redis devo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Criar uma Cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurar os clientes de Cache
Assim que a cache é criada e configurada, o passo seguinte consiste em remover a configuração do serviço de Cache gerida e adicionar a adicionar a configuração da Cache de Redis do Azure e referencia para que os clientes de cache podem aceder à cache.

* Remover a configuração do serviço de Cache gerida
* Configurar um cliente de cache com o pacote NuGet do stackexchange. redis

### <a name="remove-the-managed-cache-service-configuration"></a>Remover a configuração do serviço de Cache gerida
Antes das aplicações de cliente podem ser configuradas para Cache de Redis Azure, a configuração do serviço de Cache gerida existente e referências de assemblagem tem de ser removidas por desinstalar o pacote NuGet do serviço de Cache gerida.

Para desinstalar o pacote NuGet do serviço de Cache gerida, clique com botão direito no projeto de cliente em **Explorador de soluções** e escolha **gerir pacotes NuGet**. Selecione o **instalados pacotes** nós e tipo W**indowsAzure.Caching** na caixa de pacotes instalados de pesquisa. Selecione **Windows** **a Cache do Azure** (ou **Windows** **colocação em cache do Azure** consoante a versão do pacote NuGet), clique em **desinstalação**e, em seguida, clique em **fechar**.

![Desinstalar o pacote de NuGet do serviço de Cache gerida do Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar o pacote NuGet do serviço de Cache gerida remove as assemblagens do serviço de Cache gerida e as entradas de serviço de Cache gerida no App. config ou Web. config da aplicação cliente. Uma vez que algumas definições personalizadas não podem ser removidas quando desinstalar o pacote NuGet, abra Web. config ou o App. config e certifique-se de que os seguintes elementos são completamente removidos.

Certifique-se de que o `dataCacheClients` entrada é removida do `configSections` elemento. Não remova todo o `configSections` elemento; remove apenas o `dataCacheClients` entrada, se estiver presente.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Certifique-se de que o `dataCacheClients` secção é removida. O `dataCacheClients` secção será semelhante ao seguinte exemplo.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Depois da configuração do serviço de Cache gerida for removida, pode configurar o cliente de cache, tal como descrito na secção seguinte.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurar um cliente de cache com o pacote NuGet do stackexchange. redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migre código do serviço de Cache gerida
A API para o cliente de cache stackexchange. redis é semelhante para o serviço de Cache gerida. Esta secção fornece uma descrição geral das diferenças.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ligar à cache utilizando a classe de ConnectionMultiplexer
No serviço de Cache gerida, as ligações para a cache foram processadas pelo `DataCacheFactory` e `DataCache` classes. Na Cache de Redis do Azure, estas ligações são geridas pelo `ConnectionMultiplexer` classe.

Adicione o seguinte utilizando a instrução na parte superior de qualquer ficheiro a partir do qual pretende aceder à cache.

```c#
using StackExchange.Redis
```

Se não resolve este espaço de nomes, não se esqueça de que adicionou o pacote NuGet stackexchange. redis, tal como descrito no [configurar os clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

> [!NOTE]
> Tenha em atenção que o cliente stackexchange. redis requer o .NET Framework 4 ou superior.
> 
> 

Para ligar a uma instância da Cache de Redis do Azure, chame a estática `ConnectionMultiplexer.Connect` método e passe o ponto final e a chave da. Uma abordagem para partilhar uma instância `ConnectionMultiplexer` na sua aplicação consiste em ter uma propriedade estática que devolva uma instância ligada, tal como no seguinte exemplo. Esta abordagem fornece uma forma segura para os threads de modo a inicializar apenas uma única instância `ConnectionMultiplexer` ligada. Neste exemplo `abortConnect` está definido como FALSO, o que significa que a chamada terá êxito mesmo se não for estabelecida uma ligação para a cache. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

```c#
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
```

O ponto final da cache, as chaves e as portas podem ser obtidas a partir de **a Cache de Redis** painel para a instância da cache. Para obter mais informações, consulte [propriedades de Cache de Redis](cache-configure.md#properties).

Assim que a ligação for estabelecida, é devolvida uma referência para a base de dados de cache de Redis, chamando o `ConnectionMultiplexer.GetDatabase` método. O objeto devolvido do método `GetDatabase` é um objeto pass-through simples e não precisa de ser armazenado.

```c#
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

O cliente stackexchange. redis utiliza o `RedisKey` e `RedisValue` tipos para aceder e armazenar itens na cache. Estes tipos de mapeiam para mais idiomas primitivos, incluindo a cadeia e, muitas vezes, não são utilizados diretamente. Redis cadeias têm o tipo mais básico do valor de Redis e podem conter vários tipos de dados, incluindo fluxos binários serializados, e enquanto não é possível utilizar o tipo diretamente, irá utilizar os métodos que contêm `String` no nome. Para tipos de dados primitivos mais armazenar e obter itens do cache com o `StringSet` e `StringGet` métodos, a menos que estão a armazenar coleções ou outros tipos de dados de Redis na cache. 

`StringSet`e `StringGet` são muito semelhantes para o serviço de Cache gerida `Put` e `Get` métodos, com um principal diferença é que antes de definir e obter um objeto de .NET para a cache, tem de serializá-lo primeiro. 

Quando chamar `StringGet`, se o objeto existir, é devolvido e, se não existir, é devolvido o valor nulo. Neste caso, pode obter o valor da fonte de dados pretendida e armazená-lo na cache para uma utilização posterior. Este processo é conhecido como o padrão do lado da cache.

Para especificar a expiração de um item na cache, utilize o parâmetro `TimeSpan` de `StringSet`.

```c#
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Cache de Redis do Azure pode trabalhar com objetos .NET, bem como tipos de dados primitivos, mas antes de um objeto de .NET pode ser colocadas em cache tem de ser serializado. Isto é da responsabilidade do programador da aplicação. Isto proporciona flexibilidade o programador escolher o serializador. Para obter mais informações e código de exemplo, consulte [trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrar o estado da sessão ASP.NET e a cache de saída para a Cache de Redis do Azure
Cache de Redis do Azure tem fornecedores para o estado da sessão ASP.NET e cache de saída de página. Para migrar a sua aplicação que utiliza as versões do serviço de Cache gerida destes fornecedores, remova primeiro as secções existentes do seu Web. config e, em seguida, configure as versões de Cache de Redis do Azure dos fornecedores. Para obter instruções sobre como utilizar os fornecedores do ASP.NET de Cache de Redis do Azure, consulte [fornecedor de estado de sessão do ASP.NET para a Cache de Redis do Azure](cache-aspnet-session-state-provider.md) e [fornecedor de Cache de saída do ASP.NET para a Cache de Redis do Azure](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Passos seguintes
Explorar o [documentação de Cache de Redis do Azure](https://azure.microsoft.com/documentation/services/cache/) para tutoriais, amostras, vídeos e muito mais.

