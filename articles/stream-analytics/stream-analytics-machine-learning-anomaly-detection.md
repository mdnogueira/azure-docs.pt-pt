---
title: "Deteção de anomalias no guia de utilização do Azure (pré-visualização) | Microsoft Docs"
description: "Utilize o do stream analytics e a aprendizagem automática detetar anomalias."
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: dubansal
ms.openlocfilehash: 43a2a9784668fad2aa5b1441cfd37751c0c240b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-anomalydetection-operator"></a>Utilizar o operador ANOMALYDETECTION

> [!IMPORTANT]
> Esta funcionalidade está em pré-visualização. Não é recomendada a utilização em produção.

O **ANOMALYDETECTION** operador pode ser utilizado para detetar anomalias em fluxos de eventos.
Por exemplo, um decréscimo lento na memória livre num período de tempo tempo pode ser indicativa de uma fuga de memória ou o número de pedidos de serviço web que são estável num intervalo poderá aumentar ou diminuir significativamente.

Verifica a existência dos seguintes tipos de anomalias ao longo da duração especificada:

- Alteração de nível de bidirecional
- Tendência positiva lenta
- Tendência negativa lenta

O intervalo de tempo para até que ponto anterior no histórico de evento atual tem de ter em conta está limitado a utilizar o **LIMIT DURATION** cláusula. **ANOMALYDETECTION** , opcionalmente, pode ser limitado apenas os eventos que correspondam a uma propriedade de certo ou condição a utilizar o **quando** cláusula.

Grupos de eventos em separado com base na chave especificada no também, opcionalmente, pode processar o **PARTITION BY** cláusula. Formação e predição ocorrem independentemente em cada partição.

## <a name="syntax"></a>Sintaxe

`ANOMALYDETECTION(\<scalar_expression\>) OVER ([PARTITION BY \<partition key\>] LIMIT DURATION(\<unit\>, \<length\>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>Exemplo de utilização

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id \> 100) FROM input`|


## <a name="arguments"></a>Argumentos

- **scalar_expression**

  A expressão escalar durante o qual seria possível efetuar a deteção de anomalias. É uma expressão do tipo de vírgula flutuante ou bigint que devolve um valor único (escalar). A expressão com carateres universais  **\***  não é permitida. **scalar_expression** não pode conter outras funções analíticas ou funções externas.

- **Ao longo do (limit_duration_clause [when_clause] de [partition_by_clause])**

- **partition_by_clause** 

  O `PARTITION BY \<partition key\>` cláusula divide a aprendizagem e a formação em partições separadas. Por outras palavras, um modelo separado seria utilizado por valor `\<partition key\>` e apenas eventos com que o valor seriam utilizados para formação esse modelo e de aprendizagem. Por exemplo,

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  irá dar formação e Pontuar uma leitura em relação a outras as leituras de apenas o sensor mesmo.

- **cláusula limit_duration** duração (\<unidade\>, \<comprimento\>)

  Especifica a quantidade de histórico de evento atual é considerado o **ANOMALYDETECTION** cálculo. Consulte DATEDIFF para uma descrição detalhada do unidades suportadas e as respetivas abreviaturas.

- **when_clause** 

  Especifica uma condição booleana para os eventos considerado o **ANOMALYDETECTION** cálculo.

## <a name="return-types"></a>Tipos de retorno

