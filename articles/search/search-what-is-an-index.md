---
title: "Criar um índice da Azure Search | Microsoft Azure | Serviço de pesquisa em nuvem alojado"
description: "O que é um índice da Azure Search e como é utilizado?"
services: search
documentationcenter: 
author: ashmaka
ms.assetid: a395e166-bf2e-4fca-8bfc-116a46c5f7b1
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: 7fc45273c0f71c727b7087949cc63bbb4111f866
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-index"></a>Criar um índice da Azure Search
> [!div class="op_single_selector"]
> * [Descrição geral](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

## <a name="what-is-an-index"></a>O que é um índice?
Um *índice* é um arquivo persistente de *documentos* e outras construções utilizado por um serviço Azure Search. Um documento é uma unidade de dados pesquisáveis no índice. Por exemplo, um retalhista de comércio eletrónico pode ter um documento para cada artigo que vende, uma empresa jornalística pode ter um documento para cada artigo, etc. Mapeamento destes conceitos para equivalentes da base de dados mais familiares: um *índice* é, conceptualmente, semelhante a uma *tabela* e os *documentos* são quase equivalentes a *linhas* numa tabela.

Quando adiciona/carrega documentos e submete consultas de pesquisa para a Azure Search, submete pedidos para um índice específico no serviço de pesquisa. 

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Tipos de campo e atributos num índice da Azure Search
Na definição do esquema, tem de especificar o nome, o tipo e os atributos de cada campo do índice. O tipo de campo classifica os dados armazenados nesse campo. Os atributos são definidos em campos individuais para especificar o modo de utilização do campo. As tabelas seguintes enumeram os tipos e os atributos que pode especificar.

### <a name="field-types"></a>Tipos de campo
| Tipo | Descrição |
| --- | --- |
| *Edm.String* |Texto que pode, opcionalmente, ser atomizado para pesquisa em texto completo (separação de palavras, lematização, etc.). |
| *Collection(Edm.String)* |Uma lista de cadeias que pode, opcionalmente, ser atomizada para pesquisa em texto completo. Não existe um limite superior teórico do número de itens numa coleção, contudo, o limite superior de 16 MB de tamanho de payload aplica-se às coleções. |
| *Edm.Boolean* |Contém valores verdadeiro/falso. |
| *Edm.Int32* |Valores inteiros de 32 bits. |
| *Edm.Int64* |Valores inteiros de 64 bits. |
| *Edm.Double* |Dados numéricos de dupla precisão. |
| *Edm.DateTimeOffset* |Valores de data/hora representados no formato OData V4 (por ex. `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Um ponto que representa uma localização geográfica no mundo. |

Pode encontrar mais informações detalhadas sobre os [tipos de dados suportados aqui](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) do Azure Search.

### <a name="field-attributes"></a>Atributos do campo
| Atributo | Descrição |
| --- | --- |
| *Chave* |Uma cadeia que fornece o ID único de cada documento, utilizada para a procura de documentos. Todos os índices devem ter uma chave. Apenas um campo pode ser uma chave e o tipo deve ser definido para Edm.String. |
| *Recuperável* |Especifica se um campo pode ser devolvido num resultado da pesquisa. |
| *Filtrável* |Permite que o campo seja utilizado nas consultas de filtro.  |
| *Ordenável* |Permite a uma consulta ordenar os resultados da pesquisa através deste campo. |
| *Facetável* |Permite a um campo ser utilizado numa estrutura de [navegação por facetas](search-faceted-navigation.md) para filtragem do utilizador auto-direcionada. Geralmente os campos com valores repetitivos que pode utilizar para agrupar vários documentos (por exemplo, vários documentos que se inserem numa única marca ou categoria de serviço) funcionam melhor como facetas. |
| *Pesquisável* |Marca o campo como pesquisável no texto completo. |

Pode encontrar mais informações detalhadas sobre os [atributos de índice aqui](https://docs.microsoft.com/rest/api/searchservice/Create-Index) do Azure Search.

## <a name="guidance-for-defining-an-index-schema"></a>Orientações para definir um esquema de índice
Na criação do índice, reserve tempo na fase de planeamento para analisar cada decisão. É importante ter em consideração as suas necessidades comerciais e experiência do utilizador de pesquisa quando estiver a criar o seu índice, uma vez que cada campo deve receber os [atributos adequados](https://docs.microsoft.com/rest/api/searchservice/Create-Index). A alteração de um índice após a respetiva implementação implica reconstruir e recarregar os dados.

Caso os requisitos de armazenamento de dados alterem ao longo do tempo, pode aumentar ou diminuir a capacidade adicionando ou removendo partições. Para obter detalhes, consulte [Gerir o serviço de Pesquisa no Azure](search-manage.md) ou [Limites de Serviço](search-limits-quotas-capacity.md).

