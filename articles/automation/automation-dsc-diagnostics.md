---
title: "Reencaminhar dados de relatórios ao OMS Log Analytics do Azure Automation DSC | Microsoft Docs"
description: "Este artigo mostra como enviar pretendido Estado Configuration (DSC) dados de relatórios para gestão e de análise de registos do Microsoft Operations Management Suite para fornecer informações adicionais."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: eslesar
ms.openlocfilehash: 316031c5297a0201c8db4a9e177298c78962c673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="forward-azure-automation-dsc-reporting-data-to-oms-log-analytics"></a>Reencaminhar dados de relatórios ao OMS Log Analytics do Azure Automation DSC

Automatização pode enviar dados de estado do nó DSC a sua área de trabalho de análise de registos do Microsoft Operations Management Suite (OMS).  
Estado de conformidade é visível no portal do Azure, ou com o PowerShell, para nós e para recursos de DSC individuais em configurações de nó. Análise de registos pode:

* Obter as informações de compatibilidade para os nós geridos e recursos individuais
* Acionar um e-mail ou o alerta com base no estado de conformidade
* Escrever consultas avançadas entre os nós geridos
* Correlacionar o estado de conformidade em contas de automatização
* Visualizar o histórico de conformidade do nó ao longo do tempo

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar os seus relatórios do Automation DSC para análise de registos, é necessário:

* Versão de Novembro de 2016 ou posterior do [Azure PowerShell](/powershell/azure/overview) (v2.3.0).
* Uma conta de Automatização do Azure. Para obter mais informações, consulte [introdução da automatização do Azure](automation-offering-get-started.md)
* Uma área de trabalho de análise de registos com um **automatização e controlo** oferta de serviço. Para obter mais informações, consulte [introdução à análise de registos](../log-analytics/log-analytics-get-started.md).
* Pelo menos um nó de DSC de automatização do Azure. Para obter mais informações, consulte [máquinas de integração de gestão do Automation DSC do Azure](automation-dsc-onboarding.md) 

## <a name="set-up-integration-with-log-analytics"></a>Configurar a integração com a análise de registos

Para começar a importação de dados do Azure Automation DSC análise de registos, conclua os seguintes passos:

