---
title: "Reencaminhar dados de tarefa de automatização do Azure para a análise de registos do OMS | Microsoft Docs"
description: "Este artigo demonstra como enviar estado da tarefa e runbook fluxos de trabalho para a gestão e de análise de registos do Microsoft Operations Management Suite para fornecer informações adicionais."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 21923adaa8f8118995799319c1fd496a6e449faa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Reencaminhar estado da tarefa e fluxos de trabalho da automatização para análise de registos (OMS)
Automatização pode enviar as runbook fluxos de trabalho e o estado da tarefa para a sua área de trabalho de análise de registos do Microsoft Operations Management Suite (OMS).  Os registos de tarefa e fluxos de trabalho são visíveis no portal do Azure, ou com o PowerShell, para tarefas individuais e Isto permite-lhe efetuar as investigações simples. Agora com a análise de registos, pode:

* Obter conhecimentos aprofundados sobre as tarefas de automatização
* Acionamento um e-mail ou o alerta com base no seu estado de tarefa de runbook (por exemplo, falha ou suspenso)
* Escrever consultas avançadas nos seus fluxos de trabalho
* Correlacionar tarefas em contas de automatização
* Visualizar o histórico do trabalho ao longo do tempo     

## <a name="prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implementação
Para começar a enviar os registos de automatização à análise de registos, é necessário:

1. Versão de Novembro de 2016 ou posterior do [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
2. Uma área de trabalho de análise de registos. Para obter mais informações, consulte [introdução à análise de registos](../log-analytics/log-analytics-get-started.md). 
3. O ResourceId para a sua conta de automatização do Azure

Para localizar o ResourceId para a conta de automatização do Azure e a área de trabalho de análise de registos, execute o PowerShell seguinte:

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se tiver várias contas de automatização ou áreas de trabalho, na saída dos comandos anteriores, localizar o *nome* tem de configurar e copie o valor para *ResourceId*.

Se precisar de localizar o *nome* da sua conta de automatização no portal do Azure, selecione a conta de automatização do **conta de automatização** painel e selecione **todas as definições** .  A partir do painel **Todas as definições**, em **Definições da Conta** selecione **Propriedades**.  No painel **Propriedades**, pode ter em atenção estes valores.<br> ![Propriedades da conta de automatização](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Configurar a integração com a análise de registos
1. No seu computador, inicie **do Windows PowerShell** do **iniciar** ecrã.  
2. Copie e cole o seguinte do PowerShell e editar o valor para o `$workspaceId` e `$automationAccountId`.  Para o `-Environment` parâmetro, os valores válidos são *AzureCloud* ou *AzureUSGovernment* , dependendo do ambiente de nuvem estão a funcionar.     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }

# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

Depois de executar este script, irá ver registos de análise de registos dentro de 10 minutos de novos JobLogs ou JobStreams que está a ser escrito.

Para ver os registos, execute a consulta seguinte na pesquisa de registos de análise de registos:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verifique a configuração
Para confirmar a sua conta de automatização está a enviar os registos para a sua área de trabalho de análise de registos, verifique que diagnóstico está definido corretamente na conta de automatização com o PowerShell seguinte:

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Na saída Certifique-se de que:
+ Em *registos*, o valor para *ativado* é *verdadeiro*
+ O valor de *WorkspaceId* está definido como ResourceId da sua área de trabalho de análise de registos


## <a name="log-analytics-records"></a>Registos do Log Analytics
Cria dois tipos de registos de análise de registos de diagnóstico da automatização do Azure e é etiquetado como **tipo = AzureDiagnostics**.

### <a name="job-logs"></a>Registos da tarefa
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |Quem iniciou a operação.  Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| JobId_g |GUID que é o Id do trabalho do runbook. |
| ResultType |O estado do trabalho do runbook.  Os valores possíveis são:<br>-Novo<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>-Concluída |
| Categoria | Classificação do tipo de dados.  Para a Automatização, o valor é JobLogs. |
| OperationName | Especifica o tipo de operação efetuada no Azure.  Para automatização, o valor é a tarefa. |
| Recurso | Nome da conta de automatização |
| SourceSystem | Como a análise de registos recolhidos os dados. Sempre *Azure* para diagnóstico do Azure. |
| ResultDescription |Descreve o estado do resultado do trabalho do runbook.  Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído |
| CorrelationId |GUID que é o Id de Correlação do trabalho do runbook. |
| ResourceId |Especifica o id de recurso de conta de automatização do Azure do runbook. |
| SubscriptionId | A subscrição do Azure Id (GUID) para a conta de automatização. |
| ResourceGroup | Nome do grupo de recursos para a conta de automatização. |
| ResourceProvider | MICROSOFT. AUTOMATIZAÇÃO |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Fluxos de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |Quem iniciou a operação.  Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Em Curso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| JobId_g |GUID que é o Id do trabalho do runbook. |
| ResultType |O estado do trabalho do runbook.  Os valores possíveis são:<br>-Em curso |
| Categoria | Classificação do tipo de dados.  Para a Automatização, o valor é JobStreams. |
| OperationName | Especifica o tipo de operação efetuada no Azure.  Para automatização, o valor é a tarefa. |
| Recurso | Nome da conta de automatização |
| SourceSystem | Como a análise de registos recolhidos os dados. Sempre *Azure* para diagnóstico do Azure. |
| ResultDescription |Inclui o fluxo de saída do runbook. |
| CorrelationId |GUID que é o Id de Correlação do trabalho do runbook. |
| ResourceId |Especifica o id de recurso de conta de automatização do Azure do runbook. |
| SubscriptionId | A subscrição do Azure Id (GUID) para a conta de automatização. |
| ResourceGroup | Nome do grupo de recursos para a conta de automatização. |
| ResourceProvider | MICROSOFT. AUTOMATIZAÇÃO |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Visualizar automatização registos de análise de registos
Agora que iniciou a enviar os registos da tarefa de automatização para análise de registos, vamos ver o que pode fazer com estes registos no interior de análise de registos.

Para ver os registos, execute a seguinte consulta:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envie um e-mail quando uma tarefa de runbook falha ou suspende
Uma das nossa principal cliente pede-lhe destina-se a capacidade de enviar um e-mail ou um texto quando algo não bate com uma tarefa de runbook.   

Para criar uma regra de alerta, pode começa por criar uma pesquisa de registo para os registos da tarefa de runbook que deve invocar o alerta.  Clique em de **alerta** botão para criar e configurar a regra de alerta.

1. Na página de descrição geral da análise do registo, clique em **pesquisa registo**.
2. Criar uma consulta de pesquisa de registo para o alerta, escrevendo a pesquisa seguinte para o campo de consulta: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)` também pode agrupar pelo RunbookName utilizando:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`   

   Se configurou os registos de mais do que uma conta de automatização ou a subscrição para a sua área de trabalho, pode agrupar os alertas por subscrição e conta de automatização.  Nome da conta de automatização pode ser derivado do campo de recurso na pesquisa de JobLogs.  
