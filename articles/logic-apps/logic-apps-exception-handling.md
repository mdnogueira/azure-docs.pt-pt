---
title: "Erro & processamento de exceções - Azure Logic Apps | Microsoft Docs"
description: "Padrões de erro e excepção a processar no Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9af2f71b3d288cc6f4e271d0915545d43a1249bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Processar os erros e exceções no Azure Logic Apps

Aplicações lógicas do Azure fornece ferramentas avançadas e padrões para o ajudar a certificar-se de que o integrações são resilientes contra falhas e robusto. Qualquer arquitetura de integração pode representar um desafio de certificar-se de que adequadamente lidar com problemas de sistemas dependentes ou de um período de indisponibilidade. As Logic Apps faz com que o processamento de erros de uma experiência de primeira classe, dando-lhe as ferramentas que necessárias para agir sobre exceções e erros nos seus fluxos de trabalho.

## <a name="retry-policies"></a>Repita políticas

Uma política de repetição é o tipo de exceção e processamento de erros mais básico. Se um pedido inicial foi excedido ou falha (qualquer pedido que resulte num 429 ou resposta 5xx), esta política define se deve repetir a ação. Por predefinição, todas as ações repetir 4 vezes adicionais sobre os intervalos de 20 segundo. Por isso, caso o primeiro pedido recebe um `500 Internal Server Error` resposta, o motor de fluxo de trabalho interrompe para 20 segundos e tenta novamente o pedido. Se depois de todas as tentativas, a resposta ainda uma exceção ou falha, o fluxo de trabalho continua e marca o estado da ação como `Failed`.

