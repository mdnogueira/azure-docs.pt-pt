---
title: "Consulta de exemplos de padrões de utilização comuns no Stream Analytics | Microsoft Docs"
description: "Padrões de consulta comuns do Azure Stream Analytics"
keywords: Exemplos de consulta
services: stream-analytics
documentationcenter: 
author: samacha
manager: jenniehubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/08/2017
ms.author: samacha
ms.openlocfilehash: a27bae1828bd469d4439e0ce43098edd73f54243
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exemplos de padrões de utilização comuns do Stream Analytics de consulta
## <a name="introduction"></a>Introdução
As consultas no Azure Stream Analytics são expressas num idioma de consulta como o SQL Server. Estas consultas estão documentadas no [referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) guia. Este artigo descreve soluções para vários padrões de consulta comuns, com base nos cenários no mundo real. Este é um trabalho em curso e continua a ser atualizada com novos padrões numa base contínua.

## <a name="query-example-convert-data-types"></a>Exemplo de consulta: converter os tipos de dados
**Descrição**: definir os tipos de propriedades no fluxo de entrada.
Por exemplo, a ponderação carro futuras no fluxo de entrada como cadeias e tem de ser convertida **INT** efetuar **soma** -cópia de segurança.

**Entrada**:

| Certifique- | Hora | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Saída**:

| Certifique- | Peso |
| --- | --- |
| Honda |3000 |

