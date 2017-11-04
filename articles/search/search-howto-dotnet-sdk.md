---
title: "Como utilizar o Azure Search a partir de uma aplicação .NET | Microsoft Docs"
description: "Como utilizar o Azure Search a partir de uma aplicação .NET"
services: search
documentationcenter: 
author: brjohnstmsft
manager: jlembicz
editor: 
ms.assetid: 93653341-c05f-4cfd-be45-bb877f964fcb
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: 7273ae6a698f2af52e78ea2aae9ca5cd80f6a2b1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Como utilizar o Azure Search a partir de uma aplicação .NET
Este artigo é uma explicação passo a passo para ajudá-lo em execução com a [SDK .NET da Azure Search](https://aka.ms/search-sdk). Pode utilizar o SDK .NET para implementar uma experiência de pesquisa avançada na sua aplicação utilizando a pesquisa do Azure.

## <a name="whats-in-the-azure-search-sdk"></a>Novidades no Azure SDK de pesquisa
O SDK é composto por uma biblioteca de cliente, `Microsoft.Azure.Search`. Permite-lhe gerir os seus índices, origens de dados e indexadores, bem como carregar e gerir documentos e executar consultas, sem ter de lidar com os detalhes de HTTP e JSON.

A biblioteca de clientes define classes como `Index`, `Field`, e `Document`, bem como operações como `Indexes.Create` e `Documents.Search` no `SearchServiceClient` e `SearchIndexClient` classes. Estas classes são organizadas nas seguintes espaços de nomes:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

A versão atual do SDK .NET da Azure Search está agora disponível normalmente. Se quiser fornecer comentários para-nos incorporar na próxima versão, volte visite a nossa [página comentários](https://feedback.azure.com/forums/263029-azure-search/).

O SDK .NET suporta a versão `2016-09-01` do [API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Esta versão inclui agora suporte para analisadores personalizados e suporte de indexador de Azure Table e Blob do Azure. As funcionalidades de pré-visualização *não* parte desta versão, como o suporte para indexação ficheiros JSON e CSV, estão em [pré-visualização](search-api-2016-09-01-preview.md) e disponível através de [versão 4.0.1-preview do .NET SDK](https://aka.ms/search-sdk-preview).

Este SDK não suporta [operações de gestão](https://docs.microsoft.com/rest/api/searchmanagement/) , tais como criar e dimensionar os serviços de pesquisa e gerir chaves de API. Se precisar de gerir os recursos de pesquisa a partir de uma aplicação .NET, pode utilizar o [SDK de gestão de .NET de pesquisa do Azure](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Atualizar para a versão mais recente do SDK
Se já estiver a utilizar uma versão antiga do SDK .NET da Azure Search e gostaria de atualizar para a nova versão geralmente disponível, [neste artigo](search-dotnet-sdk-migration.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. Visual Studio 2017.
2. O seus próprios serviço da Azure Search. Para utilizar o SDK, é necessário o nome do seu serviço e uma ou mais chaves de API. [Criar um serviço no portal do](search-create-service-portal.md) irão ajudá-lo seguir esses passos.
3. Transferir o SDK .NET da Azure Search [pacote NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) utilizando "Gerir pacotes NuGet" no Visual Studio. Procurar apenas o nome do pacote `Microsoft.Azure.Search` no NuGet.org.

O SDK .NET da Azure Search suporta aplicações direcionada para o .NET Framework 4.6 e .NET Core.

## <a name="core-scenarios"></a>Cenários principais
Existem vários aspetos, que terá de fazer na sua aplicação de pesquisa. Neste tutorial, iremos abranger estes cenários principais:

* Criar um índice
* Povoar o índice com documentos
* A procurar documentos utilizando a pesquisa em texto completo e os filtros

O código de exemplo que se segue ilustra cada uma destas. Pode utilizar os fragmentos de código na sua própria aplicação.

### <a name="overview"></a>Descrição geral
A aplicação de exemplo que irá ser explorar cria um novo índice com o nome "Hotéis" preenche-lo com alguns documentos, em seguida, executa algumas consultas de pesquisa. Segue-se o programa principal, que mostra o fluxo geral:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Pode encontrar o código de origem completo da aplicação de exemplo utilizado nesta apresentação no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Explicaremos através deste passo. Primeiro é preciso criar um novo `SearchServiceClient`. Este objeto permite-lhe gerir os índices. Para construir um, tem de fornecer o nome do serviço da Azure Search, bem como uma chave de API de administração. Pode introduzir estas informações no `appsettings.json` ficheiro o [aplicação de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Se fornecer uma chave incorreta (por exemplo, uma chave de consulta onde era necessária uma chave de administrador), o `SearchServiceClient` irá gerar um `CloudException` com o erro de mensagem "Proibido" pela primeira vez chamar um método de operação no mesmo, tal como `Indexes.Create`. Se isto acontecer para si, verifique o nosso chave de API.
> 
> 

As seguintes algumas linhas chamar os métodos para criar um índice designado "Hotéis" eliminá-lo primeiro se já existir. Vamos ajudá-estes métodos um pouco mais tarde.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Em seguida, o índice tem de ser preenchido. Para fazer isto, vamos precisar de um `SearchIndexClient`. Existem duas formas de obter um: construir este, ou ao chamar `Indexes.GetClient` no `SearchServiceClient`. Podemos utilizar esta última opção para sua comodidade.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> Numa aplicação de pesquisa normal, a gestão de índice e a população são processadas por um componente separado das consultas de pesquisa. `SearchCredentials` é útil para preencher um índice, uma vez que lhe poupa o trabalho de proporcionar outras `Indexes.GetClient`. Este é realizado através da transferência da chave de administração que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte da sua aplicação que executa as consultas, é melhor criar o `SearchIndexClient` diretamente para que possa transferir uma chave de consulta em vez de uma chave de administração. Isto é consistente com o princípio do menor privilégio e irá ajudar a tornar a sua aplicação mais segura. Pode encontrar mais informações sobre chaves de administração e chaves de consulta [aqui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Agora que temos uma `SearchIndexClient`, iremos pode povoar o índice. Isto é feito através de outro método irá guiá-mais tarde.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Por último, iremos executar algumas consultas de pesquisa e apresentar os resultados. Este tempo, iremos utilizar um diferente `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Iremos irá demorar um olhar o `RunQueries` método mais tarde. Eis o código para criar a nova `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Este tempo, utilizamos uma chave de consulta, uma vez que não é necessário acesso de escrita para o índice. Pode introduzir estas informações no `appsettings.json` ficheiro o [aplicação de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se executar esta aplicação com um nome de serviço válido e chaves de API, o resultado deve ter o seguinte aspeto:

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
    Search the entire index for the term 'budget' and return only the hotelName field:
    
    Name: Roach Motel
    
    Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:
    
    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river
    
    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:
    
    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
    
    Search the entire index for the term 'motel':
    
    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
    
    Complete.  Press any key to end application...

O código fonte completo da aplicação é fornecido no final deste artigo.

Em seguida, iremos irá demorar um olhar cada um dos métodos chamados pelo `Main`.

### <a name="creating-an-index"></a>Criar um índice
Depois de criar um `SearchServiceClient`, a seguinte coisa `Main` does é eliminar o índice "Hotéis" se já existir. Que é efetuada pelo método do seguinte:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Este método utiliza a determinada `SearchServiceClient` para verificar se existe o índice e, em caso afirmativo, elimine-o.

> [!NOTE]
> O código de exemplo neste artigo utiliza os métodos síncronos do SDK .NET da Azure Search por uma questão de simplicidade. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, no método acima é utilizar `ExistsAsync` e `DeleteAsync` em vez de `Exists` e `Delete`.
> 
> 

Em seguida, `Main` cria um novo índice "Hotéis" ao chamar este método:

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>()
    };

    serviceClient.Indexes.Create(definition);
}
```

Este método cria uma nova `Index` objeto com uma lista de `Field` objetos que define o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem o comportamento de procura. O `FieldBuilder` classe utiliza reflexão para criar uma lista de `Field` objetos para o índice, examinando os atributos do e propriedades públicas a determinada `Hotel` classe de modelo. Vamos observá o `Hotel` classe mais tarde.

> [!NOTE]
> Pode criar sempre a lista de `Field` objetos diretamente em vez de utilizar `FieldBuilder` se for necessário. Por exemplo, poderá não querer utilizar uma classe de modelo ou poderá ter de utilizar uma classe de modelo existente que não pretende modificar adicionando atributos.
>
> 

Para além dos campos, também pode adicionar perfis de classificação, dos sugestores ou as opções de CORS para o índice (estes omitido da amostra de uma forma abreviada). Pode encontrar mais informações sobre o objeto de índice e partes correspondentes deverá constituintes no [referência do SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index), bem como no [referência da API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Povoar o índice
O seguinte passo na `Main` é para povoar o índice criado recentemente. Este procedimento é efetuado o método seguinte:

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Este método tem quatro partes. O primeiro cria uma matriz de `Hotel` objetos que irão servir como os nossos dados de entrada para carregar para o índice. Estes dados são "hard-coded" de simplicidade. Na sua própria aplicação, os dados serão provenientes provavelmente uma origem de dados externos, como uma base de dados do SQL Server.

A segunda parte cria um `IndexBatch` que contém os documentos. Especificar a operação que pretende aplicar o batch no momento que cria, neste caso, chamando `IndexBatch.Upload`. O batch, em seguida, é carregado para o índice da Azure Search pelo `Documents.Index` método.

> [!NOTE]
> Neste exemplo, vamos apenas estiver a carregar documentos. Se pretendesse intercalar as alterações em documentos existentes ou eliminar documentos, pode criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, ou `IndexBatch.Delete` em vez disso. Também pode misturar operações diferentes num único lote chamando `IndexBatch.New`, que demora uma coleção de `IndexAction` objetos, cada um dos quais informa a Azure Search para efetuar uma operação específica num documento. Pode criar cada `IndexAction` com a sua própria operação ao chamar o método correspondente como `IndexAction.Merge`, `IndexAction.Upload`, e assim sucessivamente.
> 
> 

O terceiro parte deste método é um bloco catch que processa um caso de erro importantes para indexação. Se o serviço Azure Search não consegue indexar alguns dos documentos no lote, uma `IndexBatchException` é emitida pelo `Documents.Index`. Isto pode acontecer se indexar documentos enquanto o seu serviço estiver sobrecarregado. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

> [!NOTE]
> Pode utilizar o `FindFailedActionsToRetry` método para construir um novo lote que contém apenas as ações que falharam uma chamada anterior para `Index`. O método é documentado [aqui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_) e não existe um debate sobre como utilizar corretamente este [no StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Por fim, o `UploadDocuments` atrasos de método de dois segundos. A indexação acontece de forma assíncrona no seu serviço da Azure Search, desta forma, a aplicação de exemplo tem de aguardar alguns momentos para se certificar de que os documentos estão disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK .NET processa documentos
Pode estar a pensar como é que o SDK NET da Azure Search consegue carregar instâncias de uma classe definida por utilizador, tal como `Hotel`, para o índice. Para ajudar a responder a essa questão, vamos ver a `Hotel` classe:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

A primeira coisa a reparar é que cada propriedade pública de `Hotel` corresponde a um campo na definição do índice, mas com uma diferença crucial: o nome de cada campo começa com uma letra minúscula ("camel case"), enquanto o nome de cada propriedade pública de `Hotel` começa com uma letra maiúscula ("Pascal case"). Este é um cenário comum em aplicações .NET que realizam enlace de dados no qual o esquema de destino está fora do controlo do programador da aplicação. Em vez de ter de violar as diretrizes de nomenclatura .NET aplicando o estilo camel-case aos nomes de propriedade, pode indicar ao SDK para mapear os nomes das propriedades no estilo camel-case automaticamente com o atributo `[SerializePropertyNamesAsCamelCase]`.

> [!NOTE]
> O SDK .NET da Azure Search utiliza a biblioteca [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e anular a serialização dos objetos do modelo personalizado de e para JSON. Se necessário, pode personalizar esta serialização. Para obter mais detalhes, consulte [serialização personalizada com JSON.NET](#JsonDotNet).
> 
> 

A segunda coisa a reparar são os atributos como `IsFilterable`, `IsSearchable`, `Key`, e `Analyzer` OptionalFieldAttribute que cada propriedade pública. Estes atributos diretamente para mapeiam o [correspondentes atributos do índice da Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). O `FieldBuilder` classe utiliza estas definições de campo para o índice de construir.

O terceiro mais importante sobre o `Hotel` classe são os tipos de dados das propriedades públicas. Os tipos .NET destas propriedades mapeiam para os tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Existem mapeamentos de tipo semelhantes entre `bool?` e `Edm.Boolean`, `DateTimeOffset?` e `Edm.DateTimeOffset`, etc. As regras específicas para o mapeamento do tipo estão documentadas com o método `Documents.Get` em [Azure Search .NET SDK reference (Referência SDK .NET do Azure Search)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_). O `FieldBuilder` classe encarrega-se desta mapeamento para si, mas ainda pode ser útil compreender o caso de ser necessário resolver quaisquer problemas de serialização.

Esta capacidade de utilizar as suas próprias classes como documentos funciona em ambas as direções; Também pode obter resultados de pesquisa e têm o SDK a anular automaticamente a serialização para um tipo à sua escolha, tal como veremos aparecer na secção seguinte.

> [!NOTE]
> O SDK .NET da Azure Search também suporta documentos dinâmicos utilizando a classe `Document`, que é um mapeamento de chave/valor dos nomes de campo para valores de campo. Isto é útil em cenários onde não sabe qual o esquema de índice no momento da conceção, nem onde seria inconveniente discretizar as classes do modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document`, bem como as sobrecargas de tipo seguro que assumem um parâmetro do tipo genérico. Apenas o último caso é utilizado no código de exemplo neste tutorial. O `Document` classe herda `Dictionary<string, object>`. Pode encontrar outros detalhes [aqui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Por que deve utilizar tipos de dados anuláveis**

Ao conceber as suas próprias classes de modelo para mapear para um índice da Azure Search, recomendamos que declare as propriedades dos tipos de valores, tais como `bool` e `int` para que seja anulável (por exemplo, `bool?` ao invés de `bool`). Se utilizar uma propriedade não anulável, terá de **garantir** que não existem documentos no seu índice de contenham um valor nulo para o campo correspondente. Nem o SDK nem o serviço da Azure Search irão ajudá-lo a impor tais pedidos.

Esta não é apenas uma preocupação hipotética: imagine um cenário onde adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelos como uma melhor prática.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serialização personalizada com JSON.NET
O SDK utiliza JSON.NET para serializar e anular a serialização dos documentos. Pode personalizar a serialização e anulação da serialização da se for necessário definir as suas próprias `JsonConverter` ou `IContractResolver` (consulte o [JSON.NET documentação](http://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes). Isto pode ser útil quando pretender adaptar uma classe de modelo existente da sua aplicação para utilização com a Azure Search e outros cenários mais avançados. Por exemplo, com a serialização personalizada, pode:

* Incluir ou excluir determinadas propriedades de classe de modelo que está a ser armazenados como campos de documentos.
* Mapeamento entre nomes de propriedade no seu código e os nomes de campos no seu índice.
* Crie atributos personalizados que podem ser utilizados para o mapeamento de propriedades para campos de documentos.

Pode encontrar exemplos para implementar serialização personalizada nos testes de unidade para o SDK .NET da Azure Search no GitHub. É um ponto de partida [esta pasta](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Contém as classes que são utilizadas pelos testes de serialização personalizada.

### <a name="searching-for-documents-in-the-index"></a>A procurar documentos no índice
É o último passo para a aplicação de exemplo para procurar alguns documentos no índice. O método seguinte efetua este procedimento:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "hotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("budget", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "lastRenovationDate desc" },
            Select = new[] { "hotelName", "lastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'motel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);
}
```

Sempre executa uma consulta, este método cria uma nova `SearchParameters` objeto. Isto é utilizado para especificar as opções adicionais para a consulta como ordenação, filtragem, paginação e facetamento. Neste método, iremos estiver a definir o `Filter`, `Select`, `OrderBy`, e `Top` propriedade de diferentes consultas. Todos os o `SearchParameters` propriedades estão documentadas [aqui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

O passo seguinte é, na verdade, executar a consulta de pesquisa. Isto é feito utilizando o `Documents.Search` método. Para cada consulta, podemos transmitir o texto de pesquisa para utilizar como uma cadeia (ou `"*"` se não houver nenhum texto de pesquisa), juntamente com os parâmetros de pesquisa que criou anteriormente. Podemos também especificar `Hotel` como o parâmetro de tipo para `Documents.Search`, que indica que o SDK para anular a serialização de documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

> [!NOTE]
> Pode encontrar mais informações sobre a sintaxe de expressão de consulta de pesquisa [aqui](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Por fim, após cada consulta este método itera através de todas as correspondências nos resultados da pesquisa, impressão cada documento para a consola:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Por sua vez, vamos um olhar cada uma das consultas. Eis o código para executar a consulta primeiro:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

Neste caso, iremos estiver a procurar hotéis que correspondem a palavra "orçamento" e queremos voltar a apenas os nomes de átrios, tal como especificado pelo `Select` parâmetro. Seguem-se os resultados:

    Name: Roach Motel

Em seguida, é útil localizar hotéis com uma velocidade de menos de 150 $ noturna e devolver apenas o átrios ID e a descrição:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Esta consulta utiliza uma OData `$filter` expressão, `baseRate lt 150`, para filtrar os documentos no índice. Pode encontrar mais informações sobre a sintaxe de OData que suporte a Azure Search [aqui](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Seguem-se os resultados da consulta:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

Em seguida, queremos localizar os dois hotéis principais que tiverem sido recentemente renovated e mostrar o nome de átrios e a data da última renovation. Apresentamos o código a seguir: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Neste caso, iremos novamente Utilize sintaxe de OData para especificar o `OrderBy` parâmetro como `lastRenovationDate desc`. Iremos também definir `Top` para 2 para garantir que apenas obtemos os documentos de topo dois. Como anteriormente, definimos `Select` para especificar os campos devem ser devolvidos.

Seguem-se os resultados:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Por fim, queremos localizar hotéis todos os que correspondem a palavra "hotel":

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

E Eis os resultados, que incluem todos os campos, uma vez que vamos não especificou o `Select` propriedade:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Este passo conclui o tutorial, mas não a interromperá aqui. **Próximos passos** fornece recursos adicionais para obter mais informações sobre a Azure Search.

## <a name="next-steps"></a>Passos seguintes
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Deepen o conhecimento do utilizador através de [vídeos e outros exemplos e tutoriais](search-video-demo-tutorial-list.md).
* Reveja [convenções de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) para obter as regras para atribuição de nomes de vários objetos.
* Reveja [tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) na Azure Search.
