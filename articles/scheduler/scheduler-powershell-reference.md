---
title: "Referência de Cmdlets do PowerShell do agendador"
description: "Referência de Cmdlets do PowerShell do agendador"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 141919ab4506b3de4c4a69670dcf54c60ee6409c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-powershell-cmdlets-reference"></a>Referência de Cmdlets do PowerShell do agendador
A tabela seguinte descreve e hiperligações para a página de referência de cada um dos principais cmdlets no agendador do Azure.

Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). 

Para obter mais informações sobre [cmdlets do Azure Resource Manager](/powershell/azure/overview), consulte [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | Descrição do cmdlet |
| --- | --- |
| [Desativar AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Desativa uma coleção de tarefas. |
| [Ativar AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Permite a uma coleção de tarefas. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Obtém as tarefas do programador. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Obtém as coleções de tarefa. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Obtém o histórico de tarefa. |
| [Novo AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Cria uma tarefa HTTP. |
| [Novo AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Cria uma coleção de tarefas. |
| [Novo AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) |Cria uma tarefa de fila de barramento de serviço. |
| [Novo AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Cria uma tarefa de tópico de barramento de serviço. |
| [Novo AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Cria uma tarefa de fila de armazenamento. |
| [Remover AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Remove uma tarefa do agendador. |
| [Remover AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Remove uma coleção de tarefas. |
| [Conjunto AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modifica uma tarefa do agendador HTTP. |
| [Conjunto AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modifica uma coleção de tarefas. |
| [Conjunto AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modifica uma tarefa de fila de barramento de serviço. |
| [Conjunto AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modifica uma tarefa de tópico de barramento de serviço. |
| [Conjunto AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modifica uma tarefa de fila de armazenamento. |

Para obter informações mais detalhadas, pode executar os seguintes cmdlets: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Veja Também
 [O que é o Scheduler?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Scheduler](scheduler-plans-billing.md)

 [Referência da API REST do Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Elevada disponibilidade e fiabilidade do Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Azure Scheduler](scheduler-outbound-authentication.md)