A função devolve um registo que contém todas as três pontuações como o resultado. As propriedades associadas os diferentes tipos de detetores anomalias são denominadas:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Para extrair os valores fora do registo individuais, utilize o **GetRecordPropertyValue** função. Por exemplo:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) \> 3.25` 


Foi detetada uma uma anomalias de um determinado tipo quando um destes pontuações de anomalias atravesse um limiar. O limiar pode ser qualquer de vírgula flutuante número \>= 0. O limiar é um compromisso entre sensibilidade e o confiança. Por exemplo, um limiar mais baixo seria efetuar deteção mais confidenciais para as alterações e gerar alertas mais, enquanto que um limiar mais elevado pode tornar deteção menos sensível e mais certeza mas mascarar algumas anomalias. O valor de limiar exato a utilizar depende do cenário. Não existe nenhum limite superior mas o intervalo recomendado é de 3.25 5.

## <a name="algorithm"></a>Algoritmo

**ANOMALYDETECTION** utiliza a deslizante semântica de janela, o que significa que a computação executa por eventos que aciona a função e uma pontuação é produzida para esse evento. A computação é baseada no Exchangeability Martingales, que operam verificando se a distribuição dos valores de eventos foi alterado. Se assim for, foi detetada uma potencial anomalias. A classificação devolvida é uma indicação de que o nível de confiança de que anomalias. Como otimização interna, **ANOMALYDETECTION** calcula a classificação de anomalias de um evento com base no *d* para *2d* visão de eventos, onde *d*é o tamanho da janela de deteção especificados.

**ANOMALYDETECTION** espera série de tempo de entrada ser uniforme. Um fluxo de eventos pode ser efetuado uniform por agregar através de uma em cascata ou hopping janela. Em cenários em que o intervalo entre eventos sempre é menor do que a janela de agregação, uma janela em cascata é suficiente para efetuar a série de tempo uniforme. Quando o intervalo pode ser maior, as lacunas podem ser preenchidas repetindo o último valor utilizando uma janela de salto. Ambos estes cenários podem ser processados no exemplo que se segue. Atualmente, o `FillInMissingValuesStep` passo não pode ser ignorado. Não ter este passo resultará num erro de compilação.

## <a name="performance-guidance"></a>Guia de desempenho

- Utilize 6 SU para tarefas. 
- Envie eventos, pelo menos, 1 segundo, à excepção.
- Uma consulta não particionadas utilizando o **ANOMALYDETECTION** função pode produzir resultados com uma latência de cálculo de cerca de 25 MS em média.
- A latência teve por uma consulta particionada varia ligeiramente com o número de partições, como o número de cálculos é superior. No entanto, a latência é sobre os mesmos que as maiúsculas e minúsculas não particionadas para um comparável número total de eventos em todas as partições.
- Ao ler os dados de não-em tempo real, uma grande quantidade de dados é ingerida rapidamente. Processamento de dados atualmente é significativamente mais lento. Foi encontrada a latência de tais cenários para aumentar a forma linear com o número de pontos de dados na janela em vez do intervalo de tamanho ou evento de janela per se. Para reduzir a latência em casos de não-em tempo real, considere utilizar um tamanho mais pequeno da janela. Em alternativa, considere a sua tarefa a partir da hora atual. Alguns exemplos de latências numa consulta não particionadas: 
    - 60 pontos de dados na janela de deteção podem resultar numa latência de 10 segundos com um débito de 3 MBps. 
    - Em 600 pontos de dados, a latência pode aceder aos cerca de 80 segundos com um débito de 0.4 MBps.

## <a name="example"></a>Exemplo

A seguinte consulta pode ser utilizada para enviar um alerta se for detetada uma anomalias.
Quando o fluxo de entrada não é uniform, o passo de agregação pode ajudar a transformá-los para uma série de hora uniforme. O exemplo utiliza **médio** , mas o tipo específico de agregação depende do cenário de utilizador. Além disso, quando uma série de tempo tem em falta irregulares maiores do que a janela de agregação, haverá não há eventos na série de tempo a deteção de anomalias de Acionador (de acordo com a deslizante semântica de janela). Como resultado, pressuposto de uniformidade da será quebrado quando chegam próximo evento. Estas situações, precisamos de uma forma de preencher os lacunas na série de tempo. Uma abordagem possível consiste em efetuar o último evento para cada janela de salto, conforme mostrado abaixo.

Conforme indicado antes, não ignore o `FillInMissingValuesStep` passo por agora. Omitindo esse passo resultará num erro de compilação.

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) \>= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) \>=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) \>=
       3.25

## <a name="references"></a>Referências

* [Deteção de anomalias – utilizando o Machine Learning detetar anomalias nos dados de séries de tempo](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Machine Learning a deteção de anomalias API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Deteção de anomalias de séries de tempo](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

