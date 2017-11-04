---
title: "Tarefas do Stream Analytics para aumentar o débito de escala | Microsoft Docs"
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
ms.openlocfilehash: a38394d825c9a9b3007b30f598b37caa08f7325f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-azure-stream-analytics-jobs-to-increase--throughput"></a>Tarefas do Stream Analytics do Azure para aumentar o débito de escala
Este artigo mostra como otimizar a uma consulta do Stream Analytics para aumentar o débito para as tarefas de análise de transmissão em fluxo. Pode utilizar o guia seguinte para dimensionar a sua tarefa para processar uma carga maior e tirar partido de mais recursos do sistema (por exemplo, mais largura de banda, mais recursos de CPU, memória mais).
Como pré-requisito, poderá ter de ler os artigos seguintes:
-   [Compreender e ajustar as Unidades de Transmissão em fluxo](stream-analytics-streaming-unit-consumption.md)
-   [Criar tarefas paralelizáveis](stream-analytics-parallelization.md)


## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Caso 1 – sua consulta é totalmente inerentemente paralelizável em partições de entrada
Se a sua consulta é totalmente inerentemente paralelizável em partições de entrada, pode seguir os seguintes passos:
1.  Criar a consulta para ser constrangedoramente paralelas utilizando **PARTITION BY** palavra-chave. Ver mais detalhes na secção tarefas Constrangedoramente paralelas [nesta página](stream-analytics-parallelization.md).
2.  Consoante os tipos de saída utilizados na sua consulta, algumas saída o não pode ser paralelizável, ou tem mais de configuração ser constrangedoramente paralelas. Por exemplo, não são paralelizáveis saídas do SQL Server, o armazém de dados do SQL Server e o Power BI. Saídas sempre são intercaladas antes de enviar para o sink de saída. Os BLOBs, tabelas, ADLS, Service Bus e função do Azure são automaticamente paralelizada. Hub de eventos e CosmosDB tem de ter a PartitionKey definida uma configuração para corresponder com o **PARTITION BY** campo (normalmente PartitionId). Para o Hub de eventos, também quais deve preste especial atenção para corresponder ao número de partições para todas as entradas e todas as saídas para evitar entre a ativação pós-falha entre as partições. 
3.  Executar a consulta com **6 SU** (que é a capacidade total de um único nó de computação) para medir o débito alcançável máximo, e se estiver a utilizar **GROUP BY**, podem quantos (cardinalidade) a tarefa de grupos de medidas Identificador. São os seguintes sintomas gerais da tarefa atingir os limites de recursos do sistema.
    - Métrica de utilização do SU % é superior a 80%. Isto indica memória utilização é elevada. Os fatores que contribuem para o aumento desta métrica descritos [aqui](stream-analytics-streaming-unit-consumption.md). 
    -   Carimbo de saída é baixar no que respeita à hora de relógio lateral. Dependendo da lógica de consulta, o carimbo de saída pode ter um deslocamento de lógica desde o momento de relógio lateral. No entanto, deve progresso em aproximadamente a velocidade a mesma. Se o carimbo de saída é baixar adicional e mais atrás, é um indicador de que o sistema é overworking. Pode ser um resultado de saída a jusante sink limitação ou elevada utilização da CPU. Não, fornecemos métrica de utilização da CPU neste momento, pelo que pode ser difícil diferenciar os dois.
        - Se o problema for devido à limitação de receptores, poderá ter de aumentar o número de partições de saída (e também entrada partições para manter a tarefa totalmente paralelizável) ou aumente a quantidade de recursos do sink (por exemplo o número de unidades de pedido para CosmosDB).
    - Diagrama de tarefa, há um por métrica de eventos de registo de segurança de partição para cada entrada. Se a métrica de eventos de registo de segurança mantém aumentar, também é um indicador de que o recurso do sistema é restringido (ou devido a limitação de sink de saída ou elevada da CPU).
4.  Depois de ter determinado os limites do que uma tarefa SU 6 pode aceder, pode Utilize para tirar conclusões forma linear a capacidade de processamento da tarefa à medida que adiciona SUs mais, partindo do princípio de que não tem quaisquer dados desfasamento que torna a determinados partição "ativos".
>[!Note]
> Escolha o número correto de unidades de transmissão em fluxo: porque o Stream Analytics cria um nó de processamento para cada SU 6 adicionadas, é preferível efectuar um divisor do número de partições de entrada, o número de nós para as partições podem ser distribuídas uniformemente em todos os nós.
> Por exemplo, ter medido o 6 tarefa SU pode alcançar 4 MB/s de processamento velocidade e a contagem da partição de entrada é 4. Pode optar por executar a tarefa com 12 SU para alcançar a taxa de processamento de aproximadamente 8 MB/s ou SU 24 para alcançar a 16 MB/s. Em seguida, pode decidir quando aumentar o número SU para a tarefa para que valor, como uma função de taxa de entrada.



## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Caso 2 - se a consulta não é constrangedoramente paralela.
Se a consulta não é constrangedoramente paralela, pode seguir os passos seguintes.
1.  Começar com uma consulta sem qualquer **PARTITION BY** primeiro para evitar a complexidade de criação de partições e execute a consulta com 6 SU para medir a carga máxima do [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Se pode alcançar a carga prevista no prazo de débito, terminar. Em alternativa, pode escolher medir a mesma tarefa em execução em 3 SU e 1 SU, para determinar o número mínimo de SU que funciona para o seu cenário.
3.  Se não é possível alcançar o débito pretendido, tente quebrar a consulta em vários passos se possível, se não tiver vários passos e atribuir SU até 6 para cada passo na consulta. Por exemplo, se tiver 3 passos, alocar SU 18 na opção "Dimensionar".
4.  Ao executar dessas tarefas, o Stream Analytics coloca cada passo no seu próprio nó com 6 recursos SU dedicados. 
5.  Se ainda não obtida o carga de destino, pode tentar utilizar **PARTITION BY** começando próximo passos para a entrada. Para **GROUP BY** operador que não pode ser naturalmente partições, pode utilizar o padrão de agregação local global ao efetuar uma particionada **GROUP BY** seguido não particionadas **GROUP BY** . Por exemplo, se pretender que a contagem de carros quantos passar booth cada utilização, a cada 3 minutos e o volume dos dados ultrapassa o que pode ser processados pelo 6 SU.

Consulta:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Na consulta acima, são contando carros por booth de utilização por partição e, em seguida, a adição de contagem de todas as partições em conjunto.

Depois de partições, para cada partição do passo, alocar SU até 6, cada partição ter 6 SU é o máximo, pelo que pode ser colocada cada partição no seu próprio nó de processamento.

> [!Note]
> Se a consulta não é possível particionar, adicionar SU adicional numa consulta vários passos pode não sempre melhorar o débito. É uma forma de obter um desempenho para reduzir o volume nos passos iniciais através do padrão de agregação local global, conforme descrito acima no passo 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Cenário 3 - está a executar muitas das consultas independentes numa tarefa.
Para determinados ISV utilize casos, onde é mais económico para processar os dados a partir de múltiplos inquilinos numa tarefa única, separadas entradas e saídas a utilizar para cada inquilino, poderá acabar em execução bastante algumas (por exemplo 20) consultas independentes uma única tarefa. Pressuposto é a carga de cada essas subconsulta relativamente pequena. Neste caso, pode seguir os passos seguintes.
1.  Neste caso, não utilize **PARTITION BY** na consulta
2.  Reduza a contagem da partição de entrada para o valor mais baixo possível de 2, se estiver a utilizar o Hub de eventos.
3.  Execute a consulta com 6 SU. Com carga esperada para cada subconsulta, adicione tantos essas subconsultas quanto possível, até que a tarefa está a atingir os limites de recursos do sistema. Consulte [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) para os sintomas quando isto acontece.
4.  Assim que estiver a atingir o limite de subconsulta medido acima, comece a adicionar a subconsulta para uma nova tarefa. O número de tarefas para ser executado como uma função do número de consultas independentes deve ser bastante linear, partindo do princípio de que não tem qualquer desfasamento de carga. Pode, em seguida, previsão 6 quantos as tarefas SU tem de ser executado como uma função do número de inquilinos que pretende servir.
5.  Ao utilizar a associação de dados de referência com estas consultas, deverá union que entradas em conjunto, antes de a associar-se com os mesmos dados de referência, em seguida, dividir os eventos se necessário. Caso contrário, cada associação de dados de referência mantém uma cópia dos dados de referência na memória, provavelmente blowing se a utilização da memória desnecessariamente.

> [!Note] 
> Quantos inquilinos para colocar em cada tarefa?
> Muitas vezes, neste padrão de consulta tem um grande número de subconsultas e resulta numa topologia muito grande e complexa. O controlador da tarefa não pode ser capaz de lidar com esse uma topologia de grandes dimensões. Como uma regra geral, permanecem em 40 inquilinos para a tarefa SU 1 e 60 inquilinos para 3 SU e 6 tarefas SU. Quando está a exceder a capacidade do controlador de, a tarefa não será iniciado com êxito.


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>Um exemplo de débito de Stream Analytics à escala
Para ajudar a compreender como dimensionar tarefas do Stream Analytics, iremos efetuar uma experimentação com base na entrada de um dispositivo Raspberry Pi. Esta fase experimental permite-na ver o efeito no débito de várias unidades de transmissão em fluxo e partições.

Neste cenário, o dispositivo envia dados de sensores (clientes) para um hub de eventos. Análise de transmissão em fluxo processa os dados e envia um alerta ou a estatística como resultado a outro hub de eventos. 

O cliente envia dados de sensores no formato JSON. A saída de dados também está no formato JSON. Os dados tem o seguinte aspeto:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

A seguinte consulta é utilizada para enviar um alerta quando um leve está desativada:

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Débito de medida

Neste contexto, o débito é a quantidade de dados de entrada processados pelo Stream Analytics dentro de um período de tempo fixo. (É medido durante 10 minutos.) Para alcançar o débito de processamento melhor para os dados de entrada, a entrada de fluxo de dados e a consulta foram particionadas. Iremos incluídos **existente** na consulta de medir os eventos de entrada quantas foram processados. Para certificar-se de que a tarefa não foi simplesmente aguardar eventos de entrada fique, cada partição do hub de eventos de entrada foi pré-carregado com cerca de 300 MB de dados de entrada.

A tabela seguinte mostra os resultados que vimos quando a partição correspondente contagens dos event hubs e vamos aumentar o número de unidades de transmissão em fluxo.  

<table border="1">
<tr><th>Partições de entrada</th><th>Partições de saída</th><th>Unidades Transmissão em Fluxo</th><th>Débito constante
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

E o gráfico seguinte mostra uma visualização da relação entre SUs e débito.

![IMG.stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

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

