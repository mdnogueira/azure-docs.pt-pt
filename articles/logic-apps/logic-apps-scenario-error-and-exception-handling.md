---
title: "Processamento de exceções & cenário de registo de erro - Azure Logic Apps | Microsoft Docs"
description: "Descreve um caso de utilização real sobre o processamento de exceções avançadas e registo de erros para o Azure Logic Apps"
keywords: 
services: logic-apps
author: hedidin
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/29/2016
ms.author: LADocs; b-hoedid
ms.openlocfilehash: 044de27c75da93c95609110d2b73336c42f746fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Cenário: Processamento de exceções e registo de erros para aplicações lógicas

Este cenário descreve como pode expandir a uma aplicação lógica para um melhor suporte de processamento de exceções. Iremos tiver utilizado um caso de utilização de reais responder à pergunta: "Aplicações lógicas do Azure suporta exceções e processamento de erros?"

> [!NOTE]
> O esquema atual do Azure Logic Apps fornece um modelo padrão para respostas de ação. Este modelo inclui validação interna e respostas de erros devolvidas por uma aplicação API.

## <a name="scenario-and-use-case-overview"></a>Descrição geral de cenário de cenário e utilização

Eis o bloco como o caso de utilização para este cenário: 

Uma organização de cuidados de saúde conhecida parte-na desenvolver uma solução do Azure que criaria um portal patient através do Microsoft Dynamics CRM Online. São necessárias para enviar os registos de sessão entre o portal patient Dynamics CRM Online e da Salesforce. Iremos foram-lhe pedidos para utilizar o [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) padrão para todas as patient registos.

O projeto tinha dois requisitos principais:  

* Um método para criar registos enviados a partir do portal do Dynamics CRM Online
* Uma forma de ver os erros que ocorreram no fluxo de trabalho

> [!TIP]
> Para um vídeo de alto nível sobre este projeto, consulte [grupo de utilizadores de integração](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "grupo de utilizadores de integração").

## <a name="how-we-solved-the-problem"></a>Como é resolvido o problema

Escolhemos [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/ "Azure Cosmos DB") como um repositório para os registos de erro e de registo (Cosmos DB refere-se para registos como documentos). Uma vez Azure Logic Apps tem um modelo padrão para todas as respostas, seria não temos que criar um esquema personalizado. Foi possível criar uma aplicação API para **inserir** e **consulta** para registos de erro e registo. Iremos também definir um esquema para cada na aplicação API.  

Foi outro requisito necessário para remover registos após uma data determinada. BD do cosmos tem uma propriedade denominada [TTL de](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "TTL de") (TTL), que permitido-nos definir um **TTL de** valor para cada registo ou a coleção. Esta capacidade eliminado a necessidade de eliminar manualmente os registos na base de dados do Cosmos.

> [!IMPORTANT]
> Para concluir este tutorial, terá de criar uma base de dados de base de dados do Cosmos e duas coleções (registo e erros).

## <a name="create-the-logic-app"></a>Criar a aplicação lógica

O primeiro passo é criar a aplicação lógica e abra a aplicação no Designer de aplicação lógica. Neste exemplo, estamos a utilizar aplicações lógicas de principal-subordinado. Vamos assumir já criou o principal e vai criar uma aplicação de lógica de subordinados.

Vamos começar porque, vamos iniciar o registo provenientes de Dynamics CRM Online, na parte superior. Iremos tem de utilizar um **pedido** acionar porque a aplicação de lógica principal aciona menor.

### <a name="logic-app-trigger"></a>Acionador de aplicação lógica

Estamos a utilizar um **pedido** acionar conforme mostrado no exemplo seguinte:

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


## <a name="steps"></a>Passos

Iremos tem de iniciar a origem (pedido) do registo patient partir do portal do Dynamics CRM Online.

1. Iremos tem de obter um novo registo de sessão do Dynamics CRM Online.

   O acionador feitos CRM fornece-nos com o **CRM PatentId**, **tipo de registo**, **novo ou atualizar o registo** (novo ou atualizar o valor booleano), e **SalesforceId**. O **SalesforceId** pode ser nulo porque só é utilizada para uma atualização.
   Vamos obter o registo CRM utilizando o CRM **PatientID** e **tipo de registo**.

