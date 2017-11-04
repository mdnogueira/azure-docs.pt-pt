---
title: "Migrar os alertas do Azure em eventos de gestão para alertas de registo de atividade | Microsoft Docs"
description: "Alertas de eventos de gestão serão removidas no 1 de Outubro. Prepare-se através de alertas de existente a migrar."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 08a457029d3721f5c38dbcd2d2aab7d09a241d8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migrar os alertas do Azure em eventos de gestão para alertas de registo de atividade


> [!WARNING]
> Alertas de eventos de gestão serão desligadas em ou depois 1 de Outubro de. Utilize as instruções abaixo para compreender se ter estes alertas e migrar caso.
>
> 

## <a name="what-is-changing"></a>O que está a alterar

Monitor do Azure (anteriormente denominado Azure Insights) oferecido uma capacidade de criar um alerta que acionada termina a sessão de eventos de gestão e gerado para um endereço de URL ou o e-mail de webhook notificações. Pode criar um destes alertas qualquer uma das seguintes formas:
* No portal do Azure para determinados tipos de recursos, sob monitorização -> alertas -> Adicionar alerta, onde "Alerta sobre" está definida como "Eventos"
* Ao executar o cmdlet Add-AzureRmLogAlertRule PowerShell
* Utilizando diretamente [a API REST do alerta](http://docs.microsoft.com/rest/api/monitor/alertrules) com odata.type = "ManagementEventRuleCondition" e dataSource.odata.type = "RuleManagementEventDataSource"
 
O seguinte script do PowerShell devolve uma lista de todos os alertas de eventos de gestão que tem na sua subscrição, bem como as condições definidas em cada alerta.

```powershell
Login-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Se tiver não existem alertas de eventos de gestão, o cmdlet do PowerShell acima irá emitir uma série de mensagens de aviso como esta:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Estas mensagens de aviso podem ser ignoradas. Se tiver alertas de eventos de gestão, o resultado deste cmdlet PowerShell terá este aspeto:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Cada alerta é separada por uma linha tracejada e detalhes incluem o ID de recurso de alerta e regras específicas a serem monitorizados.

Esta funcionalidade transitou para [alertas de registo de atividade de monitorização de Azure](monitoring-activity-log-alerts.md). Estes novos alertas permitem-lhe definir uma condição em eventos de registo de atividade e receber uma notificação quando um novo evento corresponde a condição. Também oferece vários melhoramentos de alertas de eventos de gestão:
* Pode reutilizar o grupo de destinatários de notificação ("ações") em muitos alertas utilizando [ação grupos](monitoring-action-groups.md), reduzir a complexidade da alteração que deve receber um alerta.
* Pode receber uma notificação diretamente no seu telemóvel utilizando SMS com grupos de ação.
* Pode [criam alertas de registo de atividade com modelos do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Pode criar condições com maior flexibilidade e a complexidade para satisfazer as suas necessidades específicas.
* As notificações são fornecidas mais rapidamente.
 
## <a name="how-to-migrate"></a>Como migrar
 
Para criar um alerta de registo de nova atividade, pode:
* Siga [nosso guia sobre como criar um alerta no portal do Azure](monitoring-activity-log-alerts.md)
* Saiba como [criar um alerta com um modelo do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
Alertas de eventos de gestão que tenha criado anteriormente não serão migradas automaticamente a alertas de registo de atividade. Tem de utilizar o script do PowerShell anterior para listar os alertas de eventos de gestão que atualmente configurou e recriá-los manualmente como alertas de registo de atividade. Isto deve ser efetuado antes 1 de Outubro, após o qual alertas de eventos de gestão deixará de estar visíveis na sua subscrição do Azure. Outros tipos de alertas do Azure, incluindo alertas de métricas de monitorização do Azure, Application Insights alertas e alertas de análise de registos são afetados por esta alteração. Se tiver alguma questão, publique nos comentários abaixo.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [registo de atividade](monitoring-overview-activity-logs.md)
* Configurar [alertas de registo de atividade através do portal do Azure](monitoring-activity-log-alerts.md)
* Configurar [alertas de registo de atividade através do Gestor de recursos](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Reveja o [esquema de webhook alerta de registo de atividade](monitoring-activity-log-alerts-webhook.md)
* Saiba mais sobre [notificações de serviço](monitoring-service-notifications.md)
* Saiba mais sobre [grupos de ação](monitoring-action-groups.md)
