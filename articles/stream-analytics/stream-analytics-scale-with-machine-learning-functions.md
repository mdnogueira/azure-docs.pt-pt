---
title: "Tarefa de dimensionamento com as funções do Azure Stream Analytics & AzureML | Microsoft Docs"
description: "Saiba como dimensionar corretamente tarefas do Stream Analytics (criação de partições, a quantidade SU e mais) ao utilizar as funções do Azure Machine Learning."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 47ce7c5e-1de1-41ca-9a26-b5ecce814743
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 1e1c3724462a4d2a67eab3ef42867d2aeb5d3fa1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Dimensionar a sua tarefa do Stream Analytics com as funções do Azure Machine Learning
Muitas vezes, é fácil de configurar uma tarefa de Stream Analytics e executar alguns dados de exemplo através do mesmo. O que podemos fazer quando é necessário executar a mesma tarefa com o volume de dados superior? Requer-nos compreender como configurar a tarefa de Stream Analytics para que o se dimensiona. Neste documento, iremos focar-se nos aspetos de especiais de dimensionamento tarefas do Stream Analytics com as funções de Machine Learning. Para obter informações sobre como dimensionar as tarefas do Stream Analytics em geral, consulte o artigo [dimensionamento tarefas](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função do Azure Machine Learning no Stream Analytics?
Uma função de Machine Learning no Stream Analytics pode ser utilizada como uma chamada de função regulares no idioma de consulta do Stream Analytics. No entanto, atrás cena, as chamadas de função são, na verdade, os pedidos de serviço Web do Azure Machine Learning. Serviços web Machine Learning suportam "criação de batches" várias linhas, denominado mini batch, a mesma API chamada de serviço web, para melhorar o débito global. Consulte os artigos seguintes para obter mais detalhes; [Funções do azure Machine Learning no Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) e [serviços Web do Azure Machine Learning](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar uma tarefa de Stream Analytics com as funções de Machine Learning
Quando configurar uma função de Machine Learning para a tarefa de Stream Analytics, existem dois parâmetros a ter em consideração, o tamanho do lote das chamadas de função do Machine Learning e as unidades de transmissão em fluxo (SUs) aprovisionadas para a tarefa de Stream Analytics. Para determinar os valores adequados para estas, primeiro uma decisão tem de ser efetuada entre a latência e débito, ou seja, a latência da tarefa do Stream Analytics e débito de cada SU. SUs sempre podem ser adicionados a uma tarefa para aumentar o débito de uma consulta do Stream Analytics bem particionada, embora SUs adicionais aumentam o custo de execução da tarefa.

Por isso, é importante determinar o *tolerância* de latência em execução uma tarefa de Stream Analytics. Latência adicional a execução de pedidos de serviço do Azure Machine Learning naturalmente irá aumentar com o tamanho de lote, compounds a latência da tarefa de Stream Analytics. Por outro lado, aumentar o tamanho do lote permite que a tarefa de Stream Analytics processar * mais eventos com o *mesmo número* do Machine Learning pedidos de serviço da web. Muitas vezes, o aumento de latência de serviço web de Machine Learning é sublinear para o aumento do tamanho do lote, pelo que é importante a ter em consideração o tamanho do lote mais económico para um serviço web do Machine Learning em qualquer situação indicado. O tamanho do lote predefinido para o serviço web de pedidos é 1000 e pode ser modificado utilizando o [API de REST do Stream Analytics](https://msdn.microsoft.com/library/mt653706.aspx "API de REST do Stream Analytics") ou [PowerShell de cliente para o fluxo Análise de](stream-analytics-monitor-and-manage-jobs-use-powershell.md "PowerShell de cliente para o Stream Analytics").

Depois de um tamanho de lote foi considerado, o número de transmissão em fluxo unidades (SUs) podem ser determinadas, com base no número de eventos tem da função processos por segundo. Para obter mais informações sobre unidades de transmissão em fluxo, consulte [Stream Analytics Dimensionar tarefas](stream-analytics-scale-jobs.md).

Em geral, existem 20 ligações simultâneas para o serviço web do Machine Learning para cada 6 SUs, exceto que as tarefas de SU 1 e 3 tarefas SU obtêm 20 ligações simultâneas também.  Por exemplo, se a taxa de dados de entrada é 200 000 eventos por segundo e o tamanho do lote for deixado predefinição de 1000 a latência de serviço web resultante com o batch mini do 1000 eventos é de 200 ms. Isto significa que cada ligação, pode efetuar pedidos de cinco para o serviço web do Machine Learning num segundo. Com 20 ligações, a tarefa de Stream Analytics pode processar e, por conseguinte, 100 000 eventos de 20000 eventos em 200 ms num segundo. Por isso, para processar 200 000 eventos por segundo, a tarefa de Stream Analytics necessita de 40 ligações simultâneas que chamem 12 SUs. O diagrama seguinte ilustra os pedidos da tarefa de Stream Analytics para o ponto final de serviço do Machine Learning web – cada 6 SUs tem 20 ligações simultâneas para o serviço web Machine Learning, no máximo.

![Dimensionar o Stream Analytics com o exemplo de duas tarefas de Machine Learning funções](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "escala Stream Analytics com o exemplo de tarefa duas funções do Machine Learning")

Em geral, ***B*** para o tamanho do lote, ***L*** da tarefa para a latência de serviço web no tamanho do lote B em milissegundos, com o débito de um Stream Analytics ***N*** SUs é:

![Dimensionar o do Stream Analytics com Machine Learning funções fórmula](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "do Stream Analytics com Machine Learning funções fórmula de dimensionamento")

Uma consideração adicional pode estar as 'Máx. de chamadas em simultâneo' no lado de serviço web do Machine Learning, é recomendado que defina esta opção para o valor máximo (200 atualmente).

Para obter mais informações sobre esta definição reveja o [artigo de dimensionamento para serviços Web Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemplo-Sentiment análise
O exemplo seguinte inclui uma tarefa de Stream Analytics com a análise de dados de sentimento função do Machine Learning, conforme descrito no [tutorial de integração de aprendizagem do Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é simples totalmente particionada consulta seguida de **sentimento** funcione, como mostrado seguinte:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Considere o seguinte cenário; com um débito de 10 000 tweets por segundo tem de ser criada uma tarefa de Stream Analytics para executar uma análise de dados de sentimento dos tweets (eventos). Utilizar 1 SU, foi esta tarefa de Stream Analytics ser capaz de lidar com o tráfego? Com o tamanho de lote da predefinição de 1000 a tarefa deve ser capaz de manter-se a entrada. Ainda mais a função de Machine Learning adicionada deve gerar não mais do que um segundo da latência, que é geral de latência de predefinida da análise de dados de sentimento serviço web Machine Learning (com um tamanho de lote de predefinição de 1000). A tarefa de Stream Analytics **geral** ou latência ponto-a-ponto, normalmente, seria alguns segundos. Veja mais detalhadas para esta tarefa de Stream Analytics *especialmente* as chamadas de função do Machine Learning. Ter o tamanho do lote como 1000, um débito de 10 000 eventos demorar cerca de 10 pedidos ao serviço web. Mesmo com 1 SU, existem suficiente ligações simultâneas para acomodar este tráfego de entrada.

Mas e se a taxa de eventos de entrada aumenta ao 100 x e agora tem da tarefa de Stream Analytics processar 1.000.000 tweets por segundo? Existem duas opções:

1. Aumente o tamanho de lote, ou
2. O fluxo de entrada para processar os eventos em paralelo de partição

Com a primeira opção, a tarefa **latência** aumenta.

Com a segunda opção, o SUs mais tem de ser aprovisionado e, por conseguinte, gerar mais pedidos simultâneos de Machine Learning web service. Isto significa que a tarefa **custo** aumenta.

Assumir que a latência da análise de dados de sentimento serviço web Machine Learning é 200 ms para lotes de 1000 eventos ou abaixo, ms 250 para lotes de 5000-o evento, ms 300 para 10 000 eventos lotes ou 500 ms para lotes 25,000-o evento.

1. A primeira opção, a utilizar (**não** aprovisionamento mais SUs), pode ser aumentado para incluir o tamanho do lote **25,000**. Isto por sua vez, permitiria que a tarefa processar 1.000.000 eventos com 20 ligações simultâneas para o serviço web do Machine Learning (com uma latência de 500 ms por chamada). Para que a latência adicional da tarefa de Stream Analytics devido a pedidos de função de sentimento contra os pedidos de serviço web Machine Learning teria de ser aumentada de **200 ms** para **500 ms**. No entanto, tamanho do lote **não é possível** ser aumentado infinitamente o serviços web Machine Learning requer que o tamanho do payload de um pedido seja 4 MB ou menor tempo limite de pedidos de serviço web após 100 segundos da operação.
2. Utilizar a segunda opção, o tamanho do lote for deixado em 1000, com uma latência de serviço web de 200 ms, cada 20 ligações simultâneas para o serviço web seria capazes de processar 1000 * 20 * 5 eventos = 100 000 por segundo. Por isso, para processar 1.000.000 eventos por segundo, a tarefa teria 60 SUs. Em comparação com a primeira opção, a tarefa do Stream Analytics utilizariam o pedidos mais de lote do serviço web, por sua vez gerar um custo de aumento.

Segue-se uma tabela para o débito da tarefa de Stream Analytics para diferentes SUs e tamanhos de lote (num número de eventos por segundo).

| tamanho de lote (latência de ML) | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25,000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUs** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 SUs** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUs** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200,000 |300,000 |500,000 |

Por agora, já deverá ter uma boa compreensão sobre como funcionam as funções de Machine Learning no Stream Analytics. É provável que também compreende que dados de tarefas do Stream Analytics "solicitação" de origens de dados e cada "pull" devolve um lote de eventos para a tarefa de Stream Analytics processar. Como é que este impacto de modelo de extração de Machine Learning web pedidos de serviço?

Normalmente, o tamanho do lote que definimos para funções de Machine Learning exatamente será divisible pelo número de eventos devolvidos por cada tarefa de Stream Analytics "solicitação". Quando isto ocorre que o serviço web do Machine Learning é chamado com lotes "parciais". Isto é feito não assumir overhead inerente à latência trabalhos adicionais nos eventos de agregação de solicitação a solicitação.

## <a name="new-function-related-monitoring-metrics"></a>Nova função monitorização das métricas relacionadas com
Na área de Monitor de uma tarefa de Stream Analytics, foram adicionadas três métricas adicionais relacionados com a função. Estes são pedidos de função, eventos de função e função de pedidos FALHADOS, conforme apresentado no gráfico abaixo.

![Dimensionar o do Stream Analytics com a métrica de funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "dimensionar o do Stream Analytics com a métrica de funções do Machine Learning")

O são definidos do seguinte modo:

**PEDIDOS de função**: O número de pedidos de função.

**EVENTOS de função**: os eventos número nos pedidos de função.

**PEDIDOS FALHADOS de função**: O número de pedidos de função falhada.

## <a name="key-takeaways"></a>Chaves Takeaways
Para resumir os pontos principais, para poder dimensionar uma tarefa de Stream Analytics com as funções de Machine Learning, tem de ser considerados os seguintes itens:

1. A taxa de eventos de entrada
2. A latência tolerated para a tarefa de Stream Analytics em execução (e, consequentemente, o tamanho do lote dos pedidos de serviço web do Machine Learning)
3. O aprovisionamento SUs de análise de fluxo e o número de pedidos de serviço web Machine Learning (os relacionadas com a função custos adicionais)

Uma consulta do Stream Analytics totalmente particionada foi utilizada como exemplo. Se necessitar de uma consulta mais complexa o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) é um ótimo recurso para obter ajuda adicional da equipa do Stream Analytics.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Stream Analytics, consulte:

* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
