---
title: "Filtros de aspeto de restrições na Azure Search | Microsoft Docs"
description: "Filtre critérios pela identidade de segurança do utilizador, idioma, geolocalização ou valores numéricos para reduzir os resultados da pesquisa em consultas na Azure Search, um serviço de pesquisa em nuvem alojado no Microsoft Azure."
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
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 5b4d88cb9c9662fe45de8c11534232a2905cf5a4
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Como criar um filtro de aspeto de restrições na Azure Search 

Navegação por facetas é utilizada para filtragem auto-direcionada nos resultados de consulta numa aplicação de pesquisa, onde a aplicação oferece controlos de IU para a pesquisa de âmbito para grupos de documentos (por exemplo, categorias ou marcas) e pesquisa do Azure fornece a estrutura de dados para fazer uma cópia do experiência. Neste artigo, reveja rapidamente os passos básicos para criar uma estrutura de navegação por facetas cópia a experiência de pesquisa que pretende fornecer. 

> [!div class="checklist"]
> * Escolha os campos para filtrar e facetamento
> * Conjunto de atributos em campo
> * Criar os índice e carregar dados
> * Adicionar filtros de aspeto de restrições a uma consulta
> * Processar os resultados

Facetas são dinâmicas e devolvido numa consulta. Colocar as respostas de pesquisa com os mesmos as categorias de faceta utilizadas para navegar os resultados. Se não estiver familiarizado com as facetas, o exemplo seguinte é uma ilustração de uma estrutura de navegação de faceta.

  ![](./media/search-filters/facet-nav.png)

Navegação por facetas a primeira e pretender mais detalhes? Consulte [como implementar navegação por facetas na Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Escolha os campos

Podem ser calculadas facetas através de campos de valor único, bem como as coleções. Os campos que funcionarão melhor na navegação por facetas tem cardinalidade baixa: um pequeno número de valores distintos que repita ao longo de documentos no seu corpus de procura (por exemplo, uma lista de cores, países ou nomes de marca). 

Facetamento está ativado numa base de campo ao campo ao criar o índice, definindo os seguintes atributos como TRUE: `filterable`, `facetable`. Apenas os campos filtráveis podem ser facetados.

Qualquer [campo tipo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) que, possivelmente, poderia ser utilizado na navegação por facetas está marcada como "facetável":

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections (consulte [como tipos de dados complexos aspeto](#facet-complex-fields) posteriormente neste artigo.)
+ Tipos de campo numérico: Edm.Double Edm.Int32, Edm.Int64,

Não é possível utilizar Edm.GeographyPoint na navegação por facetas. Facetas são construídas a partir de texto legível humano ou números. Como tal, facetas não são suportadas para georreplicação coordenadas. Precisa de um campo Cidade ou uma região para aspeto por localização.

## <a name="set-attributes"></a>Conjunto de atributos

Atributos de índice que controlam a utilização de um campo são adicionados à definição de campo individual no índice. No exemplo seguinte, os campos com cardinalidade baixa, útil para facetamento, consistem: categoria (átrios hotel, hostel), amenities e as classificações. 

Na .NET API, a filtragem de atributos tem de ser definida explicitamente. A API REST, facetamento e filtragem estão ativados por predefinição, o que significa que só tem de definir explicitamente os atributos quando pretender desligue-os. Embora não seja necessário tecnicamente, mostramos de autoria no seguinte exemplo REST para fins de instrução. 

> [!Tip]
> Como melhor prática de desempenho e a otimização de armazenamento, desative facetamento para campos de que nunca devem ser utilizados como um aspeto de restrições. Em particular, os campos de cadeia para os valores de singleton, como um nome de produto ou ID devem ser definidos como "Facetável": Falso para impedir que os respetivos acidental (e ineficaz) utilize na navegação por facetas.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Esta definição do índice é copiada da [criar um índice da Azure Search utilizando a API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). É idêntica, à exceção superficial diferenças nas definições de campo. Filtrável e facetáveis atributos explicitamente são adicionados na categoria, etiquetas, parkingIncluded, smokingAllowed e campos de classificação. Na prática, obterá filtrável e facetável para livre no EDM, Boolean e Edm.Int32 tipos de campo. 

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Um passo intermédio (e talvez óbvios) é que tem de [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) antes formulating uma consulta. Iremos mencionar este passo aqui por questões de exaustividade. É uma forma de determinar se o índice está disponível ao verificar a lista de índices [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicionar filtros de aspeto de restrições a uma consulta

No código da aplicação, crie uma consulta que especifica a todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, a classificação de perfis – tudo utilizado para formular um pedido. O exemplo seguinte cria um pedido que cria com base no tipo de accommodation, classificação e outros amenities de navegação de faceta.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Devolve resultados filtrados no clique em eventos

A expressão de filtro processa o evento de clique com o valor de aspeto de restrições. Determinado um aspeto de categoria, clicando a categoria "motel" é implementado através de um `$filter` expressão que seleciona accommodations desse tipo. Quando um utilizador clica "motels" para indicar que devem ser apresentados apenas motels, a consulta seguinte, a aplicação envia inclui $filter = motels' categoria eq'.

O seguinte fragmento de código adiciona categoria para o filtro se um utilizador seleccionar um valor de aspeto de categoria.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Utilizando a API REST, o pedido seria articulated como `$filter=category eq 'c1'`. Para tornar um campo de valor múltiplo de categoria, utilize a seguinte sintaxe:`$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Soluções e sugestões

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializar uma página com as facetas no local

Se pretender inicializar uma página com as facetas no local, pode enviar uma consulta como parte da inicialização da página para efetuar o seeding a página com uma estrutura de faceta inicial.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Manter uma estrutura de navegação de faceta assíncrona de resultados filtrados

Um dos desafios com navegação de faceta na Azure Search é que as facetas existem para apenas os resultados atuais. Na prática, é comum para manter um conjunto estático de faceta para que o utilizador pode navegar na inversa, retracing passos para explorar caminhos alternativos através de conteúdo de pesquisa. 

Apesar de este ser um caso de utilização comum, não é algo a estrutura de navegação de faceta atualmente fornece out of box. Os programadores que pretendem facetas estáticas normalmente contornar a limitação ao emitir duas consultas filtradas: um âmbito para os resultados, outros utilizado para criar uma lista estática de faceta para fins de navegação.

## <a name="see-also"></a>Consultar também

+ [Filtros na pesquisa do Azure](search-filters.md)
+ [Criar API REST do índice](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [API de REST de documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents)

