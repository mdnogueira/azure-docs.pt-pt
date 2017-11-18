---
title: "Exemplos de início rápido do Azure PowerShell do Monitor. | Microsoft Docs"
description: "Utilize o PowerShell para aceder às funcionalidades de monitorização do Azure, tais como o dimensionamento automático, alertas, webhooks e procure-os registos de atividade."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: robb
ms.openlocfilehash: 36836a4528c8ba04eee1c5234fd6d4e0f9545913
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemplos de início rápido do Azure PowerShell de Monitor
Este artigo apresenta o exemplo comandos do PowerShell para o ajudar a aceder às funcionalidades de monitorização do Azure. Monitor do Azure permite-lhe para serviços de Cloud de dimensionamento automático, as máquinas virtuais e aplicações Web. Também permite-lhe enviar notificações de alerta ou chamar URLs web com base nos valores de dados de telemetria configurado.

> [!NOTE]
> Monitor do Azure é o novo nome para o que foi chamado "Informações do Azure" até 25th de Setembro de 2016. No entanto, os espaços de nomes e, consequentemente, os seguintes comandos de conter a palavra "informações".
> 
> 

## <a name="set-up-powershell"></a>Configurar o PowerShell
Se ainda não o tiver feito, configure o PowerShell para executar no seu computador. Para obter mais informações, consulte [como instalar e configurar o PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Exemplos neste artigo
Os exemplos no artigo mostram como pode utilizar os cmdlets de Monitor do Azure. Também pode rever a lista completa dos cmdlets do PowerShell de Monitor do Azure em [Cmdlets de Monitor do Azure (Insights)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).

## <a name="sign-in-and-use-subscriptions"></a>A iniciar sessão e utilizar subscrições
Primeiro, inicie sessão sua subscrição do Azure.

```PowerShell
Login-AzureRmAccount
```

Verá um ecrã de início de sessão. Uma vez que iniciar sessão na sua conta, TenantID, e são apresentados o ID de subscrição predefinido. Todos os cmdlets do Azure funciona no contexto da sua subscrição predefinida. Para ver a lista de subscrições tiver acesso, utilize o seguinte comando:

```PowerShell
Get-AzureRmSubscription
```

Para alterar o contexto de trabalho para uma subscrição diferente, utilize o seguinte comando:

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Obter o registo de atividade para uma subscrição
Utilize o `Get-AzureRmLog` cmdlet.  Seguem-se alguns exemplos comuns.

Obter entradas de registo desta data/hora de modo a apresentar:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obter entradas de registo entre um intervalo de data/hora:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de registo de um grupo de recurso específico:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obter entradas de registo a partir de um fornecedor de recursos específico entre um intervalo de data/hora:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter todas as entradas de registo com um emissor específico:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

O seguinte comando obtém os últimas 1 000 eventos de registo de atividade:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`suporta muitos outros parâmetros. Consulte o `Get-AzureRmLog` referência para obter mais informações.

> [!NOTE]
> `Get-AzureRmLog`Fornece apenas 15 dias do histórico. Utilizar o **- MaxEvents** parâmetro permite-lhe consultar os eventos de N últimos, para além de 15 dias. Para eventos de acesso com mais de 15 dias, utilize a REST API ou o SDK (c# exemplo utilizando o SDK). Se não incluir **StartTime**, em seguida, o valor predefinido é **EndTime** menos uma hora. Se não incluir **EndTime**, em seguida, o valor predefinido é a hora atual. Todas as horas são em UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Obter o histórico de alertas
Para ver todos os eventos de alerta, pode consultar os registos do Azure Resource Manager utilizando os exemplos seguintes.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para ver o histórico de uma regra de alerta específica, pode utilizar o `Get-AzureRmAlertHistory` cmdlet, passando no ID de recurso de regra de alerta.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O `Get-AzureRmAlertHistory` cmdlet suporta vários parâmetros. Obter mais informações, consulte [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Obter informações sobre regras de alertas
Todos os comandos seguintes agirem sobre um grupo de recursos com o nome "montest".

Ver todas as propriedades da regra de alerta:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Obter todos os alertas no grupo de recursos:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Obter todas as regras de alerta definido para um recurso de destino. Por exemplo, todas as regras de alerta definido numa VM.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`suporta outros parâmetros. Consulte [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obter mais informações.

## <a name="create-metric-alerts"></a>Criar métricas alertas
Pode utilizar o `Add-AlertRule` cmdlet para criar, atualizar ou desativar uma regra de alerta.

Pode criar propriedades de e-mail e webhook utilizando `New-AzureRmAlertRuleEmail` e `New-AzureRmAlertRuleWebhook`, respetivamente. O cmdlet de regra de alerta, atribuir estas propriedades como ações para o **ações** propriedade de regra de alerta.

A tabela seguinte descreve os parâmetros e valores utilizados para criar um alerta utilizando uma métrica.

| Parâmetro | valor |
| --- | --- |
| Nome |simpletestdiskwrite |
| Localização desta regra de alerta |EUA Leste |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/S1/resourceGroups/montest/Providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName do alerta que é criado |\PhysicalDisk ( total) \Disk escritas/seg. Consulte o `Get-MetricDefinitions` cmdlet sobre como obter os nomes de métricos exatos |
| operador |GreaterThan |
| Valor de limiar (contagem por segundo para esta métrica) |1 |
| WindowSize (formato hh: mm:) |00:05:00 |
| agregador (estatística da métrica, que utiliza a contagem média, neste caso) |Média |
| mensagens de correio eletrónico personalizadas (matriz de cadeia) |'foo@example.com','bar@example.com' |
| enviar correio eletrónico para os proprietários, contribuintes e leitores |-SendToServiceOwners |

Criar uma ação de E-Mail

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Criar uma ação do Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra de alerta na métrica de % de CPU numa VM clássica

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Obter a regra de alerta

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet de alerta de adicionar também atualiza a regra se já existe uma regra de alerta para as propriedades indicadas. Para desativar uma regra de alerta, inclua o parâmetro **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obter uma lista de métricas disponíveis para os alertas
Pode utilizar o `Get-AzureRmMetricDefinition` cmdlet para ver a lista de todas as métricas para um recurso específico.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

O exemplo seguinte gera uma tabela com o nome da métrica e a unidade para o mesmo.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis para `Get-AzureRmMetricDefinition` está disponível em [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-autoscale-settings"></a>Criar e gerir definições de dimensionamento automático
Um recurso (uma aplicação Web, VM, o serviço em nuvem ou conjunto de dimensionamento da Máquina Virtual) pode ter apenas uma definição de dimensionamento automático configurada para ele.
No entanto, cada definição de dimensionamento automático pode ter vários perfis. Por exemplo, um para um perfil de escala com base no desempenho e um segundo para um perfil com base na agenda. Cada perfil pode ter várias regras configuradas no mesmo. Para mais informações sobre o dimensionamento automático, consulte [como uma aplicação de dimensionamento automático](../cloud-services/cloud-services-how-to-scale-portal.md).

Eis os passos a utilizar:

1. Crie Regra (s).
2. Crie perfis mapeamento as regras que criou anteriormente para os perfis.
3. Opcional: Crie as notificações de dimensionamento automático ao configurar propriedades de webhook e e-mail.
4. Crie uma definição de dimensionamento automático, com um nome no recurso de destino, mediante o mapeamento de perfis e as notificações que criou nos passos anteriores.

Os exemplos seguintes mostram como pode criar uma definição de dimensionamento automático para um conjunto de dimensionamento de Máquina Virtual para um sistema operativo do Windows com base com a métrica de utilização da CPU.

Em primeiro lugar, crie uma regra para aumentar horizontalmente, com um aumento da contagem de instâncias.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Em seguida, crie uma regra de dimensionamento, com um diminuir de contagem de instâncias.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Em seguida, crie um perfil para as regras.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade do webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a definição de dimensionamento automático, incluindo e-mail e o webhook que criou anteriormente.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por fim, crie a definição de dimensionamento automático para adicionar o perfil que criou anteriormente. 

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obter mais informações sobre a gestão de definições de dimensionamento automático, consulte [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Histórico de dimensionamento automático
O exemplo seguinte mostra como pode ver eventos de alerta e dimensionamento automático recente. Utilize a pesquisa de registo de atividade para ver o histórico de dimensionamento automático.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Pode utilizar o `Get-AzureRmAutoScaleHistory` cmdlet para obter o histórico de dimensionamento automático.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obter mais informações, consulte [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Ver detalhes de uma definição de dimensionamento automático
Pode utilizar o `Get-Autoscalesetting` cmdlet para obter mais informações sobre a definição de dimensionamento automático.

O exemplo seguinte mostra os detalhes sobre todas as definições de dimensionamento automático no grupo de recursos 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo seguinte mostra os detalhes sobre todas as definições de dimensionamento automático o grupo de recursos 'myrg1' e especificamente a definição de dimensionamento automático com o nome 'MyScaleVMSSSetting'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Remover uma definição de dimensionamento automático
Pode utilizar o `Remove-Autoscalesetting` cmdlet para eliminar uma definição de dimensionamento automático.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Gerir perfis de registo para o registo de atividade
Pode criar um *registo perfil* e exportar dados do seu registo de atividade para uma conta de armazenamento e podem configurar a retenção de dados para o mesmo. Opcionalmente, também pode transmitir os dados para o Hub de eventos. Esta funcionalidade está atualmente em pré-visualização e só pode criar um perfil de registo por subscrição. Pode utilizar os seguintes cmdlets com a sua subscrição atual para criar e gerir perfis de registo. Também pode escolher uma determinada subscrição. Embora as predefinições do PowerShell na subscrição atual, pode sempre alterar esse utilizando `Set-AzureRmContext`. Pode configurar o registo de atividade para dados de rota para qualquer conta de armazenamento ou Hub de eventos dentro dessa subscrição. Dados são escritos como ficheiros do blob no formato JSON.

### <a name="get-a-log-profile"></a>Obter um perfil de registo
Para obter os seus perfis de registo existente, utilize o `Get-AzureRmLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Adicionar um perfil de registo sem retenção de dados
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adicionar um perfil de registo com retenção de dados
Pode especificar o **- RetentionInDays** propriedade com o número de dias, como um número inteiro positivo, onde os dados são mantidos.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adicionar perfil de registo com retenção e EventHub
Além do encaminhamento dados da sua conta de armazenamento, também pode transmiti-lo para um Hub de eventos. Esta versão de pré-visualização, a configuração de conta de armazenamento é obrigatória, mas a configuração do Hub de eventos é opcional.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar os registos de diagnóstico
Muitos serviços do Azure fornecem registos adicionais e telemetria que pode fazer um ou mais dos seguintes procedimentos: 
 - ser configurado para guardar os dados na sua conta do Storage do Azure
 - enviados para os Event Hubs
 - enviados para uma área de trabalho de análise de registos do OMS. 

A operação só pode ser efetuada num nível de recursos. O hub de conta ou evento de armazenamento deve estar presente na mesma região que o recurso de destino em que está configurada a definição de diagnóstico.

### <a name="get-diagnostic-setting"></a>Obter a definição de diagnóstico
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desativar a definição de diagnóstico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Ativar a definição de diagnóstico sem retenção

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Ativar a definição de diagnóstico com retenção

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Ativar a definição de diagnóstico com o período de retenção para uma categoria de registo específico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Ativar a definição de diagnóstico para os Event Hubs

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Ativar a definição de diagnóstico para análise de registos (OMS)

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Tenha em atenção que a propriedade WorkspaceId demora a *ID de recurso* da área de trabalho. Pode obter o ID de recurso da sua área de trabalho de análise de registos com o seguinte comando:

```PowerShell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId

```

Estes comandos podem ser combinados para enviar dados para vários destinos.
