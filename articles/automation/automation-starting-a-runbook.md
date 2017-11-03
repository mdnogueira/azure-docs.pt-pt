---
title: "Iniciar um runbook na automatização do Azure | Microsoft Docs"
description: "Resume os diferentes métodos que podem ser utilizados para iniciar um runbook na automatização do Azure e fornece detalhes sobre como utilizar o portal do Azure e o Windows PowerShell."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 6ee756b4-9200-4eb2-9bda-ec156853803b
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 7537a50d3d9b773f48bc498f946ea5f63f6d530b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="starting-a-runbook-in-azure-automation"></a>Iniciar um runbook na automatização do Azure
A tabela seguinte irá ajudá-lo a determinar o método para iniciar um runbook na automatização do Azure que é mais adequado para o cenário em particular. Este artigo inclui detalhes sobre a iniciar um runbook com o portal do Azure e com o Windows PowerShell. Detalhes sobre os outros métodos são fornecidos na outra documentação que podem aceder a partir das ligações abaixo.

| **MÉTODO** | **CARACTERÍSTICAS** |
| --- | --- |
| [Portal do Azure](#starting-a-runbook-with-the-azure-portal) |<li>Método mais simples com a interface de utilizador interativa.<br> <li>Formulário para fornecer valores de parâmetros simples.<br> <li>Controle facilmente o estado da tarefa.<br> <li>Acesso autenticado com início de sessão do Azure. |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) |<li>Chamada a partir da linha de comandos com cmdlets do Windows PowerShell.<br> <li>Pode ser incluído numa solução automatizada com vários passos.<br> <li>Pedido é autenticado com o certificado ou o utilizador de OAuth principal / serviço principal.<br> <li>Indique os valores de parâmetro simples e complexos.<br> <li>Controlar o estado da tarefa.<br> <li>Cliente necessário para suportar os cmdlets do PowerShell. |
| [API de automatização do Azure](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>Método mais flexível, mas também mais complexas.<br> <li>Chamar a partir de qualquer código personalizado que pode efetuar pedidos de HTTP.<br> <li>Pedido autenticado com o certificado ou o utilizador de Oauth principal / serviço principal.<br> <li>Indique os valores de parâmetro simples e complexos.<br> <li>Controlar o estado da tarefa. |
| [Webhooks](automation-webhooks.md) |<li>Inicie o runbook único pedido de HTTP.<br> <li>Autenticar com o token de segurança no URL.<br> <li>Cliente não pode substituir os valores de parâmetros especificados ao criar o webhook. Runbook pode definir único parâmetro que é preenchido com os detalhes do pedido HTTP.<br> <li>Sem capacidade para controlar o estado da tarefa através do URL do webhook. |
| [Responder a alerta do Azure](../log-analytics/log-analytics-alerts.md) |<li>Inicie um runbook em resposta a alerta do Azure.<br> <li>Configure o webhook para a ligação para o alertar e um runbook.<br> <li>Autenticar com o token de segurança no URL. |
| [Agenda](automation-schedules.md) |<li>Inicie automaticamente o runbook no agendamento horário, diário, semanal ou mensal.<br> <li>Manipular agenda através do portal do Azure, os cmdlets do PowerShell ou API do Azure.<br> <li>Indique os valores de parâmetro a ser utilizado com base numa agenda. |
| [A partir de outro Runbook](automation-child-runbooks.md) |<li>Utilize um runbook como uma atividade no outro runbook.<br> <li>É útil para a funcionalidade utilizada por vários runbooks.<br> <li>Fornecer valores de parâmetros para o runbook subordinado e utilizar o resultado no runbook principal. |

A imagem seguinte ilustra o processo de passo a passo detalhado no ciclo de vida de um runbook. Inclui um runbook é iniciado na automatização do Azure, os componentes necessários para o Runbook Worker híbrido para executar runbooks de automatização do Azure e as interações entre os diferentes componentes de formas diferentes. Para saber mais sobre a execução de runbooks de automatização no seu centro de dados, consulte [os runbook workers híbridos](automation-hybrid-runbook-worker.md)

![Arquitetura de Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Iniciar um runbook com o portal do Azure
1. No portal do Azure, selecione **automatização** e, em seguida, em seguida, clique no nome de uma conta de automatização.
2. No Hub menu, selecione **Runbooks**.
3. No **Runbooks** painel, selecione um runbook e, em seguida, clique em **iniciar**.
4. Se o runbook tiver parâmetros, será solicitado para fornecer valores com uma caixa de texto para cada um dos parâmetros. Consulte [parâmetros do Runbook](#Runbook-parameters) abaixo para obter mais detalhes sobre os parâmetros.
5. No **tarefa** painel, pode ver o estado da tarefa de runbook.

## <a name="starting-a-runbook-with-windows-powershell"></a>Iniciar um runbook com o Windows PowerShell
Pode utilizar o [início AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) para iniciar um runbook com o Windows PowerShell. O código de exemplo seguinte inicia um runbook denominado Test-Runbook.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Início AzureRmAutomationRunbook devolve um objeto de tarefa que pode utilizar para monitorizar o estado depois do runbook for iniciado. Em seguida, pode utilizar este objeto de tarefa com [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) para determinar o estado da tarefa e [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) para obter o resultado. O código de exemplo seguinte inicia um runbook denominado Test-Runbook, aguarda até que este foi concluída e, em seguida, apresenta o resultado.

```
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Se o runbook necessitar de parâmetros, em seguida, tem de fornecê-los como um [tabela hash](http://technet.microsoft.com/library/hh847780.aspx) em que a chave da tabela hash corresponde ao nome de parâmetro e o valor é o valor do parâmetro. O exemplo seguinte mostra como iniciar um runbook com dois parâmetros de cadeia denominados FirstName e LastName, um número inteiro denominado RepeatCount e um parâmetro booleano denominado Show. Para obter informações adicionais sobre parâmetros, consulte [parâmetros do Runbook](#Runbook-parameters) abaixo.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parâmetros do Runbook
Quando inicia um runbook a partir do Portal do Azure ou do Windows PowerShell, a instrução é enviada através do serviço web automatização do Azure. Este serviço não suporta parâmetros com tipos de dados complexas. Se tiver de fornecer um valor para um parâmetro complexo, em seguida, tem de chamá-lo inline a partir de outro runbook, tal como descrito no [runbooks subordinados na automatização do Azure](automation-child-runbooks.md).

O serviço web de automatização do Azure irá fornecer a funcionalidade especial para os parâmetros que utilizam determinados tipos de dados, conforme descrito nas secções seguintes.

### <a name="named-values"></a>Valores nomeados
Se o parâmetro é o tipo de dados [object], em seguida, pode utilizar o seguinte formato JSON para lhe enviar uma lista de valores nomeados: *{Nome1: 'Value1', Name2: 'Value2', nome3: 'Valor3'}*. Estes valores devem ser tipos simples. O runbook receberá o parâmetro como um [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) com propriedades que correspondem a cada valor nomeado.

Considere o seguinte runbook de teste que aceita um parâmetro denominado utilizador.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

O seguinte texto pode ser utilizado para o parâmetro de utilizador.

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Isto resulta no seguinte resultado.

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matrizes
Se o parâmetro é uma matriz, como [array] ou [string []], em seguida, pode utilizar o seguinte formato JSON para lhe enviar uma lista de valores: *[valor1, valor2, Valor3]*. Estes valores devem ser tipos simples.

Considere o seguinte runbook de teste que aceita um parâmetro denominado *utilizador*.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

O seguinte texto pode ser utilizado para o parâmetro de utilizador.

```
["Joe","Smith",2,true]
```

Isto resulta no seguinte resultado.

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Credenciais
Se o parâmetro de tipo de dados **PSCredential**, em seguida, pode fornecer o nome de uma automatização do Azure [recurso de credencial](automation-credentials.md). O runbook obterá a credencial com o nome que especificar.

Considere o seguinte runbook de teste que aceita um parâmetro denominado credencial.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

O seguinte texto pode ser utilizado para o utilizador parâmetro supondo que existe um recurso de credencial denominado *credencial My*.

```
My Credential
```

Pressupondo que o nome de utilizador na credencial foi *jsmith*, isto resulta no seguinte resultado.

```
jsmith
```

## <a name="next-steps"></a>Passos seguintes
* A arquitetura de runbook no artigo atual fornece uma descrição geral de alto nível de runbooks que gerem recursos no Azure e no local com o Runbook Worker híbrido.  Para saber mais sobre a execução de runbooks de automatização no seu centro de dados, consulte [os Runbook Workers híbridos](automation-hybrid-runbook-worker.md).
* Para saber mais sobre como criar runbooks modulares a ser utilizada por outros runbooks para funções específicas ou comuns, consulte [Runbooks subordinados](automation-child-runbooks.md).

