---
title: "As respostas a alertas de análise de registos do OMS | Microsoft Docs"
description: "Alertas na análise de registos identificar informações importantes no seu repositório do OMS e podem proativamente notificá-lo de problemas ou da invocação ações para tentar corrigir as entradas.  Este artigo descreve como criar uma regra de alerta e detalhes de ações diferentes que podem realizar."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d936cf467ee7043b171cfc845f247f891f52f599
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Adicione ações para regras de alertas na análise de registos
Quando um [alerta é criado na análise de registos](log-analytics-alerts.md), tem a opção de [configurar a regra de alerta](log-analytics-alerts.md) para efetuar uma ou mais ações.  Este artigo descreve as diferentes ações que estão disponíveis e os detalhes sobre como configurar cada tipo.

| Ação | Descrição |
|:--|:--|
| [Correio eletrónico](#email-actions) | Envie um e-mail com os detalhes do alerta para um ou mais destinatários. |
| [Webhook](#webhook-actions) | Invocar um processo externo através de um pedido de HTTP POST único. |
| [Runbook](#runbook-actions) | Inicie um runbook na automatização do Azure. |


## <a name="email-actions"></a>Ações de e-mail
Ações de e-mail enviam um e-mail com os detalhes do alerta para um ou mais destinatários.  Pode especificar o assunto do correio, mas o respetivo conteúdo é um formato de padrão construído através da análise de registos.  Inclui as informações de resumo, como o nome do alerta além dos detalhes de até dez registos devolvidos pela pesquisa de registo.  Também inclui uma ligação para uma pesquisa de registo na análise de registos que irá devolver o conjunto completo de registos por essa consulta.   O remetente de correio é *equipa do Microsoft Operations Management Suite &lt; noreply@oms.microsoft.com &gt;* . 

Ações de correio eletrónico requerem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Assunto |Do assunto do e-mail.  Não é possível modificar o corpo da mensagem. |
| Destinatários |Endereços de todos os destinatários de correio eletrónico.  Se especificar mais de um endereço, separe os endereços com um ponto e vírgula (;). |


## <a name="webhook-actions"></a>Ações de Webhook

As ações de Webhook permitem-lhe invocar um processo externo através de um pedido de HTTP POST único.  O serviço que está a ser chamado deve suportar webhooks e determinar como irá utilizar qualquer payload recebe.  Também pode chamar uma API de REST que não suporta especificamente webhooks, desde que o pedido está num formato que compreende a API.  Exemplos de como utilizar um webhook em resposta a um alerta estão a enviar uma mensagem [Slack](http://slack.com) ou criar um incidente no [PagerDuty](http://pagerduty.com/).  Instruções completas de criação de uma regra de alerta com um webhook para chamar Slack estão disponível em [Webhooks nos alertas do Log Analytics](log-analytics-alerts-webhooks.md).

As ações de Webhook requerem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| URL do Webhook |O URL do webhook. |
| Payload JSON personalizado |Payload personalizado para enviar com o webhook.  Consulte abaixo para obter mais detalhes. |


Webhooks incluir um URL e um payload formatado em JSON que é os dados enviados para o serviço externo.  Por predefinição, o payload incluirá os valores na tabela seguinte.  Pode optar por substituir este payload por um personalizado da sua própria.  Nesse caso pode utilizar as variáveis na tabela para cada um dos parâmetros para incluir o respetivo valor no payload personalizado.

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, o payload de webook foi alterada.  Detalhes do formato estão em [API de REST de análise de registos do Azure](https://aka.ms/loganalyticsapiresponse).  Pode ver um exemplo em [amostras](#sample-payload) abaixo.

| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nome da regra de alerta. |
| AlertThresholdOperator |#thresholdoperator |Operador de limiar para a regra de alerta.  *Maior* ou *menor*. |
| AlertThresholdValue |#thresholdvalue |Valor de limiar para a regra de alerta. |
| LinkToSearchResults |#linktosearchresults |Ligar a pesquisa de registo de análise de registos que devolve os registos da consulta que criou o alerta. |
| O parâmetro ResultCount |#searchresultcount |Número de registos nos resultados da pesquisa. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Hora de fim para a consulta em formato UTC. |
| SearchIntervalInSeconds |#searchinterval |Janela de tempo para a regra de alerta. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Hora de início para a consulta em formato UTC. |
| SearchQuery |#searchquery |Consulta de pesquisa de registo utilizada pela regra de alerta. |
| SearchResults |Consulte abaixo |Registos devolvidos pela consulta no formato JSON.  Limitado para os primeiro 5 000 registos. |
| WorkspaceID |#workspaceid |ID da sua área de trabalho do OMS. |

Por exemplo, poderá especificar o payload personalizado seguinte, que inclui um único parâmetro chamado *texto*.  O serviço que chama o webhook seria possível esperar este parâmetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Este payload de exemplo seria resolver algo semelhante ao seguinte quando enviados para o webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir os resultados da pesquisa no payload personalizado, adicione a seguinte linha como uma propriedade de nível superior no json payload.  

    "IncludeSearchResults":true

Por exemplo, para criar um payload personalizado que inclua apenas o nome do alerta e os resultados da pesquisa, pode utilizar o seguinte. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Pode percorrer um exemplo completo de criação de uma regra de alerta com um webhook para iniciar um serviço externo em [criar uma ação do webhook alerta na análise de registos do OMS enviar mensagem para Slack](log-analytics-alerts-webhooks.md).


## <a name="runbook-actions"></a>Ações de Runbook
Ações de Runbook inicia um runbook na automatização do Azure.  Para utilizar este tipo de ação, tem de ter o [solução de automatização](log-analytics-add-solutions.md) instalada e configurada na sua área de trabalho do OMS.  Pode selecionar a partir de runbooks na conta de automatização que configurou na solução de automatização.

Ações de Runbook necessitam de propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:---|
| Runbook | Runbook que pretende iniciar quando é criado um alerta. |
| Executar em | Especifique **Azure** para executar o runbook na nuvem.  Especifique **worker híbrido** para executar o runbook num agente com [Runbook Worker híbrido](../automation/automation-hybrid-runbook-worker.md ) instalado.  |

Ações de Runbook iniciar o runbook com um [webhook](../automation/automation-webhooks.md).  Ao criar a regra de alerta, criará automaticamente um novo webhook para o runbook com o nome **remediação de alerta do OMS** seguido de um GUID.  

Diretamente não é possível povoar quaisquer parâmetros do runbook, mas o [$WebhookData parâmetro](../automation/automation-webhooks.md) incluirá os detalhes do alerta, incluindo os resultados da pesquisa de registo que o criou.  O runbook tem de definir **$WebhookData** como um parâmetro para o mesmo aceder às propriedades do alerta.  Os dados de alertas estão disponíveis no formato json numa única propriedade chamada **SearchResult** (para obter ações de runbook e ações de webhook no standard payload) ou **SearchResults** (as ações de webhook com personalizada Payload incluindo **IncludeSearchResults ": true**) no **RequestBody** propriedade **$WebhookData**.  Isto irá ter com as propriedades na tabela seguinte.

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, o payload de runbook foi alterada.  Detalhes do formato estão em [API de REST de análise de registos do Azure](https://aka.ms/loganalyticsapiresponse).  Pode ver um exemplo em [amostras](#sample-payload) abaixo.  

| Nó | Descrição |
|:--- |:--- |
| ID |Caminho e o GUID da pesquisa. |
| METADATA |Informações sobre o alerta, incluindo o número de registos e o estado dos resultados da pesquisa. |
| valor |Entrada separada para cada registo nos resultados da pesquisa.  Os detalhes da entrada de irão estabeler correspondência de propriedades e valores do registo. |

Por exemplo, os seguintes runbooks seria extrair os registos devolvidos pela pesquisa de registo e atribuir propriedades diferentes com base no tipo de cada registo.  Tenha em atenção que o runbook é iniciado, convertendo **RequestBody** JSON, para que as TI podem ser trabalhou com como um objeto no PowerShell.

>[!NOTE]
> Ambos estes runbooks utilizam **SearchResult** que é a propriedade que contém os resultados para obter ações de runbook e ações de webhook no payload padrão.  Se o runbook foi chamado a partir de uma resposta de webhook utilizando um payload personalizado, terá de alterar esta propriedade para **SearchResults**.

O runbook seguinte irá funcionar com o payload de um [área de trabalho de análise de registos legada](log-analytics-log-search-upgrade.md).

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResult.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }

O runbook seguinte irá funcionar com o payload de um [atualizado a área de trabalho de análise de registos](log-analytics-log-search-upgrade.md).

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody

    # Get all metadata properties    
    $AlertRuleName = $RequestBody.AlertRuleName
    $AlertThresholdOperator = $RequestBody.AlertThresholdOperator
    $AlertThresholdValue = $RequestBody.AlertThresholdValue
    $AlertDescription = $RequestBody.Description
    $LinktoSearchResults =$RequestBody.LinkToSearchResults
    $ResultCount =$RequestBody.ResultCount
    $Severity = $RequestBody.Severity
    $SearchQuery = $RequestBody.SearchQuery
    $WorkspaceID = $RequestBody.WorkspaceId
    $SearchWindowStartTime = $RequestBody.SearchIntervalStartTimeUtc
    $SearchWindowEndTime = $RequestBody.SearchIntervalEndtimeUtc
    $SearchWindowInterval = $RequestBody.SearchIntervalInSeconds

    # Get detailed search results
    if($RequestBody.SearchResult -ne $null)
    {
        $SearchResultRows    = $RequestBody.SearchResult.tables[0].rows 
        $SearchResultColumns = $RequestBody.SearchResult.tables[0].columns;

        foreach ($SearchResultRow in $SearchResultRows)
        {   
            $Column = 0
            $Record = New-Object –TypeName PSObject 
        
            foreach ($SearchResultColumn in $SearchResultColumns)
            {
                $Name = $SearchResultColumn.name
                $ColumnValue = $SearchResultRow[$Column]
                $Record | Add-Member –MemberType NoteProperty –Name $name –Value $ColumnValue -Force
                        
                $Column++
            }

            # Include code to work with the record. 
            # For example $Record.Computer to get the computer property from the record.
            
        }
    }



## <a name="sample-payload"></a>Payload de exemplo
Esta secção mostra o payload de exemplo para ações de webhook e runbook em ambos os um legado e um [atualizado a área de trabalho de análise de registos](log-analytics-log-search-upgrade.md).

### <a name="webhook-actions"></a>Ações de Webhook
Utilizam ambos estes exemplos **SearchResult** que é a propriedade que contém os resultados para as ações de webhook com payload padrão.  Se o webhook utilizado um payload personalizado que inclua os resultados da pesquisa, esta propriedade seria **SearchResults**.

#### <a name="legacy-workspace"></a>Área de trabalho de legado.
Segue-se um payload de exemplo para uma ação de webhook numa área de trabalho legada.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Type=Usage",
    "SearchResult": {
        "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/SearchGUID|10.1.0.7|2017-09-27T10-30-38Z",
        "__metadata": {
        "resultType": "raw",
        "total": 1,
        "top": 2147483647,
        "RequestId": "SearchID|10.1.0.7|2017-09-27T10-30-38Z",
        "CoreSummaries": [
            {
            "Status": "Successful",
            "NumberOfDocuments": 135000000
            }
        ],
        "Status": "Successful",
        "NumberOfDocuments": 135000000,
        "StartTime": "2017-09-27T10:30:38.9453282Z",
        "LastUpdated": "2017-09-27T10:30:44.0907473Z",
        "ETag": "636421050440907473",
        "sort": [
            {
            "name": "TimeGenerated",
            "order": "desc"
            }
        ],
        "requestTime": 361
        },
        "value": [
        {
            "Computer": "-",
            "SourceSystem": "OMS",
            "TimeGenerated": "2017-09-26T13:59:59Z",
            "ResourceUri": "/subscriptions/df1ec963-d784-4d11-a779-1b3eeb9ecb78/resourcegroups/mms-eus/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
            "DataType": "Operation",
            "StartTime": "2017-09-26T13:00:00Z",
            "EndTime": "2017-09-26T13:59:59Z",
            "Solution": "LogManagement",
            "BatchesWithinSla": 8,
            "BatchesOutsideSla": 0,
            "BatchesCapped": 0,
            "TotalBatches": 8,
            "AvgLatencyInSeconds": 0.0,
            "Quantity": 0.002502,
            "QuantityUnit": "MBytes",
            "IsBillable": false,
            "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
            "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
            "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
            "Type": "Usage",
            "MG": "00000000-0000-0000-0000-000000000000",
            "__metadata": {
            "Type": "Usage",
            "TimeGenerated": "2017-09-26T13:59:59Z"
            }
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Type%3DUsage",
    "Description": null,
    "Severity": "Low"
    }


#### <a name="upgraded-workspace"></a>Área de trabalho atualizada.
Segue-se um payload de exemplo para uma ação de webhook numa área de trabalho atualizada.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbooks

#### <a name="legacy-workspace"></a>Área de trabalho legada
Segue-se um payload de exemplo para uma ação de runbook na área de trabalho legada.

    {
        "SearchResult": {
            "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/searchGUID|10.1.0.7|TimeStamp",
            "__metadata": {
                "resultType": "raw",
                "total": 1,
                "top": 2147483647,
                "RequestId": "searchGUID|10.1.0.7|2017-09-27T10-51-43Z",
                "CoreSummaries": [{
                    "Status": "Successful",
                    "NumberOfDocuments": 135000000
                }],
                "Status": "Successful",
                "NumberOfDocuments": 135000000,
                "StartTime": "2017-09-27T10:51:43.3075124Z",
                "LastUpdated": "2017-09-27T10:51:51.1002092Z",
                "ETag": "636421063111002092",
                "sort": [{
                    "name": "TimeGenerated",
                    "order": "desc"
                }],
                "requestTime": 511
            },
            "value": [{
                "Computer": "-",
                "SourceSystem": "OMS",
                "TimeGenerated": "2017-09-26T13:59:59Z",
                "ResourceUri": "/subscriptions/AnotherSubscriptionID/resourcegroups/SampleResourceGroup/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                "DataType": "Operation",
                "StartTime": "2017-09-26T13:00:00Z",
                "EndTime": "2017-09-26T13:59:59Z",
                "Solution": "LogManagement",
                "BatchesWithinSla": 8,
                "BatchesOutsideSla": 0,
                "BatchesCapped": 0,
                "TotalBatches": 8,
                "AvgLatencyInSeconds": 0.0,
                "Quantity": 0.002502,
                "QuantityUnit": "MBytes",
                "IsBillable": false,
                "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
                "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
                "Type": "Usage",
                "MG": "00000000-0000-0000-0000-000000000000",
                "__metadata": {
                    "Type": "Usage",
                    "TimeGenerated": "2017-09-26T13:59:59Z"
                }
            }]
        }
    }

#### <a name="upgraded-workspace"></a>Área de trabalho atualizada
Segue-se um payload de exemplo para uma ação de runbook numa área de trabalho atualizada.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>Passos seguintes
- Conclua uma explicação passo a passo para [configurar um webook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
- Aprender a escrever [runbooks na automatização do Azure](https://azure.microsoft.com/documentation/services/automation) para remediar problemas identificados através de alertas.