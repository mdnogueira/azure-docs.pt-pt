---
title: "Atualizar para o SDK de .NET do Azure Search versão 1.1 | Microsoft Docs"
description: "Atualizar para o SDK de .NET do Azure Search versão 1.1"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 66f89958-a320-4a24-87f9-69315848909f
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.openlocfilehash: 9782454e3bfc697b63cde8aa28a14be0c393c36b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Atualizar para o SDK de .NET de pesquisa do Azure versão 3
Se estiver a utilizar a versão pré-visualização do 2.0 ou mais antiga do [SDK .NET da Azure Search](https://aka.ms/search-sdk), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 3.

Para obter instruções mais geral do SDK, incluindo exemplos, consulte [como utilizar o Azure Search a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 3 do SDK .NET da Azure Search contém algumas alterações de versões anteriores. São principalmente menores, para que alterar o seu código deverá ser preciso apenas esforço. Consulte [passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o código para utilizar a nova versão do SDK.

> [!NOTE]
> Se estiver a utilizar a versão 1.0.2-preview ou anterior, deverá atualizar para versão 1.1 e, em seguida, atualize para versão 3. Consulte [apêndice: passos para atualizar a versão 1.1](#UpgradeStepsV1) para obter instruções.
>
> A instância de serviço de pesquisa do Azure suporta várias versões de REST API, incluindo o mais recente. Pode continuar a utilizar uma versão quando já não é um mais recente, mas recomendamos que migre código para utilizar a versão mais recente. Quando utilizar a API REST, tem de especificar a versão da API em cada pedido através do parâmetro de versão da api. Ao utilizar o SDK .NET, a versão do SDK está a utilizar determina a versão da REST API correspondente. Se estiver a utilizar um SDK anterior, pode continuar a executar esse código sem alterações, mesmo se o serviço é atualizado para suportar uma versão mais recente da API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Novidades na versão 3
A versão 3 do SDK .NET da Azure Search destina-se a versão mais recente versão geralmente disponível da API de REST da Azure Search, especificamente 2016-09-01. Isto torna possível utilizar muitas funcionalidades novas da Azure Search a partir de uma aplicação .NET, incluindo o seguinte:

* [Analisadores personalizados](https://aka.ms/customanalyzers)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) e [Table Storage do Azure](search-howto-indexing-azure-tables.md) suporte do indexador
* Personalização de indexador através de [mapeamentos de campo](search-indexer-field-mappings.md)
* O suporte de ETags para ativar a atualização em simultâneo seguro de definições de índice, indexadores e origens de dados
* Suporte para criar definições de campo de índice de forma declarativa decorating a classe de modelo e utilizando a nova `FieldBuilder` classe.
* Suporte para o .NET Core e o perfil do .NET portátil 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Em primeiro lugar, atualizar a referência do NuGet para `Microsoft.Azure.Search` utilizando a consola de Gestor de pacotes NuGet ou ao clicar no seu referências do projeto e selecionar "Gerir NuGet pacotes..." no Visual Studio.

Depois de NuGet transferiu os novos pacotes e as respetivas dependências, reconstrua o projeto. Dependendo de como o seu código está estruturado,-reconstrua poderá com êxito. Se Sim, está pronto para começar!

Se a compilação falhar, deverá ver um erro de compilação como o seguinte:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

O passo seguinte é para corrigir este erro de compilação. Consulte [interrompendo as alterações na versão 3](#ListOfChanges) para obter detalhes sobre o que faz com que o erro e como o corrigir.

Poderá ver avisos de compilação adicionais relacionados com métodos obsoletos ou propriedades. Os avisos inclui instruções que utilizar em vez da funcionalidade despromovida. Por exemplo, se a sua aplicação utiliza o `IndexingParameters.Base64EncodeKeys` propriedade, deve obter um aviso que indica que`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Depois de ter corrigido os erros de compilação, pode efetuar alterações à sua aplicação para tirar partido das novas funcionalidades, se desejar. Novas funcionalidades no SDK passo são detalhadas no [são as novidades na versão 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Eliminar as alterações na versão 3
Existe um pequeno número de interrompendo as alterações na versão 3, que pode necessitar de código altera-se para além de recriar a sua aplicação.

### <a name="indexesgetclient-return-type"></a>Tipo de retorno Indexes.GetClient
O `Indexes.GetClient` método tem um novo tipo de retorno. Anteriormente, devolveu `SearchIndexClient`, mas isto foi alterado para `ISearchIndexClient` na versão 2.0-preview e que alteração acarreta a versão 3. Isto é para suportar clientes que pretendem mock o `GetClient` método para testes de unidade ao devolver uma implementação de mock `ISearchIndexClient`.

#### <a name="example"></a>Exemplo
Se o seu código tem o seguinte aspeto:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Pode alterá-la para esta opção para corrigir os erros de compilação:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>Já não são implicitamente pode ser convertidos em cadeias AnalyzerName, tipo de dados e outros
Existem muitos tipos no SDK .NET da Azure Search que derivem de `ExtensibleEnum`. Anteriormente foram todos os seguintes tipos implicitamente pode ser convertido no tipo `string`. No entanto, um erro foi descoberto no `Object.Equals` implementação para estas classes e corrigir os erros necessário desativar esta conversão implícita. A conversão explícita para `string` ainda tem permissão.

#### <a name="example"></a>Exemplo
Se o seu código tem o seguinte aspeto:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Pode alterá-la para esta opção para corrigir os erros de compilação:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Remover membros obsoletos

Poderá ver erros de compilação relacionados com propriedades que foram identificadas como obsoletos na versão 2.0 preview e posteriormente removidas versão 3 ou métodos. Se encontrar esses erros, eis como resolvê-los:

- Se estava a utilizar este construtor: `ScoringParameter(string name, string value)`, em alternativa, utilize este:`ScoringParameter(string name, IEnumerable<string> values)`
- Se estava a utilizar o `ScoringParameter.Value` propriedade, utilize o `ScoringParameter.Values` propriedade ou o `ToString` método em vez disso.
- Se estava a utilizar o `SearchRequestOptions.RequestId` propriedade, utilize o `ClientRequestId` propriedade em vez disso.

### <a name="removed-preview-features"></a>Funcionalidades de pré-visualização removido

Se estiver a atualizar da versão 2.0-pré-visualização para a versão 3, lembre-se de que JSON e CSV análise suporte para o Blob indexadores foi removido, uma vez que estas funcionalidades ainda estão em pré-visualização. Especificamente, os seguintes métodos do `IndexingParametersExtensions` classe ter sido removido:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se a aplicação tem uma disco rígida dependência sobre estas funcionalidades, não será possível atualizar a versão 3 do SDK .NET da Azure Search. Pode continuar a utilizar a pré-visualização-versão 2.0. No entanto,. tenha em atenção que **não é recomendada a utilização de pré-visualização SDKs em aplicações de produção**. Funcionalidades de pré-visualização são apenas de avaliação e podem ser alterados.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como utilizar o SDK .NET da Azure Search, consulte a nossa recentemente atualizado [procedimentos](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se tiver problemas, não hesite e peça-nos para obter ajuda no [fórum MSDN de pesquisa do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Se encontrar um erro, ficheiro de um problema no [repositório do GitHub de SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de que o título do problema com o prefixo "Search SDK:".

Obrigado por utilizar a pesquisa do Azure!

<a name="UpgradeStepsV1"></a>

## <a name="appendix-steps-to-upgrade-to-version-11"></a>Apêndice: Passos para atualizar a versão 1.1
> [!NOTE]
> Esta secção aplica-se apenas a utilizadores de 1.0.2-preview de versão do SDK .NET da Azure Search e mais antigos.
> 
> 

Em primeiro lugar, atualizar a referência do NuGet para `Microsoft.Azure.Search` utilizando a consola de Gestor de pacotes NuGet ou ao clicar no seu referências do projeto e selecionar "Gerir NuGet pacotes..." no Visual Studio.

Depois de NuGet transferiu os novos pacotes e as respetivas dependências, reconstrua o projeto.

Se anteriormente estavam a utilizar a versão 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview a compilação deve ter êxito e está pronto para começar!

Se estava a utilizar anteriormente versão 0.13.0-preview ou anterior, deverá ver compilação erros semelhante ao seguinte:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

O passo seguinte é para corrigir os erros de compilação um por um. A maioria irá necessitar de alterar alguns nomes de classe e método cujo nome foi mudados no SDK. [Lista de interrompendo as alterações na versão 1.1](#ListOfChangesV1) contém uma lista destas alterações de nome.

Se estiver a utilizar classes personalizadas para modelar os seus documentos e essas classes têm propriedades de tipos primitivos não anulável (por exemplo, `int` ou `bool` em c#), há uma correção de erros com a versão 1.1 do SDK do qual deve ter em mente. Consulte [correções de erros na versão 1.1](#BugFixesV1) para obter mais detalhes.

Por fim, depois de ter corrigido os erros de compilação, pode efetuar alterações à sua aplicação para tirar partido das novas funcionalidades, se desejar.

<a name="ListOfChangesV1"></a>

### <a name="list-of-breaking-changes-in-version-11"></a>Lista de interrompendo as alterações na versão 1.1
A lista seguinte está ordenada pela probabilidade de que a alteração afetará o código da aplicação.

#### <a name="indexbatch-and-indexaction-changes"></a>Alterações IndexBatch e IndexAction
`IndexBatch.Create`o nome foi mudado para `IndexBatch.New` e já não tem um `params` argumento. Pode utilizar `IndexBatch.New` para lotes misturar diferentes tipos de ações (intercala, eliminações, etc.). Além disso, existem novos métodos estáticos para a criação de lotes em que todas as ações são as mesmas: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`.

`IndexAction`já não tem construtores públicos e as respetivas propriedades agora são imutáveis. Deve utilizar os novos métodos estáticos para a criação de ações para diferentes fins: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`. `IndexAction.Create`foi removido. Se utilizou a sobrecarga que recebe apenas um documento, certifique-se de que utiliza `Upload` em vez disso.

##### <a name="example"></a>Exemplo
Se o seu código tem o seguinte aspeto:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Pode alterá-la para esta opção para corrigir os erros de compilação:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se quiser, pode simplificar ainda mais-lo a este:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Alterações de IndexBatchException
O `IndexBatchException.IndexResponse` propriedade foi alterada para `IndexingResults`, e o respetivo tipo é agora `IList<IndexingResult>`.

##### <a name="example"></a>Exemplo
Se o seu código tem o seguinte aspeto:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Pode alterá-la para esta opção para corrigir os erros de compilação:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

#### <a name="operation-method-changes"></a>Alterações de método de operação
Cada operação no SDK .NET da Azure Search está exposta como um conjunto de sobrecargas do método para os chamadores operações síncronos e assíncronos. As assinaturas e factoring destas sobrecargas do método foi alterado na versão 1.1.

Por exemplo, a operação de "Obter estatísticas de índice" em versões anteriores do SDK expostos estas assinaturas:

No `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

No `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

As assinaturas do método para a mesma operação versão 1.1 tem o seguinte aspeto:

No `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

No `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

A partir da versão 1.1, o SDK .NET da Azure Search organiza os métodos de operação diferente:

* Parâmetros opcionais são agora modelados como predefinido parâmetros em vez que as sobrecargas do método adicional. Isto reduz o número das sobrecargas do método, por vezes significativamente.
* Os métodos de extensão agora ocultar muitos dos detalhes supérfluas HTTP do chamador. Por exemplo, as versões anteriores do SDK devolveu um objecto de resposta com um código de estado HTTP, o que muitas vezes, não tem de verificar porque métodos operação throw `CloudException` para qualquer código de estado que indica um erro. Os novos métodos de extensão devolvem apenas os objetos de modelo, poupando o problemas de ter de abri-los no seu código.
* Por outro lado, as principais das interfaces agora os métodos de expõe que dão-lhe mais controlo ao nível do HTTP se o ficheiro necessário. Agora pode passar nos cabeçalhos HTTP personalizados para ser incluída no pedidos e o novo `AzureOperationResponse<T>` tipo de retorno dá-lhe acesso direto para o `HttpRequestMessage` e `HttpResponseMessage` para a operação. `AzureOperationResponse`está definido no `Microsoft.Rest.Azure` espaço de nomes e substitui `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Alterações de ScoringParameters
Uma nova classe com o nome `ScoringParameter` foi adicionado no SDK mais recente para tornar mais fácil fornecer parâmetros para a classificação de perfis de uma consulta de pesquisa. Anteriormente a `ScoringProfiles` propriedade o `SearchParameters` classe foi escrita como `IList<string>`; Agora que é digitado como `IList<ScoringParameter>`.

##### <a name="example"></a>Exemplo
Se o seu código tem o seguinte aspeto:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Pode alterá-la para esta opção para corrigir os erros de compilação: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Alterações de classe de modelo
Devido a alterações a assinatura descritas [alterações de método de operação](#OperationMethodChanges), muitas classes no `Microsoft.Azure.Search.Models` espaço de nomes foram alterado ou removido. Por exemplo:

* `IndexDefinitionResponse`foi substituído por`AzureOperationResponse<Index>`
* `DocumentSearchResponse` mudou de nome para `DocumentSearchResult`
* `IndexResult` mudou de nome para `IndexingResult`
* `Documents.Count()`agora devolve um `long` com a contagem de documentos em vez de um`DocumentCountResponse`
* `IndexGetStatisticsResponse` mudou de nome para `IndexGetStatisticsResult`
* `IndexListResponse` mudou de nome para `IndexListResult`

Para resumir, `OperationResponse`-derivada classes que existiam só de à encapsule foram removidos um objeto de modelo. As classes restantes tem tido o respetivo sufixo mudou de `Response` para `Result`.

##### <a name="example"></a>Exemplo
Se o seu código tem o seguinte aspeto:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Pode alterá-la para esta opção para corrigir os erros de compilação:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Classes de resposta e IEnumerable
Uma alteração adicional que pode afetar o seu código é que as classes de resposta que contêm coleções já não está a implementar `IEnumerable<T>`. Em vez disso, pode aceder diretamente a propriedade da coleção. Por exemplo, se o seu código tem o seguinte aspeto:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Pode alterá-la para esta opção para corrigir os erros de compilação:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="special-case-for-web-applications"></a>Caso especial para aplicações web
Se tiver uma aplicação web que serializa `DocumentSearchResponse` diretamente para enviar os resultados da pesquisa para o browser, terá de alterar o código ou os resultados não irão serializar corretamente. Por exemplo, se o seu código tem o seguinte aspeto:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Pode alterá-la por obter o `.Results` propriedade da resposta de pesquisa para corrigir a composição de resultados de pesquisa:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Terá de procurar nestes casos no seu código por si; **o compilador não irão avisar** porque `JsonResult.Data` é do tipo `object`.

#### <a name="cloudexception-changes"></a>Alterações de CloudException
O `CloudException` classe foi movido do `Hyak.Common` espaço de nomes para o `Microsoft.Rest.Azure` espaço de nomes. Além disso, o `Error` propriedade foi alterada para `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Alterações SearchServiceClient e SearchIndexClient
O tipo do `Credentials` propriedade foi alterado de `SearchCredentials` a respectiva classe base, `ServiceClientCredentials`. Se precisar de aceder a `SearchCredentials` de um `SearchIndexClient` ou `SearchServiceClient`, utilize a nova `SearchCredentials` propriedade.

Em versões anteriores do SDK, `SearchServiceClient` e `SearchIndexClient` tinha construtores demoraram um `HttpClient` parâmetro. Estes foram substituídos por construtores que requeira um `HttpClientHandler` e uma matriz de `DelegatingHandler` objetos. Isto torna mais fácil instalar os processadores personalizados para processar previamente os pedidos de HTTP, se necessário.

Por fim, os construtores que demoraram um `Uri` e `SearchCredentials` foram alterados. Por exemplo, se tiver o código que tem este aspeto:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Pode alterá-la para esta opção para corrigir os erros de compilação:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Também em atenção que o tipo do parâmetro credenciais foi alterada para `ServiceClientCredentials`. Isto é pouco provável afetar o seu código desde `SearchCredentials` é derivado de `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Transmitir um ID de pedido
Em versões anteriores do SDK, pode configurar um ID de pedido no `SearchServiceClient` ou `SearchIndexClient` e deverá ser incluído em cada pedido para a API REST. Isto é útil para resolução de problemas com o serviço de pesquisa se necessitar de contactar o suporte. No entanto, é mais útil para definir um ID de pedido exclusivo para cada operação em vez de utilizar o mesmo ID para todas as operações. Por este motivo, o `SetClientRequestId` métodos de `SearchServiceClient` e `SearchIndexClient` foram removidos. Em vez disso, pode passar um ID de pedido para cada método de operação através de opcional `SearchRequestOptions` parâmetro.

> [!NOTE]
> Numa versão futura do SDK, iremos adicionar um novo mecanismo para definir um ID de pedido globalmente no cliente objetos, que é consistente com a abordagem utilizada por outros SDKs do Azure.
> 
> 

#### <a name="example"></a>Exemplo
Se tiver o código que tem este aspeto:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Pode alterá-la para esta opção para corrigir os erros de compilação:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Alterações de nome de interface
Os nomes de interface de grupo de operação foram alterados para estar consistente com os respetivos nomes de propriedade correspondente:

* O tipo de `ISearchServiceClient.Indexes` foi alterado de `IIndexOperations` para `IIndexesOperations`.
* O tipo de `ISearchServiceClient.Indexers` foi alterado de `IIndexerOperations` para `IIndexersOperations`.
* O tipo de `ISearchServiceClient.DataSources` foi alterado de `IDataSourceOperations` para `IDataSourcesOperations`.
* O tipo de `ISearchIndexClient.Documents` foi alterado de `IDocumentOperations` para `IDocumentsOperations`.

Esta alteração é pouco provável afetar o seu código, a menos que criou mocks destas interfaces para fins de teste.

<a name="BugFixesV1"></a>

### <a name="bug-fixes-in-version-11"></a>Correções de erros na versão 1.1
Ocorreu um erro em versões anteriores do SDK .NET da Azure Search relacionado com a serialização de classes de modelo personalizada. O erro pode ocorrer se tiver criado uma classe de modelo personalizado com uma propriedade de um tipo de valor não nulo.

#### <a name="steps-to-reproduce"></a>Passos para reproduzir
Crie uma classe de modelo personalizado com uma propriedade do tipo de valor não nulo. Por exemplo, adicionar um público `UnitCount` propriedade do tipo `int` em vez de `int?`.

Se tiver um documento com o valor predefinido desse tipo de índice (por exemplo, 0 para `int`), o campo irá ser nulo na Azure Search. Se procurar, subsequentemente, esse documento, o `Search` chamada irá gerar `JsonSerializationException` complaining que não é possível converter `null` para `int`.

Além disso, os filtros podem não funcionar conforme esperado, uma vez que um valor nulo foi escrito para o índice em vez do valor pretendido.

#### <a name="fix-details"></a>Corrija os detalhes
Iremos corrigir este problema na versão 1.1 do SDK. Agora, se tiver uma classe de modelo como esta:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e definir `IntValue` como 0, esse valor é agora corretamente serializado como 0 em risco e armazenado como 0 no índice. Arredondar tripping também funciona conforme esperado.

Há um potencial problema a ter em consideração com esta abordagem: Se utilizar um tipo de modelo com uma propriedade não anulável, terá de **garantir** que não existem documentos no seu índice contenham um valor nulo para o campo correspondente. Nem o SDK nem a API REST da Azure Search irão ajudá-lo para impor esta regra.

Esta não é apenas uma preocupação hipotética: imagine um cenário onde adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelo como melhor prática.

Para obter mais detalhes sobre este erro e a correção, consulte [este problema no GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

