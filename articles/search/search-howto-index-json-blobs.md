---
title: "A indexação de blobs JSON com o indexador de blob do Azure Search"
description: "A indexação de blobs JSON com o indexador de blob do Azure Search"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/07/2017
ms.author: eugenesh
ms.openlocfilehash: 2dac2c5980970946a6b9c26ee6ee8ac0f0344144
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>A indexação de blobs JSON com o indexador de blob do Azure Search
Este artigo mostra como configurar um indexador de blob do Azure Search para extrair conteúdo estruturado de blobs JSON do Blob storage do Azure.

JSON os blobs no armazenamento de Blobs do Azure são, normalmente, um único documento JSON ou uma matriz JSON. O indexador de blob na Azure Search consegue analisar a construção, dependendo de como definir o **parsingMode** parâmetro no pedido.

| Documento JSON | parsingMode | Descrição | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por cada blob | `json` | Analisa JSON blobs como um segmento de texto único. Cada blob JSON torna-se um único documento de pesquisa do Azure. | Geralmente disponível em ambos [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) APIs. |
| Vários por blob | `jsonArray` | Analisa uma matriz JSON no blob, onde cada elemento da matriz torna-se um documento de Azure Search separado.  | Pré-visualização, no [REST api-version =`2016-09-01-Preview` ](search-api-2016-09-01-preview.md) e [pré-visualização do SDK .NET](https://aka.ms/search-sdk-preview). |

> [!Note]
> APIs de pré-visualização destinam-se para avaliação e de teste e não devem ser utilizadas em ambientes de produção.
>

## <a name="setting-up-json-indexing"></a>Como configurar a indexação de JSON
A indexação de JSON blobs é semelhante a extração do documento regular num fluxo de trabalho três partes comum para todos os indexadores na pesquisa do Azure.

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

O primeiro passo consiste em fornecer informações de ligação de origem de dados utilizadas pelo indexador. A origem de dados, foi especificado um tipo aqui como `azureblob`, determina quais comportamentos de extração de dados são invocados pelo indexador. Para o blob JSON indexação, a origem de dados é a definição é o mesmo para documentos JSON e matrizes. 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Passo 2: Criar um índice de pesquisa de destino 

Indexadores são emparelhados com um esquema de índice. Se estiver a utilizar a API (em vez do portal), prepare antecipadamente um índice, para que pode especificá-la na operação de indexador. 

> [!Note]
> Indexadores estão expostas no portal através de **importação** ação para um número limitado de indexadores geralmente disponíveis. Muitas vezes, o fluxo de trabalho de importação, muitas vezes, pode criar um índice preliminar com base nos metadados da origem de. Para obter mais informações, consulte [importar dados na Azure Search no portal do](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Passo 3: Configurar e executar o indexador

Até agora, as definições para a origem de dados e índice foram parsingMode desconhecidas. No entanto, no passo 3 para a configuração de indexador, o caminho diverges, dependendo de como pretende que o blob JSON conteúdo até serem examinados e estruturados num índice da Azure Search.

Quando chamar o indexador, efetue o seguinte:

+ Definir o **parsingMode** parâmetro `json` (para indexar cada blob como um único documento) ou `jsonArray` (se os blobs contém matrizes JSON e tem de cada elemento de uma matriz deve ser tratado como um documento separado).

+ Opcionalmente, utilize **campo mapeamentos** para escolher as propriedades que o documento JSON de origem são utilizadas para povoar o índice de pesquisa de destino. Para matrizes JSON, se a matriz de existir como uma propriedade de nível inferior, pode definir uma raiz de documento que indica onde a matriz é colocada num blob.

> [!IMPORTANT]
> Quando utiliza `json` ou `jsonArray` análise modo da Azure Search parte do princípio de que todos os blobs na sua origem de dados contém o JSON. Se precisar de suportar uma mistura de blobs JSON e não JSON na mesma origem de dados, indique no [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Como analisar JSON único blobs

Por predefinição, [indexador de blob do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa JSON blobs como um segmento de texto único. Muitas vezes, pretende manter a estrutura dos seus documentos JSON. Por exemplo, suponha que tem os seguintes documentos JSON no Blob storage do Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Definição de indexador para blobs JSON único

Utilização do indexador de blob do Azure Search, um documento JSON semelhante ao exemplo anterior é analisado para um único documento de pesquisa do Azure. O indexador carrega um índice por correspondente "text", "datePublished" e "etiquetas" da origem contra campos de forma idêntica com nome e tipo de destino.

Configuração é fornecida no corpo de uma operação de indexador. Recuperar-se de que o objeto de origem de dados definido anteriormente, especifica as informações de tipo e a ligação da origem de dados. Além disso, o índice de destino também tem de existir como um contentor vazio no seu serviço. Agenda e os parâmetros são opcionais, mas se omiti-los, o indexador for executado imediatamente, utilizando `json` pois o modo de análise.

Um pedido totalmente especificado poderão ter o seguinte aspeto:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Conforme indicado, os mapeamentos de campo não são necessários. Atribuído um índice com "text", "datePublished e"etiquetas"campos, o blob indexador pode inferir o mapeamento correto sem um campo mapeamento existe no pedido.

## <a name="how-to-parse-json-arrays-preview"></a>Como analisar matrizes JSON (pré-visualização)

Em alternativa, pode escolher para a funcionalidade de pré-visualização de matriz JSON. Esta capacidade é útil quando os blobs contém um *matriz de objetos JSON*, e pretende que cada elemento para se tornar um documento de Azure Search separado. Por exemplo, tendo em conta o seguinte blob JSON, pode povoar o índice da Azure Search com três documentos separados, cada um com os campos "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Definição de indexador para uma matriz JSON

Para uma matriz JSON, o pedido de indexador utiliza a API de pré-visualização e o `jsonArray` parser. Estes são os requisitos específicos de matriz apenas dois para indexação de blobs JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Novamente, tenha em atenção que os mapeamentos de campo não são necessários. Tendo em conta um índice com campos "id" e "text", o indexador de blob pode inferir o mapeamento correto sem uma lista de mapeamento de campos.

### <a name="nested-json-arrays"></a>Matrizes aninhadas de JSON
E se pretende índice uma matriz de objetos JSON, mas essa matriz está aninhado algures no documento? Pode escolher cuja propriedade contém a matriz através de `documentRoot` propriedade de configuração. Por exemplo, se os blobs tem o seguinte aspeto:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Utilize esta configuração para o índice da matriz contida no `level2` propriedade:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Mapeamentos campo a utilizar para criar documentos sobre pesquisa

Quando os campos de origem e de destino não estão perfeitamente alinhados, pode definir uma secção de mapeamento de campo no corpo do pedido para associações de campo a campo explícitas.

Atualmente, da Azure Search não é possível indexar documentos JSON arbitrários diretamente, porque suporta tipos de dados apenas primitivos, as matrizes de cadeia e GeoJSON pontos. No entanto, pode utilizar **campo mapeamentos** para escolher partes do seu documento JSON e "de comparação de precisão"-los em campos de nível superior do documento de pesquisa. Para saber mais sobre noções básicas de mapeamentos de campo, consulte [campo mapeamentos de indexadores de Azure Search](search-indexer-field-mappings.md).

Revisiting nosso documento JSON de exemplo:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Assuma um índice de pesquisa com os seguintes campos: `text` do tipo `Edm.String`, `date` do tipo `Edm.DateTimeOffset`, e `tags` do tipo `Collection(Edm.String)`. Tenha em atenção a discrepância entre "datePublished" na origem e `date` campo no índice. Para mapear o JSON em forma pretendida, utilize os seguintes mapeamentos de campo:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Os nomes de campo de origem nos mapeamentos de são especificados com o [JSON ponteiro](http://tools.ietf.org/html/rfc6901) notação. Pode começa com uma barra referir-se à raiz do seu documento JSON, em seguida, escolha a propriedade pretendida (a arbitrário nível de aninhamento) utilizando o caminho de valores separados por uma barra de reencaminhar.

Também pode consultar os elementos de matriz individuais através da utilização de um índice baseado em zero. Por exemplo, para escolher o primeiro elemento da matriz "etiquetas" do exemplo acima, utilize um mapeamento de campos do seguinte:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se um nome de campo de origem num caminho de mapeamento de campo se refere a uma propriedade que não existe no JSON, esse mapeamento é ignorado sem um erro. Isto é feito para que pode suportamos documentos com um esquema diferente (que é um caso de utilização comum). Porque não há nenhuma validação, tem de ter cuidado para evitar erros de digitação na sua especificação de mapeamento de campo.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Exemplo: De pedido de indexador com mapeamentos campo

O exemplo seguinte é um payload de indexador totalmente especificado, incluindo mapeamentos campo:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


## <a name="help-us-make-azure-search-better"></a>Ajude-na tornar o melhor da Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhoramentos, entrar-no nosso [UserVoice site](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Consultar também

+ [Indexadores na pesquisa do Azure](search-indexer-overview.md)
+ [Indexar o Blob Storage do Azure com a pesquisa do Azure](search-howto-index-json-blobs.md)
+ [A indexação de blobs CSV com o indexador de blob do Azure Search](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar semiestruturados dados a partir do Blob storage do Azure](search-semi-structured-data.md)