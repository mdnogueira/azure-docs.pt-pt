---
title: "Criar ciclos e âmbitos ou debatch dados em fluxos de trabalho - Azure Logic Apps | Microsoft Docs"
description: "Criar ciclos para iterar através de dados, as ações de grupo para âmbitos, ou debatch dados para iniciar o mais fluxos de trabalho no Azure Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 413a2ba9107ca259ed577825bf0a17ff5622f1ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Ciclos, Âmbitos e Divisões do Logic Apps
  
As Logic Apps fornece várias formas de trabalhar com matrizes, coleções, lotes e repete num fluxo de trabalho.
  
## <a name="foreach-loop-and-arrays"></a>Ciclo de ForEach e matrizes
  
As Logic Apps permite-lhe cíclicas através de um conjunto de dados e executar uma ação para cada item.  Isto é possível através de `foreach` ação.  No estruturador, pode especificar para adicionar um para cada ciclo.  Depois de selecionar a matriz de que pretende iterar, pode começar a adicionar ações.  Atualmente está limitado a ação apenas um por foreach ciclo, mas esta restrição irá ser lifted nas próximas semanas.  Uma vez no ciclo pode começar a especificar que devem ocorrer em cada valor de matriz.

Se utilizar a vista de código, pode especificar um para cada ciclo como abaixo.  Este é um exemplo de um para cada ciclo que envia uma mensagem de e-mail para cada endereço de correio eletrónico que contém 'microsoft.com':

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` ação pode voltar a matrizes de ativação pós-falha até 5000 linhas.  Por predefinição, cada iteração será executado em paralelo.  

### <a name="sequential-foreach-loops"></a>Ciclos de ForEach sequenciais

Para ativar um ciclo de foreach sequencialmente, executar o `Sequential` deve ser adicionada a opção de operação.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Até o ciclo
  
  Pode efetuar uma ação ou uma série de ações até que a condição é cumprida.  O cenário mais comum para esta situação está a chamar um ponto final quando obtiver resposta que procura.  No estruturador, pode especificar para adicionar um até o ciclo.  Depois de adicionar ações dentro do ciclo, pode definir a condição de saída, bem como o ciclo de limites.  Não há um atraso de 1 minuto entre ciclos de ciclo.
  
  Se utilizar a vista de código, pode especificar um até ciclo, conforme mostrado abaixo.  Este é um exemplo de um ponto final de HTTP ao chamar até que o corpo da resposta tem o valor 'Concluída'.  A atualização estará concluída quando a 
  
  * Resposta de HTTP tem o estado 'Concluída'
  * Atingir para 1 hora
  * Tem looped 100 vezes
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn e debatching

Por vezes, um acionador poderá receber uma matriz de itens que pretende debatch e iniciar um fluxo de trabalho por item.  Isto pode ser conseguido através de `spliton` comando.  Por predefinição, se o seu swagger acionador Especifica um payload de que é uma matriz, um `spliton` serão adicionados e iniciar uma execução por item.  SplitOn só pode ser adicionado a um acionador.  Isto pode ser manualmente configurado ou substituí-lo na vista de código de definição.  Atualmente SplitOn pode debatch matrizes até 5000 itens.  Não pode ter um `spliton` e também implementar o padrão de resposta síncronas.  Qualquer fluxo de trabalho chamado que tem um `response` ação além `spliton` será executada no modo assíncrono e enviar um imediata `202 Accepted` resposta.  

SplitOn pode ser especificado na vista de código como o exemplo seguinte.  Isto recebe uma matriz de itens e debatches em cada linha.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>âmbitos

É possível uma série de ações em conjunto com um âmbito de grupo.  Isto é particularmente útil para implementar o processamento de exceções.  O estruturador pode adicionar um novo âmbito e começar a adicionar quaisquer ações dentro do mesmo.  Pode definir âmbitos na vista de código como o seguinte:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```