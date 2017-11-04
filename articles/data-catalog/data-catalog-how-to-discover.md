---
title: "Como descobrir origens de dados no catálogo de dados do Azure | Microsoft Docs"
description: "Este artigo realça como detetar recursos de dados registados com o catálogo de dados do Azure, incluindo a procura e filtragem e utilizando as capacidades de highlighting acessos do portal do catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 7c2a1d61bc13ba1cde404e2b732933d2d314e3e3
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Como descobrir origens de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
Catálogo de dados do Azure é um serviço em nuvem completamente gerido que funciona como um sistema de registo e a deteção de origens de dados empresariais. Por outras palavras, pessoas, detetar, compreender e utilizar origens de dados ajuda-o catálogo de dados e ajuda as organizações a obter maior valor dos respetivos dados existentes. Depois de uma origem de dados é registada no catálogo de dados, indexa os metadados pelo serviço, para que pode procurar facilmente para detetar dados que precisa.

## <a name="searching-and-filtering"></a>Procurar e filtrar
A deteção no catálogo de dados utiliza dois mecanismos principais: pesquisar e filtrar.

A pesquisa foi concebida para ser intuitiva e poderosa. Por predefinição, é feita a correspondência dos termos de pesquisa em relação a qualquer propriedade no catálogo, incluindo anotações fornecidas pelo utilizador.

A filtragem foi concebida para complementar a pesquisa. Pode selecionar características específicas, como especialistas, tipo de origem de dados, tipo de objeto e etiquetas. Pode ver apenas os recursos de dados correspondente e restringir os resultados da pesquisa a recursos correspondentes.

Ao utilizar uma combinação de pesquisa e filtragem, pode navegar rapidamente as origens de dados que foram registadas no catálogo de dados para detetar as origens de dados que é necessário.

## <a name="search-syntax"></a>Sintaxe de pesquisa
Apesar da pesquisa de texto livre predefinido é simples e intuitivas, também pode utilizar a sintaxe de pesquisa do catálogo de dados para maior controlo sobre os resultados da pesquisa. Pesquisa do catálogo de dados suporta as seguintes técnicas:

| Técnica | Utilização | Exemplo |
| --- | --- | --- |
| Pesquisa básica |Pesquisa básica que utiliza um ou mais termos de pesquisa. Os resultados são qualquer recurso que corresponda uma propriedade com um ou mais dos termos especificados. |`sales data` |
| Âmbito de propriedade |Devolva apenas origens de dados onde o termo de pesquisa tem correspondência com a propriedade especificada. |`name:finance` |
| Operadores Booleanos |Ampliar ou reduzir uma pesquisa por com operações booleanas. |`finance NOT corporate` |
| Agrupar com parênteses |Utilize parênteses para agrupar partes da consulta para alcançar isolamento lógico, especialmente em conjunto com operadores booleanos. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operadores de Comparação |Utilize comparações além igualdade para propriedades que têm tipos de dados numéricos e de data. |`modifiedTime > "11/05/2014"` |

Para mais informações sobre uma pesquisa no catálogo de dados, consulte o [catálogo de dados do Azure](https://msdn.microsoft.com/library/azure/mt267594.aspx) artigo.

## <a name="hit-highlighting"></a>Detetor de ocorrências
Ao visualizar os resultados da pesquisa, qualquer apresentadas propriedades que correspondem aos termos de pesquisa especificada (por exemplo, o nome de recurso de dados, a descrição e as etiquetas) são realçadas para torná-lo mais fácil de identificar a razão pela qual um recurso de dados fornecido foi devolvido por uma pesquisa especificada.

> [!NOTE]
> Para desativar o realce acessos, utilize o **realce** comutador no portal do catálogo de dados.
>
>

Ao visualizar os resultados da pesquisa, poderá não sempre ser óbvios razão pela qual um recurso de dados está incluído, mesmo com realce acessos ativada. Porque todas as propriedades são pesquisadas por predefinição, poderá ser devolvido um recurso de dados devido a uma correspondência, numa propriedade ao nível da coluna. E porque vários utilizadores podem anotar recursos de dados registados com as suas próprias etiquetas e descrições, nem todos os metadados podem ser apresentados na lista de resultados da pesquisa.

Predefinida a vista de mosaico, para cada mosaico apresentado nos resultados da pesquisa inclui um **corresponde ao termo de pesquisa da vista** ícone, para que pode ver rapidamente o número de correspondências e a respetiva localização e para ir para-las, se pretender.

 ![Detetor de ocorrências e se corresponde ao procurar no portal do catálogo de dados do Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Resumo
Uma vez que registar uma origem de dados com o catálogo de dados copia metadados estruturais e descritivo da origem de dados para o serviço de catálogo, a origem de dados fica mais fáceis de detetar e compreender. Depois de já registou uma origem de dados, pode detetá-lo ao utilizar a filtragem e pesquisar a partir do portal do catálogo de dados.

## <a name="next-steps"></a>Passos seguintes
* Para obter detalhes passo a passo sobre como detetar origens de dados, consulte [introdução ao catálogo de dados do Azure](data-catalog-get-started.md).
