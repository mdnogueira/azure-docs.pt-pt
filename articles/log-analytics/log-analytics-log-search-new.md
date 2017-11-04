---
title: "Início de sessão pesquisas Log Analytics do Azure | Microsoft Docs"
description: "Requer uma pesquisa de registo para obter os dados da análise de registos.  Este artigo descreve a forma como o novo registo pesquisas são utilizadas na análise de registos e fornece os conceitos que tem de compreender antes de criar um."
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
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 5f040d1480433ccf4c0b2b22c0cf1e25a7151d74
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="understanding-log-searches-in-log-analytics"></a>Noções sobre pesquisas de registo na análise de registos

Requer uma pesquisa de registo para obter os dados da análise de registos.  Se estiver a analisar os dados no portal, configurar uma regra de alerta para ser notificado de uma condição específica ou obtenção de dados utilizando a API de análise do registo, utilizará uma pesquisa de registo para especificar os dados que pretende.  Este artigo descreve como as pesquisas de registo são utilizadas na análise de registos e fornece os conceitos que devem saber antes de criar um. Consulte o [passos](#next-steps) secção para obter detalhes sobre a criação e edição de pesquisas de registo e para as referências na linguagem de consulta.

## <a name="where-log-searches-are-used"></a>Onde as pesquisas de registo são utilizadas

As diferentes formas em que irá utilizar pesquisas de registo na análise de registos incluem o seguinte:

- **Portais.** Pode efetuar análises interativas dos dados no repositório no portal do Azure ou o [portal da análise avançadas](https://go.microsoft.com/fwlink/?linkid=856587).  Isto permite-lhe editar a consulta e analise os resultados numa variedade de formatos e visualizações.  A maioria das consultas que criar serão iniciado dos portais e, em seguida, copiado depois de verificar se funciona conforme esperado.
- **Regras de alertas.** [Regras de alerta](log-analytics-alerts.md) proativamente identificar problemas de dados na sua área de trabalho.  Cada regra de alerta baseiam-se uma pesquisa de registo que é executada automaticamente em intervalos regulares.  Os resultados serão inspecionados para determinar se deve ser criado um alerta.
- **Vistas.**  Pode criar visualizações de dados a ser incluído nos dashboards do utilizador com [estruturador de vistas](log-analytics-view-designer.md).  Registo pesquisas fornecerem os dados utilizados pelo [mosaicos](log-analytics-view-designer-tiles.md) e [partes de visualização](log-analytics-view-designer-parts.md) em cada vista.  Pode explorar para baixo de partes de visualização a página de pesquisa de registo para efetuar mais análise nos dados.
- **Exportar.**  Quando exporta dados da área de trabalho de análise de registos para o Excel ou [Power BI](log-analytics-powerbi.md), cria uma pesquisa de registo para definir os dados para exportar.
- **PowerShell.** Pode executar um script do PowerShell a partir de uma linha de comandos ou um runbook de automatização do Azure que utiliza [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) para obter dados de análise de registos.  Este cmdlet requer uma consulta para determinar os dados a obter.
- **API de análise do registo.**  O [API de pesquisa de registo de análise de registos](log-analytics-log-search-api.md) permite que qualquer cliente de REST API obter dados da área de trabalho.  O pedido de API inclui uma consulta que é executada em relação a análise de registos para determinar os dados a obter.

![Pesquisas de registo](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Como os dados de análise de registos são organizados
Quando criar uma consulta, que comece por determinar que tabelas contêm os dados que procura. Cada [origem de dados](log-analytics-data-sources.md) e [solução](../operations-management-suite/operations-management-suite-solutions.md) armazena os respetivos dados nas tabelas dedicadas na área de trabalho de análise de registos.  Documentação para cada origem de dados e a solução inclui o nome do tipo de dados que cria e uma descrição de cada uma das respetivas propriedades.  Demasiadas consultas apenas exigirá dados a partir de um único tabelas, mas outros utilizadores podem utilizar uma variedade de opções para incluir dados de várias tabelas.

![Tabelas](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>Escrever uma consulta
O núcleo do registo de pesquisa na análise de registos é [um idioma de consulta extensas](https://docs.loganalytics.io/) que lhe permite obter e analisar dados do repositório de diversas formas.  Neste mesmo idioma de consulta é utilizado para [Application Insights](../application-insights/app-insights-analytics.md).  Aprender a escrever uma consulta é fundamental para a criação de pesquisas de registo na análise de registos.  Irá iniciar normalmente com consultas básicas e, em seguida, avança para utilizar a funções mais avançadas, como os seus requisitos se tornam mais complexos.

A estrutura básica de uma consulta é uma tabela de origem, seguida de uma série de operadores separados por um caráter de pipe `|`.  Em conjunto pode encadeiam múltiplos operadores para refinar os dados e realizar funções avançadas.

Por exemplo, suponha que pretendia localizar os computadores principais dez com a maior parte dos eventos de erro ao longo do último dia.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Ou, talvez que pretende localizar os computadores que ainda não tinham um heartbeat no último dia.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

Como vai um gráfico de linhas com a utilização do processador para cada computador da última semana?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

Pode ver partir estes exemplos rápidos que independentemente do tipo de dados que está a trabalhar com a estrutura da consulta é semelhante.  Pode divida-lo para baixo em diferentes passos onde os dados resultantes de um comando são enviados através do pipeline para o comando seguinte.

Também pode consultar dados nas áreas de trabalho de análise de registos na sua subscrição.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


Para obter documentação completa sobre o idioma de consulta de análise de registos do Azure, incluindo tutoriais e referência de linguagem, consulte o [documentação de idioma de consulta de análise de registos do Azure](https://docs.loganalytics.io/).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [portais que utilizar para criar e editar registo pesquisas](log-analytics-log-search-portals.md).
- Veja uma [tutorial sobre como escrever consultas](log-analytics-tutorial-viewdata.md) utilizando a linguagem de consulta de novo.
