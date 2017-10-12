---
title: "Criar um índice (API .NET - Azure Search) | Microsoft Docs"
description: "Criar um índice no código utilizando o SDK .NET da Azure Search."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: fac41903c3e5731d17f832ff58145fe74dfa29f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Criar um índice da Azure Search utilizando o SDK .NET
> [!div class="op_single_selector"]
> * [Descrição geral](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Este artigo irá guiá-lo através do processo de criação de um [índice](https://docs.microsoft.com/rest/api/searchservice/Create-Index) da Azure Search utilizando o [SDK .NET da Azure Search](https://aka.ms/search-sdk).

Antes de consultar este guia e criar um índice, deverá já ter [criado um serviço Azure Search](search-create-service-portal.md).

> [!NOTE]
> Todo o código de exemplo neste artigo está escrito em C#. Pode localizar o código de origem completo [no GitHub](http://aka.ms/search-dotnet-howto). Também pode ler sobre o [Azure Search .NET SDK (SDK .NET do Azure Search)](search-howto-dotnet-sdk.md) para um percurso mais detalhado através do código de exemplo.


## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificar a sua chave de API do administrador do serviço Azure Search
Agora que aprovisionou um serviço do Azure Search, está quase pronto para emitir pedidos HTTP contra o seu ponto final de serviço utilizando o SDK .NET. Em primeiro lugar, precisa de obter uma das chaves de API que foi gerada para o serviço de pesquisa que aprovisionou. O SDK .NET irá enviar esta chave de API em cada pedido para o seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

1. Para localizar as chaves de API do seu serviço, inicie sessão no [portal do Azure](https://portal.azure.com/)
2. Aceda ao painel do seu serviço Azure Search
3. Clique no ícone "Chaves"

O seu serviço terá *chaves de administração* e *chaves de consulta*.

* As suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade para gerir o serviço, criar e eliminar índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária caso opte por voltar a gerar a chave principal e vice-versa.
* As suas *chaves de consulta* concedem acesso só de leitura para índices e documentos e, normalmente, são distribuídas por aplicações cliente que emitem pedidos de pesquisa.

Para o efeito de criação de um índice, pode utilizar tanto a chave de administrador principal como a secundária.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>Criar uma instância da classe SearchServiceClient
Para começar a utilizar o SDK .NET dAzure Search, terá de criar uma instância da classe `SearchServiceClient`. Esta classe tem vários construtores. O pretendido recebe o nome do serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` molda a sua chave de API.

O código abaixo cria um novo `SearchServiceClient` com valores para o nome do serviço de pesquisa e a chave de API armazenados num ficheiro de configuração da aplicação (`appsettings.json` no caso do [exemplo de aplicação](http://aka.ms/search-dotnet-howto)):

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` tem uma propriedade `Indexes`. Esta propriedade fornece todos os métodos de que necessita para criar, listar, atualizar ou eliminar os índices da Azure Search.

> [!NOTE]
> A classe `SearchServiceClient` gere ligações com o seu serviço de pesquisa. Se possível, para evitar abrir demasiadas ligações, tente partilhar uma única instância de `SearchServiceClient` na sua aplicação. Os seus métodos são seguros para threads de forma a permitir esta partilha.
> 
> 

<a name="DefineIndex"></a>

## <a name="define-your-azure-search-index"></a>Definir o índice da Azure Search
Uma única chamada para o método `Indexes.Create` irá criar o índice. Este método aceita como parâmetro um objeto `Index` que define o índice da Azure Search. Tem de criar um objeto `Index` e inicializá-lo da seguinte forma:

1. Defina a propriedade `Name` do objeto `Index` para o nome do seu índice.
2. Defina a propriedade `Fields` do objeto `Index` para uma matriz de objetos `Field`. A forma mais fácil para criar os objetos `Field` é com o método `FieldBuilder.BuildForType`, transmitindo uma classe de modelo para o parâmetro de tipo. Uma classe de modelo tem propriedades que mapeiam os campos do seu índice. Isto permite-lhe vincular documentos a partir do índice de pesquisa para instâncias da sua classe de modelo.

> [!NOTE]
> Se não pretender utilizar uma classe de modelo, ainda pode definir o índice ao criar `Field` objetos diretamente. Pode fornecer o nome do campo ao construtor, juntamente com o tipo de dados (ou analisador para os campos de cadeia). Também pode definir outras propriedades, como `IsSearchable`, `IsFilterable`, etc.
>
>

É importante ter em consideração as suas necessidades comerciais e a experiência do utilizador de pesquisa quando estiver a criar o seu índice, uma vez que cada campo deve receber as [propriedades adequadas](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Estas propriedades estabelecem quais as funcionalidades de pesquisa (filtragem, facetamento, ordenação de pesquisa de texto completo, etc.) aplicáveis a cada campo. Por cada propriedade que não seja definida de forma explicita, a classe `Field` predefine como desativada a funcionalidade de pesquisa correspondente, a menos que a ative especificamente.

Para o nosso exemplo, atribuímos o nome "hotéis" ao nosso índice e definimos os nossos campos com uma classe de modelo. Cada propriedade da classe do modelo tem atributos que determinam os comportamentos relacionados com a pesquisa do campo de índice correspondente. A classe de modelo é definida da seguinte forma:

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

Escolhemos cuidadosamente os atributos para cada propriedade com base na forma como acreditamos que serão utilizados numa aplicação. Por exemplo, é provável que as pessoas que procuram hotéis estejam interessadas em correspondências de palavras-chave no campo `description`, desta forma, ativamos a pesquisa em texto completo para esse campo através da adição do atributo `IsSearchable` à propriedade `Description`.

Tenha em atenção que exatamente um campo no seu índice do tipo `string` deve ser designado como o campo *chave* adicionando o atributo `Key` (consulte `HotelId` no exemplo acima).

A definição de índice acima utiliza um analisador de idioma para o campo `description_fr` porque destina-se ao armazenamento de texto em francês. Consulte [o tópico de suporte de idioma](https://docs.microsoft.com/rest/api/searchservice/Language-support), bem como a [mensagem de blogue](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondente, para obter mais informações sobre analisadores de idiomas.

> [!NOTE]
> Por predefinição, o nome de cada propriedade na classe do modelo é utilizado como o nome do campo correspondente no índice remissivo. Se pretender mapear todos os nomes de propriedade para os nomes dos campos camel-case, marque a classe com o atributo `SerializePropertyNamesAsCamelCase`. Se pretender mapear para um nome diferente, pode utilizar o atributo `JsonProperty` como a propriedade `DescriptionFr` acima. O atributo `JsonProperty` tem precedência sobre o atributo `SerializePropertyNamesAsCamelCase`.
> 
> 

Agora que definimos uma classe de modelo, podemos criar uma definição de índice muito facilmente:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="create-the-index"></a>Criar o índice
Agora que tem um objeto `Index` inicializado, pode criar o índice ao chamar simplesmente `Indexes.Create` no seu objeto `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

Para um pedido com êxito, o método regressará de forma normal. Se existir um problema com o pedido, tal como um parâmetro inválido, o método irá gerar `CloudException`.

Quando terminar com um índice e pretender eliminá-lo, chame somente o método `Indexes.Delete` no seu `SearchServiceClient`. Por exemplo, aqui temos um procedimento para eliminar o índice "hotéis":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> O código de exemplo neste artigo utiliza os métodos síncronos do SDK .NET da Azure Search por uma questão de simplicidade. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, nos exemplos acima pode utilizar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Depois de criar um índice da Azure Search, estará pronto para [carregar o conteúdo para o índice](search-what-is-data-import.md) para que possa começar a pesquisar os seus dados.

