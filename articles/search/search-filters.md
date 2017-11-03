---
title: Filtros na Azure Search | Microsoft Docs
description: "Filtrar por identidade de segurança do utilizador, idioma, geolocalização ou valores numéricos para reduzir os resultados da pesquisa em consultas na Azure Search, um serviço de pesquisa em nuvem alojado no Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/19/2017
ms.author: heidist
ms.openlocfilehash: 2e8721684b1d4ed0e7392d85ea1df0f595860a05
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="filters-in-azure-search"></a>Filtros no Azure Search 

A *filtro* fornece critérios de seleção de documentos utilizados numa consulta de pesquisa do Azure. Procura e não filtrada inclui todos os documentos no índice. Um filtro de âmbitos de uma consulta de pesquisa a um subconjunto de documentos. Por exemplo, um filtro pode restringir a pesquisa em texto completo para apenas esses produtos ter uma marca específica ou a cor, em pontos de preços acima de um determinado limiar.

Alguns experiências de pesquisa impõe requisitos de filtro como parte da implementação, mas pode utilizar filtros sempre que pretende restringir a utilização de pesquisa *baseado no valor* critérios (definir o âmbito de pesquisa para o tipo de produto "books" para a categoria" não-fiction"publicado por"Simon e Schuster").

Se em vez disso, o seu objetivo é visada pesquisa sobre dados específicos do *estruturas* (definir o âmbito de pesquisa a um campo de revisões de cliente), existem métodos alternativos, descritos abaixo.

## <a name="when-to-use-a-filter"></a>Quando utilizar um filtro

Os filtros são fundamentais sobre para várias experiências de pesquisa, incluindo "Localizar quase-me", segurança e navegação por facetas filtra que mostram apenas esses documentos de um utilizador está autorizado a ver. Se implementar qualquer um destes experiências, um filtro é necessário. É o filtro anexado à consulta de pesquisa que fornece as coordenadas de geolocalização, a categoria de faceta selecionada pelo utilizador ou o ID de segurança do requerente.

Cenários de exemplo incluem o seguinte:

1. Utilize um filtro para o índice com base nos valores de dados no índice do setor. Tendo em conta um esquema com cidade, tipo housing e amenities, poderá criar um filtro para selecionar explicitamente documentos que satisfaçam os critérios (em Seattle, condos, marítima de são Francisco). 

  Pesquisa em texto completo com as mesmas entradas frequentemente produz resultados semelhantes, mas um filtro é mais preciso requer uma correspondência exata do termo de filtro de conteúdos no seu índice. 

2. Utilize um filtro se a experiência de pesquisa é fornecido com um requisito de filtro:

 * [Navegação por facetas](search-faceted-navigation.md) utiliza um filtro para passar volta a categoria de faceta selecionada pelo utilizador.
 * Georreplicação-search utiliza um filtro para transmitir as coordenadas da localização atual no "Localizar quase-me" aplicações. 
 * Filtros de segurança de passar identificadores de segurança como critérios de filtro, onde uma correspondência no índice funciona como um proxy para direitos de acesso ao documento.

3. Utilize um filtro se pretender que os critérios de pesquisa num campo numérico. 

  Os campos numéricos são recuperável no documento e podem aparecer nos resultados da pesquisa, mas não são pesquisáveis (sujeitos a pesquisa em texto completo) individualmente. Se precisar de critérios de seleção de com base em dados numéricos, utilize um filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Métodos alternativos para reduzir o âmbito

Se pretender um efeito para diminuir nos resultados da pesquisa, filtros não são à sua escolha apenas. Estas alternativas pode ser uma opção melhor, consoante o objetivo:

 + `searchFields`o parâmetro de consulta pegs pesquisa a campos específicos. Por exemplo, se o seu índice fornece os campos separados para obter descrições em inglês e espanhol, pode utilizar searchFields para os campos a utilizar para pesquisa em texto completo de destino. 

+ `$select`parâmetro é utilizado para especificar os campos para incluir num resultado de definir, trimming efetivamente a resposta antes de a enviar para a aplicação de chamada. Este parâmetro não refine a consulta ou reduza a coleção de documentos, mas se uma resposta granular sobre o seu objetivo, este parâmetro é uma opção a ter em consideração. 

