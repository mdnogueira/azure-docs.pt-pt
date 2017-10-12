---
title: "Consultar o Índice da Azure Search | Microsoft Docs"
description: "Crie uma consulta de pesquisa na pesquisa do Azure e utilize parâmetros de pesquisa para filtrar e ordenar os resultados da pesquisa."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 69205d7a-363f-4b92-a53f-6ca818a3d2c7
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 04/26/2017
ms.author: ashmaka
ms.openlocfilehash: a22b82829df4659681940267e64c98d345453958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="query-your-azure-search-index"></a>Consultar o índice da Azure Search
> [!div class="op_single_selector"]
> * [Descrição geral](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Ao submeter pedidos de pesquisa para a Azure Search, existem vários parâmetros que podem ser especificados em conjunto com as palavras reais que são escritas na caixa de pesquisa da sua aplicação. Esses parâmetros de consulta permitem obter um maior controlo da [experiência de pesquisa em texto completo](search-lucene-query-architecture.md).

Segue-se uma lista que explica resumidamente as utilizações comuns dos parâmetros de consulta na Azure Search. Para a cobertura total dos parâmetros de consulta e do respetivo comportamento, consulte as páginas detalhadas para a [API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) e o [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary).

## <a name="types-of-queries"></a>Tipos de consultas
A Azure Search oferece muitas opções para criar consultas extremamente poderosas. Os dois principais tipos de consulta que irá utilizar são `search` e `filter`. Uma consulta `search` pesquisa um ou mais termos em todos os campos *pesquisáveis* no seu índice e funciona do modo que seria de esperar para um motor de busca como o Google ou o Bing. Uma consulta `filter` avalia uma expressão booleana através de todos os campos *filtráveis* num índice. Ao contrário das pesquisas `search`, as pesquisas `filter` correspondem aos conteúdos exatos de um campo, o que significa que são sensíveis às maiúsculas e minúsculas para campos de cadeia.

Pode utilizar as pesquisas e os filtros em conjunto ou separadamente. Caso sejam utilizados em conjunto, o filtro é aplicado primeiro ao índice completo e, em seguida, a pesquisa é executada nos resultados do filtro. Por conseguinte, os filtros podem ser uma técnica útil para melhorar o desempenho de consultas, uma vez que reduzem o conjunto de documentos necessários à consulta de pesquisa para processamento.

A sintaxe para expressões de filtro é um conjunto do [idioma do filtro de OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Para consultas de pesquisa pode utilizar a [sintaxe simplificada](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) ou a [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) abordadas a seguir.

### <a name="simple-query-syntax"></a>Sintaxe de consulta simples
A [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) é o idioma de consulta predefinido utilizado na Azure Search. A sintaxe de consulta simples suporta vários operadores de pesquisa comuns, incluindo operadores E, OU, NÃO, frase, sufixo e precedência.

### <a name="lucene-query-syntax"></a>Sintaxe de consulta Lucene
A [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) permite utilizar um idioma de consulta amplamente adotado e expressivo, desenvolvido como parte do [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

A utilização desta sintaxe de consulta permite obter facilmente as seguintes capacidades: [Consultas com âmbito de campo](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields), [pesquisa difusa](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy), [pesquisa de proximidade](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity), [intensificação do termo](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost), [pesquisa de expressão regular](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex), [pesquisa com carateres universais](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard), [noções básicas de sintaxe](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax) e [consultas com operadores booleanos](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean).

## <a name="ordering-results"></a>Ordenar resultados
Ao receber resultados de uma consulta de pesquisa, pode pedir à Azure Search para apresentar os resultados ordenados pelos valores num campo específico. Por predefinição, a Azure Search ordena os resultados da pesquisa com base na classificação de pontuação de pesquisa de cada documento, a qual deriva de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se pretender que a Azure Search devolva os resultados ordenados por um valor diferente da pontuação de pesquisa, pode utilizar o parâmetro de pesquisa `orderby`. Pode especificar o valor do parâmetro `orderby` para incluir os nomes de campo e as chamadas para a [`geo.distance()` função](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) para valores geoespaciais. Cada expressão pode ser seguida por `asc` para indicar que os resultados foram pedidos por ordem ascendente e por `desc` para indicar que os resultados foram pedidos por ordem descendente. A ordem ascendente de classificação predefinida.

## <a name="paging"></a>Paginação
A Azure Search facilita a implementação da paginação dos resultados da pesquisa. Através dos parâmetros `top` e `skip`, pode processar facilmente pedidos de pesquisa que permitem receber o conjunto total de resultados da pesquisa em subconjuntos geríveis e ordenados que permitem facilmente boas práticas da IU de pesquisa. Ao receber esses subconjuntos de resultados mais pequenos, pode também receber a contagem de documentos no conjunto total de resultados da pesquisa.

Pode saber mais sobre a paginação dos resultados da pesquisa no artigo [Como paginar os resultados da pesquisa na Azure Search](search-pagination-page-layout.md).

## <a name="hit-highlighting"></a>Detetor de ocorrências
Na Azure Search, o realce da parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é facilitado através dos parâmetros `highlight`, `highlightPreTag` e `highlightPostTag`. Pode especificar os campos *pesquisáveis* que devem ter o respetivo texto com correspondência realçado, bem como especificar as etiquetas de cadeias exatas a adicionar no início e no fim do texto com correspondência devolvido pela Azure Search.

## <a name="try-out-query-syntax"></a>Experimente a sintaxe de consulta

É a melhor maneira de entender as diferenças de sintaxe pelo envio de consultas e revisão dos resultados.

+ Utilize o [Search Explorer](search-explorer.md) no portal do Azure. Implementando [o índice de exemplo](search-get-started-portal.md), pode consultar o índice em minutos, usando as ferramentas no portal.

+ Use [Fiddler](search-fiddler.md) ou Chrome Postman para enviar consultas para um índice que enviou para o serviço de pesquisa. Ambas as ferramentas oferecem suporte a chamadas REST para um ponto final HTTP. 