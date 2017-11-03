---
title: Filtros de idioma na Azure Search | Microsoft Docs
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
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 31404e9ae2dac559f6b4f9f8c0edd0a785142912
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Como filtrar por idioma na Azure Search 

Um requisito-chave numa aplicação de pesquisa multilingue é a capacidade para procurar através de e obter resultados no idioma do utilizador. Na Azure Search, uma forma para cumprir os requisitos de idioma de uma aplicação multilingue é criar uma série de campos dedicado para armazenar cadeias num idioma específico e, em seguida, limitar as pesquisa em texto completo para apenas esses campos no momento da consulta.

Parâmetros de consulta no pedido são utilizados para definir o âmbito da operação de pesquisa e, em seguida, limitar os resultados de quaisquer campos que não fornecem conteúdo compatível com a experiência de pesquisa que pretende fornecer.

| Parâmetros | Objetivo |
|-----------|--------------|
| **searchFields** | Os limites completa a pesquisa em texto à lista de campos com nome. |
| **$select** | Corta a resposta para incluir apenas os campos que especificar. Por predefinição, são devolvidos todos os campos recuperável. O **$select** parâmetro permite-lhe escolher aqueles a devolver. |

A taxa de êxito desta técnica hinges na integridade do conteúdo do campo. A pesquisa do Azure não traduzir as cadeias ou efetuar a deteção de idioma. Trata-se até que para se certificar de que os campos contêm as cadeias de que espera.

## <a name="define-fields-for-content-in-different-languages"></a>Definir os campos para o conteúdo em idiomas diferentes

Na Azure Search, consultas de destino num índice único. Os programadores que pretendem fornecer específicas do idioma cadeias numa experiência de procura única, normalmente, definem campos dedicados para armazenar os valores: um campo para inglês cadeias, um para francês e assim sucessivamente. 

No nossos exemplos, incluindo o [disponível exemplo](search-get-started-portal.md) mostrado abaixo, poderá ter visto definições de campo semelhantes à seguinte captura de ecrã. Repare como este exemplo mostra o idioma atribuições de analisador para os campos neste índice. Os campos que contêm cadeias melhor efetuar na pesquisa de texto completo quando emparelhado com um analisador foi desenvolvido para processar as regras linguístico do idioma de destino.

  ![](./media/search-filters/lang-fields.png)

> [!Note]
> Para obter exemplos de código que mostra as definições de campo com analisadores de idiomas, consulte [definir um índice (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) e [definir um índice (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Um passo intermédio (e talvez óbvios) é que tem de [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) antes formulating uma consulta. Iremos mencionar este passo aqui por questões de exaustividade. É uma forma de determinar se o índice está disponível ao verificar a lista de índices [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Restringir a consulta e limitar os resultados

Parâmetros de consulta são utilizados para limitar a pesquisa a campos específicos e, em seguida, limitar os resultados de quaisquer campos não é útil para o seu cenário. Tendo em conta um objetivo de pesquisa restrições para os campos com cadeias francês, teria de utilizar **searchFields** como destino a consulta nos campos com cadeias nesse idioma. 

Por predefinição, uma pesquisa devolve todos os campos que estão marcados como recuperável. Como tal, pode querer excluir os campos que não está em conformidade com a experiência de pesquisa de específicas do idioma que pretende fornecer. Especificamente, se limitados procura a um campo com cadeias francês, provavelmente pretende excluir os campos com cadeias inglês os resultados. Utilizar o **$select** fornecem parâmetro controlar a ativação pós-falha os campos são devolvidos para a aplicação de chamada de consulta.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Embora o argumento de no $filter na consulta, este caso de utilização é vivamente afiliado conceitos de filtro, pelo que iremos apresentá-lo como um cenário de filtragem.

## <a name="see-also"></a>Consultar também

+ [Filtros na pesquisa do Azure](search-filters.md)
+ [Analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Como completa a pesquisa em texto funciona na Azure Search](search-lucene-query-architecture.md)
+ [API de REST de documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents)

