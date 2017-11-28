---
title: "O Azure Stream Analytics: Compreender e ajustar as unidades de transmissão em fluxo | Microsoft Docs"
description: Compreenda os fatores afetam o desempenho no Azure Stream Analytics.
keywords: "unidade de transmissão em fluxo, consultar o desempenho"
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e1fb9ee3147f94b173b0fd324943b8801b984d2b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="understand-and-adjust-streaming-units"></a>Compreender e ajustar as unidades de transmissão em fluxo

O Azure Stream Analytics agrega o desempenho "ponderação" em execução uma tarefa em unidades de transmissão em fluxo (SUs). SUs representam os recursos informáticos que são consumidos para executar uma tarefa. As SUs proporcionam uma forma de descrever a capacidade de processamento do evento relativo com base numa medida combinada de CPU, memória e velocidades de leitura e escrita. Esta permite capacidade focar-se na lógica de consulta e remove a necessidade de saber o armazenamento de camada de considerações de desempenho, alocar memória para a tarefa manualmente e aproximada a core-contagem de da CPU necessária para executar a tarefa de uma forma atempada.

Para alcançar a latência baixa processamento de transmissão em fluxo, as tarefas do Azure Stream Analytics executar todo o processamento na memória. Quando executar memória esgotada, falha a tarefa de transmissão em fluxo. Como resultado, para uma tarefa de produção, é importante monitorizar a utilização de recursos de uma tarefa de transmissão em fluxo e certifique-se de que existe suficiente recursos alocados para manter as tarefas em execução 24/7.

A métrica é um número de percentagem vão de % de 0 a 100%. Para uma tarefa de transmissão em fluxo com requisitos de espaço mínimo, a métrica de utilização do SU % é, normalmente, entre 10% a 20%. É melhor manter a métrica inferior a 80% para caber picos ocasionais.  Pode definir um alerta na métrica (consulte [aqui para configurar alertas métricas](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/insights-alerts-portal)).



