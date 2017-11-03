---
title: Como paginar os resultados da pesquisa na Azure Search | Microsoft Docs
description: "Paginação na Azure Search, um serviço de pesquisa em nuvem alojado no Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: a0a1d315-8624-4cdf-b38e-ba12569c6fcc
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: heidist
ms.openlocfilehash: 1054e15a2751c53aad5dbc8054c4cec41102dee9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-page-search-results-in-azure-search"></a>Como paginar os resultados da pesquisa no Azure Search
Este artigo fornece orientação sobre como utilizar a API de REST do serviço de pesquisa do Azure para implementar elementos padrão de uma página de resultados de pesquisa, tal como contagens totais, obtenção de documento, as ordens de ordenação e navegação.

Em cada caso mencionado abaixo, as opções relacionadas com a página que contribuem para os dados ou informações à sua página de resultados de pesquisa especificadas através do [pesquisa documento](http://msdn.microsoft.com/library/azure/dn798927.aspx) pedidos enviados para o serviço de pesquisa do Azure. Os pedidos de incluir um comando GET, caminho e os parâmetros de consulta que o informam o serviço que está a ser pedido e como formular a resposta.

> [!NOTE]
> Um pedido válido inclui um número de elementos, tais como um URL do serviço e um caminho, verbo HTTP `api-version`, e assim sucessivamente. Para uma forma abreviada, iremos recortadas exemplos para realçar apenas a sintaxe que é relevante para paginação. Consulte o [API de REST do serviço de pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) documentação para obter detalhes sobre a sintaxe do pedido.
> 
> 

## <a name="total-hits-and-page-counts"></a>Totais de pedidos e contagem de páginas
Que mostra o número total de resultados devolvidos por uma consulta e, em seguida, devolver esses resultados no segmentos mais pequenos, é fundamental para praticamente todas as páginas de pesquisa.

![][1]

Na Azure Search, utilize o `$count`, `$top`, e `$skip` parâmetros para estes valores de retorno. O exemplo seguinte mostra um exemplo de pedido para pedidos totais, devolvido como `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Obter documentos em grupos de 15 e também mostrar pedidos totais, que começa com a primeira página:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Paginating resultados requer o `$top` e `$skip`, onde `$top` Especifica quantos itens a devolver num batch, e `$skip` Especifica quantos itens para ignorar. No exemplo seguinte, cada página mostra os itens de 15 em seguida, indicado pelas hiperligações incrementais no `$skip` parâmetro.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Esquema
Na página de resultados de pesquisa, pode querer mostram uma imagem em miniatura, um subconjunto dos campos e uma ligação para uma página de produto completa.

 ![][2]

Na Azure Search, teria de utilizar `$select` e um comando de pesquisa para implementar esta experiência.

Para devolver um subconjunto de campos para um esquema em mosaico:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Imagens e os ficheiros de suporte de dados não são diretamente pesquisáveis e devem ser armazenados no noutra plataforma de armazenamento, como o armazenamento de Blobs do Azure, para reduzir os custos. O índice e a documentos, defina um campo que armazena o endereço de URL do conteúdo externo. Em seguida, pode utilizar o campo como referência da imagem. O URL da imagem deve ser o documento.

Para obter uma página de descrição do produto para uma **onClick** evento, utilize [pesquisa documento](http://msdn.microsoft.com/library/azure/dn798929.aspx) passem a chave do documento para obter. O tipo de dados da chave é `Edm.String`. Neste exemplo, é *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Ordenar por relevância, classificação ou preço
Ordenação ordena, muitas vezes, a predefinição para relevância, mas é comum para disponibilizar ordenação alternativa as ordens de imediato para que os clientes podem rapidamente reshuffle resultados existentes para uma ordem de classificação diferente.

 ![][3]

Na Azure Search, ordenação baseiam o `$orderby` para todos os campos que são indexados como uma expressão`"Sortable": true.`

Relevância é fortemente associada com perfis de classificação. Pode utilizar a predefinição de classificação, que depende de análise de texto e estatísticas para classificar todos os resultados, com as pontuações superiores acontecimentos para documentos com mais ou mais fortes correspondências um termo de pesquisa que ordem.

As ordens de ordenação alternativo associados normalmente **onClick** eventos que volta a chamada para um método que é criada a sequência de ordenação. Por exemplo, fornecido este elemento da página:

 ![][4]

Poderá criar um método que aceita a opção de ordenação selecionado como entrada e devolve uma lista ordenada para os critérios associados essa opção.

 ![][5]

> [!NOTE]
> Enquanto a predefinição de classificação é suficiente para vários cenários, recomendamos que basing relevância num perfil de classificação personalizado em vez disso. Um perfil de classificação personalizado dá-lhe uma forma de itens de aumento que são mais úteis para a sua empresa. Consulte [adicionar um perfil de classificação](http://msdn.microsoft.com/library/azure/dn798928.aspx) para obter mais informações. 
> 
> 

## <a name="faceted-navigation"></a>Navegação por facetas
Navegação de pesquisa é comum numa página de resultados, muitas vezes, localizado no lado ou superior de uma página. Na Azure Search, navegação por facetas fornece auto-direcionada pesquisa com base em filtros predefinidos. Consulte [navegação por facetas na Azure Search](search-faceted-navigation.md) para obter mais detalhes.

## <a name="filters-at-the-page-level"></a>Filtros ao nível da página
Caso o design de solução dedicado pesquisa páginas para tipos específicos de conteúdo (por exemplo, uma aplicação de revenda online tem departamentos listados na parte superior da página), pode inserir uma expressão de filtro juntamente com um **onClick**eventos para abrir uma página num estado pré-filtrada. 

Pode enviar um filtro com ou sem uma expressão de pesquisa. Por exemplo, o pedido seguinte irá filtrar no nome de marca, devolver apenas esses documentos que correspondem ao mesmo.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Consulte [documentos sobre pesquisa (API da Azure Search)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter mais informações sobre `$filter` expressões.

## <a name="see-also"></a>Veja Também
* [API de REST do serviço de pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx)
* [Operações do índice](http://msdn.microsoft.com/library/azure/dn798918.aspx)
* [Operações do documento](http://msdn.microsoft.com/library/azure/dn800962.aspx)
* [Vídeos e tutoriais sobre pesquisa do Azure](search-video-demo-tutorial-list.md)
* [Navegação por facetas na pesquisa do Azure](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