1. Inicie sessão sua conta do Azure no PowerShell. Consulte [iniciar sessão com o Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Obter o _ResourceId_ da sua conta de automatização, executando o seguinte comando do PowerShell: (se tiver mais do que uma conta de automatização, escolha o _ResourceID_ para a conta que pretende configurar).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. Obter o _ResourceId_ da sua área de trabalho de análise de registos, executando o seguinte comando do PowerShell: (se tiver mais de uma área de trabalho, escolha o _ResourceID_ para a área de trabalho que pretende configurar).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. Execute o seguinte comando do PowerShell, substituindo `<AutomationResourceId>` e `<WorkspaceResourceId>` com o _ResourceId_ valores de cada um dos passos anteriores:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Se pretender parar a importar dados do Automation DSC do Azure para a análise de registos, execute o seguinte comando do PowerShell.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>Consulte os registos de DSC

Depois de configurar a integração com a análise de registos para os seus dados de DSC de automatização, um **pesquisa registo** botão será apresentado no **nós de DSC** painel da sua conta de automatização. Clique em de **pesquisa registo** botão para ver os registos de dados do nó DSC.

![Botão de procura de registo](media/automation-dsc-diagnostics/log-search-button.png)

O **pesquisa de registo** abre painel e vir um **DscNodeStatusData** operação para cada nó de DSC e um **DscResourceStatusData** operação para cada [DSC recurso](https://msdn.microsoft.com/powershell/dsc/resources) chamado na configuração do nó aplicada a esse nó.

O **DscResourceStatusData** operação contém informações de erro para quaisquer recursos de DSC que falhou.

Clique em cada operação na lista para ver os dados para essa operação.

Também pode ver os registos pesquisando [na análise de registos. Consulte [localizar dados através de pesquisas de registo](../log-analytics/log-analytics-log-searches.md).
Escreva a consulta seguinte para localizar os registos de DSC:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

Também pode diminuir a consulta com o nome de operação. Por exemplo: ' tipo = AzureDiagnostics ResourceProvider = "MICROSOFT. Categoria de AUTOMATIZAÇÃO"="DscNodeStatus"OperationName ="DscNodeStatusData"

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>Enviar um e-mail quando ocorre uma falha de uma verificação de compatibilidade de DSC

Um dos nossos pedidos de cliente superior é a capacidade de enviar um e-mail ou um texto quando algo não bate com uma configuração de DSC.   

Para criar uma regra de alerta, pode começa por criar uma pesquisa de registo dos registos de relatório de DSC deve invocar o alerta.  Clique em de **alerta** botão para criar e configurar a regra de alerta.

1. Na página de descrição geral da análise do registo, clique em **pesquisa registo**.
1. Crie uma consulta de pesquisa de registo para o alerta, escrevendo a pesquisa seguinte para o campo de consulta:`Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`

  Se configurou os registos de mais do que uma conta de automatização ou a subscrição para a sua área de trabalho, pode agrupar os alertas por subscrição e conta de automatização.  
  Nome da conta de automatização pode ser derivado do campo de recurso na pesquisa de DscNodeStatusData.  
1. Para abrir o **Adicionar regra de alerta** ecrã, clique em **alerta** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [alertas na análise de registos](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Falha ao localizar recursos de DSC em todos os nós

Uma vantagem de utilizar a análise de registos é que pode procurar verificações falhadas entre nós.
Localizar todas as instâncias de recursos de DSC que falhou.

1. Na página de descrição geral da análise do registo, clique em **pesquisa registo**.
1. Crie uma consulta de pesquisa de registo para o alerta, escrevendo a pesquisa seguinte para o campo de consulta:`Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`

### <a name="view-historical-dsc-node-status"></a>Ver estado histórico de nó DSC

Por fim, poderá visualizar o histórico de estado do nó DSC ao longo do tempo.  
Pode utilizar esta consulta para procurar o estado do Estado do nó DSC ao longo do tempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Esta ação apresenta um gráfico de estado do nó ao longo do tempo.

## <a name="log-analytics-records"></a>Registos do Log Analytics

Diagnóstico da automatização do Azure cria duas categorias de registos de análise de registos.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando executou a verificação de compatibilidade. |
| OperationName |DscNodeStatusData |
| ResultType |Indica se o nó está em conformidade. |
| NodeName_s |O nome do nó gerido. |
| NodeComplianceStatus_s |Indica se o nó está em conformidade. |
| DscReportStatus |Indica se a verificação de compatibilidade foi executada com êxito. |
| ConfigurationMode | Como é aplicada a configuração para o nó. Os valores possíveis são __"ApplyOnly"__,__"ApplyandMonitior"__, e __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: aplica-se a configuração de DSC e faz nada adicional a menos que é feito o Push de uma nova configuração para o nó de destino ou quando uma nova configuração é retirada de um servidor. Após a aplicação inicial de uma nova configuração, DSC não verificar que se desviam de um estado anteriormente configurado. DSC tenta aplicar a configuração até ter êxito antes de __ApplyOnly__ entra em vigor. </li><li> __ApplyAndMonitor__: Este é o valor predefinido. O MMC aplica-se as configurações de novo. Após a aplicação inicial de uma nova configuração, se o nó de destino drifts do estado pretendido, DSC relatórios discrepância nos registos. DSC tenta aplicar a configuração até ter êxito antes de __ApplyAndMonitor__ entra em vigor.</li><li>__ApplyAndAutoCorrect__: DSC aplica-se as configurações de novo. Após a aplicação inicial de uma nova configuração, se o nó de destino drifts do estado pretendido, DSC relatórios discrepância nos registos e, em seguida, volta a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerido. |
| IPAddress | O endereço IPv4 do nó gerido. |
| Categoria | DscNodeStatus |
| Recurso | O nome da conta de automatização do Azure. |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| NodeId_g |GUID que identifica o nó gerido. |
| DscReportId_g |GUID que identifica o relatório. |
| LastSeenTime_t |Data e hora quando o relatório foi pela última vez visualizado. |
| ReportStartTime_t |Data e hora quando o relatório foi iniciado. |
| ReportEndTime_t |Data e hora quando o relatório foi concluído. |
| NumberOfResources_d |O número de recursos de DSC chamado na configuração aplicada ao nó. |
| SourceSystem | Como a análise de registos recolhidos os dados. Sempre *Azure* para diagnóstico do Azure. |
| ResourceId |Especifica a conta de automatização do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | A subscrição do Azure Id (GUID) para a conta de automatização. |
| ResourceGroup | Nome do grupo de recursos para a conta de automatização. |
| ResourceProvider | MICROSOFT. AUTOMATIZAÇÃO |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID que é o Id de correlação do relatório de compatibilidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando executou a verificação de compatibilidade. |
| OperationName |DscResourceStatusData|
| ResultType |Indica se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerido. |
| Categoria | DscNodeStatus |
| Recurso | O nome da conta de automatização do Azure. |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| NodeId_g |GUID que identifica o nó gerido. |
| DscReportId_g |GUID que identifica o relatório. |
| DscResourceId_s |O nome da instância de recursos do DSC. |
| DscResourceName_s |O nome do recurso de DSC. |
| DscResourceStatus_s |Indica se o recurso de DSC está em conformidade. |
| DscModuleName_s |O nome do módulo do PowerShell que contém os recursos de DSC. |
| DscModuleVersion_s |A versão do módulo do PowerShell que contém os recursos de DSC. |
| DscConfigurationName_s |O nome da configuração aplicado ao nó. |
| ErrorCode_s | O código de erro se o recurso falhou. |
| ErrorMessage_s |A mensagem de erro se o recurso falhou. |
| DscResourceDuration_d |O tempo, em segundos, que executou o recursos de DSC. |
| SourceSystem | Como a análise de registos recolhidos os dados. Sempre *Azure* para diagnóstico do Azure. |
| ResourceId |Especifica a conta de automatização do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | A subscrição do Azure Id (GUID) para a conta de automatização. |
| ResourceGroup | Nome do grupo de recursos para a conta de automatização. |
| ResourceProvider | MICROSOFT. AUTOMATIZAÇÃO |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID que é o Id de correlação do relatório de compatibilidade. |

## <a name="summary"></a>Resumo

Ao enviar os dados do Automation DSC para análise de registos, pode obter um melhor aprofundadas sobre o estado dos seus nós de DSC de automatização por:

* Configurar alertas para notificá-lo quando existe um problema
* Utilizar vistas personalizadas e consultas de pesquisa para visualizar os resultados de runbook, estado de tarefa de runbook e outras relacionados com indicadores chaves ou métricas.  

Análise de registos dá-lhe maior visibilidade operacional aos seus dados de DSC de automatização e pode ajudar a incidentes de endereço mais rapidamente.  

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como construir consultas de pesquisa diferentes e reveja os registos de DSC de automatização com a análise de registos, consulte o artigo [pesquisas de registo na análise de registos](../log-analytics/log-analytics-log-searches.md)
* Para obter mais informações sobre como utilizar o DSC de automatização do Azure, consulte [introdução ao Azure Automation DSC](automation-dsc-getting-started.md)
* Para saber mais sobre o OMS Log Analytics e as origens das coleções de dados, veja [Collecting Azure storage data in Log Analytics overview (Descrição geral da Recolha de dados do armazenamento do Azure no Log Analytics)](../log-analytics/log-analytics-azure-storage.md)

