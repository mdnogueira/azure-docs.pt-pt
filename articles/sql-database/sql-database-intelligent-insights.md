---
title: "Monitorizar a utilização de base de dados com Insights inteligente - SQL Database do Azure | Microsoft Docs"
description: "SQL Server da base de dados inteligente as informações do Azure utiliza intelligence incorporado continuamente monitorizar a utilização de base de dados através de artificial intelligence e detetar eventos acontece que causam um fraco desempenho."
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
ms.openlocfilehash: 823855d88396a14ff7e5428a12d71384cdfe95a1
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="intelligent-insights"></a>Informações Inteligentes

SQL Server da base de dados inteligente as informações do Azure permite-lhe saber o que acontece com o desempenho de base de dados.

Insights inteligentes utiliza intelligence incorporado continuamente monitorizar a utilização de base de dados através de artificial intelligence e detetar eventos acontece que causam um fraco desempenho. Depois de detetada, é efetuada uma análise detalhada que gera um registo de diagnóstico com uma avaliação inteligente do problema. Esta avaliação é composta por uma análise da causa raiz do problema de desempenho da base de dados e, sempre que possível, recomendações para melhorias de desempenho. 

## <a name="what-can-intelligent-insights-do-for-you"></a>O que fazer Insights inteligente para si?

Insights inteligentes é uma capacidade exclusiva de intelligence incorporada do Azure que fornece o seguinte valor:

- Monitorização proativa
- Informações acerca do desempenho personalizáveis
- Deteção inicial de degradação do desempenho de base de dados
- Análise de problemas detetados da causa raiz
- Recomendações de melhoramento de desempenho
- Ampliar a capacidade de centenas de milhares de bases de dados
- Impacto positivo para recursos de DevOps e o custo total de propriedade

## <a name="how-does-intelligent-insights-work"></a>Como funciona o Insights inteligente?

Insights inteligentes analisa o desempenho de base de dados SQL, comparando a carga de trabalho da base de dados de última hora com a carga de trabalho de linha de base de sete dias anteriores. Carga de trabalho de base de dados é composta por consultas determinadas para ser mais significativas ao nível de desempenho da base de dados, tais como as consultas mais repetidas e maiores. Uma vez que cada base de dados é exclusivo com base na respetiva estrutura, dados, utilização e aplicação, cada linha de base de carga de trabalho que é gerada é específicos e exclusivo para uma instância individual. Inteligentes Insights, independentemente da linha de base a carga de trabalho, também monitoriza absolutos limiares operacionais e Deteta problemas com tempos de espera excessiva, exceções críticas e problemas relacionados com parameterizations de consulta que poderão afetar o desempenho.

Depois de um problema de degradação de desempenho é detetado a partir de várias métricas observadas utilizando artificial intelligence, a análise é executada. Um registo de diagnóstico é gerado com uma inteligente informações sobre o que acontece com a base de dados. Insights inteligentes torna mais fácil controlar o problema de desempenho de base de dados do seu aspeto primeiro até que a resolução. Cada detetado o problema é controlado através do respetivo ciclo de vida de deteção inicial do problema e a verificação das melhorias de desempenho para a respetiva conclusão. As atualizações são fornecidas no registo de diagnóstico a cada 15 minutos. 

