---
title: "Registo de inteligente diagnósticos de desempenho do Insights - SQL Database do Azure | Microsoft Docs"
description: "Inteligentes Insights fornece um registo de diagnóstico de problemas de desempenho de SQL Database do Azure"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 358986f58c431aebfe7b41daa8c40ba641dc408a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Utilizar o registo de diagnóstico de desempenho inteligente Insights SQL Database do Azure

Esta página fornece informações sobre como utilizar o registo de diagnóstico de desempenho de SQL Database do Azure gerado pelo [Insights inteligente](sql-database-intelligent-insights.md), o formato e os dados que contém para o desenvolvimento personalizado tem. Pode enviar este registo de diagnóstico para [Log Analytics do Azure](../log-analytics/log-analytics-azure-sql.md), [Event Hubs do Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Storage do Azure](sql-database-metrics-diag-logging.md#stream-into-storage), ou uma solução de terceiros para DevOps personalizado alertas e relatórios capacidades.

## <a name="log-header"></a>Cabeçalho de registo

O registo de diagnóstico utiliza o formato de padrão de JSON para a saída findings Insights inteligente. A propriedade categoria exato para aceder a um registo de Insights inteligente é o valor fixo "SQLInsights".

O cabeçalho do registo é comum e é composto o carimbo de hora (TimeGenerated) que mostra que foi criada uma entrada. Também inclui um ID de recurso (ResourceId) refere-se a base de dados do SQL Server específica relacionada com a entrada. A categoria (categoria), o nível (nível) e o nome da operação (Oeprationname) sejam corrigidos propriedades cujos valores não alteram. Indicam que a entrada de registo se a fins informativa e que são provenientes de Insights inteligente (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID do problema e a base de dados afetadas

A propriedade de identificação do problema (issueId_d) fornece uma forma de controlar exclusivamente problemas de desempenho até que o se estiver resolvidos. Insights inteligentes situa cada ciclo de vida do problema como "Ativo", "Verificar" ou "Completo". Através de cada um destes fases de estado, Insights inteligente pode registar vários registos de eventos no registo. Para cada um destas entradas, o número de ID do problema permanece exclusivo. Insights inteligentes controla o problema através do respetivo ciclo de vida e gera um insight no registo de diagnóstico a cada 15 minutos.

Depois de é detetado um problema de desempenho e para dura-lo, desde que o problema é reportado como "Ativo" sob a propriedade de estado (status_s). Depois de um problema detetado é atenuado, tem de verificar e reportados como "Verificar" como a propriedade de estado (status_s). Se o problema já não estiver presente, a propriedade de estado (status_s) relatórios este problema, como "Concluir".

Juntamente com o ID do problema, o registo de diagnóstico relatórios de início (intervalStartTime_t) e carimbos de hora de fim (intervalEndTme_t) do evento específico relacionada com um problema reportado no registo de diagnóstico.

A propriedade de conjunto elástico (elasticPoolName_s) indica que pertence a base de dados com um problema de conjunto elástico. Se a base de dados não faz parte de um conjunto elástico, esta propriedade não tem um valor. A base de dados em que foi detetado um problema das é divulgado na propriedade de nome (databaseName_s) da base de dados.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Problemas detetados

A secção seguinte do registo de desempenho Insights inteligente contém problemas de desempenho que foram detetados através de incorporado artificial intelligence. As deteções são das divulgados nas propriedades no registo de diagnóstico de JSON. Estas deteções consistem a categoria de um problema, o impacto do problema, as consultas afetadas e as métricas. As propriedades de deteções de Trojans podem conter vários problemas de desempenho que foram detetados.

Forem reportados problemas de desempenho detetado com a estrutura de propriedade de deteções seguinte:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Padrões de desempenho detetável e os detalhes que são debitados no registo de diagnóstico são fornecidos na tabela seguinte.

### <a name="detection-category"></a>Categoria de deteção

A propriedade categoria (categoria) descreve a categoria de padrões de desempenho detectable. Consulte a tabela seguinte para todas as categorias possíveis de padrões de desempenho detectable. Para obter mais informações, consulte [resolver problemas de desempenho de base de dados do Insights inteligente](sql-database-intelligent-insights-troubleshoot-performance.md).

Consoante o problema de desempenho detetado, os detalhes debitados no diagnóstico do ficheiro de registo diferem em conformidade.

| Padrões de desempenho detetável | Detalhes debitados |
| :------------------- | ------------------- |
| Por se terem atingido os limites de recursos | <li>Recursos afetados</li><li>Hashes de consulta</li><li>Percentagem de consumo de recursos</li> |
| Aumento de carga de trabalho | <li>Número de consultas cuja execução aumentado</li><li>Hashes de consulta de consultas com maior contribuição para o aumento de carga de trabalho</li> |
| Pressão de memória | <li>Clerk de memória</li> |
| Bloqueio | <li>Afetados hashes de consulta</li><li>Bloquear os hashes de consulta</li> |
| Aumento MAXDOP | <li>Hashes de consulta</li><li>Tempos de espera CXP</li><li>Tempos de espera</li> |
| Pagelatch contenção | <li>Os hashes de consultas, fazendo com que a contenção de consulta</li> |
| Índice em falta | <li>Hashes de consulta</li> |
| Nova consulta | <li>Consulta de hash das consultas novo</li> |
| Estatística de espera invulgar | <li>Tipos de atividade invulgar espera</li><li>Hashes de consulta</li><li>Tempos de espera de consulta</li> |
| TempDB contenção | <li>Os hashes de consultas, fazendo com que a contenção de consulta</li><li>Atribuição de consulta para a base de dados pagelatch contenção espera tempo geral [%]</li> |
| Falta DTU do conjunto elástico | <li>Agrupamento elástico</li><li>Base de dados de consumo de DTU superior</li><li>Percentagem de DTU utilizada pelo consumidor superior do conjunto</li> |
| Plano de regressão | <li>Hashes de consulta</li><li>Bom plano IDs</li><li>Os IDs de plano incorreto</li> |
| Alteração de valor de configuração de âmbito de base de dados | <li>Alterações de configuração de âmbito de base de dados em comparação comparadas os valores predefinidos</li> |
| Cliente lenta | <li>Hashes de consulta</li><li>Tempos de espera</li> |
| Mudança para versão anterior do escalão de preço | <li>Notificação de texto</li> |

### <a name="impact"></a>Impacto

O impacto (impacto) propriedade descreve quanto um comportamento detetado contribuído para o problema que está a ter uma base de dados. Intervalo de impactos de 1 a 3, com 3 como contribuição mais elevada, 2 como moderada e 1, pois a contribuição mais baixa. O valor de impacto pode ser utilizado como entrada para a automatização de alerta personalizada, consoante as suas necessidades específicas. As consultas de propriedade afetados (QueryHashes) fornecem uma lista da consulta hashes que foram afetados por uma deteção específica.

### <a name="impacted-queries"></a>Consultas afectadas

A secção seguinte do registo inteligente Insights fornece informações sobre consultas específicas que foram afetados por problemas de desempenho detetado. Estas informações das são divulgadas como uma matriz de objetos incorporados na propriedade impact_s. A propriedade impacto é constituído por entidades e as métricas. Entidades de mensagens em fila referir-se a uma consulta específica (tipo: consulta). O hash de consulta exclusivo das é divulgado sob a propriedade value (valor). Além disso, as consultas das divulgados é seguido por uma métrica e um valor que indica um problema de desempenho detetado.

No exemplo de registo seguinte, a consulta com o hash 0x9102EXZ4 foi detetada a ter uma duração de aumento de execução (métrica: DurationIncreaseSeconds). O valor de segundos 110 indica que esta consulta específica demorou 110 segundos já está a executar. Porque podem ser detetadas várias consultas, esta secção de registo específico pode incluir várias entradas de consulta.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Métricas

A unidade de medida para cada métrica comunicada é fornecida ao abrigo a propriedade de métrica (Métrica) com os valores possíveis de segundos, o número e percentagem. O valor de uma métrica de medida é reportado na propriedade value (valor).

A propriedade DurationIncreaseSeconds fornece a unidade de medida em segundos. A unidade de CriticalErrorCount da medida é um número que representa uma contagem de erros.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Recomendações de análise e melhoramento da causa raiz

A última parte do registo de desempenho Insights inteligente diz respeito à análise da causa raiz automatizada do problema de degradação de desempenho identificados. As informações aparecem no compatível com humanos verbiage na propriedade do Analysis Services (rootCauseAnalysis_s) de causa raiz. Recomendações de melhoria estão incluídas no registo de sempre que possível.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Pode utilizar o registo de desempenho Insights inteligente com [Log Analytics do Azure]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) ou uma solução de terceiros para DevOps personalizado alertas e as capacidades de relatórios.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [Insights inteligente](sql-database-intelligent-insights.md) conceitos.
- Saiba como [resolver problemas de desempenho de SQL Database do Azure com o Insights inteligente](sql-database-intelligent-insights-troubleshoot-performance.md).
- Saiba como [monitorizar SQL Database do Azure utilizando o Azure SQL Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql).
- Saiba como [recolher e consumir dados de registo dos seus recursos do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



