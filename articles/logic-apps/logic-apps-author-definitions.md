---
title: Definir fluxos de trabalho com JSON - Azure Logic Apps | Microsoft Docs
description: "Como escrever as definições de fluxo de trabalho no JSON para aplicações lógicas"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7f9e5a10066df8a464c285273e77a85c0d562ebb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>Criar definições de fluxo de trabalho para aplicações lógicas utilizando JSON

Pode criar definições de fluxo de trabalho para [Azure Logic Apps](logic-apps-what-are-logic-apps.md) simples e declarativo linguagem de JSON. Se ainda não o fez, consulte primeiro [como criar a sua primeira aplicação lógica com o Designer de aplicação lógica](logic-apps-create-a-logic-app.md). Além disso, consulte o [completa de referência para o idioma de definição de fluxo de trabalho](http://aka.ms/logicappsdocs).

## <a name="repeat-steps-over-a-list"></a>Repita os passos através de uma lista

Para iterar através de uma matriz que tenha até 10 000 itens e efetuar uma ação para cada item, utilize o [foreach tipo](logic-apps-loops-and-scopes.md).

## <a name="handle-failures-if-something-goes-wrong"></a>Processar falhas se algo não bate

Normalmente, o que pretende incluir uma *passo remediação* — algumas lógica que executa *se, e apenas se* um ou mais das suas chamadas de falha. Neste exemplo obtém dados de vários locais, mas se a chamada falhar, queremos publicar uma mensagem algures, pelo que iremos pode identificar que falha mais tarde:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

Para especificar que `postToErrorMessageQueue` só é executada após `readData` tem `Failed`, utilize o `runAfter` propriedade, por exemplo, para especificar uma lista de valores possíveis, para que `runAfter` pode ser `["Succeeded", "Failed"]`.

Por fim, porque este exemplo processa agora o erro, mas já não marcar a run as `Failed`. Porque adicionámos o passo para processar esta falha neste exemplo, tem de executar `Succeeded` apesar de um passo `Failed`.

## <a name="execute-two-or-more-steps-in-parallel"></a>Executar passos de dois ou mais em paralelo

Para executar várias ações em paralelo, o `runAfter` propriedade tem de ser equivalente ao tempo de execução. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

Neste exemplo, ambos `branch1` e `branch2` estiver configurada para ser executada `readData`. Como resultado, ambos os ramos executam em paralelo. Timestamp para ambos os ramos é idêntico.

![Paralelo](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>Associar dois ramos paralelos

Pode associar duas ações que estão configuradas serem executadas em paralelo adicionando itens para a `runAfter` propriedade do exemplo anterior.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![Paralelo](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>Itens de lista de mapa para uma configuração diferente

Em seguida, vamos supor que queremos obter conteúdo diferente com base no valor de uma propriedade. Podemos criar um mapa de valores para destinos como um parâmetro:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

Neste caso, vamos primeiro de obter uma lista dos artigos. Com base na categoria do que foi definida como um parâmetro, o segundo passo utiliza um mapa para procurar o URL para obter o conteúdo.

Algumas vezes a ter em atenção aqui: 

*   O [ `intersection()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) função verifica se a categoria corresponde a uma das categorias definidas conhecidas.

*   Depois de obtemos a categoria, iremos pode solicitar o item de mapa utilizando parênteses Retos:`parameters[...]`

## <a name="process-strings"></a>Cadeias de processo

Pode utilizar várias funções para manipular cadeias. Por exemplo, suponha que temos uma cadeia que queremos para passar para um sistema, mas não são certeza sobre o processamento adequado para a codificação de carateres. Uma das alternativas consiste em base64 codificar esta cadeia. No entanto, para evitar escape num URL, vamos substituir alguns carateres. 

Pretendemos também uma subcadeia de nome a ordem porque os primeiro cinco carateres não são utilizados.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Trabalho do dentro para fora:

1. Obter o [ `length()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) para o nome do orderer, por isso, vamos voltar o número total de carateres.

2. Subtrair 5, uma vez que queremos uma cadeia mais curta.

3. Na realidade, colocar o [ `substring()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Iremos começar no índice `5` e aceda o resto da cadeia.

4. Converter este subcadeia para um [ `base64()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) cadeia.

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)todos os o `+` carateres com `-` carateres.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)todos os o `/` carateres com `_` carateres.

## <a name="work-with-date-times"></a>Trabalhar com as horas de data

Data vezes pode ser útil, especialmente quando estiver a tentar solicitar dados de uma origem de dados que não suporta naturalmente *acionadores*. Também pode utilizar data vezes para encontrar demoram quanto vários passos.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

Neste exemplo, extrair a `startTime` do passo anterior. Em seguida, vamos obter a hora atual e subtrair um segundo:

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

Pode utilizar outras unidades de tempo, como `minutes` ou `hours`. Por fim, vamos pode comparar estes dois valores. Se o primeiro valor é menor que o segundo valor, em seguida, mais do que um segundo foi efectuada com êxito uma vez que foi colocada a ordem.

Para formatar datas, podemos utilizar ao mesmo tempo cadeia. Por exemplo, para obter o RFC1123, utilizamos [ `utcnow('r')` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). Para saber mais sobre a formatação de data, consulte [linguagem de definição de fluxo de trabalho](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## <a name="deployment-parameters-for-different-environments"></a>Parâmetros de implementação para os diferentes ambientes

Normalmente, ciclos de vida de implementação tem um ambiente de desenvolvimento, um ambiente de teste e ambiente de produção. Por exemplo, poderá utilizar a mesma definição em todos os estes ambientes, mas utilizar bases de dados diferentes. Da mesma forma, pode querer utilizar a mesma definição em regiões diferentes para elevada disponibilidade, mas que cada instância da aplicação lógica para comunicar com a base de dados nessa região.
Este cenário é diferente do demorar parâmetros *runtime* onde em vez disso, deve utilizar o `trigger()` funcionar do exemplo anterior.

Pode começar com uma definição básica com este exemplo:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

O real no `PUT` pedido para aplicações lógicas, pode fornecer o parâmetro `uri`. Porque já não existe um valor predefinido, o payload da aplicação lógica requer este parâmetro:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Cada ambiente, pode fornecer um valor diferente para o `connection` parâmetro. 

Para todas as opções que tem de criar e gerir as logic apps, consulte o [documentação da REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx). 
