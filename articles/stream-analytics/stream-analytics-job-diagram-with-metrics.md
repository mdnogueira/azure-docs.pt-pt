---
title: "O Azure Stream Analytics condicionada por dados depuração utilizando o diagrama de tarefa | Microsoft Docs"
description: "Resolver problemas relacionados com a sua tarefa do Stream Analytics utilizando o diagrama de tarefa e as métricas."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/01/2017
ms.author: samacha
ms.openlocfilehash: 5b689c07bf8baa531c7a50ca50ed5140c1787e7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Depuração utilizando o diagrama de tarefa condicionada por dados

O diagrama de tarefa no **monitorização** painel no portal do Azure pode ajudar a visualizar o pipeline de tarefa. Mostra entradas, saídas e passos de consulta. Pode utilizar o diagrama de tarefa para examinar as métricas para cada passo, mais rapidamente isolar a origem de um problema ao resolver problemas.

## <a name="using-the-job-diagram"></a>Utilizar o diagrama de tarefa

No portal do Azure, enquanto uma tarefa de Stream Analytics, sob **suporte + resolução de problemas**, selecione **diagrama de tarefa**:

![Diagrama de tarefa com a métrica - localização](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selecione cada passo de consulta para ver a secção correspondente numa painel de edição de consultas. Um gráfico de métrico para o passo é apresentado num painel inferior da página.

![Diagrama de tarefa com a métrica - tarefa básico](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Para ver as partições da entrada de Event Hubs do Azure, selecione **...** É apresentado um menu de contexto. Também pode ver o fusão de entrada.

![Diagrama de tarefa com a métrica - expanda partição](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Para ver o gráfico métrico para apenas uma única partição, selecione o nó de partição. As métricas são apresentadas na parte inferior da página.

![Diagrama de tarefa com a métrica - as métricas mais](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Para ver o gráfico de métricas para uma fusão, selecione o nó de fusão. O gráfico seguinte mostra que não há eventos foram removidos ou ajustados.

![Diagrama de tarefa com a métrica - grelha](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Para ver os detalhes do tempo e valor métrico, aponte para o gráfico.

![Diagrama com a métrica de tarefa - coloque o cursor](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Resolver problemas utilizando as métricas

O **QueryLastProcessedTime** métrica indica que um passo específico recebeu dados. Ao observar a topologia, pode trabalhar trás do processador de saída para ver qual passo não está a receber dados. Se um passo não está a obter dados, vá para o passo de consulta imediatamente antes da mesma. Verifique se o passo de consulta anterior tenha uma janela de tempo e, se houver tempo suficiente foi efectuada com êxito para o mesmo para dados de saída. (Tenha em atenção que tempo windows são ajustado para a hora.)
 
Se o passo de consulta anterior é um processador de entrada, utilize as métricas de entrada para o ajudar a resposta seguintes questões de destino. Podem ajudar a determinar se uma tarefa está a obter dados da sua origem de entrada. Se a consulta estiver particionada, examine cada partição.
 
### <a name="how-much-data-is-being-read"></a>Quantidade de dados está a ser lido?

*   **InputEventsSourcesTotal** é o número de unidades de dados de leitura. Por exemplo, o número de blobs.
*   **InputEventsTotal** é o número de eventos de leitura. Esta métrica está disponível por partição.
*   **InputEventsInBytesTotal** é o número de bytes lidos.
*   **InputEventsLastArrivalTime** é atualizado com a hora de colocados em fila de todos os eventos recebidos.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Tempo está a mover reencaminhar? Se real eventos são lidos, pontuação não pode ser emitida.

*   **InputEventsLastPunctuationTime** indica quando uma pontuação foi emitida para fazer com que a hora continue a avançar. Se pontuação não é emitida, o fluxo de dados pode obter bloqueado.
 
### <a name="are-there-any-errors-in-the-input"></a>Existem erros na entrada?

*   **InputEventsEventDataNullTotal** é uma contagem de eventos que tenham dados nulo.
*   **InputEventsSerializerErrorsTotal** é uma contagem dos eventos que não foi anulada corretamente.
*   **InputEventsDegradedTotal** é uma contagem dos eventos que tenha tido um problema diferente com a anulação da serialização.
 
### <a name="are-events-being-dropped-or-adjusted"></a>São eventos que está a ser removidos ou ajustados?

*   **InputEventsEarlyTotal** é o número de eventos que tenham um carimbo de aplicação antes da marca d'água alta.
*   **InputEventsLateTotal** é o número de eventos com um carimbo de uma aplicação depois da marca d'água alta.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** é os número eventos removidos antes da hora de início da tarefa.
 
### <a name="are-we-falling-behind-in-reading-data"></a>São iremos baixar ao ler os dados?

*   **InputEventsSourcesBackloggedTotal** indica quantos mais mensagens necessita para ser de leitura para as entradas de IoT Hub do Azure e Hubs de eventos.


## <a name="get-help"></a>Obter ajuda
Para obter assistência adicional, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da REST API de gestão de análise de fluxo](https://msdn.microsoft.com/library/azure/dn835031.aspx)
