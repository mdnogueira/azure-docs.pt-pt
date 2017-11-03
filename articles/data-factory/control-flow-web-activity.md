---
title: Web atividade de Azure Data Factory | Microsoft Docs
description: "Saiba como pode utilizar a atividade de Web, uma das atividades de fluxo de controlo suportadas pela fábrica de dados, para invocar um ponto final de REST de um pipeline."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: shlo
ms.openlocfilehash: b5661dea3a63f6e7e5b67261bc9704061ae9c5b6
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="web-activity-in-azure-data-factory"></a>Atividade de Web no Azure Data Factory
A atividade Web pode ser utilizada para chamar um ponto final REST personalizado a partir de um pipeline do Data Factory. Pode transmitir conjuntos de dados e serviços ligados aos quais a atividade tem acesso e que pode consumir. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [documentação do Data Factory versão 1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Sintaxe

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade de web | Cadeia | Sim
tipo | Tem de ser definido como **WebActivity**. | Cadeia | Sim
Método | Método de REST API para o ponto final de destino. | Cadeia. <br/><br/>Tipos suportados: "GET", "Publicar", "Colocar" | Sim
URL | Ponto final de destino e o caminho | Cadeia (ou expressão com o resultType da cadeia) | Sim
Cabeçalhos | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma e o tipo de um pedido: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Cadeia (ou expressão com o resultType da cadeia) | Sim, é necessário o cabeçalho Content-type. `"headers":{ "Content-Type":"application/json"}`
Corpo | Representa o payload de que é enviado para o ponto final. Necessário para métodos POST/PUT.  | Cadeia (ou expressão com o resultType da cadeia). <br/><br/>Consulte o esquema do payload de pedido no [esquema de payload de pedido](#request-payload-schema) secção. | Não
Autenticação | Método de autenticação utilizado para chamar o ponto final. Os tipos suportados são "Basic ou ClientCertificate." Para obter mais informações, consulte [autenticação](#authentication) secção. Se não for necessária a autenticação, exclua esta propriedade. | Cadeia (ou expressão com o resultType da cadeia) | Não
Conjuntos de dados | Lista de conjuntos de dados transmitido para o ponto final. | Matriz de referências de conjunto de dados. Pode ser uma matriz vazia. | Sim
linkedServices | Lista de serviços ligados transmitido para o ponto final. | Matriz de referências de serviço ligado. Pode ser uma matriz vazia. | Sim

> [!NOTE]
> Pontos finais REST, que invoca a atividade de web tem de devolver uma resposta de tipo de JSON.

## <a name="authentication"></a>Autenticação

### <a name="none"></a>Nenhuma
Se não for necessária a autenticação, não inclua a propriedade de "autenticação".

### <a name="basic"></a>Básica
Especifique o nome de utilizador e palavra-passe para utilizar com a autenticação básica. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado de cliente
Especifique com codificação base64 conteúdo de um ficheiro PFX e a palavra-passe. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Esquema de payload de pedido
Quando utiliza o método POST/PUT, a propriedade body representa o payload de que é enviado para o ponto final. Pode passar conjuntos de dados e serviços ligados como parte do payload. Segue-se o esquema para o payload de: 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>Exemplo
Neste exemplo, a atividade de web no pipeline chama um ponto final REST. Transmite um serviço ligado SQL do Azure e um conjunto de dados SQL do Azure para o ponto final. O ponto final REST utiliza a cadeia de ligação de SQL do Azure para ligar ao servidor SQL do Azure e devolve o nome da instância do SQL server. 

### <a name="pipeline-definition"></a>Definição de pipeline

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Valores de parâmetros de pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Código de ponto final de serviço Web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Passos seguintes
Outras atividades de fluxo de controlo suportadas pela fábrica de dados, consulte: 

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
