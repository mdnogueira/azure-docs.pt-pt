---
title: "Criar alertas para os serviços do Azure - PowerShell | Microsoft Docs"
description: "Acionador e-mails, as notificações, chamar URLs de Web sites (webhooks) ou automatização quando forem cumpridas condições que especificar."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
ms.openlocfilehash: d3fca8675c1f15b8fd0f952cfbf520f5c68478b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Criar métricas alertas no Monitor do Azure para serviços do Azure - PowerShell
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Descrição geral
Este artigo mostra como configurar alertas de métricas do Azure com o PowerShell.  

Pode receber um alerta com base na monitorização métricas para ou eventos nos seus serviços do Azure.

* **Valores métricos** -o alerta é acionado quando o valor de uma métrica especificado atravesse um limiar atribuir em qualquer direção. Ou seja, aciona ambas quando a condição for satisfeita primeiro e, em seguida, posteriormente quando condição que é já não está a ser cumprido.    
* **Eventos de registo de atividade** -pode acionar um alerta num *cada* eventos ou apenas quando ocorre um determinados eventos. Para saber mais sobre alertas de registo de atividade [clique aqui](monitoring-activity-log-alerts.md)

Pode configurar um alerta de métrico para fazer o seguinte quando aciona:

* enviar notificações por e-mail para o administrador de serviços e coadministradores
* envie correio eletrónico para e-mails adicionais que especificar.
* Chamar um webhook
* iniciar a execução de um runbook do Azure (apenas a partir do portal do Azure)

Pode configurar e obter informações sobre regras de alerta utilizando

* [Portal do Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interface de linha de comandos (CLI)](insights-alerts-command-line-interface.md)
* [API de REST de Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Para obter informações adicionais, pode sempre escrever ```Get-Help``` e, em seguida, o comando do PowerShell pretende obter ajuda na.

## <a name="create-alert-rules-in-powershell"></a>Criar regras de alertas no PowerShell
1. Inicie sessão no Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```
2. Obter uma lista das subscrições que tem disponíveis. Certifique-se de que estão a funcionar com a subscrição correta. Se não estiver, defina-o à direita com o resultado da `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Para listar as regras existentes num grupo de recursos, utilize o seguinte comando:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Para criar uma regra, tem de ter várias partes importantes de informações pela primeira vez.

  * O **ID de recurso** para o recurso que pretende definir um alerta para
  * O **as definições de métrica** disponíveis para esse recurso

     Uma forma de obter o ID de recurso é utilizar o portal do Azure. Pressupondo que o recurso já está a ser criado, selecione-o no portal. Em seguida, no painel do seguinte, selecione *propriedades* sob o *definições* secção. **ID de recurso** é um campo no painel seguinte. Outra forma consiste em utilizar o [Explorador de recursos do Azure](https://resources.azure.com/).

     É um ID de recurso de exemplo para uma aplicação web

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Pode utilizar `Get-AzureRmMetricDefinition` para ver a lista de todas as definições de métricas para um recurso específico.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     O exemplo seguinte gera uma tabela com o nome da métrica e a unidade para esse métrica.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Uma lista completa das opções disponíveis para o Get-AzureRmMetricDefinition está disponível através da execução `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. O exemplo a seguir configura um alerta no recurso web site. Os acionadores de alerta sempre que recebe consistentemente qualquer tráfego de 5 minutos e novamente quando não recebe nenhum tráfego de 5 minutos.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Para criar o webhook ou enviar o e-mail quando um alerta é acionado, crie primeiro o e-mail e/ou webhooks. Em seguida, crie de imediato a regra posteriormente com-etiqueta de ações e, como mostrado no exemplo seguinte. Não é possível associar webhook ou e-mails com já criou regras através do PowerShell.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Para verificar que os alertas não foi criados corretamente ao observar as regras individuais.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Elimine os alertas. Estes comandos, elimine as regras que criou anteriormente neste artigo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Passos seguintes
* [Obter uma descrição geral da monitorização do Azure](monitoring-overview.md) , incluindo os tipos de informações que pode recolher e monitorizar.
* Saiba mais sobre [configurar webhooks alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [configurar alertas de eventos de registo de atividade](monitoring-activity-log-alerts.md).
* Saiba mais sobre [Runbooks de automatização do Azure](../automation/automation-starting-a-runbook.md).
* Obter um [descrição geral de recolha de registos de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recolher métricas de alta frequência detalhadas do seu serviço.
* Obter um [descrição geral da coleção de métricas](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e reativa.
