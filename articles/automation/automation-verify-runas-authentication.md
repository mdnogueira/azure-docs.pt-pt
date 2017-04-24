---
title: "Validar a configuração da conta de Automatização do Azure | Microsoft Docs"
description: "Este artigo descreve como confirmar que a configuração da sua conta de automatização está corretamente configurada."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Testar a autenticação da conta Run As de Automatização do Azure
Depois de criar uma conta de Automatização com êxito, pode executar um teste simples para confirmar que consegue autenticar com êxito no Azure Resource Manager ou na implementação clássica do Azure com a sua conta Run As de Automatização recentemente criada ou atualizada.    

## <a name="automation-run-as-authentication"></a>Autenticação Run As de Automatização

1. No portal do Azure, abra a conta de Automatização criada anteriormente.  
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook **AzureAutomationTutorialScript** e, em seguida, clique em **Iniciar** para iniciar o runbook.  Irá receber um aviso a confirmar que pretende iniciar o runbook.
4. Uma [tarefa do runbook](automation-runbook-execution.md) é criada, o painel Tarefa é apresentado e o estado da tarefa é apresentado no mosaico **Resumo da Tarefa**.  
5. O estado da tarefa começará como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na nuvem fique disponível. Em seguida, irá mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.  
6. Quando tiver concluído a tarefa de runbook, vemos um estado de **Concluído**.<br> ![Teste do Runbook de Principal de Segurança](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Para ver os resultados detalhados do runbook, clique no mosaico **Saída**.
8. No painel **Saída**, deve verificar que foi autenticado com sucesso e devolveu uma lista de todos os recursos disponíveis no grupo de recursos.
9. Feche o painel **Saída** para voltar para o painel **Resumo da Tarefa**.
10. Feche o **Resumo da tarefa** e o painel do runbook **AzureAutomationTutorialScript** correspondente.

## <a name="classic-run-as-authentication"></a>Autenticação Run As clássica
Execute os seguintes passos, se for gerir recursos no modelo de implementação clássica, para confirmar que consegue autenticar com êxito com a nova conta Run As Clássica.     

1. No portal do Azure, abra a conta de Automatização criada anteriormente.  
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook **AzureClassicAutomationTutorialScript** e, em seguida, clique em **Iniciar** para iniciar o runbook.  Irá receber um aviso a confirmar que pretende iniciar o runbook.
4. Uma [tarefa do runbook](automation-runbook-execution.md) é criada, o painel Tarefa é apresentado e o estado da tarefa é apresentado no mosaico **Resumo da Tarefa**.  
5. O estado da tarefa começará como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na nuvem fique disponível. Em seguida, irá mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.  
6. Quando tiver concluído a tarefa de runbook, vemos um estado de **Concluído**.<br><br> ![Teste do Runbook de Principal de Segurança](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Para ver os resultados detalhados do runbook, clique no mosaico **Saída**.
8. No painel **Saída**, deve verificar que foi autenticado com sucesso e devolveu uma lista de todas as VMs clássicas na subscrição.
9. Feche o painel **Saída** para voltar para o painel **Resumo da Tarefa**.
10. Feche o **Resumo da Tarefa** e painel do runbook **AzureClassicAutomationTutorialScript** correspondente.

## <a name="next-steps"></a>Passos seguintes
* Para começar com runbooks do PowerShell, consulte [My first PowerShell runbook (O meu primeiro runbook do PowerShell)](automation-first-runbook-textual-powershell.md).
* Para obter mais informações sobre a Criação de Gráficos, consulte [Graphical authoring in Azure Automation (Criação de gráficos na Automatização do Azure)](automation-graphical-authoring-intro.md).

