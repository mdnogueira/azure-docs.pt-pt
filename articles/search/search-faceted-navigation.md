---
title: "Como implementar navegação por facetas na Azure Search | Microsoft Docs"
description: "Adicione navegação por facetas para aplicações que se integram com o Azure Search, um serviço de pesquisa em nuvem alojado no Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: mblythe
editor: 
ms.assetid: cdf98fd4-63fd-4b50-b0b0-835cb08ad4d3
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 3/10/2017
ms.author: heidist
ms.openlocfilehash: 413f498eeb0bbc9a971c7a65200ed2fd8caa9aaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Como implementar a navegação por facetas no Azure Search
Navegação por facetas é um mecanismo de filtragem que fornece pesquisa auto-direcionada navegação nas aplicações de pesquisa. O termo 'navegação por facetas' poderá estar familiarizado, mas provavelmente, utilizou-lo antes de. Tal como o exemplo seguinte mostra, navegação por facetas é nothing mais do que as categorias utilizadas para filtrar os resultados.

 ![Demonstração de Portal de tarefa de pesquisa do Azure][1]

Navegação por facetas é um ponto de entrada alternativo para procurar. Oferece uma alternativa conveniente para escrever expressões complexas pesquisa manualmente. Facetas podem ajudar a encontrar aquilo que procura, garantindo que não obter resultados de zero. Como um programador, facetas permitem-lhe expor os critérios de pesquisa mais úteis para navegar corpus a pesquisa. Em aplicações de revenda online, navegação por facetas, muitas vezes, é criada sobre marcas, departamentos (lugar do kid), tamanho, preço, popularidade e as classificações. 

Implementar a navegação por facetas difere entre as tecnologias de pesquisa. Azure Search, navegação por facetas é criada no momento da consulta, com campos tem no seu esquema anteriormente.

-   Em consultas que cria a aplicação, terá de enviar uma consulta *parâmetros de consulta do aspeto* para obter o aspeto disponível valores de filtro para esse conjunto de resultados do documento.

-   Cortar, na verdade, o resultado do documento definido, a aplicação também tem de aplicar um `$filter` expressão.

No seu desenvolvimento de aplicações, escrever código que constrói consultas constitui o volume do trabalho. Muitos dos comportamentos de aplicação que seria de esperar da navegação por facetas são fornecidos pelo serviço, incluindo suporte incorporado para intervalos de definir e obter contagens de resultados de aspeto de restrições. O serviço também inclui as predefinições sensible que o ajudam a evitar estruturas de navegação unwieldy. 

## <a name="sample-code-and-demo"></a>Código de exemplo e demonstração
Este artigo utiliza um portal de pesquisa de tarefa como exemplo. O exemplo é implementado como uma aplicação ASP.NET MVC.