![Fluxo de trabalho de análise de desempenho de base de dados](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

As métricas utilizadas para medir e detetar problemas de desempenho de base de dados são com base na duração de consulta, pedidos de tempo limite, tempos de espera excessivo e pedidos Ocorreu um erro ao. Para obter mais informações sobre as métricas, consulte o [métricas de deteção](sql-database-intelligent-insights.md#detection-metrics) secção deste documento.

Identificou a base de dados SQL degradations de desempenho são registadas no registo de diagnóstico com entradas inteligentes, que consistem em das seguintes propriedades:

| Propriedade             | Detalhes              |
| :------------------- | ------------------- |
| Informações de base de dados | Metadados sobre uma base de dados em que foi detetada uma informação, como um URI do recurso. |
| Intervalo de tempo observado | Hora de início e de fim para o período dos conhecimentos detetado. |
| Métricas afectadas | Métricas que causou uma informação ser gerado: <ul><li>Consulta [segundos] aumentar a duração.</li><li>Aguardar excessiva [segundos].</li><li>Pedidos de excedido [percentagem].</li><li>Ocorreu um erro ao escalamento pedidos [percentagem].</li></ul>|
| Valor de impacto | Valor de uma métrica de medida. |
| Consultas afetadas e códigos de erro | Consultar o código hash ou erro. Estes podem ser utilizados para correlacionar facilmente para consultas afetadas. As métricas que são compostos aumento da duração de consulta, tempo de espera, contagens de tempo limite ou códigos de erro são fornecidas. |
| Deteções | Deteção identificada na base de dados durante a hora de um evento. Existem 15 padrões de deteção. Para obter mais informações, consulte [resolver problemas de desempenho de base de dados do Insights inteligente](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Análise da causa raiz | A causa do problema identificado num formato legível por humanos raiz. Algumas informações podem conter uma recomendação de melhoramento de desempenho, sempre que possível. |
|||

Problemas de desempenho que são registados no registo de diagnóstico são sinalizados com um dos três Estados de um ciclo de vida do problema: "Ativo", "Verificar" e "Concluir". Depois de desempenho foi detetado o problema e como o tempo que tem considerado como presente pelo intelligence incorporado de base de dados SQL, o problema está assinalado como "Ativo". Quando o problema é considerado atenuados, verificá-lo e o estado do problema é alterado para "Verificar". Depois de intelligence incorporada de base de dados SQL considera o problema resolvido, o estado do problema está assinalado como "Concluir".

## <a name="use-intelligent-insights"></a>Utilizar o Insights inteligentes

Inteligentes Insights é um registo de diagnóstico de desempenho inteligente. Pode ser integrado com outros produtos para consumo e aplicações específicas deste tipo são Log Analytics do Azure, Event Hubs do Azure e storage do Azure ou terceiros produtos. 

Insights inteligentes, juntamente com o Log Analytics do Azure é normalmente utilizado para ver as informações através de um browser e, talvez, uma das formas mais fácil para obter desativar zero com utilizar o produto. Insights inteligentes juntamente com os Event Hubs do Azure é normalmente utilizado para configurar a monitorização e alertas cenários personalizados. Insights inteligentes, juntamente com o storage do Azure é normalmente utilizado para o desenvolvimento de aplicações personalizadas, como por exemplo relatórios personalizados, ou talvez arquivo de dados e a obtenção.

Integração do Insights inteligente com outros produtos Log Analytics do Azure, Event Hub do Azure, armazenamento do Azure ou produtos de terceiros para consumo é realizada através do primeiro ativar inteligente das informações no registo (SQLInsights) e, em seguida, configurar Insights inteligentes registar dados para transmissão em fluxo para um destes produtos. Para obter mais informações sobre como ativar o registo de Insights inteligente e configurar dados de registo para transmissão em fluxo para um produto consumo, consulte [métricas da SQL Database do Azure e o registo de diagnóstico](sql-database-metrics-diag-logging.md). 

Para obter uma descrição prática utilizando inteligente conhecimentos aprofundados com o Log Analytics do Azure e para cenários de utilização normal, veja o vídeo incorporado:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Insights inteligentes shines na deteção e de resolução de problemas de desempenho de base de dados SQL. Para utilizar Insights inteligente para resolver problemas de desempenho de base de dados SQL, consulte [problemas de desempenho de resolver problemas relacionados com a base de dados de SQL do Azure com o Insights inteligente](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="set-up-intelligent-insights-with-log-analytics"></a>Configurar o Insights inteligente com a análise de registos 

Inicie sessão solução fornece relatórios de análise e capacidades de informações do inteligente de alerta de registo de diagnóstico dados.

Para utilizar Insights inteligente com a análise de registos, configurar dados de registo de Insights inteligente para transmissão em fluxo para análise de registos, consulte [métricas da SQL Database do Azure e o registo de diagnóstico](sql-database-metrics-diag-logging.md). 

O exemplo seguinte mostra um relatório de Insights inteligente na análise de SQL do Azure:

![Relatório de Insights inteligente](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Depois do registo de diagnóstico Insights inteligente é configurado para dados de fluxo para análise do SQL Server, pode [monitorizar a base de dados do SQL Server utilizando o SQL Server análise](../log-analytics/log-analytics-azure-sql.md).

## <a name="set-up-intelligent-insights-with-event-hubs"></a>Configurar o Insights inteligente com os Event Hubs

Para utilizar Insights inteligente com os Event Hubs, configurar dados de registo de Insights inteligente para transmissão em fluxo para os Event Hubs, consulte [registos de diagnóstico do Azure de fluxo para os Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).

Para utilizar os Hubs de eventos para o programa de configuração personalizada de monitorização e alertas, consulte [nos Event Hubs que fazer com métricas e diagnóstico registos](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs). 

## <a name="set-up-intelligent-insights-with-storage"></a>Configurar inteligente conhecimentos aprofundados com o armazenamento

Para utilizar inteligente Insights com o armazenamento, configurar dados de registo de Insights inteligente para transmissão em fluxo para o armazenamento, consulte [fluxo para o armazenamento de Azure](sql-database-metrics-diag-logging.md#stream-into-storage).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Integrações personalizadas do registo Insights inteligente

Para utilizar Insights inteligente com ferramentas de terceiros ou para a monitorização de desenvolvimento e alertas personalizados, consulte [utilize iniciar o diagnóstico de desempenho de base de dados do Insights inteligente](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Métricas de deteção

Métricas utilizadas para modelos de deteção que geram Insights inteligente baseiam-se na monitorização:

- duração de consulta
- Pedidos de tempo limite
- Tempo de espera excessiva
- Ocorreu um erro ao terminar pedidos

Pedidos de duração e o tempo limite da consulta são utilizados como modelos principais em detetar problemas de desempenho da carga de trabalho de base de dados. São utilizados porque estes medem diretamente o que acontece com a carga de trabalho. Para detetar todos os cenários possíveis de desempenho da carga de trabalho degradação, excessiva de tempo de espera e Ocorreu um erro ao escalamento pedidos são utilizados como modelos adicionais para indicar problemas que afetam o desempenho da carga de trabalho.

O sistema considera automaticamente as alterações à carga de trabalho e alterações ao número de pedidos de consulta efetuadas dinamicamente a base de dados para determinam os limiares de desempenho de base de dados normais e fora do comum.

Todas as métricas são consideradas em conjunto em várias relações através de um modelo de dados scientifically derivada que categoriza cada problema de desempenho detetado. As informações fornecidas através de uma informação inteligente incluem:

* Detalhes do problema de desempenho detetado. 
* Uma análise da causa raiz do problema detetado. 
* Recomendações sobre como melhorar o desempenho da base de dados monitorizado do SQL sempre que possível.

## <a name="query-duration"></a>duração de consulta

O modelo de degradação de duração de consulta analisa consultas individuais e Deteta o aumento no tempo que demora para compilar e executar uma consulta em comparação comparada a linha de base de desempenho.

Se intelligence incorporada de base de dados SQL Deteta um aumento significativo na compilação da consulta ou tempo de execução de consulta que afeta o desempenho da carga de trabalho, estas consultas são sinalizadas como duração de consulta problemas de degradação de desempenho. 

O registo de diagnóstico Insights inteligente produz o hash de consulta da consulta está degradada no desempenho. O hash de consulta indica se a degradação do desempenho foi relacionada para consulta compilação ou tempo de execução aumento, que aumentado consulta tempo de duração.

## <a name="timeout-requests"></a>Pedidos de tempo limite

O modelo de degradação de pedidos de tempo limite analisa consultas individuais e Deteta algum aumento na tempos limite ao nível de execução da consulta e nos tempos de pedido limite se global ao nível da base de dados em comparação comparado o período de linha de base de desempenho.

Algumas das consultas poderão tempo limite, mesmo antes de atingirem a fase de execução. Através de meios dos trabalhadores abortadas vs. os pedidos efetuados, intelligence incorporada de base de dados SQL avalia e analisa todas as consultas atingiu a base de dados se recebeu para a fase de execução ou não. 

Depois do número de tempos limite para consultas executados ou o número de trabalhadores de pedido abortada atravesse o limiar de sistema gerido, um registo de diagnóstico é preenchido com insights inteligentes.

As informações geradas contêm o número de pedidos de excedido e o número de consultas excedido. Indicação a degradação do desempenho está relacionada com aumento de limite de tempo a fase de execução ou o nível de base de dados global é fornecido. Quando o aumento de tempos limite é considerado significativo para o desempenho da base de dados, estas consultas são sinalizadas como problemas de degradação de desempenho do tempo limite. 

## <a name="excessive-wait-times"></a>Tempos de espera excessiva

O modelo de tempo de espera excessivo monitoriza consultas de base de dados individuais. Deteta estatísticas de espera de consulta invulgarmente elevada ultrapassados os limiares absolutos gerida pelo sistema. As seguinte consulta excessivo tempo de espera as métricas são observadas utilizando a nova funcionalidade de servidor de SQL, consulta arquivo Aguarde estatísticas (sys.query_store_wait_stats):

- Por se terem atingido os limites de recursos
- Por se terem atingido os limites recursos de agrupamento elástico
- Número excessivo de threads de trabalho ou de sessão
- Bloqueio excessivo da base de dados
- Pressão de memória
- Outras estatísticas de espera

Por se terem atingido os limites de recursos ou limites de recursos do conjunto elástico denotam que o consumo de recursos disponíveis em subscrições ou no agrupamento elástico ultrapassados limiares absolutos. Estas estatísticas indicam degradação do desempenho de cargas de trabalho. Um número excessivo de threads de trabalho ou de sessão indica uma condição em que o número de threads de trabalho ou sessões iniciadas ultrapassados limiares absolutos. Estas estatísticas indicam degradação do desempenho de cargas de trabalho.

Base de dados excessivo bloqueio indica uma condição em que a contagem de bloqueios numa base de dados ultrapassou limiares absolutos. Este stat indica uma degradação do desempenho de cargas de trabalho. Memória pressão é uma condição em que o número de threads pedir memória concede ultrapassou um limite absoluto. Este stat indica uma degradação do desempenho de cargas de trabalho.

Deteção de estatísticas outros espera indica uma condição em que diversas métricas medidas através de estatísticas de espera de arquivo para consulta ultrapassou um limite absoluto. Estas estatísticas indicam degradação do desempenho de cargas de trabalho.

Depois de tempos de espera excessivo são detetados, consoante os dados disponíveis, o diagnóstico Insights inteligente registo saídas hashes das consultas que afetam e afetados degradados no desempenho, os detalhes das métricas que fazer com que as consultas de espera em execução, e tempo de espera de medida.

## <a name="errored-requests"></a>Ocorreu um erro ao pedidos

Ocorreu um erro ao pedidos degradação modelo de monitores individuais consultas e Deteta um aumento no número de consultas que ocorreu um erro ao terminar em comparação com o período de linha de base. Este modelo também monitoriza as exceções críticas que ultrapassados limiares absolutos geridos pelo intelligence incorporado de base de dados SQL. O sistema considera automaticamente o número de pedidos de consulta efetuadas para a base de dados e contas para efetuar quaisquer alterações de carga de trabalho no período de monitorizado.

Quando o aumento medido em pedidos Ocorreu um erro ao relativamente ao número de pedidos efetuados geral é considerado significativo para o desempenho da carga de trabalho, consultas afetadas são sinalizadas como problemas de degradação de desempenho de pedidos Ocorreu um erro ao.

O registo de Insights inteligente produz a contagem de pedidos Ocorreu um erro ao. Indica se a degradação do desempenho foi relacionados com um aumento de pedidos Ocorreu um erro ao ou sobreutilização um limiar monitorizado exceção crítica e a hora medida a degradação do desempenho. 

Se qualquer uma das exceções críticas monitorizadas cruzada os limiares absolutos geridos pelo sistema, é gerada uma informação inteligente com detalhes de exceção crítica.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [resolver problemas de desempenho de base de dados SQL com o Insights inteligente](sql-database-intelligent-insights-troubleshoot-performance.md).
* Utilize o [registo de diagnóstico de desempenho de base de dados de SQL inteligente Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
* Saiba como [monitorizar a base de dados SQL, através da análise do SQL Server](../log-analytics/log-analytics-azure-sql.md).
* Saiba como [recolher e consumir dados de registo dos seus recursos do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


