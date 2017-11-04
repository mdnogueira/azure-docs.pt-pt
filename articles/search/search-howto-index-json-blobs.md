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
ms.openlocfilehash: bf4d3a517e1308a142d21cffff64f3c6e104eb62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>A indexação de blobs JSON com o indexador de blob do Azure Search
Este artigo mostra como configurar o indexador de blob do Azure Search para extrair conteúdo estruturado de blobs que contêm JSON.

## <a name="scenarios"></a>Cenários
Por predefinição, [indexador de blob do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa JSON blobs como um segmento de texto único. Muitas vezes, pretende manter a estrutura dos seus documentos JSON. Por exemplo, dado o documento JSON

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Pode querer analisá-lo para um documento de pesquisa do Azure com campos de "etiquetas", "datePublished" e "text".

Em alternativa, quando os blobs contêm um **matriz de objetos JSON**, poderá pretender que a cada elemento da matriz para se tornar um documento de Azure Search separado. Por exemplo, fornecido um blob com este JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Pode povoar o índice da Azure Search com três documentos separados, cada um com os campos "id" e "text".

> [!IMPORTANT]
> A matriz JSON análise funcionalidade está atualmente em pré-visualização. Está disponível apenas na API REST utilizando a versão **2016-09-01-Preview**. Lembre-se, pré-visualizar APIs foram concebidas para avaliação e de teste e não deve ser utilizadas em ambientes de produção.
>
>

## <a name="setting-up-json-indexing"></a>Como configurar a indexação de JSON
A indexação de JSON blobs é semelhante a extração do documento regular. Primeiro, crie a origem de dados, tal como faria normalmente: 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Em seguida, crie o índice de pesquisa de destino se ainda não tiver um. 

Por fim, crie um indexador e defina o `parsingMode` parâmetro `json` (para indexar cada blob como um único documento) ou `jsonArray` (se os blobs contém matrizes JSON e tem de cada elemento de uma matriz deve ser tratado como um documento separado):

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

Se necessário, utilize **campo mapeamentos** para escolher as propriedades do documento JSON origem utilizado para povoar o índice de pesquisa de destino, conforme mostrado na secção seguinte.

> [!IMPORTANT]
> Quando utiliza `json` ou `jsonArray` análise modo da Azure Search parte do princípio de que todos os blobs na sua origem de dados contém o JSON. Se precisar de suportar uma mistura de blobs JSON e não JSON na mesma origem de dados, indique no [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>
>

## <a name="using-field-mappings-to-build-search-documents"></a>Mapeamentos campo a utilizar para criar documentos sobre pesquisa
Atualmente, da Azure Search não é possível indexar documentos JSON arbitrários diretamente, porque suporta tipos de dados apenas primitivos, as matrizes de cadeia e GeoJSON pontos. No entanto, pode utilizar **campo mapeamentos** para escolher partes do seu documento JSON e "de comparação de precisão"-los em campos de nível superior do documento de pesquisa. Para saber mais sobre noções básicas de mapeamentos de campo, consulte [mapeamentos de campo de indexador de Azure Search ponte as diferenças entre as origens de dados e os índices de pesquisa](search-indexer-field-mappings.md).

Regressam ao nosso documento JSON de exemplo:

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Imaginemos que tem um índice de pesquisa com os seguintes campos: `text` do tipo `Edm.String`, `date` do tipo `Edm.DateTimeOffset`, e `tags` do tipo `Collection(Edm.String)`. Para mapear o JSON em forma pretendida, utilize os seguintes mapeamentos de campo:

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

Se os documentos JSON só contenham propriedades simples de nível superior, pode não necessitar de mapeamentos de campo de todo. Por exemplo, se o seu JSON tem o seguinte aspeto, as propriedades de nível superior "text", "datePublished" e "etiquetas" diretamente mapeia para os campos correspondentes no índice de pesquisa:

    {
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13"
       "tags" : [ "search", "storage", "howto" ]    
     }

Eis um payload completo de indexador com mapeamentos campo:

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

## <a name="indexing-nested-json-arrays"></a>A indexação de matrizes aninhadas de JSON
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

## <a name="help-us-make-azure-search-better"></a>Ajude-na tornar o melhor da Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhoramentos, entrar-no nosso [UserVoice site](https://feedback.azure.com/forums/263029-azure-search/).
