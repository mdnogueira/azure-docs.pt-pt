---
title: "Instruções de API de REST de monitorização do Azure | Microsoft Docs"
description: "Como autenticar pedidos e utilize a API de REST de Monitor do Azure para obter as definições de métrica disponíveis e a métricos valores."
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.search.region: 
ms.search.scope: 
ms.search.validFrom: 
ms.dyn365.ops.version: 
ms.topic: article
ms.date: 09/18/2017
ms.author: mcollier
ms.openlocfilehash: ab522b444c234e1159acfea1780bae1801c4d047
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Instruções de API de REST de monitorização do Azure
Este artigo mostra como efetuar a autenticação, pelo que pode utilizar o seu código a [referência de API de REST do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

A API de Monitor do Azure torna possível através de programação obter as definições de métrica de predefinidas disponíveis, granularidade e valores de métricos. Os dados podem ser guardados num arquivo de dados separada, como SQL Database do Azure, base de dados do Azure Cosmos ou do Azure Data Lake. A partir daí analysis adicionais podem ser efetuadas conforme necessário.

Para além de trabalhar com vários pontos de dados métricos, a API de Monitor também possibilita a lista de regras de alertas, ver registos de atividade e muito mais. Para obter uma lista completa de operações disponíveis, consulte o [referência de API de REST do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Os pedidos de autenticação do Azure Monitor
O primeiro passo é autenticar o pedido.

Todas as tarefas executadas contra a API de Monitor do Azure utilizam o modelo de autenticação do Azure Resource Manager. Por conseguinte, todos os pedidos têm de ser autenticados no Azure Active Directory (Azure AD). Uma abordagem para autenticar a aplicação de cliente é criar um Azure AD principal de serviço e obter o token de autenticação (JWT). O script de exemplo seguinte demonstra a criação de um serviço do Azure AD principal através do PowerShell. Para instruções mais detalhadas, consulte a documentação em [com o Azure PowerShell para criar um principal de serviço para aceder a recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password). Também é possível [criar um principal de serviço através do portal do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar a API de Monitor do Azure, a aplicação cliente deve utilizar o principal de serviço criado anteriormente para se autenticar. O script do PowerShell de exemplo seguinte mostra uma abordagem, utilizando o [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) para obter o token de autenticação JWT. O token JWT é transmitido como parte de um parâmetro de autorização de HTTP nos pedidos para a API de REST de Monitor do Azure.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Após a autenticação, pode, em seguida, executar consultas contra a API de REST de Monitor do Azure. Existem duas consultas úteis:

1. Lista as definições de métricas para um recurso
2. Obter os valores de métricos

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Obter as definições de métrica (Multidimensional API)

Utilize o [definições de métrica de Monitor de Azure REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions) para aceder à lista de métricas que estão disponíveis para um serviço.

**Método**: introdução

**URI de pedido**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{ resourceProviderNamespace}*/*{resourceType}*/*{resourceName*/providers/microsoft.insights/metricDefinitions? API-version =*{apiVersion}*

Por exemplo, para obter as definições de métricas para uma conta de armazenamento do Azure, o pedido deverá aparecer da seguinte forma:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2017-05-01-preview"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Para obter as definições de métrica utilizando as métricas de Monitor de Azure multidimensionais REST API, utilize "2017-05-01-preview" como a versão da API.
>
>

O corpo da resposta JSON resultante é semelhante ao seguinte exemplo: (tenha em atenção que a segunda métrica tem dimensões)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Obter valores de dimensão (Multidimensional API)
Depois das definições de métricas disponíveis são conhecidas, poderão existir alguns métricas que tem dimensões. Antes de consultar a métrica que pretende detetar que o intervalo de valores tem uma dimensão. Com base nestes valores de dimensão, em seguida, pode optar por filtrar ou as métricas com base nos valores de dimensão ao consultar as métricas de segmento. Utilizar nome 'value' a métrica (não o ' localizedValue') para todos os pedidos de filtragem (por exemplo, obter os pontos de dados métricos 'CpuTime' e 'Pedidos'). Se não existem filtros forem especificados, é devolvida a métrica de predefinição.

> [!NOTE]
> Para obter os valores de dimensão utilizando a API de REST de Monitor do Azure, utilize "2017-05-01-preview" como a versão da API.
>
>

**Método**: introdução

**URI de pedido**: https://management.azure.com/subscriptions/*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/*{ recursos-fornecedor-namespace}*/*{tipo de recurso}*/*{nome do recurso}*/providers/microsoft.insights/metrics?metric= *{métrica}*& timespan =*{starttime/endtime}*& $filter =*{filtro}*& resultType = metadados & api-version =*{ apiVersion}*

Por exemplo, para obter a lista de possíveis valores da dimensão' nome da API' para a métrica de 'Transações' durante um intervalo de tempo especificado, o pedido seria o seguinte:

```PowerShell
$filter = "APIName eq '*'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-01T00:00:00Z/2017-09-10T00:00:00Z&resultType=metadata&$filter=${filter}&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
O corpo da resposta JSON resultante é semelhante ao seguinte exemplo:

```JSON
{
  "timespan": "2017-09-01T00:00:00Z/2017-09-10T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "PutPage"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "Unknown"
            }
          ]
        },
        ...
      ]    
    }
  ]
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Obter a métricos valores (Multidimensional API)
Depois das definições de métricas disponíveis e valores de dimensão possíveis são conhecidos, em seguida, é possível obter os valores da métricos relacionados. Utilize nome 'value' a métrica (não o ' localizedValue') para todos os pedidos de filtragem. Se não existem filtros de dimensão forem especificados, é devolvida a métrica de agregados rolled cópias de segurança.

