---
title: "Análise - a pesquisa de elevado desempenho e a ferramenta de consulta do Azure Application Insights | Microsoft Docs"
description: "Descrição geral da análise, a ferramenta de pesquisa de diagnóstico de elevado desempenho do Application Insights. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: adda6335b702470cd491f07d750236c368325a9e
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="analytics-in-application-insights"></a>Análise no Application Insights
A análise é a ferramenta de pesquisa e a consulta poderosa de [Application Insights](app-insights-overview.md). Análise é uma ferramenta web, pelo que não é necessária nenhuma configuração. Se já tiver configurado o Application Insights para uma das suas aplicações, em seguida, pode analisar os dados da sua aplicação, abrindo a análise da sua aplicação [painel Descrição geral](app-insights-dashboards.md).

![Abrir portal.azure.com, abra o recurso do Application Insights e clique em análise.](./media/app-insights-analytics/001.png)

Também pode utilizar o [playground análise](https://go.microsoft.com/fwlink/?linkid=859557) que é um ambiente de demonstração gratuita com uma grande quantidade de dados de exemplo.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Consultar dados nas análise
Uma consulta típica começa com um nome de tabela seguido por uma série de *operadores* separados por `|`.
Por exemplo, vamos descobrir o número de pedidos nossa aplicação recebida de diferentes países, durante as últimos 3 horas:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Iremos começar com o nome da tabela *pedidos* e adicionar elementos direcionados conforme necessário.  Primeiro, iremos definir um filtro de tempo para rever apenas os registos da últimas 3 horas.
Iremos contagem, em seguida, o número de registos por país (que dados são encontrados na coluna *client_CountryOrRegion*). Por fim, vamos compor resultados de um gráfico circular.
<br>

![Resultados da consulta](./media/app-insights-analytics/030.png)

O idioma tem muitas funcionalidades apelativo:

* [Filtro](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) a telemetria de aplicações não processados por quaisquer campos, incluindo as métricas e propriedades personalizadas.
* [Associar](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) várias tabelas – correlacione pedidos com vistas de página, chamadas de dependência, exceções e rastreios de registo.
* Poderosas análises [agregações](https://docs.loganalytics.io/docs/Learn/Tutorials/Aggregation-functions).
* Visualizações imediatas e poderosas.
* [REST API](https://dev.applicationinsights.io/) que pode utilizar para executar consultas através de programação, por exemplo a partir do PowerShell.

O [completa referência de linguagem](https://go.microsoft.com/fwlink/?linkid=856079) fornece detalhes sobre cada comando suportado e atualiza regularmente.

## <a name="next-steps"></a>Passos seguintes
* Começar a utilizar o [portal da análise](https://go.microsoft.com/fwlink/?linkid=856587)
* Introdução ao [escrever consultas](https://go.microsoft.com/fwlink/?linkid=856078)
* Reveja o [dos SQL Server-utilizadores cheat folha](https://aka.ms/sql-analytics) para traduções dos idioms mais comuns.
* Experimente a análise no nosso [playground](https://analytics.applicationinsights.io/demo) se a aplicação não está a enviar dados para o Application Insights ainda.
* Veja o [as vídeo introdutórias](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).