Pode configurar as políticas de repetição no **entradas** para uma ação específica. Por exemplo, pode configurar uma política de repetição para tentar máximo de 4 vezes através de intervalos de 1 hora. Para obter detalhes completos sobre as propriedades de entrada, consulte [ações de fluxo de trabalho e é acionado][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Se quisesse a ação de HTTP para repetir 4 vezes e aguarde 10 minutos entre cada tentativa, teria de utilizar a seguinte definição:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Para obter mais informações sobre a sintaxe suportado, consulte o [secção política de repetição ações de fluxo de trabalho e é acionado][retryPolicyMSDN].

## <a name="catch-failures-with-the-runafter-property"></a>Detetar falhas com a propriedade RunAfter

Cada ação de aplicação lógica declara que ações devem ser concluído antes do início da ação, tal como ordenar os passos no fluxo de trabalho. Na definição de ação, esta ordem é conhecido como o `runAfter` propriedade. Esta propriedade é um objeto que descreve quais as ações e Estados de ação executar a ação. Por predefinição, todas as ações adicionadas através do Designer de aplicação lógica estão definidas como `runAfter` passo anterior se o passo anterior `Succeeded`. No entanto, pode personalizar este valor a acionar ações quando têm ações anteriores `Failed`, `Skipped`, ou um conjunto possíveis destes valores. Se pretender adicionar um item para um tópico de barramento de serviço designado após uma ação específica `Insert_Row` falhar, pode utilizar o seguinte `runAfter` configuração:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Tenha em atenção o `runAfter` propriedade está definida a acionar se o `Insert_Row` é ação `Failed`. Para executar a ação, se o estado da ação `Succeeded`, `Failed`, ou `Skipped`, utilizam esta sintaxe:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Ações que executaram e concluir com êxito após ter uma ação anterior tenha falhado, estão marcados como `Succeeded`. Este comportamento significa que se com êxito catch todas as falhas no fluxo de trabalho, a execução em si é marcado como `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Âmbitos e resultados avaliar ações

Semelhante à forma como pode executar após ações individuais, pode também agrupar ações em conjunto no interior de um [âmbito](../logic-apps/logic-apps-loops-and-scopes.md), que atua como um agrupamento lógico das ações. Âmbitos são úteis para organizar as suas ações de aplicação lógica e efetuar avaliações agregadas o estado de um âmbito. O âmbito próprio recebe um Estado depois de terminar todas as ações de um âmbito. O estado de âmbito é determinado dos mesmos critérios como uma execução. Se a ação final um ramo de execução é `Failed` ou `Aborted`, o estado é `Failed`.

A acionar ações específicas para quaisquer falhas que tenham acontecido dentro do âmbito, pode utilizar `runAfter` com um âmbito que está marcado como `Failed`. Se *qualquer* falha nas ações no âmbito, executar após a falha de um âmbito permite criar uma única ação para detetar falhas.

### <a name="getting-the-context-of-failures-with-results"></a>Obter o contexto de falhas com resultados

Embora a deteção de falhas de um âmbito é útil, pode também querer contexto para ajudar a compreender exatamente as ações de falha e quaisquer erros ou códigos de estado que foram devolvidos. O `@result()` função de fluxo de trabalho fornece o contexto sobre o resultado de todas as ações de um âmbito.

`@result()`assume um parâmetro único, o nome de âmbito e devolve uma matriz de todos os ação resultados de dentro desse âmbito. Estes objetos de ação incluem os mesmos atributos como a `@actions()` produz de objeto, incluindo a hora de início de ação, hora de fim de ação, estado da ação, entradas de ação, correlação da ação IDs e ação. Para enviar o contexto de todas as ações que falha dentro de um âmbito, emparelhar facilmente um `@result()` funcionar com uma `runAfter`.

Para executar uma ação *para cada* ação num âmbito que `Failed`, filtrar a matriz de resultados para as ações que falharam, que pode ser emparelhado `@result()` com um  **[filtro matriz](../connectors/connectors-native-query.md)**  ação e um  **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)**  ciclo. Pode colocar a matriz de resultado filtrados e efetuar uma ação para cada falha utilizando o **ForEach** ciclo. Eis um exemplo, seguido por uma explicação detalhada, que envia um pedido POST de HTTP com o corpo da resposta de todas as ações que falhou no âmbito `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Eis as instruções detalhadas para descrever o que acontece:

1. Para obter o resultado de todas as ações dentro `My_Scope`, a **filtro matriz** filtros de ação `@result('My_Scope')`.

2. A condição de **filtro matriz** alguma `@result()` item que tem o Estado igual a `Failed`. Esta condição filtra matriz com todos os resultados de ação de `My_Scope` para uma matriz com falha apenas resultados de ação.

3. Efetuar uma **para cada** ação no **filtrado matriz** produz. Este passo executa uma ação *para cada* falhou o resultado da ação que foi filtrado anteriormente.

    Se uma única ação no âmbito de falha, as ações no `foreach` execute apenas uma vez. 
    Muitas ações falhadas causam uma ação por falha.

4. Enviar um HTTP POST `foreach` do item de corpo de resposta, ou `@item()['outputs']['body']`. O `@result()` forma item é igual a `@actions()` formam e pode ser analisado da mesma forma.

5. Inclua dois cabeçalhos personalizados com o nome da ação falhada `@item()['name']` e a executar o cliente ID de controlo `@item()['clientTrackingId']`.

Para referência, eis um exemplo de um único `@result()` item, que mostra o `name`, `body`, e `clientTrackingId` propriedades que são analisadas no exemplo anterior. Fora de um `foreach`, `@result()` devolve uma matriz destes objetos.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Para efetuar diferentes excepção a processar a padrões, pode utilizar as expressões apresentadas anteriormente. Pode optar por executar uma única excepção a processar a ação fora do âmbito que aceita a matriz filtrada completa de falhas e remova o `foreach`. Também pode incluir outras propriedades útil do `@result()` resposta apresentada anteriormente.

## <a name="azure-diagnostics-and-telemetry"></a>Telemetria e diagnóstico do Azure

Os padrões de anteriores são ótimos processar erros e exceções dentro de uma execução, mas também pode identificar e respondem a erros independentes a execução de si próprio. 
[Diagnóstico do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornece uma forma simples para enviar todos os eventos de fluxo de trabalho (incluindo todos os Estados de execução e a ação) para uma conta de armazenamento do Azure ou de um Hub de eventos do Azure. Para avaliar os Estados da execução, pode monitorizar os registos e as métricas ou publicá-los em qualquer ferramenta monitorização que preferir. Uma opção de potencial é transmitido em fluxo através do Hub de eventos do Azure para todos os eventos [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics, pode escrever consultas em direto fora de quaisquer anomalias, médias ou falhas dos registos de diagnóstico. Do Stream Analytics pode facilmente de saída para outras origens de dados como filas, tópicos, SQL Server, base de dados do Azure Cosmos e Power BI.

## <a name="next-steps"></a>Passos Seguintes

* [Veja como um cliente baseia-se com Azure Logic Apps de processamento de erros](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Localizar mais cenários e exemplos de Logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Saiba como criar implementações automatizadas para aplicações lógicas](../logic-apps/logic-apps-create-deploy-template.md)
* [Criar e implementar aplicações lógicas com o Visual Studio](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
