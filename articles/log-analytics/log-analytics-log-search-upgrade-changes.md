---
title: "O que é alterado no Log Analytics do Azure? | Microsoft Docs"
description: "Este artigo fornece perguntas mais frequentes sobre a atualização de análise de registos para o novo idioma de consulta."
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
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>O que é alterado no Log Analytics do Azure?
Para além do idioma de consulta em si, existem vários melhoramentos e as alterações que deve ter em consideração quando está a sua área de trabalho de análise de registos [atualizado para o novo idioma de consulta](log-analytics-log-search-new.md).  Este artigo descreve brevemente as alterações entre uma área de trabalho de legado e atualizada com ligações para conteúdo detalhada para cada um. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

Consulte [pesquisa FAQ e problemas conhecidos de registo de análise de registos nova](log-analytics-log-search-faq.md) para obter uma descrição dos problemas conhecidos com a atualização e as respostas à pergunta comum.  

## <a name="query-language"></a>Linguagem da consulta
A alteração principal na atualização do Log Analytics é o idioma de consulta nova que tenha melhorias significativas através de idioma anterior.  

Pode obter uma comparação direta das operações comuns entre o idioma de legado e o novo idioma [transição para o novo idioma de consulta de análise de registos do Azure](log-analytics-log-search-transition.md).  Documentação completa sobre o novo idioma está disponível em [idioma de consulta de análise do Azure registo](https://docs.loganalytics.io).


## <a name="computer-groups"></a>Grupos de computadores
O método para criar um grupo de computadores não alterado, embora agora tem de fornecer um alias exclusivo para cada um.  Grupos de computadores com base na procura de registo tem de utilizar a sintaxe de idioma de novo.

Há uma sintaxe de novo para utilizar grupos de computadores numa pesquisa de registo.  Em vez de utilizar a função de $ComputerGroups, grupos de computadores são agora cada implementado como uma função com um alias de exclusivo.  Utilizar o alias na pesquisa de registo como qualquer outra função.  

Estão disponíveis detalhes em [pesquisas de registo de grupos de computadores na análise de registos](log-analytics-computer-groups.md).


## <a name="log-search-portals"></a>Portais de pesquisa de registos
Além de portal de registo de pesquisa para criar e executar pesquisas de registo, agora, pode utilizar o portal de análise avançadas que fornece funcionalidades de edição significativamente melhoradas.

Uma breve descrição dos dois portais está disponível em [portais para criação e edição de consultas de registo no Log Analytics do Azure](log-analytics-log-search-portals.md).  Pode percorrer um tutorial sobre o novo portal em [começar a utilizar o Portal da análise](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal).

## <a name="alerts"></a>Alertas
A mesma nas áreas de trabalho atualizadas funcionam os alertas, mas a consulta que executam têm de ser escrita num novo idioma.  As regras de alerta existentes que tinha antes da atualização serão automaticamente convertidas para o novo idioma.  Pode obter mais detalhes [alertas de compreender na análise de registos](log-analytics-alerts.md).

O formato de payload para runbooks e webhooks enviados a partir de alertas foi alterado.  Obter os detalhes para o novo formato de payload no [adicionar ações para regras de alertas na análise de registos](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Dashboards
[Dashboards](log-analytics-dashboards.md) estão a ser preterido.  Pode continuar a utilizar qualquer mosaicos que adicionou ao dashboard antes da sua área de trabalho foi atualizada, mas não é possível editar os mosaicos ou adicionar novos.  Utilize o estruturador de vistas para criar vistas personalizadas que possui uma funcionalidade mais rica definir que dashboards.

Estão disponíveis detalhes no estruturador de vistas em [estruturador de vistas de utilização para criar vistas personalizadas no Log Analytics](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
O processo para exportar dados de análise de registos para o Power BI foi alterada para áreas de trabalho atualizadas e as agendas existentes que criou antes de atualizar irão tornar-se desativada.  

Estão disponíveis detalhes em [dados de análise de registos de exportação para o Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
Get-AzureRmOperationalInsightsSearchResults para executar pesquisas de registo a partir do PowerShell não irá funcionar com uma área de trabalho atualizada.  Utilize o Invoke-LogAnalyticsQuery para esta funcionalidade com uma área de trabalho atualizada.

Estão disponíveis detalhes em [API de REST de análise de registos do Azure - Cmdlets do PowerShell](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets).

## <a name="log-search-api"></a>API de pesquisa de registo
A pesquisa de registo a REST API foi alterada e qualquer soluções que utilizam a versão legada tem de ser atualizadas para utilizar a nova versão da API.   

Estão disponíveis detalhes sobre a nova versão da API em [API de REST de análise de registos do Azure](https://dev.loganalytics.io/).

## <a name="next-steps"></a>Passos seguintes

- Consulte [pesquisa FAQ e problemas conhecidos de registo de análise de registos nova](log-analytics-log-search-faq.md) para obter uma descrição dos problemas conhecidos com a atualização e as respostas à pergunta comum.