3. Para abrir o **Adicionar regra de alerta** ecrã, clique em **alerta** na parte superior da página. Para obter mais detalhes sobre as opções para configurar o alerta, consulte [alertas na análise de registos](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Localizar todas as tarefas que foram concluídas com erros
Para além dos alertas sobre falhas, pode encontrar quando uma tarefa de runbook tem um erro de não interrupção. Nestes casos PowerShell produz uma sequência de erro, mas os erros de não interrupção não causam a tarefa suspender ou falhar.    

1. Na sua área de trabalho de análise de registos, clique em **pesquisa registo**.
2. No campo de consulta, escreva `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g` e, em seguida, clique em **pesquisa**.

### <a name="view-job-streams-for-a-job"></a>Fluxos de trabalho de vista de uma tarefa
Quando está a depurar uma tarefa, também poderá procurar nos fluxos de trabalho.  A consulta seguinte mostra todos os fluxos de uma única tarefa com o GUID 2ebd22ea-e05e-4eb9 - 9d 76-d73cbd4356e0:   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>Ver o estado do histórico de tarefas
Por fim, poderá visualizar o histórico do trabalho ao longo do tempo.  Pode utilizar esta consulta para procurar o estado das suas tarefas ao longo do tempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![Gráfico de estado de tarefa históricos do OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Resumo
Ao enviar os dados de estado e de fluxo da tarefa de automatização à análise de registos, pode obter uma melhor aprofundadas sobre o estado das suas tarefas de automatização por:
+ Configurar alertas para notificá-lo quando existe um problema
+ Utilizar vistas personalizadas e consultas de pesquisa para visualizar os resultados de runbook, estado de tarefa de runbook e outras relacionados com indicadores chaves ou métricas.  

Análise de registos dá-lhe maior visibilidade operacional para as tarefas de automatização e pode ajudar a incidentes de endereço mais rápidas.  

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como construir consultas de pesquisa diferentes e rever os registos dos trabalhos da Automatização com o Log Analytics, veja [Log searches in Log Analytics (Pesquisas de registos no Log Analytics)](../log-analytics/log-analytics-log-searches.md)
* Para compreender como criar e obter mensagens de erro e de saída a partir de runbooks, consulte [Runbook de saída e as mensagens](automation-runbook-output-and-messages.md)
* Para saber mais sobre a execução dos runbooks, como monitorizar trabalhos de runbooks e outros detalhes técnicos, veja [Track a runbook job (Acompanhar um trabalho de runbook)](automation-runbook-execution.md)
* Para saber mais sobre o OMS Log Analytics e as origens das coleções de dados, veja [Collecting Azure storage data in Log Analytics overview (Descrição geral da Recolha de dados do armazenamento do Azure no Log Analytics)](../log-analytics/log-analytics-azure-storage.md)
