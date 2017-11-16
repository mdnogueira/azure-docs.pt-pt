---
title: "Execução do Runbook na automatização do Azure | Microsoft Docs"
description: "Descreve os detalhes da forma como é processado um runbook na automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: d10c8ce2-2c0b-4ea7-ba3c-d20e09b2c9ca
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 22852fed184022b4eae298d6cc531fd383eff552
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="runbook-execution-in-azure-automation"></a>Execução do Runbook na automatização do Azure
Quando inicia um runbook na automatização do Azure, é criada uma tarefa. Uma tarefa é uma instância de execução individual de um runbook. Um trabalho de automatização do Azure é atribuído para executar cada tarefa. Enquanto trabalhadores são partilhados por várias contas do Azure, a diferentes contas de automatização de tarefas estão isoladas entre si. Pode não ter controlar ao longo do que trabalho processa o pedido para a tarefa. Um único runbook pode ter várias tarefas em execução em simultâneo.  O ambiente de execução para a mesma conta de automatização de tarefas pode ser reutilizado. Ao ver a lista de runbooks no portal do Azure, lista o estado de todas as tarefas que foram iniciadas para cada runbook. Pode ver a lista de tarefas para cada runbook para poder controlar o estado de cada. Para obter uma descrição dos Estados de tarefa diferente [Estados das tarefas](#job-statuses).

O diagrama seguinte mostra o ciclo de vida de uma tarefa de runbook para [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) e [runbooks do fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Estados - tarefa do fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

O diagrama seguinte mostra o ciclo de vida de uma tarefa de runbook para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks).

![Estados de tarefa - o Script do PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

As tarefas têm acesso aos seus recursos do Azure ao efetuar uma nova ligação à sua subscrição do Azure. Apenas têm acesso a recursos no seu centro de dados se esses recursos estão acessíveis a partir da nuvem pública.

## <a name="job-statuses"></a>Estados das tarefas
A tabela seguinte descreve os diferentes Estados possíveis das tarefas.

| Estado | Descrição |
|:--- |:--- |
| Concluído |A tarefa foi concluída com êxito. |
| Com Falhas |Para [runbooks gráfico e o fluxo de trabalho do PowerShell](automation-runbook-types.md), o runbook não conseguiu compilar.  Para [runbooks de Script do PowerShell](automation-runbook-types.md), Falha ao iniciar o runbook ou a tarefa encontrou uma exceção. |
| Falha ao aguardar a recursos |A tarefa falhou porque atingiu o [fração justa](#fairshare) limitar três vezes e iniciadas a partir do ponto de verificação mesmo ou desde o início do runbook cada vez. |
| Em Fila |A tarefa está aguardar recursos num trabalho de automatização fique disponível para que as que possa ser iniciado. |
| A Iniciar |A tarefa foi atribuída a uma função de trabalho e o sistema está a iniciá-la. |
| A retomar |O sistema está a retomar a tarefa depois foi suspenso. |
| Em Execução |A tarefa está em execução. |
| Aguardar execução, dos recursos |A tarefa foi descarregada porque atingiu o [fração justa](#fairshare) limite. Retoma em breve a partir do último ponto de verificação. |
| Parada |A tarefa foi parada pelo utilizador antes de estar concluído. |
| A Parar |O sistema está a parar a tarefa. |
| Suspenso |A tarefa foi suspensa pelo utilizador, pelo sistema ou por um comando no runbook. Uma tarefa suspensa pode ser iniciada novamente e retomar a partir do último ponto de verificação ou a partir do início do runbook não tem pontos de verificação. O runbook apenas será suspenso pelo sistema quando ocorre uma exceção. Por predefinição, ErrorActionPreference está definido como **continuar**, significado que mantém a executar a tarefa de um erro. Se esta variável de preferência estiver definida como **parar**, em seguida, a tarefa suspende a um erro.  Aplica-se a [runbooks gráfico e o fluxo de trabalho do PowerShell](automation-runbook-types.md) apenas. |
| A suspensão |O sistema está a tentar suspender a tarefa o pedido do utilizador. O runbook tem de atingir o próximo ponto de verificação antes de poder ser suspenso. Se já passado o último ponto de verificação, em seguida, concluir antes de poder ser suspenso.  Aplica-se a [runbooks gráfico e o fluxo de trabalho do PowerShell](automation-runbook-types.md) apenas. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Visualizar o estado de tarefa do portal do Azure
Pode ver um Estado de todas as tarefas de runbook resumido ou explorar detalhes de uma tarefa de runbook específico no portal do Azure ou ao configurar a integração com a sua área de trabalho de análise de registos do Microsoft Operations Management Suite (OMS) para reencaminhar o estado da tarefa de runbook e tarefa fluxos.  Para obter mais informações sobre a integração com a análise de registos do OMS, consulte [reencaminhar estado da tarefa e fluxos de trabalho da automatização para análise de registos (OMS)](automation-manage-send-joblogs-log-analytics.md).  

### <a name="automation-runbook-jobs-summary"></a>Tarefas de runbook de automatização resumidas
À direita da sua conta de automatização selecionada, pode ver um resumo de todas as tarefas de runbook para uma conta de automatização selecionada em **estatísticas de tarefa** mosaico.<br><br> ![Mosaico de estatísticas de tarefa](./media/automation-runbook-execution/automation-account-job-status-summary.png).<br> Este mosaico mostra uma contagem e a representação gráfica do Estado da tarefa para todas as tarefas executadas.  

Ao clicar no mosaico apresenta o **tarefas** painel, que inclui uma lista resumida de todas as tarefas executadas, com estado, execução da tarefa e os tempos de início e de conclusão.<br><br> ![Painel de tarefas de conta de automatização](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)<br><br>  Pode filtrar a lista de tarefas, selecionando **filtre as tarefas** e um runbook específico, o estado da tarefa, ou na lista pendente, o intervalo de data/hora para procurar a partir de filtro.<br><br> ![Estado da tarefa de filtro](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Em alternativa, pode ver os detalhes de resumo da tarefa para um runbook específico ao selecionar esse runbook a partir de **Runbooks** painel na sua conta de automatização e, em seguida, selecione o **tarefas** mosaico.  Isto apresenta o **tarefas** painel, e a partir daí, pode clicar no registo da tarefa para ver os detalhes e saída.<br><br> ![Painel de tarefas de conta de automatização](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)<br> 

### <a name="job-summary"></a>Resumo da Tarefa
Pode ver uma lista de todas as tarefas que foram criadas para um determinado runbook e o respetivo estado mais recente. Pode filtrar esta lista por Estado da tarefa e o intervalo de datas da última alteração efetuada à tarefa. Para ver informações detalhadas e de saída, clique no nome de uma tarefa. A vista detalhada da tarefa inclui os valores para os parâmetros do runbook fornecidos para essa tarefa.

Pode utilizar os seguintes passos para ver as tarefas de um runbook.

1. No portal do Azure, selecione **automatização** e, em seguida, selecione o nome de uma conta de automatização.
2. Do hub, selecione **Runbooks** e, em seguida, no **Runbooks** painel selecionar um runbook a partir da lista.
3. No painel do runbook selecionado, clique em de **tarefas** mosaico.
4. Clique das tarefas na lista e no painel de detalhes da tarefa de runbook, pode ver os detalhes e o resultado.

## <a name="retrieving-job-status-using-windows-powershell"></a>Obter o estado da tarefa com o Windows PowerShell
Pode utilizar o [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) para obter as tarefas criadas para um runbook e os detalhes de uma tarefa específica. Se iniciar um runbook do Windows PowerShell com [início AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), em seguida, devolve a tarefa resultante. Utilize [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)de saída para obter uma tarefa de saída do.

Os seguintes comandos de exemplo obter a última tarefa de um runbook de exemplo e apresentam o estado, os valores fornecidos para os parâmetros do runbook e o resultado da tarefa.

    $job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
    –RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output

## <a name="fair-share"></a>Fração justa
Para partilhar recursos entre todos os runbooks na nuvem, o automatização do Azure irá descarregar temporariamente todas as tarefas depois tem estado em execução para a três horas.  Durante este tempo, as tarefas de [runbooks baseados em PowerShell](automation-runbook-types.md#powershell-runbooks) estão parados e não será reiniciado.  Mostra o estado de tarefa **parado**.  Este tipo de runbook é sempre reiniciado desde o início, uma vez que não suportam a pontos de verificação.  

[Os runbooks do PowerShell fluxo de trabalho baseada em](automation-runbook-types.md#powershell-workflow-runbooks) será possível retomar a partir da sua última [ponto de verificação](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints).  Depois de executar a três horas, a tarefa de runbook será suspenso de serviço e o respetivo estado mostra **em execução, aguardar recursos**.  Quando uma sandbox fica disponível, o runbook será reiniciado automaticamente, pelo serviço de automatização e retoma a partir do último ponto de verificação.  Este é o comportamento normal do fluxo de trabalho do PowerShell para suspender/reinício.  Se o runbook novo exceder a três horas Runtime, o processo é repetido até três vezes.  Após o reinício terceiro, se o runbook ainda não foi concluída em três horas, em seguida, a tarefa de runbook é falhou e mostra o estado da tarefa **falha, aguardar recursos**.  Neste caso, recebe a seguinte exceção com a falha.

*A tarefa não é possível continuar a ser executada porque este foi expulso repetidamente do ponto de verificação mesmo. Certifique-se que o Runbook não efetua operações demoradas sem a persistência de estado.*

Isto serve para proteger o serviço de runbooks em execução indefinidamente sem concluir, dado que não estão a conseguir torná-lo para o ponto de verificação seguinte sem a ser descarregado novamente.

Se o runbook tiver sem pontos de verificação ou a tarefa não chegara o primeiro ponto de verificação antes de a ser descarregado, em seguida, esta reiniciar desde o início.  

Quando criar um runbook, deve certificar-se de que o tempo para executar todas as atividades entre dois pontos de verificação não excede três horas. Poderá ter de adicionar pontos de verificação para o runbook para se certificar de que não atingiu o limite de três horas ou dividir longa executar operações. Por exemplo, o runbook pode executar um reindex numa grande base de dados do SQL Server. Se esta operação única não for concluída dentro do limite de quota justa, em seguida, a tarefa são descarregado e reiniciar a partir do início. Neste caso, deve dividir a operação de reindex em vários passos, tais como reindexing uma tabela de cada vez e, em seguida, insira um ponto de verificação após cada operação para que a tarefa foi retomada após a última operação seja concluída.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre os diferentes métodos que podem ser utilizados para iniciar um runbook na automatização do Azure, consulte o artigo [iniciar um runbook na automatização do Azure](automation-starting-a-runbook.md)

