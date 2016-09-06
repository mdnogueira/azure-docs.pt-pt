<properties
    pageTitle="Criar um índice da Azure Search utilizando o SDK .NET | Microsoft Azure | Serviço de pesquisa na nuvem alojado"
    description="Criar um índice no código utilizando o SDK .NET da Azure Search."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# Criar um índice da Azure Search utilizando o SDK .NET
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Este artigo irá guiá-lo através do processo de criação de um [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) da Azure Search utilizando o [SDK .NET da Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de consultar este guia e criar um índice, deverá já ter [criado um serviço Azure Search](search-create-service-portal.md).

Tenha em atenção que todo o código de exemplo neste artigo está escrito em C#. Pode localizar o código de origem completo [no GitHub](http://aka.ms/search-dotnet-howto).

## I. Identificar a sua chave de API do administrador do serviço Azure Search
Agora que aprovisionou um serviço do Azure Search, está quase pronto para emitir pedidos HTTP contra o seu ponto final de serviço utilizando o SDK .NET. Em primeiro lugar, precisa de obter uma das chaves de API que foi gerada para o serviço de pesquisa que aprovisionou. O SDK .NET irá enviar esta chave de API em cada pedido para o seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

1. Para localizar as chaves de API do seu serviço, tem de iniciar sessão no [Portal do Azure](https://portal.azure.com/)
2. Aceda ao painel do seu serviço Azure Search
3. Clique no ícone "Chaves"

O seu serviço terá *chaves de administração* e *chaves de consulta*.

  - As suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade para gerir o serviço, criar e eliminar índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária caso opte por voltar a gerar a chave principal e vice-versa.
  - As suas *chaves de consulta* concedem acesso só de leitura para índices e documentos e, normalmente, são distribuídas por aplicações cliente que emitem pedidos de pesquisa.

Para o efeito de criação de um índice, pode utilizar tanto a chave de administrador principal como a secundária.

<a name="CreateSearchServiceClient"></a>
## II. Criar uma instância da classe SearchServiceClient
Para começar a utilizar o SDK .NET dAzure Search, terá de criar uma instância da classe `SearchServiceClient`. Esta classe tem vários construtores. O pretendido recebe o nome do serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` molda a sua chave de API.

O código abaixo cria um novo `SearchServiceClient` utilizando valores para o nome do serviço de pesquisa e a chave de API armazenados num ficheiro de configuração da aplicação (`app.config` ou `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` tem uma propriedade `Indexes`. Esta propriedade fornece todos os métodos de que necessita para criar, listar, atualizar ou eliminar os índices da Azure Search.

> [AZURE.NOTE] A classe `SearchServiceClient` gere ligações com o seu serviço de pesquisa. Se possível, para evitar abrir demasiadas ligações, tente partilhar uma única instância de `SearchServiceClient` na sua aplicação. Os seus métodos são seguros para threads de forma a permitir esta partilha.

<a name="DefineIndex"></a>
## III. Definir o índice da Azure Search através da classe `Index`
Uma única chamada para o método `Indexes.Create` irá criar o índice. Este método aceita como parâmetro um objeto `Index` que define o índice da Azure Search. Tem de criar um objeto `Index` e inicializá-lo da seguinte forma:

1. Defina a propriedade `Name` do objeto `Index` para o nome do seu índice.
2. Defina a propriedade `Fields` do objeto `Index` para uma matriz de objetos `Field`. Cada um dos objetos `Field` define o comportamento de um campo no seu índice. Pode fornecer o nome do campo ao construtor, juntamente com o tipo de dados (ou analisador para os campos de cadeia). Também pode definir outras propriedades, como `IsSearchable`, `IsFilterable`, etc.

É importante ter em consideração as suas necessidades comerciais e experiência do utilizador de pesquisa quando estiver a criar o seu índice, uma vez que cada `Field` deve receber as [propriedades adequadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estas propriedades estabelecem quais as funcionalidades de pesquisa (filtragem, facetamento, ordenação de pesquisa de texto completo, etc.) aplicáveis a cada campo. Por cada propriedade que não seja definida de forma explicita, a classe `Field` predefine como desativada a funcionalidade de pesquisa correspondente, a menos que a ative especificamente.

Para o nosso exemplo, atribuímos o nome "hotéis" ao nosso índice e definimos os nossos campos do seguinte modo:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Escolhemos cuidadosamente os valores da propriedade para cada `Field` com base na forma como acreditamos que serão utilizados numa aplicação. Por exemplo, é provável que as pessoas que procuram hotéis não estarão interessadas em correspondências de palavras-chave no campo `description`, desta forma, ativamos a pesquisa em texto completo para esse campo através da definição `IsSearchable` para `true`.

Tenha em atenção que exatamente um campo no seu índice do tipo `DataType.String` deve ser designado como o campo _chave_ definindo `IsKey` como `true` (consulte `hotelId` no exemplo acima).

A definição de índice acima utiliza um analisador de idioma personalizado para o campo `description_fr` porque destina-se ao armazenamento de texto em francês. Consulte [o tópico de suporte de idioma na MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx), bem como a [mensagem de blogue](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondente, para obter mais informações sobre analisadores de idiomas.

> [AZURE.NOTE]  Tenha em atenção que ao transferir o `AnalyzerName.FrLucene` no construtor, o `Field` será automaticamente do tipo `DataType.String` e terá a função `IsSearchable` definida como `true`.

## IV. Criar o índice
Agora que tem um objeto `Index` inicializado, pode criar o índice ao chamar simplesmente `Indexes.Create` no seu objeto `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

Para um pedido com êxito, o método regressará de forma normal. Se existir um problema com o pedido, tal como um parâmetro inválido, o método irá gerar `CloudException`.

Quando terminar com um índice e pretender eliminá-lo, chame somente o método `Indexes.Delete` no seu `SearchServiceClient`. Por exemplo, aqui temos um procedimento para eliminar o índice "hotéis":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] O código de exemplo neste artigo utiliza os métodos síncronos do SDK .NET da Azure Search por uma questão de simplicidade. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, nos exemplos acima pode utilizar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.

## Seguinte
Depois de criar um índice da Azure Search, estará pronto para [carregar o conteúdo para o índice](search-what-is-data-import.md) para que possa começar a pesquisar os seus dados.



<!--HONumber=ago16_HO5-->