Para obter mais informações sobre o parâmetro, consulte [documentos sobre pesquisa > pedido > parâmetros de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filtros no pipeline de consulta

No momento da consulta, um analisador de filtro aceita critérios como entrada, converte a expressão atómicas expressões e baseia-se de uma árvore de filtros, em seguida, é avaliada através de campos filtráveis num índice.  

Filtragem ocorre antes de pesquisa, elegíveis os documentos para incluir no processamento a jusante para obtenção de documento e relevância classificação. Quando emparelhado com uma cadeia de procura, o filtro de forma eficaz reduz a área de superfície da operação de pesquisa subsequentes. Quando utilizado individualmente (por exemplo, quando a cadeia de consulta está vazio onde `search=*`), os critérios de filtro é a entrada única. 

## <a name="filter-definition"></a>Definição do filtro

Os filtros são expressões de OData, articulated utilizando um [subconjunto da sintaxe de OData V4 suportado na Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Pode especificar um filtro para cada **pesquisa** operação, mas o filtro de si próprio pode incluir vários campos, vários critérios, e se utilizar um **ismatch** função, várias expressões. Uma expressão de filtro com várias partes, pode especificar predicados por qualquer ordem. Não há nenhum significativa na ganhos no desempenho se tentar reorganizar predicados numa sequência específico.

O limite do disco rígido uma expressão de filtro é o limite máximo no pedido. O pedido completo, que inclui o filtro, pode ter um máximo de 16 MB para o POST ou 8 KB para o GET. Os limites parciais está correlacionado com o número de cláusulas na expressão de filtro. Uma boa regra geral é que se tiver centenas de cláusulas, está em risco de execução para o limite. É recomendável conceber a sua aplicação de forma a que não gere os filtros de tamanho unbounded.

Os exemplos seguintes representam as definições de filtro prototypical em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

```

## <a name="filter-design-patterns"></a>Padrões de conceção de filtro

Os exemplos seguintes mostram vários padrões de conceção para cenários de filtro. Para obter mais ideias, consulte [sintaxe de expressão de OData > exemplos](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

+ Autónomo **$filter**, sem uma cadeia de consulta, útil quando a expressão de filtro é capaz de qualificar completamente a documentos de interesse. Sem uma cadeia de consulta, não há nenhum lexical ou linguístico análise, sem classificação e sem classificação. Tenha em atenção de que a cadeia de procura está vazia.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combinação de cadeia de consulta e **$filter**, onde o filtro cria o subconjunto, e a cadeia de consulta fornece as entradas do termo de pesquisa em texto completo sobre o subconjunto filtrado. A utilização de um filtro com uma cadeia de consulta é o padrão do código mais comuns.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Composta consultas, separadas por "ou", cada um com os seus próprios critérios de filtro (por exemplo, ' beagles' no 'preguiçoso') ou 'siamese' no 'cat'. OU tinha as expressões são avaliadas individualmente, com as respostas a partir de cada um combinados uma resposta enviada para a aplicação de chamada. Neste padrão de conceção é conseguido através da função de search.ismatch. Pode utilizar a versão não classificação (search.ismatch) ou a versão de classificação (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Siga cópias de segurança com estes artigos para obter orientações sobre abrangente em casos de utilização específicos:

+ [Filtros de facetas](search-filters-facets.md)
+ [Filtros de idioma](search-filters-language.md)
+ [limitação de segurança](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos de campo para filtrar

A API REST, filtrável é *no* por predefinição. Filtráveis campos aumentam o tamanho do índice; Certifique-se definir `filterable=FALSE` para os campos que não pretende utilizar, na verdade, num filtro. Para obter mais informações sobre as definições para definições de campo, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

O SDK .NET, o filtrável é *desativar* por predefinição. A API para definir a propriedade filtrável [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). No exemplo abaixo, o conjunto na definição de campo BaseRate.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Requisitos de reindexing

Se um campo é não filtrável e que pretende tornar filtrável, terá de adicionar um novo campo ou reconstrua o campo existente. Alterar uma definição de campo altera a estrutura física do índice. Na Azure Search, todos os caminhos de acesso permitido são indexados para a velocidade de consulta rápida, que exige uma reconstrução do mesmo as estruturas de dados quando forem efetuadas alterações de definições de campo. 

Reconstruir campos individuais, pode ser uma operação de impacto baixa, que requerem apenas uma operação de intercalação que envia a chave de documento existente e valores associados para o índice, se deixar o resto cada documento. Se tiver um requisito de reconstrução, consulte as seguintes ligações para instruções:

 + [Ações de indexação utilizando o SDK .NET](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Ações de indexação utilizando a API REST](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Noções básicas de filtro de texto

Filtros de texto são válidos para campos de cadeia, a partir do qual pretende solicitar algumas arbitrária coleção de documentos com base nos valores dentro corpus de pesquisa.

Para os filtros de texto compostos por cadeias, não é não lexical analysis ou quebra de palavra, pelo que são comparações de correspondências exatas apenas. Por exemplo, suponha um campo *f* contém "sunny dia", `$filter=f eq 'Sunny'`não corresponderem, mas `$filter=f eq 'Sunny day'` será. 

Cadeias de texto são maiúsculas e minúsculas. Não há nenhum inferior-tem maiúsculas e minúsculas das palavras cased em maiúsculas: `$filter=f eq 'Sunny day'` não irá encontrar ' dia sunny '.


| Abordagem | Descrição | 
|----------|-------------|
| [Search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma função de fornecer lista delimitada por vírgulas de cadeias para um determinado campo. As cadeias compõem os critérios de filtro, o que são aplicados a todos os campos no âmbito de consulta. <br/><br/>`search.in(f, ‘a, b, c’)`ponto de vista semântico equivalente ao `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, exceto que executa muito mais rapidamente quando a lista de valores é grande.<br/><br/>Recomendamos o **search.in** funcionar para [filtros de segurança](search-security-trimming-for-azure-search.md) e para quaisquer filtros é composto por texto não processado a correspondência nos valores num campo especificado. Esta abordagem foi concebida para velocidade. Pode contar com tempo de resposta subsecond centenas e os milhares de valores. Enquanto não existe nenhum limite explícito no número de itens que pode passar para a função, os aumentos de latência in proportion to o número de cadeias que fornecer. | 
| [Search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma função que permite-lhe misturar operações de pesquisa em texto completo com operações de filtro estritamente booleano na mesma expressão de filtro. Permite que várias combinações de filtro de consulta num pedido. Também pode utilizá-la para um *contém* filtro para filtrar numa cadeia parcial dentro de uma cadeia de maior. |  
| [$filter = cadeia de operador do campo](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma expressão definido pelo utilizador é composto por campos, operadores e valores. | 

## <a name="numeric-filter-fundamentals"></a>Noções básicas de filtro numérico

Os campos numéricos não são `searchable` no contexto de pesquisa em texto completo. Apenas cadeias estão sujeitos a pesquisa em texto completo. Por exemplo, se introduzir 99,99 como um termo de pesquisa, não será regressar itens um preço em $99,99. Em vez disso, o que poderá ver itens de que tem o número de 99 campos de cadeia do documento. Assim, se tiver dados numéricos, pressuposto é que irá utilizá-los para filtros, incluindo intervalos, facetas, grupos e assim sucessivamente. 

Documentos que contenham os campos numéricos (preço, tamanho, SKU, ID) fornecem esses valores nos resultados da pesquisa, se o campo está marcado como `retrievable`. O ponto de aqui é que a pesquisa em texto completo em si não é aplicável a tipos de campo numérico.

## <a name="next-steps"></a>Passos seguintes

Em primeiro lugar, tente **Explorador de pesquisa** no portal para submeter consultas com **$filter** parâmetros. O [real-propriedade-sample índice](search-get-started-portal.md) fornece resultados interessantes para o seguinte filtrado consultas quando cole-os para a barra de pesquisa:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Para trabalhar com mais exemplos, consulte [sintaxe de expressão de filtro de OData > exemplos](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

## <a name="see-also"></a>Consultar também

+ [Como completa a pesquisa em texto funciona na Azure Search](search-lucene-query-architecture.md)
+ [API de REST de documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
