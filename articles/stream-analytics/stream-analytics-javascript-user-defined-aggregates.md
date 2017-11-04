---
title: Azure Stream Analytics JavaScript definido pelo utilizador agregados | Microsoft Docs
description: "Executar consulta avançada mechanics com os agregados definidos pelo utilizador de JavaScript"
keywords: JavaScript, os agregados, uda definido pelo utilizador
services: stream-analytics
author: minhe-msft
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/28/2017
ms.author: minhe
ms.openlocfilehash: b3863a34ed146e54c6d60e035957b942a1976ff9
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure Stream Analytics JavaScript definido pelo utilizador agregados (pré-visualização)

O Azure Stream Analytics suporta definido pelo utilizador agregados (UDA) escritos em JavaScript, permite-lhe implementar a lógica empresarial de monitorização de estado complexas. Dentro do UDA tem controlo total sobre a estrutura de dados de estado, acumulação de estado, decumulation de estado e cálculo de resultados de agregação. O artigo apresenta as duas diferentes que das interfaces JavaScript UDA, passos para criar um UDA e como utilizar UDA com baseado no Windows operações na consulta do Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Agregados definidos pelo utilizador de JavaScript

Um agregado definido pelo utilizador é utilizado por cima de uma especificação de janela de tempo para agregar sobre os eventos nessa janela e produz um valor de resultado único. Existem dois tipos de interfaces UDA Stream Analytics suporta hoje em dia, AccumulateOnly e AccumulateDeaccumulate. Ambos os tipos de UDA podem ser utilizados por janela em cascata, janela de salto e a deslizante janela. AccumulateDeaccumulate UDA efetua melhor AccumulateOnly UDA quando utilizado em conjunto com a janela de salto e a deslizante janela. Escolha um dos dois tipos de com base no algoritmo que utilizar.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly agregados

Agrega AccumulateOnly só pode acumular novos eventos para o seu estado, o algoritmo não permite deaccumulation dos valores. Selecione este tipo de agregação deaccumulate quando um evento informações a partir do valor de estado não são possível implementar. Segue-se o modelo de JavaScript para AccumulatOnly agregados:

````JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate agregados

Agrega AccumulateDeaccumulate permite deaccumulation de um valor acumulado anterior do Estado, por exemplo, remover um par chave-valor de uma lista de valores de eventos ou subtrair um valor a partir de um Estado de agregação sum. Segue-se o modelo de JavaScript para AccumulateDeaccumulate agregados:

````JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

## <a name="uda---javascript-function-declaration"></a>UDA - declaração da função de JavaScript

Cada UDA JavaScript é definido por uma declaração de objeto de função. Seguem-se os elementos principais numa definição de UDA.

### <a name="function-alias"></a>Alias de função

Alias da função é o identificador UDA. Quando chamado numa consulta do Stream Analytics, utilize sempre alias UDA juntamente com um "uda". prefixo.

### <a name="function-type"></a>Tipo de função

Para UDA, o tipo de função deve ser **Javascript UDA**.

### <a name="output-type"></a>Tipo de saída

Específico escreva essa tarefa de Stream Analytics suportada ou "Qualquer" se pretender processar o tipo na sua consulta.

### <a name="function-name"></a>Nome de função

O nome deste objeto de função. O nome da função literalmente deve corresponder ao UDA alias (pré-visualize comportamento, iremos estiver a considerar função anónima de suporte quando GA).

### <a name="method---init"></a>O método serializationmethod init()

O método init() inicializa o estado do agregado. Este método é chamado quando a janela é iniciado.

### <a name="method--accumulate"></a>Método – accumulate()

O método accumulate() calcula o estado UDA com base no estado anterior e os valores de evento atual. Este método é chamado quando um evento introduz uma janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW ou SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Método – deaccumulate()

O método deaccumulate() recalcula Estado com base no estado anterior e os valores de evento atual. Este método é chamado quando um evento deixa um SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Método – deaccumulateState()

O método deaccumulateState() recalcula com base no estado anterior e o estado de um salto de estado. Este método é chamado quando um conjunto de eventos deixe uma HOPPINGWINDOW.

### <a name="method--computeresult"></a>Método – computeResult()

O método de computeResult() devolve resultados de agregação com base no estado atual. Este método é denominado no final de um período de tempo (TUMBLINGWINDOW, HOPPINGWINDOW e SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA suportadas os tipos de dados de entrada e de saída
Para tipos de dados UDA do JavaScript, consulte a secção **conversão do tipo de Stream Analytics e JavaScript** de [integrar o JavaScript UDFs](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>A adição de um JavaScript UDA do portal do Azure

A seguir Vamos guiá-o processo de criação de um UDA do Portal. O exemplo que utilizamos aqui é informática médias ponderado de tempo.

Agora vamos criar um UDA JavaScript sob uma tarefa do ASA existente, os seguintes passos.

1. Inicie sessão no portal do Azure e localize a tarefa de Stream Analytics existente.
1. Em seguida, clique na ligação de funções em **tarefa TOPOLOGIA**.
1. Clique em de **adicionar** ícone para adicionar uma nova função.
1. Na vista de nova função, selecione **JavaScript UDA** como o tipo de função, em seguida, verá um modelo UDA predefinido apareçam no editor.
1. Preencha "TWA" como o alias UDA e altere a implementação de função como o seguinte:

    ````JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ````

1. Depois de clicar no botão "Guardar", o UDA mostra a lista de função.

1. Clique na nova função "TWA", pode verificar a definição de função.

## <a name="calling-javascript-uda-in-asa-query"></a>Chamar JavaScript UDA na consulta do ASA

No portal do Azure e abrir o seu trabalho, editar a consulta e chamar TWA() função com um prefixo de mandato "uda.". Por exemplo:

````SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
````

## <a name="testing-query-with-uda"></a>Testar a consulta com UDA

Crie um ficheiro JSON local com abaixo conteúdo, carregue o ficheiro à tarefa do Stream Analytics e testar acima consulta.

````JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
````

## <a name="get-help"></a>Obter ajuda

Para obter ajuda adicional, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Gestão de Stream Analytics referência da REST API do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