2. Em seguida, precisamos de adicionar a nossa aplicação de API do DocumentDB **InsertLogEntry** operação conforme mostrado aqui no Designer de aplicação lógica.

   **Inserir entrada de registo**

   ![Inserir entrada de registo](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Inserir entrada de erro**

   ![Inserir entrada de registo](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **A verificação para criar registo falha**

   ![Condição](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Código de origem da aplicação lógica

> [!NOTE]
> Os exemplos seguintes são apenas exemplos. Porque este tutorial é baseado numa implementação agora na produção, o valor de um **origem nó** pode não apresentar as propriedades que estão relacionados com o agendamento de uma sessão. > 

### <a name="logging"></a>Registo

O exemplo de código de aplicação lógica seguinte mostra como processar registo.

#### <a name="log-entry"></a>Entrada de registo

Eis o código de origem da aplicação de lógica para inserir uma entrada de registo.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Pedido de registo

Eis a mensagem de pedido de registo publicada para a aplicação API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Resposta de registo

Eis a mensagem de resposta de registo da aplicação API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Agora vamos ver os passos de processamento de erros.

### <a name="error-handling"></a>Processamento de erros

O exemplo de código de aplicação lógica seguinte mostra como pode implementar o processamento de erros.

#### <a name="create-error-record"></a>Criar registo de erro

Eis o código de origem da aplicação de lógica para a criação de um registo de erro.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Erro de inserção na base de dados do Cosmos – pedido

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Inserir o erro na base de dados do Cosmos - resposta

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Resposta de erro do Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Devolver a resposta de volta para a aplicação de lógica principal

Depois de obter a resposta, pode passar a resposta de volta para a aplicação de lógica principal.

#### <a name="return-success-response-to-parent-logic-app"></a>Devolver a resposta de êxito a aplicação de lógica principal

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Devolver a resposta de erro a aplicação de lógica principal

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Portal e do repositório do cosmos DB

A nossa solução adicionada capacidades com [Cosmos DB](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Portal de gestão de erro

Para ver os erros, pode criar uma aplicação web MVC para apresentar os registos de erro da base de dados do Cosmos. O **lista**, **detalhes**, **editar**, e **eliminar** operations estão incluído na versão atual.

> [!NOTE]
> Editar operação: Cosmos DB substitui todo o documento. Os registos mostrados no **lista** e **detalhe** as vistas são apenas exemplos. Não são registos de sessão patient real.

Seguem-se exemplos de nosso detalhes da aplicação MVC criados com a abordagem descrita anteriormente.

#### <a name="error-management-list"></a>Lista de gestão de erros
![Lista de erros](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Vista de detalhes do erro gestão
![Detalhes do Erro](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal de gestão do registo

Para ver os registos, também foi criada uma aplicação web MVC. Seguem-se exemplos de nosso detalhes da aplicação MVC criados com a abordagem descrita anteriormente.

#### <a name="sample-log-detail-view"></a>Vista de detalhes de registo de exemplo
![Vista de detalhes de registo](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Detalhes da aplicação API

#### <a name="logic-apps-exception-management-api"></a>API de gestão de exceção de aplicações do lógica

A nossa aplicação de API de gestão do open source Azure Logic Apps exceção fornece uma funcionalidade, conforme descrito aqui – existem dois controladores:

* **ErrorController** insere um registo de erro (documento) numa coleção do DocumentDB.
* **LogController** insere um registo (documento) numa coleção do DocumentDB.

> [!TIP]
> Utilizam os dois controladores `async Task<dynamic>` operações, permitindo que as operações de resolver em runtime, para que possa criar o esquema do DocumentDB no corpo da operação. 
> 

Todos os documentos no DocumentDB tem de ter um ID exclusivo. Estamos a utilizar `PatientId` e adicionar um timestamp que é convertida para um valor de timestamp Unix (double). Iremos truncar o valor para remover o valor fracional.

Pode ver o código de origem do nosso controlador Erro API [a partir do GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Chamamos a API de uma aplicação lógica, utilizando a seguinte sintaxe:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Verifica a expressão do anterior exemplo de código para o *Create_NewPatientRecord* estado **falha**.

## <a name="summary"></a>Resumo

* Pode facilmente implementar registo e erros numa aplicação lógica.
* Pode utilizar o DocumentDB como o repositório para registos de erro e de registo (documentos).
* Pode utilizar MVC para criar um portal para apresentar os registos de registo e o erro.

### <a name="source-code"></a>Código de origem

O código de origem para a aplicação API de gestão exceção Logic Apps está disponível neste [repositório do GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de gestão de exceção de aplicação lógica").

## <a name="next-steps"></a>Passos seguintes

* [Ver mais cenários e exemplos de aplicação lógica](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Saiba mais sobre a monitorização de aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Criar modelos de implementação automática para as logic apps](../logic-apps/logic-apps-create-deploy-template.md)
