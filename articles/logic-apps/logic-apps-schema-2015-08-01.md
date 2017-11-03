---
title: "Esquema de atualizações de pré-visualização de 1-Agosto de 2015 - Azure Logic Apps | Microsoft Docs"
description: "Criar definições de JSON para o Azure Logic Apps com o esquema versão 2015-08-01-pré-visualização"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: LADocs; stepsic
ms.openlocfilehash: 35d7a56d5607dcc18a4407c65b92962d3d0dcd1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Atualizações do esquema para o Azure Logic Apps - preview 1 de Agosto de 2015

Este novo esquema e a API versão para o Azure Logic Apps inclui melhoramentos essenciais que tornam as logic apps mais fiável e mais fáceis de utilizar:

*   O **APIApp** tipo de ação é atualizado para uma nova [ **APIConnection** ](#api-connections) tipo de ação.
*   **Repetir** é mudado para [ **Foreach**](#foreach).
*   O [ **serviço de escuta HTTP** aplicação API](#http-listener) já não é necessário.
*   Chamar fluxos de trabalho subordinado utiliza um [novo esquema](#child-workflows).

<a name="api-connections"></a>
## <a name="move-to-api-connections"></a>Mover para ligações de API

A alteração maior é que já não tem de implementar as API Apps na sua subscrição do Azure, pelo que pode utilizar as APIs. Seguem-se as formas como pode utilizar as APIs:

* APIs geridas
* Suas APIs Web personalizado

Cada forma é processada de forma ligeiramente porque a respetiva gestão e modelos de alojamento são diferentes. Uma vantagem deste modelo é que estiver já não está restringida a recursos que são implementados no seu grupo de recursos do Azure. 

### <a name="managed-apis"></a>APIs geridas

Microsoft gere algumas APIs em seu nome, como o Office 365, Salesforce, Twitter e FTP. Pode utilizar algumas APIs geridas como-é, por exemplo, o Bing traduzir, enquanto outras precisam de configuração. Esta configuração denomina um *ligação*.

Por exemplo, quando utiliza o Office 365, tem de criar uma ligação que contém o token de início de sessão do Office 365. Este token é armazenada e atualizado para que a sua aplicação lógica sempre pode chamar a API do Office 365 em segurança. Em alternativa, se pretender ligar ao seu servidor SQL ou FTP, tem de criar uma ligação com a cadeia de ligação. 

Nesta definição, estas ações são denominadas `APIConnection`. Eis um exemplo de uma ligação que chama o Office 365 para enviar uma mensagem de e-mail:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

O `host` objeto é parte de entradas que é exclusiva para ligações de API e contém peças de tow: `api` e `connection`.

O `api` tem tempo de execução do URL onde gerida que API está alojado. Pode ver todos os disponíveis APIs geridas chamando `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Quando utilizar uma API, a API poderá ou poderá não ter qualquer *parâmetros de ligação* definido. Se a API não, não *ligação* é necessária. Se a API, terá de criar uma ligação. A criação de ligação com o nome que escolher. Em seguida, a referenciar o nome no `connection` dentro de objeto a `host` objeto. Para criar uma ligação num grupo de recursos, chamada:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Com o corpo do seguinte:

```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues": {
        "accountName": "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location": "{Logic app's location}"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Implementar APIs geridas por um modelo Azure Resource Manager

Pode criar uma aplicação completa num modelo Azure Resource Manager, desde o início de sessão interativo não é necessário.
Se o início de sessão é necessário, pode configurar tudo com o modelo Azure Resource Manager, mas ainda tem de visitar o portal para autorizar as ligações. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/', parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:, Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Pode ver neste exemplo, as ligações são apenas recursos que residem no seu grupo de recursos. Fizerem referência as APIs geridas disponíveis na sua subscrição.

### <a name="your-custom-web-apis"></a>Suas APIs Web personalizado

Se utilizar os suas próprias APIs, aqueles não gerida pela Microsoft, utilize o incorporado **HTTP** ação chamá-los. Para uma experiência ideal, deve expor um ponto final de Swagger para a API. Este ponto final permite o Designer de aplicação lógica para compor as entradas e saídas para a API. Sem Swagger, o estruturador pode mostrar apenas as entradas e saídas como opaco objetos JSON.

Eis um exemplo que mostra o novo `metadata.apiDefinitionUrl` propriedade:

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata": {
              "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method": "GET"
            }
        }
    }
}
```

Se alojar a API Web no App Service do Azure, a API Web é automaticamente apresentado na lista de ações disponíveis no designer. Caso contrário, terá de colar o URL diretamente. O ponto final de Swagger tem de ser não autenticado para ser utilizado no Designer de aplicação lógica, embora possa proteger API com qualquer métodos que suporta Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Chamada implementado as API apps 2015-08-01-pré-visualização

Se tiver implementado anteriormente uma aplicação API, pode chamar a aplicação com o **HTTP** ação.

Por exemplo, se utilizar o Dropbox para ficheiros de lista, o **pré-visualização 2014-12-01** definição da versão de esquema pode ter algo semelhante ao seguinte:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Pode construir a ação de HTTP equivalente como neste exemplo, enquanto a secção de parâmetros da definição da aplicação lógica permanece inalterada:

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata": {
              "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method": "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Walking através destas propriedades de um de cada:

| Propriedade de ação | Descrição |
| --- | --- |
| `type` |`Http`em vez de`APIapp` |
| `metadata.apiDefinitionUrl` |Para utilizar esta ação no Designer de aplicação lógica, incluem o ponto final de metadados, que é construído a partir de:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` |Construídas a partir de:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |Sempre`POST` |
| `inputs.body` |Idênticos para os parâmetros de aplicação API |
| `inputs.authentication` |Idênticos para a autenticação da aplicação de API |