> [!NOTE]
> Para obter os valores de métricos multidimensionais utilizando a API de REST de Monitor do Azure, utilize "2017-05-01-preview" como a versão da API.
>
>

**Método**: introdução

**URI de pedido**: https://management.azure.com/subscriptions/*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/*{ recursos-fornecedor-namespace}*/*{tipo de recurso}*/*{nome do recurso}*/providers/microsoft.insights/metrics?metric= *{métrica}*& timespan =*{starttime/endtime}*& $filter =*{filtro}*& intervalo =*{um intervalo de agregação}* & agregação =*{aggreation}*& api-version =*{apiVersion}*

Por exemplo, para obter os valores de métricos a métrica de 'as ' transações de armazenamento durante um intervalo de 5 minutos, para todas as transações para o nome da API 'GetBlobProperties', o pedido seria o seguinte:

```PowerShell
$filter = "APIName eq 'GetBlobProperties'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-19T02:00:00Z/2017-09-19T02:05:00Z&$filter=${filter}&interval=PT1M&aggregation=Count&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
O corpo da resposta JSON resultante é semelhante ao seguinte exemplo:

```JSON
{
  "cost": 0,
  "timespan": "2017-09-19T02:00:00Z/2017-09-19T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "count": 2.0
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "count": 1.0
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "count": 3.0
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "count": 7.0
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "count": 2.0
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="retrieve-metric-definitions"></a>Obter as definições de métrica
Utilize o [definições de métrica de Monitor de Azure REST API](https://msdn.microsoft.com/library/mt743621.aspx) para aceder à lista de métricas que estão disponíveis para um serviço.

**Método**: introdução

**URI de pedido**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{ resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions? API-version =*{apiVersion}*

Por exemplo, para obter as definições de métricas para uma aplicação de lógica do Azure, o pedido deverá aparecer da seguinte forma:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Para obter as definições de métrica utilizando a API de REST de Monitor do Azure, utilize "2016-03-01" como a versão da API.
>
>

O corpo da resposta JSON resultante é semelhante ao seguinte exemplo:
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Para obter mais informações, consulte o [lista as definições de métricas para um recurso na API REST da Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) documentação.

## <a name="retrieve-metric-values"></a>Obter valores de métricos
Depois das definições de métricas disponíveis são conhecidas, em seguida, é possível obter os valores da métricos relacionados. Utilizar nome 'value' a métrica (não o ' localizedValue') para todos os pedidos de filtragem (por exemplo, obter os pontos de dados métricos 'CpuTime' e 'Pedidos'). Se não existem filtros forem especificados, é devolvida a métrica de predefinição.

> [!NOTE]
> Para obter os valores de métricos utilizando a API de REST de Monitor do Azure, utilize "2016-09-01" como a versão da API.
>
>

**Método**: introdução

**URI de pedido**: https://management.azure.com/subscriptions/*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/*{ recursos-fornecedor-namespace}*/*{tipo de recurso}*/*{nome do recurso}*/providers/microsoft.insights/metrics?$filter= *{filtro}*& api-version =*{apiVersion}*

Por exemplo, para obter os pontos de dados métricos RunsSucceeded para o intervalo de tempo indicado e um grão de tempo de 1 hora, o pedido seria o seguinte:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

O corpo da resposta JSON resultante é semelhante ao seguinte exemplo:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Para obter vários pontos de dados ou de agregação, adicione nomes de definição de métrica e tipos de agregação para o filtro, como mostrado no exemplo seguinte:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
O corpo da resposta JSON resultante é semelhante ao seguinte exemplo:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Utilizar ARMClient
Uma abordagem adicional consiste em utilizar [ARMClient](https://github.com/projectkudu/armclient) no seu computador Windows. ARMClient processa a autenticação do Azure AD (e o token JWT resultante) automaticamente. Os passos seguintes descrevem a utilização de ARMClient para obter os dados métricos:

1. Instalar [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/armclient).
2. Na janela de terminal, escreva *armclient.exe início de sessão*. Se o fizer, irá pedir-lhe para iniciar sessão no Azure.
3. Tipo *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Tipo *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Por exemplo, para obter as definições de métricas para uma aplicação lógica específica, emita o comando seguinte:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Obter o ID de recurso
Utilizando a API REST realmente pode ajudar a compreender as definições de métrica disponíveis, a granularidade e os valores relacionados. Se a informações são útil quando utilizar o [biblioteca de gestão do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Para o código anterior, o ID de recurso a utilizar é o caminho completo para o recurso do Azure pretendido. Por exemplo, para consultar em relação a uma aplicação Web do Azure, o ID de recurso, seria:

*/subscriptions/{Subscription-ID}/resourceGroups/{Resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

A lista seguinte contém alguns exemplos de formatos de ID de recurso para vários recursos do Azure:

* **IoT Hub** -/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Agrupamento elástico de SQL** -/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Sql/servers/*{conjunto-db}*/elasticpools/*{nome de conjunto de sql}*
* **Base de dados do SQL (v12)** -/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Sql/servers/*{nome do servidor}* /databases/*{nome-base de dados}*
* **Barramento de serviço** -/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.ServiceBus/*{namespace}* / *{nome-servicebus}*
* **Conjuntos de dimensionamento de máquina virtual** -/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{nome da vm}*
* **VMs** -/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.Compute/virtualMachines/*{nome da vm}*
* **Os Event Hubs** -/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}*{id de subscrição}*/resourceGroups/*{nome de grupo de recursos}*/providers/Microsoft.EventHub/namespaces/*{ eventhub-namespace}*

Existem abordagens alternativas para obter o ID de recurso, incluindo a utilização do Explorador de recursos do Azure, visualizar o recurso pretendido no portal do Azure e através do PowerShell ou a CLI do Azure.

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure
Para localizar o ID de recurso de um recurso pretendido, uma abordagem útil consiste em utilizar o [Explorador de recursos do Azure](https://resources.azure.com) ferramenta. Navegue para o recurso pretendido e, em seguida, observar o ID apresentado como a captura de ecrã seguinte:

![ALT "Explorador de recursos do Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure
O ID de recurso também pode ser obtido a partir do portal do Azure. Para tal, navegue para o recurso pretendido e, em seguida, selecione propriedades. O ID de recurso é apresentado na secção de propriedades, como mostrado na captura de ecrã seguinte:

![ALT "ID de recurso apresentado no painel de propriedades no portal do Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
O ID de recurso pode ser obtido utilizando cmdlets do PowerShell do Azure. Por exemplo, para obter o ID de recurso para uma aplicação de lógica do Azure, execute o cmdlet Get-AzureLogicApp, como no exemplo seguinte:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

O resultado deve ser semelhante ao seguinte exemplo:
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>CLI do Azure
Para obter o ID de recurso para uma conta de armazenamento do Azure utilizando a CLI do Azure, execute o comando 'Mostrar da conta de armazenamento az', conforme mostrado no exemplo seguinte:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

O resultado deve ser semelhante ao seguinte exemplo:
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Aplicações lógicas do Azure não foram ainda disponível através da CLI do Azure, deste modo, uma conta de armazenamento do Azure é mostrada no exemplo anterior.
>
>

## <a name="retrieve-activity-log-data"></a>Obter dados de registo de atividade
Para além das definições de métrica e os valores relacionados, também é possível utilizar a API de REST de Monitor do Azure para obter informações adicionais de interessantes relacionados com recursos do Azure. Por exemplo, é possível consultar [registo de atividade](https://msdn.microsoft.com/library/azure/dn931934.aspx) dados. O exemplo seguinte demonstra utilizando a API de REST de Monitor do Azure para dados de registo de atividade de consulta dentro de um intervalo de datas específico para uma subscrição do Azure:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Passos seguintes
* Reveja o [descrição geral da monitorização](monitoring-overview.md).
* Ver o [suportado métricas com a monitorização do Azure](monitoring-supported-metrics.md).
* Reveja o [do Microsoft Azure monitorizar referência da API REST](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Reveja o [biblioteca de gestão do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
