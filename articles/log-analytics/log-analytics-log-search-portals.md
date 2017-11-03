---
title: "Portais para criação e edição de consultas de registo no Log Analytics do Azure | Microsoft Docs"
description: "Este artigo descreve os portais que pode utilizar pesquisas de registo de análise de registos do Azure para criar e editar."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte; bwren
ms.openlocfilehash: b205f226d95d94b938a70a834ac0147e76d459ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portais para criação e edição de consultas de registo no Log Analytics do Azure

Pode utilizar pesquisas de registo numa variedade de locais em toda a análise de registos para obter dados da área de trabalho.  Para, efetivamente, criação e edição consultas para além de trabalhar de forma interativa com dados devolvidos entanto, tem duas opções são descritas abaixo.  

## <a name="log-search"></a>Pesquisas de registos 
A página de pesquisa de registo está acessível a partir do portal do Azure.  É adequado para a criação de consultas básicas que podem ser criadas numa única linha.  Pesquisa de registo pode ser utilizada sem iniciar um portal externo e pode utilizá-lo para efetuar uma variedade de funções com pesquisas de registo, incluindo a criação de regras de alerta, criar grupos de computadores e exportar os resultados da consulta.  

Pesquisa de registo fornece várias funcionalidades para editar a consulta sem ter um conhecimento completo do idioma de consulta.  Pode obter um resumo destas funcionalidades no [pesquisas de registo de criar na análise de registos do Azure utilizando a pesquisa de registo](log-analytics-log-search-log-search-portal.md).


![Página de pesquisa de registo](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portal da análise avançada
O portal de análise avançada é um portal dedicado que fornece funcionalidade avançada não está disponível no registo de pesquisa do portal do Azure.  As funcionalidades incluem a capacidade de editar uma consulta em várias linhas, seletivamente executar código, o Intellisense context confidencial e análise inteligente.  O portal de análise avançadas é mais adequado para conceber consultas complexas que estão a guardar como uma pesquisa de registo ou copiadas e coladas outros elementos de análise de registos.  Inicie o portal de análise avançadas de uma ligação na página de pesquisa de registo.

![Portal da análise avançada](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Devido ao respetivas funcionalidades avançadas, normalmente, irá utilizar o portal de análise avançadas como a principal ferramenta para criação e edição de consultas.  Uma vez que tenha determinado que a consulta funciona conforme esperado, em seguida, irá copiar e colá-lo noutro local, como a página de pesquisa de registo ou o estruturador de vistas.  Porque o portal de análise avançadas suporta consultas com várias linhas, embora, precisa de tomar em consideração o seguinte quando copiar uma consulta de neste portal.

- Comentários tem de ser removidos da consulta antes tem copiados e colados para outra localização.  Pode comente uma linha por precedente-lo com duas barras (/ /).  Quando cole uma consulta de linha vários uma única linha, as quebras de linha são removidas.  Se os comentários são incluídos, todos os carateres após o primeiro comentário são considerados parte o comentário.


## <a name="next-steps"></a>Passos seguintes

- Percorrer um tutorial sobre como utilizar [pesquisa registo](log-analytics-tutorial-viewdata.md) para saber como criar consultas utilizando a linguagem de consulta
- Veja o [portal da análise avançadas](https://go.microsoft.com/fwlink/?linkid=856587) para criar consultas sofisticadas e utilizar como ambiente de desenvolvimento para as suas pesquisas de registo.