## <a name="configure-stream-analytics-streaming-units-sus"></a>Configurar o do Stream Analytics (SUs) de unidades de transmissão em fluxo
1. Inicie sessão no [portal do Azure](http://portal.azure.com/)
2. Na lista de recursos, localize a tarefa de Stream Analytics que pretende dimensionar e, em seguida, abri-lo. 
3. Na página de tarefa, em configurar, clique em **escala**. 

    ![Configuração de tarefa do Stream Analytics portal do Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Utilize o controlo de deslize para definir os SUs para a tarefa. Tenha em atenção de que está limitado a definições de SU específicas. 


## <a name="monitor-job-performance"></a>Monitorizar o desempenho de tarefa
No portal do Azure, pode controlar o débito de uma tarefa:

![O Azure Stream Analytics monitorizar tarefas][img.stream.analytics.monitor.job]

Calcule o débito esperado da carga de trabalho. Se o débito é menor que o esperado, otimizar a partição da entrada, otimizar a consulta e adicionar SUs ao seu trabalho.


## <a name="how-many-sus-are-required-for-a-job"></a>SUs quantos são necessários para uma tarefa?

Escolher o número de SUs necessárias para uma determinada tarefa depende da configuração de partição para as entradas e a consulta que está definida dentro da tarefa. O **escala** página permite-lhe definir o número correto de SUs. É recomendado alocar mais SUs que os necessários. O motor de processamento de Stream Analytics otimiza para latência e débito ao custo de atribuição de memória adicional.

Em geral, a melhor prática é iniciar com 6 SUs para consultas que não utilizem **PARTITION BY**. Em seguida, determine o lugar para cima sweet utilizando um método de avaliação e erro na qual pode modificar o número de SUs depois de passar representativos quantidades de dados e examine a métrica de utilização do SU %.

Para obter mais informações sobre como escolher o número correto de SUs, consulte esta página: [tarefas de escala do Azure Stream Analytics para aumentar o débito](stream-analytics-scale-jobs.md)

> [!Note]
> Escolher SUs quantos são necessários para uma tarefa específica depende da configuração de partição para as entradas e a consulta definida para a tarefa. Pode selecionar até a sua cota de SUs para uma tarefa. Por predefinição, cada subscrição do Azure tem uma quota de 200 até SUs para todas as tarefas de análise numa região específica. Para aumentar o SUs para as suas subscrições depois desta quota, contacte [Microsoft Support](http://support.microsoft.com). Os valores válidos para SUs por tarefa são 1, 3, 6 e até em incrementos de 6.



## <a name="factors-increasing-su-utilization"></a>Fatores aumentar % de utilização de SU 
### <a name="stateful-query-logic"></a>Lógica de monitorização de estado de consulta 
Um da capacidade exclusiva da tarefa do Azure Stream Analytics consiste em efetuar o processamento de monitorização de estado, como as agregações, associações temporais e as funções analíticas temporais. Cada um destes operadores mantém algumas Estados. 
#### <a name="windowed-aggregates"></a>As agregações
O tamanho do Estado de uma agregação em janela é proporcional ao número de grupos (cardinalidade) no grupo de pela operadora de rede. Por exemplo, a consulta seguinte, o número associado clusterid é a cardinalidade da consulta. 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


Para poder ameliorate problemas causados por cardinalidade elevada na consulta anterior, pode enviar eventos para Event Hub particionada por ' clusterid ' e a consulta de escalamento horizontal, permitindo que o sistema para processar cada partição de entrada com o separadamente **PARTITION BY**  conforme mostrado no exemplo abaixo:


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado, o número de clusterid entra em cada nó é reduzido, reduzindo a cardinalidade do grupo pela operadora de rede. 

As partições do Hub de eventos devem ser particionadas pela chave de agrupamento para evitar a necessidade de um passo reduzido. Detalhes adicionais são abordados [aqui](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-overview). 
#### <a name="temporal-join"></a>Associação temporal
O tamanho do Estado de uma associação temporal é proporcional ao número de eventos na sala de associação, o que é a taxa de entrada de eventos um múltiplo pelo tamanho de espaço wiggle temporal wiggle. 

O número de eventos sem correspondência na União afetar a utilização de memória para a consulta. A seguinte consulta está à procura de encontrar as visualizações de anúncios que geram cliques:

    SELECT id
    FROM clicks 
    INNER JOIN, impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

Neste exemplo, é possível que muitos dos anúncios são apresentados algumas pessoas clicar na mesma e é necessário para manter todos os eventos na janela de tempo. A memória consumida é proporcional ao tamanho da janela e à velocidade dos eventos. 

Para corrigir isto, enviar eventos para o Hub de eventos particionado as chaves de associação (id neste caso) e a consulta de escalamento horizontal, permitindo que o sistema para processar cada partição de entrada com o separadamente **PARTITION BY** conforme mostrado:

    SELECT id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clocks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

Quando a consulta estiver particionada, é espalhada por vários nós. Como resultado o número de eventos provenientes em cada nó é reduzido, reduzindo o tamanho do estado mantido na janela de associação. 
#### <a name="temporal-analytic-function"></a>Função analíticas temporal
O tamanho do Estado de uma função analíticas temporal é proporcional à taxa de eventos um múltiplo pela duração. A remediação é semelhante à associação temporal. Pode ampliar a consulta utilizando **PARTITION BY**. 

### <a name="out-of-order-buffer"></a>Memória intermédia fora de ordem 
Utilizador pode configurar o tamanho de memória intermédia fora de ordem, no caso de encomenda de painel de configuração. A memória intermédia é utilizada para conter entradas para a duração da janela e reordenar. O tamanho da memória intermédia é proporcional à taxa de entrada de eventos um múltiplo pelo tamanho da janela fora de ordem. O tamanho da janela predefinido é 0. 

Para corrigir isto, aumentar horizontalmente a consulta utilizando **PARTITION BY**. Quando a consulta estiver particionada, é espalhada por vários nós. Como um resultados o número de eventos provenientes em cada nó é reduzido, reduzindo o número de eventos numa memória intermédia cada reordenação. 

### <a name="input-partition-count"></a>Contagem da partição de entrada 
Cada partição de entrada de uma tarefa de entrada tem uma memória intermédia. O maior número de partições de entrada, o mais recursos consome a tarefa. Para cada SU, o Azure Stream Analytics pode processar aproximadamente 1 MB/s de entrada, pelo que poderá querer corresponde ao número de ASA SU com o número de partição do seu Hub de eventos. Normalmente, a tarefa 1SU é suficiente para um Hub de eventos com 2 partições (qual é o mínimo para o Hub de eventos). Se o Hub de eventos tem mais partições, a tarefa do ASA consumir mais recursos, mas não necessariamente utiliza o débito adicional fornecido pelo Hub de eventos. Para uma tarefa de 6SU, poderá necessitar de partições de 4 ou 8 do Hub de eventos. Utilizando um Hub de eventos com 16 partições ou superior num 1SU tarefa frequentemente contribui para a utilização de recursos excessivos e deve ser evitada. 

### <a name="reference-data"></a>Dados de referência 
Dados de referência do ASA são carregados na memória para a pesquisa rápida. Com a implementação atual, cada operação de associação com dados de referência mantém uma cópia dos dados de referência na memória, mesmo se associar os mesmos dados de referência várias vezes. Para consultas com **PARTITION BY**, cada partição tem uma cópia dos dados de referência, pelo que as partições são completamente desassociadas. Com o efeito de multiplicador, utilização de memória pode rapidamente muito elevada se associar dados de referência várias vezes com várias partições.  

#### <a name="use-of-udf-functions"></a>Utilização de funções UDF
Quando adiciona uma função UDF, o Azure Stream Analytics carrega o tempo de execução do JavaScript para a memória. Isto irá afetar a % SU.


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Criar consultas paralelizáveis no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Tarefas do Stream Analytics do Azure para aumentar o débito de escala](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
