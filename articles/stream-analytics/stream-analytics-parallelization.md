---
title: Tirar partido parallelization de consulta no Azure Stream Analytics | Microsoft Docs
description: "Saiba como dimensionar as tarefas do Stream Analytics ao configurar partições de entrada, a definição de consulta de Otimização e definir as unidades de transmissão em fluxo de trabalho."
keywords: "dados de transmissão em fluxo, processamento de dados de transmissão em fluxo otimizar a análise"
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeanb
ms.openlocfilehash: 33685152c7e7cb1d066661d85a018d30c25442dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Tirar partido parallelization de consulta no Azure Stream Analytics
Este artigo mostra como tirar partido das parallelization do Azure Stream Analytics. Saiba como dimensionar as tarefas do Stream Analytics, deve configurar partições de entrada e otimizar a definição de consulta de análise.
Como pré-requisito, poderá estar familiarizado com a noção de unidade de transmissão em fluxo descrito em [compreender e ajustar as unidades de transmissão em fluxo](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de uma tarefa de Stream Analytics?
Uma definição de tarefa do Stream Analytics inclui entradas, uma consulta e de saída. Entradas são onde a tarefa lê o fluxo de dados do. A consulta é utilizada para transformar o fluxo de entrada de dados e o resultado é onde a tarefa envia os resultados da tarefa.  

Uma tarefa requer, pelo menos, uma origem de entrada para a transmissão de dados. A origem de entrada de fluxo de dados pode ser armazenada num hub de eventos do Azure ou no armazenamento de Blobs do Azure. Para obter mais informações, consulte [introdução ao Azure Stream Analytics](stream-analytics-introduction.md) e [começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partições na origens e sinks
Dimensionamento uma tarefa de Stream Analytics tira partido das partições na entrada ou saída. Criação de partições permite que divide os dados em subconjuntos com base na chave de partição. Um processo que consome os dados (por exemplo, uma tarefa de análise de transmissão em fluxo) pode consumir e escrever diferentes partições em paralelo, o que aumenta o débito. 

### <a name="inputs"></a>Entradas
Todas as entradas de Azure Stream Analytics podem tirar partido da criação de partições:
-   EventHub (é necessário definir explicitamente a chave de partição)
-   IoT Hub (é necessário definir explicitamente a chave de partição)
-   Armazenamento de blobs

### <a name="outputs"></a>saídas

Quando trabalha com o Stream Analytics, pode tirar partido as saídas de criação de partições:
-   Armazenamento do Azure Data Lake
-   Funções do Azure
-   Tabela do Azure
-   Armazenamento de blobs
-   CosmosDB (é necessário definir explicitamente a chave de partição)
-   EventHub (é necessário definir explicitamente a chave de partição)
-   IoT Hub (é necessário definir explicitamente a chave de partição)
-   Service Bus

Saídas de Power BI, o SQL e o armazém de dados do SQL Server não suportam a criação de partições. No entanto, pode ainda de partição a entrada conforme descrito em [nesta secção](#multi-step-query-with-a-grouping-key) 

Para obter mais informações sobre as partições, consulte os artigos seguintes:

* [Descrição geral dos Event Hubs funcionalidades](../event-hubs/event-hubs-features.md#partitions)
* [Criação de partições de dados](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="embarrassingly-parallel-jobs"></a>Tarefas constrangedoramente paralelas
Um *constrangedoramente paralelas* tarefa é o cenário mais dimensionável temos no Azure Stream Analytics. Uma partição da entrada para uma instância da consulta se liga a uma partição de saída. Este paralelismo tem os seguintes requisitos:

1. Se a lógica de consulta depende a mesma chave a ser processada pela mesma instância de consulta, tem de se certificar de que os eventos de ir para a mesma partição da sua entrada. Para os event hubs, isto significa que os dados do evento têm de ter o **PartitionKey** valor definido. Em alternativa, pode utilizar os remetentes particionados. Para armazenamento de BLOBs, isto significa que os eventos são enviados para a mesma pasta de partição. Se a lógica de consulta não requer a mesma chave a ser processado pela mesma instância de consulta, pode ignorar este requisito. Um exemplo desta lógica seria uma consulta de filtro de projeto selecione simples.  

2. Depois dos dados são dispostos no lado de entrada, tem de se certificar de que a sua consulta está particionada. Isto requer a utilização **PARTITION BY** em todos os passos. São permitidos vários passos, mas todos eles têm de ser particionados pela mesma chave. Atualmente, a chave de criação de partições tem de ser definida **PartitionId** por ordem para a tarefa ser totalmente paralela.  

3. Na maioria das nossa saída pode tirar partido da criação de partições, no entanto, se utilizar um tipo de saída que não suporta a criação de partições, a tarefa não será totalmente paralela. Consulte o [saída secção](#Outputs) para obter mais detalhes.

4. O número de partições de entrada tem de ser igual o número de partições de saída. Saída de armazenamento de blob atualmente não suporta partições. Mas que okay, vez que herda o esquema de partições da consulta a montante. Seguem-se exemplos de valores de partição que permitem que uma tarefa totalmente paralela:  

   * as partições de saída de 8 partições entrada para o event hub e o hub de eventos 8
   * 8 event hub entrada as partições e de saída de armazenamento de BLOBs  
   * 8 blob entrada as partições de armazenamento e de saída de armazenamento de BLOBs  
   * 8 blob partições de entrada de armazenamento e 8 partições de saída de hub de eventos  

As secções seguintes abordam alguns cenários de exemplo que são constrangedoramente paralelos.

### <a name="simple-query"></a>Consulta simples

* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições

Consulta:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Esta consulta é um filtro simple. Por conseguinte, não temos de preocupar com a entrada que está a ser enviada para o hub de eventos de criação de partições. Tenha em atenção que a consulta inclui **PARTIÇÃO por PartitionId**, por isso que satisfaz o requisito #2 a partir de versões anteriores. Para a saída, temos de configurar a saída de hub de eventos na tarefa com o conjunto de chaves de partição para **PartitionId**. Última verificação de um é certificar-se de que o número de partições de entrada for igual ao número de partições de saída.

### <a name="query-with-a-grouping-key"></a>Consultar com uma chave de agrupamento

* Entrada: Hub de eventos com 8 partições
* Dados de saída: Armazenamento de BLOBs

Consulta:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tem uma chave de agrupamento. Por conseguinte, os eventos agrupados tem de ser enviados para a mesma partição do Hub de eventos. Uma vez que, neste exemplo, vamos Agrupar por TollBoothID, deverá ser se de que o TollBoothID é utilizado como a chave de partição, quando os eventos são enviados para o Hub de eventos. Em seguida, no ASA, podemos utilizar **PARTIÇÃO por PartitionId** herdar este esquema de partição e ativar parallelization completa. Uma vez que a saída do armazenamento de BLOBs, não temos de preocupar com a configuração de um valor de chave de partição, de acordo com o requisito de #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exemplo de cenários que são *não* constrangedoramente paralelas

Na secção anterior, iremos mostrou alguns cenários constrangedoramente paralelos. Nesta secção, vamos discutir cenários que não cumpram todos os requisitos para ser constrangedoramente paralelas. 

### <a name="mismatched-partition-count"></a>Contagem da partição sem correspondência
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 32 partições

Neste caso, é irrelevante-se a consulta é. Se a contagem da partição de entrada não corresponde à contagem da partição de saída, a topologia não se encontra constrangedoramente paralelo. + no entanto, continua a poder aceder alguns nível ou parallelization.

### <a name="query-using-non-partitioned-output"></a>Consultar através de saída não particionadas
* Entrada: Hub de eventos com 8 partições
* Saída: PowerBI

Saída de PowerBI atualmente não suporta a criação de partições. Por conseguinte, este cenário não é constrangedoramente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de vários passo com diferentes valores PARTITION BY
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições

Consulta:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Como pode ver, o segundo passo utiliza **TollBoothId** como a chave de criação de partições. Este passo não é o mesmo que o primeiro passo e, por conseguinte, requer um shuffle de fazer. 

Os exemplos anteriores mostram algumas tarefas do Stream Analytics que está em conformidade com (ou não) uma topologia constrangedoramente paralela. Se estão em conformidade com, têm a possibilidade de dimensionamento máximo. Atualizações para as tarefas que não se ajustem um destes perfis, documentação de orientação de dimensionamento ficará disponíveis no futuro. Por agora, utilize as orientações gerais nas secções seguintes.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcular o máximo de unidades de uma tarefa de transmissão em fluxo
O número total de unidades de transmissão em fluxo que pode ser utilizado por uma tarefa de Stream Analytics depende o número de passos da consulta definida para a tarefa e o número de partições para cada passo.

### <a name="steps-in-a-query"></a>Passos numa consulta
Uma consulta pode ter um ou vários passos. Cada passo é uma subconsulta definida pelo **WITH** palavra-chave. A consulta que está fora do **WITH** palavra-chave (apenas uma consulta) também é contabilizado como um passo, tais como o **SELECIONE** instrução na seguinte consulta:

Consulta:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Esta consulta tem dois passos.

> [!NOTE]
> Esta consulta é abordada em mais detalhe posteriormente no artigo.
>  

### <a name="partition-a-step"></a>Um passo de partição
Um passo de criação de partições requer as seguintes condições:

* A origem de entrada têm de ser particionada. 
* O **SELECIONE** instrução de consulta tem de leitura de uma origem de entrada particionada.
* A consulta dentro do passo tem de ter o **PARTITION BY** palavra-chave.

Quando uma consulta está particionada, os eventos de entrada são processados e agregada numa partição separada grupos e saídas eventos são gerados para cada um dos grupos. Se pretender uma agregação combinada, tem de criar um segundo passo não particionadas a agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcular o máximo de unidades de uma tarefa de transmissão em fluxo
Todos os passos não particionado em conjunto podem escalar unidades de transmissão em fluxo até seis (SUs) para uma tarefa de Stream Analytics. Além disto, pode adicionar 6 SUs para cada partição num passo particionado.
Pode ver alguns **exemplos** na tabela abaixo.

| Consulta                                               | Máx. de SUs para a tarefa |
| --------------------------------------------------- | ------------------- |
| <ul><li>A consulta contém um passo.</li><li>O passo não está particionado.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada particionado 16.</li><li>A consulta contém um passo.</li><li>O passo está particionado.</li></ul> | 96 (6 * 16 partições) |
| <ul><li>A consulta contém dois passos.</li><li>Nenhum dos passos está particionada.</li></ul> | 6 |
| <ul><li>O fluxo de dados de entrada está particionado por 3.</li><li>A consulta contém dois passos. O passo de entrada está particionado e não é o segundo passo.</li><li>O <strong>SELECIONE</strong> instrução lê a partir da entrada particionada.</li></ul> | 24 (18 para passos particionados + 6 para obter os passos não particionadas |

### <a name="examples-of-scaling"></a>Exemplos de dimensionamento

A seguinte consulta calcula o número de carros dentro de uma janela de três minutos passar de uma estação de utilização que tem três tollbooths. Esta consulta pode ser ampliada até seis SUs.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para utilizar mais SUs para a consulta, o fluxo de dados de entrada e a consulta têm de ser particionados. Uma vez que a partição de fluxo de dados está definida para 3, pode ser escalada para a seguinte consulta modificada até 18 SUs:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando uma consulta está particionada, os eventos de entrada são processados e agregados em grupos de partição separada. Eventos de saída também são gerados para cada um dos grupos. Criação de partições pode causar alguns resultados inesperados quando o **GROUP BY** campo não é a chave de partição no fluxo de dados de entrada. Por exemplo, o **TollBoothId** campo na consulta anterior não é a chave de partição de **Input1**. O resultado é que os dados da TollBooth n. º 1 serem distribuídos em várias partições.

Cada um do **Input1** partições serão processadas separadamente pelo Stream Analytics. Como resultado, serão criados vários registos da contagem de carro para o mesmo tollbooth na mesma janela em cascata. Se não é possível alterar a chave de partição de entrada, este problema pode ser corrigido através da adição de um passo de partição não para agregados valores em partições, como no exemplo seguinte:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Esta consulta pode ser ampliada para 24 SUs.

> [!NOTE]
> Se a associação a dois fluxos, certifique-se de que os fluxos particionados a chave de partição da coluna que utiliza para criar as associações. Certifique-se também tiver o mesmo número de partições em ambos os fluxos.
> 
> 





## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