Esta abordagem deverão funcionar para todas as ações de aplicação API. No entanto, lembre-se de que estas aplicações API anterior já não são suportadas. Por isso, deve mover para as duas outras opções anteriores, uma API gerida ou a alojar a API Web personalizados.

<a name="foreach"></a>
## <a name="renamed-repeat-to-foreach"></a>Mudar o nome 'Repetir' para 'foreach'

Para a versão do esquema anteriores, recebemos comentários do cliente que **repetir** foi confuso e não foi corretamente capturar que **repetir** foi realmente um para cada ciclo. Como resultado, podemos ter mudado `repeat` para `foreach`. Por exemplo, anteriormente seria escrita:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Agora, teria de escrita:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

A função `@repeatItem()` anteriormente foi utilizado para referenciar o item atual a ser iterated ativação pós-falha. Esta função é agora simplificada para `@item()`. 

### <a name="reference-outputs-from-foreach"></a>Saídas de referência de 'foreach'

Para a simplificação, as saídas da `foreach` ações não são eliminadas num objeto chamada `repeatItems`. Enquanto as saídas do anterior `repeat` foram de exemplo:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Agora estas saídas são:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Anteriormente, para obter o corpo da ação ao referenciar estes saídas:

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "repeat": "@outputs('pingBing').repeatItems",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@repeatItem().outputs.body"
            }
        }
    }
}
```

Agora pode efetuar em vez disso:

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "foreach": "@outputs('pingBing')",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@item().outputs.body"
            }
        }
    }
}
```

Com estas alterações, as funções `@repeatItem()`, `@repeatBody()`, e `@repeatOutputs()` são removidos.

<a name="http-listener"></a>
## <a name="native-http-listener"></a>Serviço de escuta HTTP nativo

As capacidades do serviço de escuta de HTTP estão agora incorporadas. Por isso, já não precisar de implementar uma aplicação de API do serviço de escuta de HTTP. Consulte [todos os detalhes sobre como efetuar o ponto final de aplicação de lógica possível chamar EndRead aqui](../logic-apps/logic-apps-http-endpoint.md). 

Com estas alterações, Removemos o `@accessKeys()` função, o que são substituídos com o `@listCallbackURL()` função para obter o ponto final quando for necessário. Além disso, tem de definir pelo menos um acionador agora na sua aplicação lógica. Se pretender `/run` o fluxo de trabalho, tem de ter um estes acionadores: `manual`, `apiConnectionWebhook`, ou `httpWebhook`.

<a name="child-workflows"></a>
## <a name="call-child-workflows"></a>Chamar fluxos de trabalho de subordinados

Anteriormente, chamar fluxos de trabalho subordinado necessário ir para o fluxo de trabalho, obter o token de acesso e colando o token a definição da aplicação lógica onde pretende chamar esse fluxo de trabalho subordinados. Com o novo esquema, o motor de Logic Apps gera automaticamente uma SAS no tempo de execução do fluxo de trabalho subordinados pelo que não terá de colar qualquer segredos para a definição. Segue-se um exemplo:

```
"mynestedwf": {
    "type": "workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "myendpointtrigger"
        },
        "queries": {
            "extrafield": "specialValue"
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "conditions": []
}
```

Uma segunda melhoria é que está a dar os fluxos de trabalho subordinado acesso total para o pedido de entrada. Isto significa que pode passar parâmetros a *consultas* secção e no *cabeçalhos* objeto e que pode definir completamente o corpo de todo.

Por fim, são necessárias alterações ao fluxo de trabalho subordinados. Enquanto foi anteriormente chamar um fluxo de trabalho subordinados diretamente, agora tem de definir um ponto final de Acionador no fluxo de trabalho do elemento principal chamar. Geralmente, deverá ser adicionado um acionador que tenha `manual` escreva e, em seguida, utilizar essa acionador na definição do principal. Tenha em atenção o `host` propriedade especificamente tem um `triggerName` porque tem de especificar sempre cujo accionador que está a invocar.

## <a name="other-changes"></a>Outras alterações

### <a name="new-queries-property"></a>Nova propriedade de 'consultas'

Todos os tipos de ação suportam agora uma nova entrada chamada `queries`. Esta entrada pode ser um objeto estruturado, em vez de ter de Monte manualmente a cadeia.

### <a name="renamed-parse-function-to-json"></a>Função 'Parse()' cujo nome foi alterado para 'json()'

Que estamos a adicionar mais conteúdos tipos em breve, pelo que iremos mudado o `parse()` funcionar para `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Disponível em breve: APIs de integração do Enterprise

Temos versões geridas ainda das APIs de integração empresarial, como o AS2. Entretanto, pode utilizar os APIs de BizTalk implementada existente através da ação de HTTP. Para obter detalhes, consulte "Utilizar suas API apps já implementados" no [Roteiro da integração](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