**Solução**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**EXPLICAÇÃO**: Utilize um **CAST** instrução no **ponderação** campo para especificar o respetivo tipo de dados. Ver a lista de tipos de dados suportados no [tipos de dados (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Exemplo de consulta: Utilize como/não como para o padrão de correspondência
**Descrição**: verificar se um valor de campo do evento corresponde a um determinado padrão.
Por exemplo, verifique que o resultado devolve plates de licença que começam com um e terminar com 9.

**Entrada**:

| Certifique- | LicensePlate | Hora |
| --- | --- | --- |
| Honda |ABC 123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA 999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC 369 |2015-01-01T00:00:03.0000000Z |

**Saída**:

| Certifique- | LicensePlate | Hora |
| --- | --- | --- |
| Toyota |AAA 999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC 369 |2015-01-01T00:00:03.0000000Z |

**Solução**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**EXPLICAÇÃO**: Utilize o **como** instrução para verificar o **LicensePlate** campo valor. Deve começar com um um, em seguida, ter qualquer cadeia de zero ou mais carateres e, em seguida, terminar com um 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemplo de consulta: especificar lógica para casos/valores diferentes (instruções maiúsculas)
**Descrição**: forneça um cálculo diferente de um campo, com base num critério específico.
Por exemplo, forneça uma descrição de cadeia para tornar o número de carros do mesmo transmitido, com um caso especial para 1.

**Entrada**:

| Certifique- | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Saída**:

| CarsPassed | Hora |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Solução**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**EXPLICAÇÃO**: O **caso** cláusula permite-nos fornecer um cálculo diferentes, com base em algumas critérios (no nosso caso, o número de carros na janela do agregado).

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemplo de consulta: enviar dados para várias saídas
**Descrição**: enviar dados para a vários destinos de saída de uma única tarefa.
Por exemplo, analisar os dados para um alerta baseadas em limiares e arquivar todos os eventos para o blob storage.

**Entrada**:

| Certifique- | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Certifique- | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Certifique- | Hora | Contagem |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Solução**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**EXPLICAÇÃO**: O **INTO** cláusula informa o Stream Analytics que as saídas ao escrever os dados a partir esta instrução.
A primeira consulta é um pass-through dos dados recebemos para uma saída que são denominados **ArchiveOutput**.
A consulta segundo efetua algumas agregação simple e filtragem e -envia os resultados para um sistema de alerta a jusante.

Tenha em atenção que também pode reutilizar os resultados das expressões de tabela comuns (ctes recursivos) (tal como **WITH** instruções) em várias instruções de saída. Esta opção tem o benefício adicional de abertura menos leitores para a origem de entrada.
Por exemplo: 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-count-unique-values"></a>Exemplo de consulta: contagem de valores exclusivos
**Descrição**: contabilizar o número de valores de campo exclusivo que aparecem na sequência dentro de uma janela de tempo.
Por exemplo, quantas exclusivo faz com que o de automóveis passadas a booth utilização numa janela segundo 2?

**Entrada**:

| Certifique- | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Saída:**

| Contagem | Hora |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Solução:**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Explicação:**
**CONTAGEM (tornar distintos)** devolve o número de valores distintos no **tornar** coluna dentro de uma janela de tempo.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemplo de consulta: determinar se um valor foi alterada
**Descrição**: observar um valor anterior para determinar se é diferente do valor atual.
Por exemplo, é o automóvel anterior em viagem de utilização do mesmo disponibilizar como o automóvel atual?

**Entrada**:

| Certifique- | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Saída**:

| Certifique- | Hora |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Solução**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**EXPLICAÇÃO**: Utilize **desfasamento** para observar no fluxo de entrada um evento novamente e obter o **tornar** valor. Em seguida, compare-lo para o **tornar** valor do evento atual e o evento de saída, se forem diferentes.

## <a name="query-example-find-the-first-event-in-a-window"></a>Exemplo de consulta: localizar o primeiro evento numa janela
**Descrição**: localizar o primeiro carro em cada intervalo de 10 minutos.

**Entrada**:

| LicensePlate | Certifique- | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Saída**:

| LicensePlate | Certifique- | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Solução**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Agora vamos alterar o problema e localize o primeiro carro de uma marca específica em cada intervalo de 10 minutos.

| LicensePlate | Certifique- | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solução**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>Exemplo de consulta: localizar o último evento numa janela
**Descrição**: localizar o último automóvel em cada intervalo de 10 minutos.

**Entrada**:

| LicensePlate | Certifique- | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Saída**:

| LicensePlate | Certifique- | Hora |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solução**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**EXPLICAÇÃO**: Existem dois passos da consulta. Primeiro localiza o carimbo de hora mais recente do windows 10 minutos. O segundo passo associa os resultados da consulta primeiro com a sequência original para localizar os eventos que correspondem à última carimbos de hora em cada janela. 

## <a name="query-example-detect-the-absence-of-events"></a>Exemplo de consulta: detetar a ausência de eventos
**Descrição**: verificar se uma sequência não tem um valor que corresponde a um determinado critério.
Por exemplo, 2 carros consecutivos de disponibilizar a mesma introduzido viagem utilização dentro de 90 segundos último?

**Entrada**:

| Certifique- | LicensePlate | Hora |
| --- | --- | --- |
| Honda |ABC 123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA 999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF 987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI 345 |2015-01-01T00:00:04.0000000Z |

**Saída**:

| Certifique- | Hora | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA 999 |ABC 123 |2015-01-01T00:00:01.0000000Z |

**Solução**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**EXPLICAÇÃO**: Utilize **desfasamento** para observar no fluxo de entrada um evento novamente e obter o **tornar** valor. Compará-lo para o **tornar** valor no evento atual e, em seguida, o evento de saída se são iguais. Também pode utilizar **desfasamento** para obter dados sobre o automóvel anterior.

## <a name="query-example-detect-the-duration-between-events"></a>Exemplo de consulta: detetar a duração entre eventos
**Descrição**: localizar a duração de um evento específico. Por exemplo, tendo em conta um clickstream da web, determine o tempo gasto numa funcionalidade.

**Entrada**:  

| Utilizador | Funcionalidade | Evento | Hora |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Iniciar |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Fim |2015-01-01T00:00:08.0000000Z |

**Saída**:  

| Utilizador | Funcionalidade | Duração |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Solução**:

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**EXPLICAÇÃO**: Utilize o **último** função para obter a última **tempo** valor quando o tipo de evento foi **iniciar**. O **último** funcionar utiliza **PARTITION BY [user]** para indicar que o resultado é calculado por utilizador exclusivo. A consulta tem um limiar máximo de 1 hora para a diferença de tempo entre **iniciar** e **parar** eventos, mas é configurável conforme necessário **(limite DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Exemplo de consulta: detetar a duração de uma condição
**Descrição**: localizar fora quanto Ocorreu uma condição.
Por exemplo, suponha que um erro resultou em todos os carros ter uma ponderação incorreta (acima pounds 20.000). Queremos de computação durante o erros.

**Entrada**:

| Certifique- | Hora | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Saída**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Solução**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**EXPLICAÇÃO**: Utilize **desfasamento** para ver o fluxo de entrada para 24 horas e procure instâncias onde **StartFault** e **StopFault** são abrangido pelo ponderação < 20000.

## <a name="query-example-fill-missing-values"></a>Exemplo de consulta: preencher os valores em falta
**Descrição**: para o fluxo de eventos que têm valores em falta, produzir um fluxo de eventos com intervalos regulares.
Por exemplo, gere um evento a cada cinco segundos que o ponto de dados mais recentemente visualizado os relatórios.

**Entrada**:

| T | valor |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Saída (primeiras 10 linhas)**:

| windowend | lastevent.t | lastevent.Value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Solução**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**EXPLICAÇÃO**: esta consulta gera eventos a cada cinco segundos e produz o último evento que foi recebido anteriormente. O [Hopping janela](https://msdn.microsoft.com/library/dn835041.aspx "Hopping janela – Azure Stream Analytics") duração determina até que ponto anterior a consulta de procura para localizar o evento mais recente (300 segundos neste exemplo).

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

