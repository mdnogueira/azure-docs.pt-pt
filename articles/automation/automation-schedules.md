---
title: "Agendas na automatização do Azure | Microsoft Docs"
description: "As agendas da automatização são utilizadas para agendar runbooks na automatização do Azure para iniciar automaticamente. Descreve como criar e gerir uma agenda para que possa começar automaticamente um runbook num determinado momento ou numa agenda periódica."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 446dc79202938ee96cbb090345d9c191060afe76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendar um runbook na Automatização do Azure
Para agendar um runbook na automatização do Azure para iniciar o um tempo especificado, ligue-o a uma ou mais agendas. Uma agenda pode ser configurada para executar uma vez ou um ocorrer hora a hora ou diária agenda para runbooks no portal clássico do Azure e para os runbooks no portal do Azure, também pode agendá-las para semanais, mensais, específicos dias da semana ou dias do mês , ou num determinado dia do mês.  Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks ligados ao mesmo.

> [!NOTE]
> As agendas não suportam atualmente configurações de DSC de automatização do Azure.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell
Os cmdlets na tabela seguinte são utilizados para criar e gerir agendas com o Windows PowerShell na automatização do Azure. Estes são enviados como parte do [módulo Azure PowerShell](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| **Cmdlets do Gestor de recursos do Azure** | |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Obtém uma agenda. |
| [Novo AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Cria uma nova agenda. |
| [Remover AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Remove uma agenda. |
| [Conjunto AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Define as propriedades de uma agenda existente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Obtém agendada runbooks. |
| [Registar AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa um runbook com uma agenda. |
| [AzureRmAutomationScheduledRunbook anular o registo](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates um runbook a partir de uma agenda. |
| **Cmdlets de gestão do serviço do Azure** | |
| [Get-AzureAutomationSchedule](/powershell/module/azure/get-azureautomationschedule?view=azuresmps-3.7.0) |Obtém uma agenda. |
| [Novo AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) |Cria uma nova agenda. |
| [Remover AzureAutomationSchedule](/powershell/module/azure/remove-azureautomationschedule?view=azuresmps-3.7.0) |Remove uma agenda. |
| [Conjunto AzureAutomationSchedule](/powershell/module/azure/set-azureautomationschedule?view=azuresmps-3.7.0) |Define as propriedades de uma agenda existente. |
| [Get-AzureAutomationScheduledRunbook](/powershell/module/azure/get-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Obtém agendada runbooks. |
| [Registar AzureAutomationScheduledRunbook](/powershell/module/azure/register-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Associa um runbook com uma agenda. |
| [AzureAutomationScheduledRunbook anular o registo](/powershell/module/azure/unregister-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Dissociates um runbook a partir de uma agenda. |

## <a name="creating-a-schedule"></a>Criar uma agenda
Pode criar uma nova agenda para runbooks no portal do Azure, no portal clássico ou do Windows PowerShell. Também tem a opção de criar uma nova agenda quando ligar um runbook a uma agenda no portal do Azure clássico ou do Azure.

> [!NOTE]
> A automatização do Azure utiliza os módulos mais recentes na sua conta de automatização quando uma nova tarefa agendada é executada.  Para evitar o impacto dos runbooks e os processos que automatizar, deverá testar primeiro todos os runbooks que tenha ligado agendas com uma conta de automatização dedicada para fins de teste.  Esta ação valida o agendada runbooks continua a funcionar corretamente e se não estiver, ainda pode resolver problemas e aplicar as alterações necessárias antes de migrar a versão do runbook atualizado para produção.  
>  A conta de automatização não obter automaticamente as novas versões de módulos, a menos que atualizou-los manualmente, selecionando o [os módulos do Azure Update](automation-update-azure-modules.md) opção do **módulos** . 
>  

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova agenda no portal do Azure
1. No portal do Azure, da sua conta de automatização, selecione **agendas** na secção **recursos partilhados** à esquerda. 
2. Clique em **adicionar uma agenda** na parte superior da página.
4. No **nova agenda** painel, escreva um **nome** e opcionalmente um **Descrição** para a nova agenda.
5. Selecione se a agenda é executada uma vez, ou com base numa agenda reoccurring selecionando **uma vez** ou **periodicidade**.  Se selecionar **uma vez** especificar um **hora de início** e, em seguida, clique em **criar**.  Se selecionar **periodicidade**, especifique um **hora de início** e a frequência com que frequência pretende runbook repetir - por **hora**, **dia**, **semana**, ou por **mês**.  Se selecionar **semana** ou **mês** na lista pendente, o **opção de periodicidade** aparece no painel e após a seleção, o **opção de periodicidade** painel é apresentado e pode selecionar o dia da semana se tiver selecionado **semana**.  Se tiver selecionado **mês**, pode optar por **dias da semana** ou dias específicos do mês de calendário e por fim, que pretende executá-lo no último dia do mês ou não e, em seguida, clique em **OK**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Para criar uma nova agenda no portal clássico do Azure
1. No portal clássico do Azure, selecione de automatização e, em seguida, selecione o nome de uma conta de automatização.
2. Selecione o **ativos** separador.
3. Na parte inferior da janela, clique em **Adicionar definição**.
4. Clique em **adicionar agenda**.
5. Escreva um **nome** e opcionalmente um **Descrição** para a nova agenda. Pode executar a sua agenda **uma vez**, **horária**, **diária**, **semanal**, ou **mensal**.
6. Especifique um **hora de início** e outras opções consoante o tipo de agenda que selecionou.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para criar uma nova agenda com o Windows PowerShell
Pode utilizar o [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) cmdlet para criar uma nova agenda na automatização do Azure para runbooks clássicos, ou [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet para runbooks no portal do Azure. Tem de especificar a hora de início da agenda e a frequência que deve ser executada.

Os comandos de exemplo seguintes mostram como criar um agendamento para a partir do dia 15 e 30th de cada mês utilizando um cmdlet do Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

Os comandos de exemplo seguintes mostram como criar uma nova agenda que é executado por dia às 3:30 da Tarde iniciar no 20 de Janeiro de 2015, com um cmdlet de gestão de serviço do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Ligar uma agenda a um runbook
Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks ligados ao mesmo. Se um runbook tiver parâmetros, pode fornecer valores para os mesmos. Tem de fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para os parâmetros opcionais.  Estes valores são utilizados sempre que o runbook for iniciado por este agendamento.  Pode anexar o mesmo runbook ao agendamento outro e especifique os valores de parâmetro diferentes.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para ligar uma agenda a um runbook com o portal do Azure
1. No portal do Azure, da sua conta de automatização, selecione **Runbooks** na secção **automatização de processos** à esquerda.
2. Clique no nome do runbook a agendar.
3. Se o runbook não está atualmente associado a uma agenda, em seguida, é-lhe dada a opção para criar uma nova agenda ou a ligação para uma agenda existente.  
4. Se o runbook tiver parâmetros, pode selecionar a opção **modificar definições de execução (predefinição: Azure)** e **parâmetros** é apresentado o painel onde pode introduzir as informações em conformidade.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Para ligar uma agenda a um runbook com o portal clássico do Azure
1. No portal clássico do Azure, selecione **automatização** e, em seguida, clique no nome de uma conta de automatização.
2. Selecione o **Runbooks** separador.
3. Clique no nome do runbook a agendar.
4. Clique em de **agenda** separador.
5. Se o runbook não está atualmente associado a uma agenda, em seguida, é-lhe dada a opção de **ligação para uma nova agenda** ou **ligação para uma agenda existente**.  Se o runbook está atualmente associado a uma agenda, clique em **ligação** na parte inferior da janela para aceder a estas opções.
6. Se o runbook tiver parâmetros, recebem os respetivos valores.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Para ligar uma agenda a um runbook com o Windows PowerShell
Pode utilizar o [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para associar um agendamento ao runbook clássico ou [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) cmdlet para runbooks no portal do Azure.  Pode especificar valores para os parâmetros do runbook com o parâmetro de parâmetros. Consulte [iniciar um Runbook na automatização do Azure](automation-starting-a-runbook.md) para obter mais informações sobre a especificação de valores de parâmetros.

Os comandos de exemplo seguintes mostram como ligar uma agenda a um runbook com um cmdlet do Azure Resource Manager com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
Os comandos de exemplo seguintes mostram como associar um agendamento utilizando um cmdlet de gestão de serviço do Azure com os parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Desativar uma agenda
Ao desativar uma agenda, todos os runbooks ligado ao mesmo já não é executado nessa agenda. Manualmente pode desactivar uma agenda ou definir um prazo de expiração para agendas com uma frequência quando criá-los. Quando for atingida a hora de expiração, a agenda está desativada.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desativar uma agenda do portal do Azure
1. No portal do Azure, da sua conta de automatização, selecione **agendas** na secção **recursos partilhados** à esquerda.
2. Clique no nome de uma agenda para abrir o painel de detalhes.
3. Alteração **ativada** para **não**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Para desativar uma agenda do portal clássico do Azure
Pode desativar uma agenda no portal clássico do Azure da página de detalhes de agenda para a agenda.

1. No portal clássico do Azure, selecione de automatização e, em seguida, clique no nome de uma conta de automatização.
2. Selecione o separador de recursos.
3. Clique no nome de uma agenda para abrir a página de detalhes.
4. Alteração **ativada** para **não**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para desativar uma agenda com o Windows PowerShell
Pode utilizar o [conjunto AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) cmdlet para alterar as propriedades de uma agenda existente para um runbook clássico ou [conjunto AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) cmdlet para runbooks no portal do Azure. Para desativar o agendamento, especifique **falso** para o **IsEnabled** parâmetro.

Os comandos de exemplo seguintes mostram como desactivar uma agenda de um runbook com um cmdlet do Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

Os comandos de exemplo seguintes mostram como desactivar uma agenda com o cmdlet de gestão de serviço do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Passos seguintes
* Para começar com runbooks na automatização do Azure, consulte [iniciar um Runbook na automatização do Azure](automation-starting-a-runbook.md) 

