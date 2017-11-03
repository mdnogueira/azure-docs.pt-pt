---
title: "Ativar o registo de diagnóstico para eventos de lote - Azure | Microsoft Docs"
description: "Registar e analisar eventos de registo de diagnóstico para recursos da conta do Azure Batch, como conjuntos e tarefas."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7bc6fd9921ab0f2374ace33ea5c1ab93a78f860
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Eventos de registo para a avaliação de diagnóstico e monitorização de soluções do Batch

Tal como acontece com vários serviços do Azure, o serviço Batch emite o registo de eventos de certos recursos durante a duração do recurso. Pode ativar os registos de diagnóstico do Azure Batch para eventos de registo de recursos, como conjuntos e tarefas e, em seguida, utilizar os registos para a avaliação de diagnóstico e monitorização. Eventos como agrupamento de criar, eliminar agrupamento, início da tarefa, tarefa concluída e outros estão incluídos nos registos de diagnóstico do Batch.

> [!NOTE]
> Este artigo aborda os eventos de registo de recursos de conta do Batch próprios, não da tarefa e dados de saída de tarefas. Para obter detalhes sobre a armazenar os dados de saída dos seus trabalhos e tarefas, consulte [saída de tarefas e manter o Azure Batch](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* [Conta de Batch do Azure](batch-account-create-portal.md)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Para manter os registos de diagnóstico do Batch, tem de criar uma conta de armazenamento do Azure onde o Azure irá armazenar os registos. Especificar esta conta de armazenamento quando que [ativar o registo de diagnóstico](#enable-diagnostic-logging) para a sua conta do Batch. A conta de armazenamento que especificar quando ativar a recolha de registos não é o mesmo que uma conta de armazenamento ligado que referida no [pacotes de aplicações](batch-application-packages.md) e [persistência de saída de tarefas](batch-task-output.md) artigos.
  
  > [!WARNING]
  > É **cobrados** para os dados armazenados na sua conta do Storage do Azure. Isto inclui os registos de diagnóstico abordados neste artigo. Manter isto em consideração quando conceber a sua [política de retenção de registo](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Ativar o registo de diagnósticos
Registo de diagnóstico não está ativado por predefinição para a sua conta do Batch. Tem de ativar explicitamente cada conta de Batch que pretende monitorizar registo de diagnóstico:

[Como ativar a coleção de registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

Recomendamos que leia o completo [descrição geral do Azure os registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artigo para obter uma compreensão dos não só como ativar o registo, mas as categorias de registo suportadas pelos vários serviços do Azure. Por exemplo, do Azure Batch atualmente suporta uma categoria de registo: **registos do serviço**.

## <a name="service-logs"></a>Registos do serviço
Os registos de serviço do Azure Batch contém eventos emitidos pelo serviço Azure Batch durante a duração de um recurso de Batch, como um conjunto ou tarefas. Cada evento emitido pelo Batch é armazenado na conta de armazenamento especificada no formato JSON. Por exemplo, este é o corpo de uma amostra **conjunto criar eventos**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Cada corpo de evento reside num ficheiro. JSON na conta de armazenamento do Azure especificada. Se pretender aceder diretamente os registos, poderá pretender rever o [esquema de registos de diagnóstico na conta de armazenamento](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Eventos de registo do serviço
O serviço Batch emite atualmente os seguintes eventos de registo do serviço. Esta lista não pode ser exaustiva, uma vez que os eventos adicionais poderão ter sido adicionados uma vez que este artigo foi atualizado pela última vez.

| **Eventos de registo do serviço** |
| --- |
| [Criar conjunto][pool_create] |
| [Início de eliminação do conjunto][pool_delete_start] |
| [Eliminar conjunto concluída][pool_delete_complete] |
| [Início de redimensionamento de conjunto][pool_resize_start] |
| [Redimensionamento de agrupamento completo][pool_resize_complete] |
| [Início da tarefa][task_start] |
| [Tarefa concluída][task_complete] |
| [Falha de tarefa][task_fail] |

## <a name="next-steps"></a>Passos seguintes
Para além de armazenar eventos de registo de diagnóstico de uma conta de armazenamento do Azure, também pode transmitir eventos de registo do serviço de Batch um [Hub de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md)e enviá-las para [Log Analytics do Azure](../log-analytics/log-analytics-overview.md).

* [Transmitir os registos de diagnóstico do Azure para os Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Fluxo de eventos de diagnóstico de Batch para o serviço de entrada de dados altamente dimensionável, os Event Hubs. Hubs de eventos podem ingerir milhões de eventos por segundo, que pode, em seguida, transformar e armazenar utilizando um fornecedor de análise em tempo real.
* [Analisar os registos de diagnóstico do Azure através da análise do registo](../log-analytics/log-analytics-azure-storage.md)
  
  Envie registos de diagnóstico para análise de registos, onde pode analisá-las no portal do Operations Management Suite (OMS) ou, exportá-las para análise no Power BI ou no Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
