---
title: "Monitorizar fábricas de dados utilizando o Monitor do Azure | Microsoft Docs"
description: "Saiba como utilizar o Monitor do Azure para monitorizar os pipelines de fábrica de dados, permitindo os registos de diagnóstico com informações do Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: 3d9ec6325e25477bf4ee0475caeca64b75b1f89f
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Monitorizar fábricas de dados utilizando o Monitor do Azure  
Aplicações em nuvem são complexas com várias partes mover. A monitorização fornece dados para se certificar de que a aplicação permanece cópias de segurança e em execução em bom estado. Também o ajuda a stave desativar potenciais problemas ou resolver passado aqueles. Além disso, pode utilizar dados de monitorização para obter conhecimentos aprofundados sobre a sua aplicação. Este conhecimento pode ajudar a melhorar o desempenho da aplicação ou maintainability ou automatizar ações que caso contrário necessitem intervenção manual.

Monitor do Azure fornece registos e as métricas de infraestrutura de nível de base para a maioria dos serviços no Microsoft Azure. Para obter mais informações, consulte [descrição geral da monitorização](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Os registos de diagnóstico do Azure são registos emitidos por um recurso que fornecem dados avançados, frequentes sobre o funcionamento desse recurso. Fábrica de dados produz os registos de diagnóstico no Monitor do Azure. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [monitorizar e gerir pipelines na fábrica de dados version1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="diagnostic-logs"></a>Registos de diagnósticos

* Guardá-las para um **conta de armazenamento** de inspeção de auditoria ou manual. Pode especificar o período de retenção (em dias) utilizando as definições de diagnóstico.
* Fluxo sejam **Event Hubs** para ingestão por um serviço independente ou uma solução de análise personalizada, tais como o Power BI.
* Analisá-los com **análise de registos do Operations Management Suite (OMS)**

Pode utilizar um armazenamento conta ou event hub espaço de nomes que não se encontra na mesma subscrição que o recurso que está a emitir os registos. O utilizador que configura a definição tem de ter acesso de controlo (RBAC) de acesso baseado em funções adequadas para ambas as subscrições.

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

### <a name="diagnostic-settings"></a>Definições de diagnóstico
Os registos de diagnóstico para recursos de computação não são configurados utilizando definições de diagnóstico. Definições de diagnóstico para um controlo de recursos:

* Onde são enviados os registos de diagnóstico (conta de armazenamento, os Event Hubs, e/ou análise de registos do OMS).
* As categorias de registo são enviadas.
* Quanto cada categoria de registo deve ser mantida na conta de armazenamento
* Uma retenção de zero dias significa que os registos são mantidos indefinidamente. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.
* Se as políticas de retenção estão definidas, mas armazenar os registos numa conta do storage está desativada (por exemplo, apenas os Event Hubs ou OMS opções são selecionadas), as políticas de retenção não tem qualquer efeito.
* As políticas de retenção são aplicada por-dia, no fim do dia (UTC), registos a partir do dia em que é agora a retenção política são eliminadas. Por exemplo, se tiver uma política de retenção de um dia, no início do dia de hoje os registos de ontem de antes do dia seriam eliminados.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Ativar registos de diagnóstico através de REST APIs

Criar ou atualizar uma definição de diagnóstico na API REST da Azure Monitor

**Pedido**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Cabeçalhos**
* Substitua `{api-version}` com `2016-09-01`.
* Substitua `{resource-id}` com o ID de recurso do recurso para o qual gostaria de editar as definições de diagnóstico. Para obter mais informações [utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-portal.md).
* Definir o `Content-Type` cabeçalho para `application/json`.
* Defina o cabeçalho de autorização para um token web JSON que obteve do Azure Active Directory. Para obter mais informações, consulte [autenticar pedidos](../active-directory/develop/active-directory-authentication-scenarios.md).

**Corpo**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
} 
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| StorageAccountId |Cadeia | O ID de recurso da conta do storage para o qual pretende enviar registos de diagnóstico |
| serviceBusRuleId |Cadeia | O service bus ID da regra de espaço de nomes de barramento serviço, na qual gostaria de ter criado para os registos de diagnóstico de transmissão em fluxo do Event Hubs. A regra ID é o formato: "{ID de recurso de barramento de serviço} /authorizationrules/ {nome da chave}".|
| WorkspaceId | Tipo complexo | Matriz de grains de métrica de tempo e as respetivas políticas de retenção. Atualmente, esta propriedade está vazia. |
|metrics| Os valores dos parâmetros do pipeline de executam a ser transmitido para o pipeline invocado| Um objeto JSON mapear os nomes de parâmetros para valores de argumento | 
| registos| Tipo complexo| Nome de uma categoria de registo de diagnóstico para um tipo de recurso. Para obter a lista de categorias de registo de diagnóstico para um recurso, execute primeiro uma operação de definições de diagnóstico de GET. |
| categoria| Cadeia| Matriz de categorias de registo e as respetivas políticas de retenção |
| Intervalo de agregação | Cadeia | A granularidade de métricas que são capturadas num formato de duração ISO 8601. Tem de ser PT1M (um minuto)|
| ativado| Valor booleano | Especifica se a coleção dessa categoria métrica ou de registo está ativada para este recurso|
| retentionPolicy| Tipo complexo| Descreve a política de retenção para uma categoria de métrica ou de registo. Utilizado para apenas opção de conta de armazenamento.|
| dias| Int| Número de dias a manter as métricas ou os registos. Um valor de 0 mantém os registos indefinidamente. Utilizado para apenas opção de conta de armazenamento. |

**Resposta**

200 OK


```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Obter informações sobre a definição de diagnóstico na API REST da Azure Monitor

**Pedido**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Cabeçalhos**
* Substitua `{api-version}` com `2016-09-01`.
* Substitua `{resource-id}` com o ID de recurso do recurso para o qual gostaria de editar as definições de diagnóstico. Para obter mais informações através de grupos de recursos para gerir os recursos do Azure.
* Definir o `Content-Type` cabeçalho para `application/json`.
* Defina o cabeçalho de autorização para um Web Token JSON que obteve do Azure Active Directory. Para obter mais informações, consulte Authenticating pedidos.

**Resposta**

200 OK

```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/0ee78edb-a0ad-456c-a0a2-901bf542c102/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Obter mais informações aqui] (https://msdn.microsoft.com/en-us/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>Esquema dos registos & de eventos

### <a name="activity-run-logs-attributes"></a>Atributos de registos de execução da atividade

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties:" 
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia | Nível dos registos de diagnóstico. Nível 4 é sempre o caso dos registos de execução da atividade. | `4`  |
| correlationId |Cadeia | ID exclusivo para controlar um determinado pedido ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | Cadeia | Hora do evento na timespan, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Cadeia| ID de execução da atividade | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Cadeia| ID do pipeline executar | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia | ID de recurso associada para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia | Categoria de registos de diagnóstico. Definir esta propriedade para "ActivityRuns" | `ActivityRuns` |
|nível| Cadeia | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativa" | `Informational` |
|operationName| Cadeia |Nome da atividade com o estado. Se o estado é o início heartbeat, é `MyActivity -`. Se o estado é o heartbeat do fim, é `MyActivity - Succeeded` com o estado final | `MyActivity - Succeeded` |
|pipelineName| Cadeia | Nome do pipeline | `MyPipeline` |
|activityName| Cadeia | Nome da atividade | `MyActivity` |
|start| Cadeia | Início da atividade executada nos timespan, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|Fim| Cadeia | Executam extremidades da atividade em timespan, formato UTC. Se não terminou a atividade ainda (registo de diagnóstico para iniciar uma atividade), um valor predefinido de `1601-01-01T00:00:00Z` está definido.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Atributos de registos de execução de pipeline

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties": 
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia | Nível dos registos de diagnóstico. Nível 4 é o caso dos registos de execução da atividade. | `4`  |
| correlationId |Cadeia | ID exclusivo para controlar um determinado pedido ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | Cadeia | Hora do evento na timespan, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Cadeia| ID do pipeline executar | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia | ID de recurso associada para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia | Categoria de registos de diagnóstico. Definir esta propriedade para "PipelineRuns" | `PipelineRuns` |
|nível| Cadeia | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativa" | `Informational` |
|operationName| Cadeia |Nome do pipeline com o estado. "Pipeline - foi concluída com êxito" com o estado final aquando da conclusão do pipeline executar| `MyPipeline - Succeeded` |
|pipelineName| Cadeia | Nome do pipeline | `MyPipeline` |
|start| Cadeia | Início da atividade executada nos timespan, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|Fim| Cadeia | Fim da atividade é executada no timespan, formato UTC. Se não terminou a atividade ainda (registo de diagnóstico para iniciar uma atividade), um valor predefinido de `1601-01-01T00:00:00Z` está definido.  | `2017-06-26T20:55:29.5007959Z` |
|status| Cadeia | Estado final do pipeline de run (com êxito ou falha) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Atributos de registos de execução do acionador

```json
{ 
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
} 

```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia | Nível dos registos de diagnóstico. Defina o nível 4 para registos de execução da atividade. | `4`  |
| correlationId |Cadeia | ID exclusivo para controlar um determinado pedido ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | Cadeia | Hora do evento na timespan, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Cadeia| ID do acionador executar | `08587023010602533858661257311` |
|resourceId| Cadeia | ID de recurso associada para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia | Categoria de registos de diagnóstico. Definir esta propriedade para "PipelineRuns" | `PipelineRuns` |
|nível| Cadeia | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativa" | `Informational` |
|operationName| Cadeia |Nome do acionador com o estado final se com êxito desencadeou. "MyTrigger - foi concluída com êxito" se o heartbeat foi concluída com êxito| `MyTrigger - Succeeded` |
|triggerName| Cadeia | Nome do acionador | `MyTrigger` |
|triggerType| Cadeia | Tipo de accionador (acionador Manual ou acionador de agenda) | `ScheduleTrigger` |
|triggerEvent| Cadeia | Eventos do acionador | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Cadeia | Início do acionador fire no timespan, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Cadeia | Estado final de se acionador acionado com êxito (com êxito ou falha) | `Succeeded`|

### <a name="metrics"></a>Métricas

Monitor do Azure permite-lhe consumir telemetria ganhar visibilidade sobre o desempenho e estado de funcionamento das suas cargas de trabalho no Azure. O tipo de dados de telemetria do Azure mais importante é as métricas (também denominadas de contadores de desempenho) emitidas pelo Azure mais recursos. Monitor do Azure fornece várias formas de configurar e consumir estas métricas de monitorização e resolução de problemas.

ADFV2 emite as métricas seguintes

| **Métricas**           | **Nome a apresentar métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Foi concluída com êxito as métricas de execução de pipeline | Contagem    | Total                | Totais de pipelines é executado com êxito dentro de uma janela de minuto |
| PipelineFailedRuns   | Falha de métricas de execução de pipeline    | Contagem    | Total                | Totais de pipelines executa falhada numa janela minuto    |
| ActiviySucceededRuns | Foi concluída com êxito as métricas de execuções de atividade | Contagem    | Total                | Atividade total é executado com êxito dentro de uma janela de minuto  |
| ActivityFailedRuns   | Falha de métricas de execuções de atividade    | Contagem    | Total                | Atividade total é executada com falhas numa janela minuto     |
| TriggerSucceededRuns | Foi concluída com êxito as métricas de execuções do acionador  | Contagem    | Total                | Acionador total é executado com êxito dentro de uma janela de minuto   |
| TriggerFailedRuns    | Falha de métricas de execução do acionador     | Contagem    | Total                | Acionador total é executado falhada dentro de uma janela de minuto      |

Para aceder às métricas, siga as instruções no artigo - https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics 

## <a name="next-steps"></a>Passos seguintes
Consulte [monitorizar e gerir pipelines programaticamente](monitor-programmatically.md) artigo para saber mais sobre como monitorizar e gerir pipelines, executando a aplicação. 