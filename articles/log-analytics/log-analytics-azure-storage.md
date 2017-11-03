---
title: "Recolher registos do serviço do Azure e as métricas de análise de registos | Microsoft Docs"
description: "Configure o diagnóstico nos recursos do Azure para escrever os registos e as métricas de análise de registos."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7a3785e39f0d1cf849dbbf0d83d89eaed58c5b0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Recolher métricas para utilização na análise de registos e registos do serviço do Azure

Existem quatro formas diferentes de recolha de registos e as métricas para os serviços do Azure:

1. Diagnóstico do Azure diretamente ao Log Analytics (*diagnóstico* na tabela seguinte)
2. Diagnóstico do Azure para armazenamento do Azure ao Log Analytics (*armazenamento* na tabela seguinte)
3. Os conectores para serviços do Azure (*conectores* na tabela seguinte)
4. Scripts para recolher e, em seguida, colocar dados no Log Analytics (espaços em branco na seguinte tabela e a serviços não listados)


| Serviço                 | Tipo de Recurso                           | Registos        | Métricas     | Solução |
| --- | --- | --- | --- | --- |
| Gateways de aplicação    | Network/applicationgateways   | Diagnóstico | Diagnóstico | [Análise de Gateway de aplicação do Azure](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Informações de aplicação    |                                         | conector   | conector   | [Conector do Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (pré-visualização) |
| Contas de Automatização     | Microsoft.Automation/AutomationAccounts | Diagnóstico |             | [Obter mais informações](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Contas do batch          | Microsoft.Batch/batchAccounts           | Diagnóstico | Diagnóstico | |
| Serviços cloud clássico  |                                         | Armazenamento     |             | [Obter mais informações](log-analytics-azure-storage-iis-table.md) |
| Serviços cognitivos      | Microsoft.CognitiveServices/accounts    |             | Diagnóstico | |
| Análise do Data Lake     | Microsoft.DataLakeAnalytics/accounts    | Diagnóstico |             | |
| Arquivo data Lake         | Microsoft.DataLakeStore/accounts        | Diagnóstico |             | |
| Espaço de nomes de Hub de eventos     | Microsoft.EventHub/namespaces           | Diagnóstico | Diagnóstico | |
| Hubs IoT                | Microsoft.Devices/IotHubs               |             | Diagnóstico | |
| Cofre de Chaves               | Microsoft.KeyVault/vaults               | Diagnóstico |             | [Análise de KeyVault](log-analytics-azure-key-vault.md) |
| Balanceadores de carga          | Microsoft.Network/loadBalancers         | Diagnóstico |             |  |
| Aplicações Lógicas              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnóstico | Diagnóstico | |
| Grupos de Segurança de Rede | Microsoft.Network/networksecuritygroups | Diagnóstico |             | [Análise de grupo de segurança de rede do Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Cofres de recuperação         | Microsoft.RecoveryServices/vaults       |             |             | [O Azure Recovery Services análise (pré-visualização)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Procurar serviços         | Microsoft.Search/searchServices         | Diagnóstico | Diagnóstico | |
| Espaço de nomes do Service Bus   | Microsoft.ServiceBus/namespaces         | Diagnóstico | Diagnóstico | [Análise de barramento de serviço (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Armazenamento     |             | [Análise de recursos de infraestrutura de serviço (pré-visualização)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnóstico | [Análise de SQL do Azure (pré-visualização)](log-analytics-azure-sql.md) |
| Armazenamento                 |                                         |             | Script      | [Análise de armazenamento do Azure (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtual Machines        | Microsoft.Compute/virtualMachines       | Extensão   | Extensão <br> Diagnóstico  | |
| Conjuntos de dimensionamento de máquinas virtuais | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnóstico | |
| Web farms de servidores        | Microsoft.Web/serverfarms               |             | Diagnóstico | |
| Web Sites               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnóstico | [Análise de aplicações Web do Azure (pré-visualização)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) |


> [!NOTE]
> Para monitorizar máquinas virtuais do Azure (Linux e Windows), recomendamos que instale o [extensão da VM de análise do registo](log-analytics-azure-vm-extension.md). O agente fornece-lhe insights recolhidos a partir de máquinas virtuais. Também pode utilizar a extensão para conjuntos de dimensionamento de Máquina Virtual.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Diagnóstico do Azure diretamente ao Log Analytics
Muitos recursos do Azure são capazes de escrever os registos de diagnóstico e métricas diretamente ao Log Analytics e isto é a forma preferencial de recolha de dados para análise. Ao utilizar o diagnóstico do Azure, dados são escritos imediatamente a análise de registos e não é necessário escrever primeiro os dados para o armazenamento.

Recursos do Azure que suportem [Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md) pode enviar os registos e as métricas diretamente ao Log Analytics.

* Para obter os detalhes sobre as métricas disponíveis, consulte [suportado métricas com a monitorização do Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Para obter os detalhes dos registos disponíveis, consulte [suportado serviços e o esquema para os registos de diagnóstico](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Ativar diagnósticos com o PowerShell
Precisa de Novembro de 2016 (v2.3.0) ou versão posterior do [Azure PowerShell](/powershell/azure/overview).

O exemplo do PowerShell seguinte mostra como utilizar [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) para ativar o diagnóstico num grupo de segurança de rede. A mesma abordagem funciona para todos os recursos suportados - definir `$resourceId` para o id de recurso do recurso que pretende ativar o diagnóstico para.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Ativar o diagnóstico com modelos do Resource Manager

Para ativar o diagnóstico num recurso quando é criado e tem o diagnóstico enviados para a área de trabalho de análise de registos pode utilizar um modelo semelhante às abaixo. Neste exemplo é para uma conta de automatização, mas funciona para todos os tipos de recurso suportados.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Diagnóstico do Azure para armazenamento, em seguida, ao Log Analytics

Para recolher registos de dentro de alguns dos recursos, é possível enviar os registos de armazenamento do Azure e, em seguida, configure a análise de registos para ler os registos a partir do armazenamento.

Análise de registos pode utilizar esta abordagem para recolher diagnósticos de armazenamento do Azure para os registos e os seguintes recursos:

| Recurso | Registos |
| --- | --- |
| Service Fabric |ETWEvent <br> Eventos operacionais <br> Evento de Ator fiável <br> Evento de serviço fiável |
| Virtual Machines |Linux Syslog <br> Eventos do Windows <br> Registo do IIS <br> Windows ETWEvent |
| Funções da Web <br> Funções de trabalho |Linux Syslog <br> Eventos do Windows <br> Registo do IIS <br> Windows ETWEvent |

> [!NOTE]
> São-lhe cobrados a taxas de dados do Azure normais para armazenamento e transações quando enviar diagnósticos para uma conta do storage e quando análise de registos lê os dados da sua conta de armazenamento.
>
>

Consulte [armazenamento de BLOBs de utilização para o IIS e a tabela de armazenamento para os eventos](log-analytics-azure-storage-iis-table.md) para saber mais sobre como a análise de registos pode recolher estes registos.

## <a name="connectors-for-azure-services"></a>Conectores para serviços do Azure

Não há um conector para o Application Insights, que permite que os dados recolhidos pelo Application Insights para ser enviado para análise de registos.

Saiba mais sobre o [conector do Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Scripts para recolher e publique os dados para análise de registos

Para os serviços do Azure que não fornecem uma forma direta para enviar os registos e as métricas para análise de registos pode utilizar um script de automatização do Azure para recolher os registos e as métricas. O script, em seguida, pode enviar os dados para análise de registos utilizando o [API de recoletores de dados](log-analytics-data-collector-api.md)

Tem de galeria do modelo do Azure [exemplos de utilização da automatização do Azure](https://azure.microsoft.com/en-us/resources/templates/?term=OMS) para recolher dados de serviços e enviar para análise de registos.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o blob storage para o IIS e a tabela de armazenamento para eventos](log-analytics-azure-storage-iis-table.md) ler os registos para esse diagnóstico de escrita nos registos IIS escritos para armazenamento de BLOBs ou armazenamento de tabelas de serviços do Azure.
* [Permitir soluções](log-analytics-add-solutions.md) para obter informações sobre os dados.
* [Utilizar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.