-   Ver e testar a demonstração de trabalhar online em [demonstração de Portal do Azure Search tarefa](http://azjobsdemo.azurewebsites.net/).

-   Transferir o código a partir de [repositório Azure-Samples no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Introdução
Se estiver familiarizado com o desenvolvimento de pesquisa, a melhor forma de pensar de navegação por facetas é que mostra as possibilidades para pesquisa auto-direcionada. É um tipo de experiência de pesquisa de desagregação, com base em filtros predefinidos, utilizados para a restrição rapidamente para baixo os resultados da pesquisa através de ações de ponto e clique. 

### <a name="interaction-model"></a>Modelo de interação

A experiência de pesquisa para navegação por facetas é iterativa, vamos começar por compreendê-la como uma sequência de consultas que unfold em resposta a ações do utilizador.

O ponto de partida é uma página de aplicação que fornece navegação por facetas, normalmente colocada a periphery. Navegação por facetas é frequentemente uma estrutura de árvore, com as caixas de verificação para cada valor ou texto clicável. 

1. Uma consulta enviada para a Azure Search Especifica a estrutura de navegação por facetas através de um ou mais parâmetros de consulta de aspeto de restrições. Por exemplo, a consulta poderá incluir `facet=Rating`, talvez com um `:values` ou `:sort` opção para aperfeiçoar ainda mais a apresentação.
2. A camada de apresentação compõe uma página de pesquisa que fornece navegação por facetas, utilizando as facetas especificadas no pedido.
3. Tendo em conta uma estrutura de navegação por facetas que inclui a classificação, clicar em "4" para indicar que devem ser apresentados apenas os produtos com uma classificação de 4 ou superior. 
4. Em resposta, a aplicação envia uma consulta que inclua`$filter=Rating ge 4` 
5. A camada de apresentação atualiza a página, que mostra um conjunto de resultados reduzida, que contêm apenas esses itens que satisfaçam os critérios novo (neste caso, os produtos classificados 4 e até).

Um aspeto é um parâmetro de consulta, mas não a confunda com entrada de consulta. Nunca é utilizada como critério de seleção de uma consulta. Em vez disso, considere os parâmetros de consulta de faceta como entradas para a estrutura de navegação volta na resposta. Para cada parâmetro de consulta do aspeto que fornece, da Azure Search avalia documentos quantos são nos resultados da parciais para cada valor de aspeto de restrições.

Tenha em atenção o `$filter` no passo 4. O filtro é um aspeto importante de navegação por facetas. Embora facetas e os filtros são independentes na API, terá de a fornecer a experiência de que pretende. 

### <a name="app-design-pattern"></a>Padrão de conceção de aplicação

No código da aplicação, o padrão é utilizar os parâmetros de consulta de faceta para devolver a estrutura de navegação por facetas juntamente com os resultados do aspeto, além de uma expressão de $filter.  A expressão de filtro processa o evento de clique com o valor de aspeto de restrições. Considere o `$filter` expressão como code-behind o corte real dos resultados da pesquisa devolveu para a camada de apresentação. Tendo em conta um aspeto de cores, clicando a cor vermelho é implementado através um `$filter` expressão que seleciona apenas esses itens que tenham uma cor de vermelho. 

### <a name="query-basics"></a>Noções básicas de consulta

Na Azure Search, um pedido é especificado através de um ou mais parâmetros de consulta (consulte [documentos sobre pesquisa](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter uma descrição de cada um deles). Nenhum dos parâmetros de consulta são necessárias, mas tem de ter, pelo menos, um para que uma consulta ser válida.

Entendida precisão, como a capacidade de filtrar pedidos irrelevantes, é conseguido através de uma ou ambas estas expressões:

-   **pesquisa =**  
    O valor deste parâmetro constitui a expressão de pesquisa. Poderá ser um único fragmento de texto, ou uma expressão de pesquisa complexas que inclui vários operadores e termos de licenciamento. No servidor, é utilizada uma expressão de pesquisa para pesquisa em texto completo, consultar campos pesquisáveis no índice para a correspondência termos, devolver resultados por ordem de classificação. Se definir `search` como nulo, consulta execução é efetuada através de no índice completo (ou seja, `search=*`). Neste caso, outros elementos de consulta, tal como um `$filter` ou a classificação de perfil, são os principais fatores que afetam o os documentos são devolvidos `($filter`) e a ordem pela qual (`scoringProfile` ou `$orderby`).

-   **$filter =**  
    Um filtro é um mecanismo poderoso para limitar o tamanho dos resultados da pesquisa com base nos valores de atributos de documento específico. A `$filter` é avaliado pela primeira vez, seguido pela lógica facetamento que gera o valores disponíveis e contagens correspondentes para cada valor

As expressões de pesquisa complexas diminuir o desempenho da consulta. Sempre que possível, utilize expressões de filtro bem construído para aumentar a precisão e melhorar o desempenho de consulta.

Para compreender melhor como um filtro adiciona mais precisão, compare uma expressão de pesquisa complexas para um que inclui uma expressão de filtro:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Ambas as consultas são válidas, mas a segunda é superior se que procura não motels parking em Seattle.
-   A primeira consulta depende desses palavras específicas a serem mencionado ou não foi mencionado em campos de cadeia, como o nome, descrição e qualquer outro campo que contém dados pesquisáveis.
-   A consulta segundo procura precisas correspondências em dados estruturados e é provável que seja muito mais exata.

Em aplicações que incluem a navegação por facetas, certifique-se de que cada ação do utilizador através de uma estrutura de navegação por facetas acompanhada por uma restrição de resultados da pesquisa. Para limitar os resultados, utilize uma expressão de filtro.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Criar uma aplicação de navegação por facetas
Implementar navegação por facetas Azure Search no código da aplicação que cria o pedido de pesquisa. A navegação por facetas baseia-se nos elementos no seu esquema que definidas anteriormente.

Predefinidas na sua pesquisa índice é o `Facetable [true|false]` atributos de índice, definir os campos selecionados para ativar ou desativar a utilização de uma estrutura de navegação por facetas. Sem `"Facetable" = true`, um campo não pode ser utilizado na navegação de faceta.

A camada de apresentação no seu código fornece a experiência de utilizador. Este deve listar as partes constituintes do painel de navegação por facetas, tais como a etiqueta, valores, as caixas de verificação e a contagem. A API REST da Azure Search desconhece plataforma, por isso, utilize qualquer idioma e a plataforma que pretende. O mais importante é a incluir elementos de IU que suportam atualização incremental, com o estado de IU atualizado selecionados cada aspeto adicional. 

No momento da consulta, o código da aplicação cria um pedido que inclui `facet=[string]`, um parâmetro de pedido que fornece o campo aspeto pelo. Uma consulta pode ter vários aspetos, tais como `&facet=color&facet=category&facet=rating`, cada um deles separados por um caráter ' e ' comercial (&).

Código da aplicação também deve construir um `$filter` expressão para processar os eventos de clique na navegação por facetas. A `$filter` reduz os resultados da pesquisa, o valor de aspeto a utilizar como critérios de filtro.

A pesquisa do Azure devolve os resultados da pesquisa, com base num ou mais termos que introduziu, juntamente com as atualizações para a estrutura de navegação por facetas. Na Azure Search, navegação por facetas é uma construção de nível de único, com valores de aspeto, contagens e dos resultados quantos encontram-se para cada um deles.

Nas secções seguintes, vamos assumir um olhar como criar cada parte.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Criar o índice
Facetamento está ativado numa base de campo ao campo no índice, através deste atributo de índice: `"Facetable": true`.  
Todos os tipos de campo, possivelmente, poderia ser utilizados na navegação por facetas são `Facetable` por predefinição. Os tipos de campo incluem `Edm.String`, `Edm.DateTimeOffset`e todos os tipos de campo numérico (essencialmente, todos os tipos de campo são facetável exceto `Edm.GeographyPoint`, que não pode ser utilizado na navegação por facetas). 

Ao criar um índice, uma melhor prática para navegação por facetas é explicitamente desativar facetamento para campos de que nunca devem ser utilizados como um aspeto de restrições.  Em particular, os campos de cadeia para os valores de singleton, como um nome de produto ou ID devem ser definidos como `"Facetable": false` para impedir a respetiva utilização acidental (e ineficaz) navegação por facetas. Facetamento desativar onde o ficheiro não será necessário ajuda a manter o tamanho do índice pequeno e normalmente melhora o desempenho.

Segue-se parte do esquema para a aplicação de exemplo de demonstração do Portal de tarefa, cortado de alguns atributos para reduzir o tamanho:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Como pode ver o esquema de exemplo, `Facetable` está desativada para campos de cadeia não devem ser utilizados como facetas, tais como valores de ID. Facetamento desativar onde o ficheiro não será necessário ajuda a manter o tamanho do índice pequeno e normalmente melhora o desempenho.

> [!TIP]
> Como melhor prática, inclua o conjunto completo de atributos de índice para cada campo. Embora `Facetable` está ativada por predefinição para quase todos os campos, propositadamente definição cada atributo pode ajudar a analisar as implicações de cada decisão de esquema. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Verifique os dados
A qualidade dos seus dados tem um efeito direto na se a estrutura de navegação por facetas materializes como esperada. Também afeta o facilitar a construção de filtros para reduzir o conjunto de resultados.

Se pretender aspeto pela marca ou preço, cada documento deve conter valores para *BrandName* e *ProductPrice* que sejam produtivos como uma opção de filtro, válido e consistente.

Seguem-se alguns lembretes que limpar para:

* Para todos os campos que pretende aspeto pelo, faça a mesmo se contém valores que são adequados como filtros na pesquisa auto-direcionada. Os valores devem ser suficientemente distinctive para oferecer uma opção clara entre opções concorrentes, curto e descritivo.
* Misspellings ou valores quase correspondentes. Se aspeto cor e valores de campo incluem laranja e Ornage (um misspelling), um aspeto de restrições baseado num campo de cor seria processará ambos.
* Texto maiúsculas misto também pode wreak estragos na navegação por facetas, com cor de laranja e volte a aparecer como dois valores diferentes de cor de laranja. 
* Versões único e plural do mesmo valor podem resultar num aspeto de restrições separado para cada.

Como pode imagine, diligence na preparação de dados é um aspeto da navegação por facetas eficaz essencial.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Criar a IU
Trabalho volta a partir da camada de apresentação pode ajudar a descobrir os requisitos que podem ser omitidos caso contrário e compreenderem que capacidades são essenciais para a experiência de pesquisa.

Em termos de navegação por facetas, a sua página web ou aplicação apresenta a estrutura de navegação por facetas, Deteta intervenção do utilizador na página e insere os elementos foi alterados. 

Para aplicações web, AJAX geralmente é utilizado na camada de apresentação porque permite-lhe atualizar as alterações incrementais. Também pode utilizar o ASP.NET MVC ou em qualquer plataforma de visualização que possam ligar a um serviço da Azure Search através de HTTP. A aplicação de exemplo referida durante neste artigo – o **demonstração de Portal do Azure Search tarefa** – acontece ser uma aplicação ASP.NET MVC.

No exemplo, navegação por facetas baseia-se à página de resultados de pesquisa. O exemplo seguinte, obtido a partir de `index.cshtml` ficheiro da aplicação de exemplo, mostra o HTML estático estrutura para apresentar navegação por facetas na pesquisa resulta página. A lista de aspetos é criada ou reconstruída dinamicamente quando submeter um termo de pesquisa, ou selecione ou desmarque um aspeto de restrições.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

O seguinte fragmento de código do `index.cshtml` página cria de forma dinâmica o HTML para apresentar o primeiro aspeto, a título de negócio. Funções semelhantes criar dinamicamente o HTML para as outras facetas. Cada aspeto tem uma etiqueta e uma contagem, que apresenta o número de itens, encontrados para que resultam de aspeto de restrições.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Ao conceber a página de resultados de pesquisa, lembre-se de adicionar um mecanismo para limpar facetas. Se adicionar as caixas de verificação, pode facilmente verá como limpar os filtros. Para outras esquemas, poderá ser necessário um padrão de trilho ou outra abordagem criativos. Por exemplo, a aplicação de exemplo do Portal de pesquisa de tarefa, pode clicar no `[X]` depois de um aspeto selecionado para limpar o aspeto de restrições.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Construir a consulta
O código de escrita para a criação de consultas deve especificar todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, a classificação de perfis – tudo utilizado para formular um pedido. Nesta secção, vamos explorar onde facetas ajustam uma consulta e, como os filtros são utilizados com facetas para fornecer um conjunto de resultados reduzida.

Tenha em atenção que as facetas são integrais nesta aplicação de exemplo. A experiência de pesquisa na demonstração de Portal de tarefa é foi concebida para navegação por facetas e os filtros. A colocação da navegação por facetas na página prominent demonstra a importância. 

Um exemplo é frequentemente um bom local para começar. O exemplo seguinte, obtido a partir de `JobsSearch.cs` ficheiro, baseiam-se um pedido que cria a navegação de faceta com base no título de negócio, localização, tipo de colocação e salário mínimo. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Um parâmetro de consulta de aspeto de restrições está definido como um campo e consoante o tipo de dados, pode ser mais parametrizada por lista delimitada por vírgulas que inclui `count:<integer>`, `sort:<>`, `interval:<integer>`, e `values:<list>`. Uma lista de valores é suportada para dados numéricos quando configurar intervalos. Consulte [documentos sobre pesquisa (API da Azure Search)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para detalhes de utilização.

Juntamente com as facetas, o pedido formulated pela sua aplicação, deve também criar filtros para restringir o conjunto de documentos de candidatos com base numa seleção de valor de aspeto de restrições. Para um arquivo de bicicleta navegação por facetas fornece processáveis a perguntas como *que cores, os fabricantes e tipos de bikes estão disponíveis?*. Filtragem de respostas a perguntas como *que bikes exatos são vermelhos, bikes das regiões montanhosas, deste estime preços intervalo?*. Quando clicar em "Red" para indicar que devem ser apresentados apenas os produtos vermelhos, a consulta seguinte, a aplicação envia inclui `$filter=Color eq ‘Red’`.

O seguinte fragmento de código do `JobsSearch.cs` página adiciona o título de empresas selecionados para o filtro se Selecionou um valor de aspeto de título de negócio.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Dicas e melhores práticas

### <a name="indexing-tips"></a>Sugestões de indexação
**Melhorar a eficiência do índice, se não utilizar uma caixa de pesquisa**

Se a sua aplicação utiliza navegação por facetas exclusivamente (ou seja, nenhuma caixa de pesquisa), pode marcar o campo como `searchable=false`, `facetable=true` para produzir um índice mais compactação. Além disso, a indexação ocorre apenas nos valores de aspeto de todo, com sem quebra de palavra ou a indexação das partes de componente de um valor de palavra multi.

**Especifique os campos podem ser utilizados como facetas**

Recuperar-se de que o esquema do índice determina os campos estão disponíveis para utilizar como um aspeto de restrições. Partindo do princípio de que um campo é facetável, a consulta Especifica qual campos para aspeto pelo. O campo através do qual está facetamento fornece os valores que são apresentados por baixo da etiqueta. 

Os valores que são apresentados em cada etiqueta são obtidos a partir do índice. Por exemplo, se o campo de aspeto de restrições está *cor*, os valores disponíveis para filtragem adicionais são os valores para esse campo - vermelho, negra e assim sucessivamente.

Para numéricos e DateTime apenas valores, explicitamente pode definir valores num campo de faceta (por exemplo, `facet=Rating,values:1|2|3|4|5`). É permitida uma lista de valores para estes tipos de campo simplificar a separação de resultados de aspeto de restrições em intervalos contíguos (qualquer um dos intervalos com base nos valores numéricos ou períodos de tempo). 

**Por predefinição só pode ter um nível de navegação por facetas** 

Conforme indicado, não há nenhum suporte direto para aninhamento facetas numa hierarquia. Por predefinição, a navegação por facetas na Azure Search só suporta um nível de filtros. No entanto, existem soluções. Pode codificar uma estrutura hierárquica aspeto num `Collection(Edm.String)` com uma entrada de ponto por hierarquia. Implementar esta solução está fora do âmbito deste artigo. 

### <a name="querying-tips"></a>Sugestões de consulta
**Validar os campos**

Se criar a lista de aspetos dinamicamente com base na entrada de utilizador não fidedigno, confirme que os nomes dos campos existentes por facetas são válidos. Em alternativa, os nomes de escape, quando criar URLs utilizando `Uri.EscapeDataString()` no .NET, ou equivalente na sua plataforma de eleição.

### <a name="filtering-tips"></a>Sugestões de filtragem
**Aumentar a precisão da pesquisa com filtros**

Utilize filtros. Se confiar nos apenas pesquisa expressões sozinhas, decorrentes pode causar um documento deve ser devolvida que não tem o valor de aspeto preciso em qualquer um dos respetivos campos.

**Aumentar o desempenho de pesquisa com filtros**

Filtros de restringir o conjunto de documentos do candidato para pesquisa e excluir da classificação. Se tiver um grande conjunto de documentos, utilizar um aspeto de restrições seletivo desagregar frequentemente dá-lhe um melhor desempenho.
  
**Apenas os campos por facetas de filtro**

Por facetas desagregar, normalmente, pretende incluir apenas os documentos que tenham o valor de aspeto num campo específico (por facetas), não em qualquer lugar em todos os campos pesquisáveis. Adicionar um filtro reinforces o campo de destino ao instruir o serviço de pesquisa apenas no campo por facetas para um valor correspondente.

**Compactar resultados de faceta com mais filtros**

Resultados de aspeto de restrições são encontrados nos resultados da pesquisa que correspondem a um termo de aspeto de restrições de documentos. No exemplo seguinte, nos resultados de pesquisa para *a informática em nuvem*, 254 itens têm também *especificação interna* como um tipo de conteúdo. Os itens não são necessariamente mutuamente exclusivos. Se um item cumpre os critérios de ambos os filtros, é contabilizado em cada um deles. Este quais a duplicação é possível quando facetamento no `Collection(Edm.String)` campos, que são frequentemente utilizados para implementar a marcação de documento.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Em geral, se achar que os resultados de faceta consistentemente são demasiado grandes, recomendamos que adicionar mais filtros para dar aos utilizadores mais opções para a restrição a pesquisa.

### <a name="tips-about-result-count"></a>Sugestões sobre como contagem de resultados

**Limitar o número de itens no painel de navegação aspeto**

Para cada campo por facetas na árvore de navegação, há um limite predefinido de 10 valores. Esta predefinição faz sentido para estruturas de navegação porque mantém a lista de valores para um tamanho de fácil gestão. Pode substituir a predefinição atribuindo um valor de contagem.

* `&facet=city,count:5`Especifica que apenas as primeiro cinco cidades encontradas na parte superior classificada os resultados são devolvidas como um resultado de aspeto de restrições. Considere uma consulta de exemplo com um termo de pesquisa de "airport" e 32 correspondências. Se a consulta especifica `&facet=city,count:5`, apenas as primeiro cinco cidades exclusivas com mais documentos nos resultados da pesquisa são incluídas nos resultados de aspeto de restrições.

Tenha em atenção a distinção entre os resultados de aspeto de restrições e os resultados da pesquisa. Os resultados da pesquisa são todos os documentos que correspondam a consulta. Resultados de aspeto de restrições são correspondências para cada valor de aspeto de restrições. No exemplo, os resultados da pesquisa incluírem nomes cidade que não estão na lista de classificação de faceta (5 no nosso exemplo). Os resultados que são filtrados através de navegação por facetas ficam visíveis quando limpar facetas ou escolha outras facetas além da cidade. 

> [!NOTE]
> Debater `count` quando existe mais de um tipo pode ser confuso. A tabela seguinte oferece um breve resumo da forma como o termo é utilizado na API da Azure Search, código de exemplo e documentação. 

* `@colorFacet.count`<br/>
  No código de apresentação, deverá ver um parâmetro de contagem no aspeto, utilizado para apresentar o número de resultados de aspeto de restrições. Nos resultados de aspeto, contagem indica o número de documentos que corresponda ao intervalo ou o termo de aspeto de restrições.
* `&facet=City,count:12`<br/>
  Uma consulta de aspeto, pode definir contagem para um valor.  A predefinição é 10, mas pode pode defini-la superior ou inferior. Definição `count:12` obtém topo 12 corresponde nos resultados de faceta por contagem de documentos.
* "`@odata.count`"<br/>
  A resposta de consulta, este valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, é maior do que a soma de todos os resultados de faceta combinada, devido à presença de itens que correspondem ao termo de pesquisa, mas tem não corresponde ao valor de aspeto de restrições.

**Obter contagens nos resultados de aspeto de restrições**

Quando adicionar um filtro a uma consulta por facetas, pode querer manter a declaração de faceta (por exemplo, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, aspeto = classificação não é necessário, mas manter-devolve o número de valores de faceta para classificações 4 e posterior. Por exemplo, se clicar em "4" e a consulta inclui um filtro para maior ou igual a "4", contagens são devolvidos para cada classificação que é 4 e posterior.  

**Certifique-se de que obter contagens de faceta exata**

Em determinadas circunstâncias, poderá achar que as contagens de aspeto de restrições não correspondem os conjuntos de resultados (consulte [navegação por facetas na pesquisa do Azure (mensagem num fórum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Contagens de faceta podem estar incorretas devido à arquitetura de fragmentação. Todos os índices de pesquisa tem vários shards e cada partição horizontal reporta as facetas de N principais por contagem de documentos, que é, em seguida, combinada um resultado único. Se algum shards tem muitos valores correspondentes, enquanto outras pessoas tenham menos, pode constatar que alguns valores de faceta estão em falta ou em-contados nos resultados.

Apesar deste comportamento pode alterar em qualquer altura, se encontrar este comportamento hoje em dia, pode contorná-lo por artificialmente inflating a contagem:<number> para um grande número de impor completo de relatórios de cada ID de partição horizontal. Se o valor da contagem: é maior que ou igual ao número de valores exclusivos no campo, são garantida resultados precisos. No entanto, quando são elevadas contagens de documentos, há uma penalidade de desempenho, por isso, utilize esta opção judiciously.

### <a name="user-interface-tips"></a>Sugestões de interface de utilizador
**Adicionar etiquetas para cada campo na navegação de faceta**

As etiquetas são definidas habitualmente no HTML ou no formato (`index.cshtml` a aplicação de exemplo). Não há nenhuma API na Azure Search para etiquetas de navegação de faceta ou quaisquer outros metadados.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtro com base num intervalo
Facetamento através de intervalos de valores é um requisito de aplicação de pesquisa comuns. São suportados intervalos de dados numéricos e os valores DateTime. Pode ler mais sobre cada abordagem no [documentos sobre pesquisa (API da Azure Search)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

A pesquisa do Azure simplifica a construção de intervalo ao fornecer duas abordagens para um intervalo de computação. Para ambas as abordagens, pesquisa do Azure cria os intervalos adequados indicados as entradas que forneceu. Por exemplo, se especificar valores de intervalo de 10 | 20 | 30, este cria automaticamente os intervalos de 0 a 10, 10 20, 20 a 30. A aplicação, opcionalmente, pode remover quaisquer intervalos que estão vazios. 

**Abordagem 1: Utilizar o parâmetro de intervalo**  
Para definir as facetas de preços em incrementos de $10, tem de especificar:`&facet=price,interval:10`

**Abordagem 2: Utilizar uma lista de valores**  
Para dados numéricos, pode utilizar uma lista de valores.  Considere o período de aspeto de um `listPrice` campo, composto da seguinte forma:

  ![Lista de valores de exemplo][5]

Para especificar um intervalo de aspeto de restrições, como a captura de ecrã anterior, utilize uma lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Baseia-se cada intervalo de 0 a utilizar como ponto de partida, um valor na lista como um ponto final e, em seguida, cortada do intervalo anterior para criar intervalos discretos. A pesquisa do Azure efetua estas coisas como parte da navegação por facetas. Não é necessário escrever código para structuring cada intervalo.

### <a name="build-a-filter-for-a-range"></a>Criar um filtro para um intervalo
Para filtrar documentos com base num intervalo que selecionar, pode utilizar o `"ge"` e `"lt"` filtrar operadores numa expressão de duas partes, que define os pontos finais do intervalo. Por exemplo, se escolher o intervalo de 10 25 para um `listPrice` campo, o filtro seria `$filter=listPrice ge 10 and listPrice lt 25`. O código de exemplo, a expressão de filtro utiliza **priceFrom** e **priceTo** parâmetros para definir os pontos finais. 

  ![Consulta para um intervalo de valores][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrar com base na distância
De comuns para ver filtra que o ajudam escolha um arquivo, restaurante ou de destino com base no respetiva proximidade à sua localização atual. Apesar deste tipo de filtro aspeto que poderá ter navegação por facetas, é apenas um filtro. -O aqui iremos mencionar os dos que são especificamente procuram conselhos de implementação para esse problema de estrutura específica.

Existem duas funções Geoespacial na Azure Search, **geo.distance** e **geo.intersects**.

* O **geo.distance** função devolve a distância, em quilómetros, entre dois pontos. Um ponto é um campo e o outro é uma constante transmitida como parte do filtro. 
* O **geo.intersects** função devolve true se um determinado ponto estiver dentro de um polígono especificado. O ponto é um campo e o polígono está especificado como uma lista de constante de coordenadas transmitido como parte do filtro.

Pode encontrar exemplos de filtro [sintaxe de expressão de OData (Azure Search)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Experimentar a demonstração
A demonstração de Portal do Azure Search tarefa contém exemplos referenciados neste artigo.

-   Ver e testar a demonstração de trabalhar online em [demonstração de Portal do Azure Search tarefa](http://azjobsdemo.azurewebsites.net/).

-   Transferir o código a partir de [repositório Azure-Samples no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

À medida que trabalha com os resultados da pesquisa, veja o URL para as alterações na construção de consulta. Esta aplicação acontece a acrescentar facetas para o URI à medida que seleciona cada um deles.

1. Para utilizar a funcionalidade de mapeamento da aplicação de demonstração, obter uma chave do Bing Maps do [Dev Center do Bing Maps](https://www.bingmapsportal.com/). Cole-o sobre a chave existente no `index.cshtml` página. O `BingApiKey` definição o `Web.config` ficheiro não é utilizado. 

2. Execute a aplicação. Colocar a apresentação da opcional ou dispensar a caixa de diálogo.
   
3. Introduza um termo de pesquisa, tal como "analista" e clique no ícone de pesquisa. A consulta executa rapidamente.
   
   Uma estrutura de navegação por facetas também é devolvida com os resultados da pesquisa. Na página de resultados de pesquisa, a estrutura de navegação por facetas inclui contagens para cada resultado do aspeto de restrições. Nenhum facetas são selecionadas, para que todos os resultados correspondentes são devolvidos.
   
   ![Resultados da pesquisa antes de selecionar facetas][11]

4. Clique num título de negócio, localização ou salário mínimo. Facetas foram nulos na pesquisa inicial, mas como estas entrarem em valores, os resultados da pesquisa são recortados dos itens que já não correspondem.
   
   ![Resultados da pesquisa depois de selecionar facetas][12]

5. Para limpar a consulta por facetas para que pode tentar comportamentos de consulta diferentes, clique em de `[X]` após as facetas selecionadas para limpar as facetas.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Saiba mais
Veja [detalhada da pesquisa do Azure](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Em 45:25, não há uma demonstração sobre como implementar facetas.

Para mais informações sobre os princípios de design para navegação por facetas, recomendamos as seguintes ligações:

* [Estruturar para efetuar uma pesquisa por facetas](http://www.uie.com/articles/faceted_search/)
* [Padrões de conceção: Navegação por facetas](http://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/azure-search-faceting-example.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png
[11]: ./media/search-faceted-navigation/faceted-search-before-facets.png
[12]: ./media/search-faceted-navigation/faceted-search-after-facets.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

