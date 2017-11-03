---
title: "Os runbooks subordinados na automatização do Azure | Microsoft Docs"
description: "Descreve os diferentes métodos para iniciar um runbook na automatização do Azure a partir de outro runbook e partilhar informações entre eles."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 919887b9-43e2-4c16-883c-f81807fe37db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 617e18f5435c7eacb7751ccca6ac2f3814745f04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="child-runbooks-in-azure-automation"></a>Runbooks subordinados na automatização do Azure
É uma boa prática na automatização do Azure para escrever runbooks modulares, reutilizáveis com uma função discreta que pode ser utilizada por outros runbooks. Um runbook principal chamará, frequentemente, um ou mais runbooks subordinados para executar a funcionalidade necessária. Existem duas formas de chamar runbooks subordinados e cada uma delas apresenta diferenças distintas que deverá compreender para que possa determinar qual é a mais adequada aos seus diferentes cenários.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar um runbook subordinado com a execução inline
Para invocar um runbook inline a partir de outro runbook, utilize o nome do runbook e forneça valores para os respetivos parâmetros, exatamente da mesma forma que utilizaria uma atividade ou um cmdlet.  Todos os runbooks na mesma conta de automatização estão disponíveis para todos os outros sejam utilizados desta forma. O runbook principal espera que o runbook subordinado seja concluído antes de mover para a linha seguinte e qualquer resultado é devolvido diretamente ao principal.

Quando invoca um runbook inline, é executado na mesma tarefa que o runbook principal. Não haverá nenhuma indicação no histórico da tarefa do runbook subordinado que a executou. Quaisquer exceções e qualquer fluxo de saída do runbook subordinado serão associadas ao principal. Isto resulta em menos tarefas e facilita-las para controlar e resolver problemas, uma vez que eventuais exceções geradas pelo runbook subordinado e qualquer uma das suas saídas de fluxo são associadas a tarefa principal.

Quando um runbook for publicado, todos os runbooks subordinados que aquele invoque já tem de ser publicados. Isto acontece porque a automatização do Azure cria uma associação a todos os runbooks subordinados quando um runbook é compilado. Se não estão, o runbook principal irá aparecer publicar corretamente, mas gerará uma exceção quando é iniciada. Se isto acontecer, pode voltar a publicar o runbook principal para referenciar adequadamente os runbooks subordinados. Não é necessário voltar a publicar o runbook principal se qualquer um dos runbooks subordinados forem alterados, porque a associação será já foram criada.

Os parâmetros de um runbook subordinado invocado inline podem ser qualquer tipo de dados, incluindo objetos complexos e não existe nenhum [serialização JSON](automation-starting-a-runbook.md#runbook-parameters) que estejam quando iniciar o runbook utilizando o Portal de gestão do Azure ou com o Cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Tipos de runbooks
Que tipos podem chamar entre si:

* A [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) pode chamar cada outro inline (ambos são PowerShell com base em).
* A [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e runbooks do fluxo de trabalho do PowerShell gráfico pode chamar cada outro inline (ambos são fluxo de trabalho do PowerShell com base)
* Os tipos de PowerShell os tipos de fluxo de trabalho do PowerShell não é possível chamar entre si inline e tem de utilizar AzureRmAutomationRunbook de início.

Quando publicar a fim de ordem:

* A ordem de publicação de runbooks só é importante para runbooks do fluxo de trabalho do PowerShell e o gráfico fluxo de trabalho do PowerShell.

Quando chamar um runbook subordinado gráfico ou fluxo de trabalho do PowerShell com a execução inline, basta utilizar o nome do runbook.  Quando chamar um runbook subordinado do PowerShell, tem de precedido respetivo nome com *.\\*  para especificar que o script está localizado no diretório local. 

### <a name="example"></a>Exemplo
O exemplo seguinte invoca um runbook subordinado de teste que aceita três parâmetros, um objeto complexo, um número inteiro e um booleano. O resultado do runbook subordinado é atribuído a uma variável.  Neste caso, o runbook subordinado é um runbook de fluxo de trabalho do PowerShell

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Segue-se mesmo exemplo utilizando um runbook do PowerShell do menor.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true


## <a name="starting-a-child-runbook-using-cmdlet"></a>Iniciar um runbook subordinado com o cmdlet
Pode utilizar o [início AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) cmdlet para iniciar um runbook, conforme descrito em [para iniciar um runbook com o Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Existem dois modos de utilização para que este cmdlet.  Um modo, o cmdlet devolve o id da tarefa, assim que a tarefa subordinada é criada para o runbook subordinado.  No outro modo, que permitem ao especificar o **-aguarde** parâmetro, o cmdlet irá aguardar até o subordinado tarefa estiver concluída e devolve o resultado do runbook subordinado.

A tarefa de um runbook subordinado iniciado com um cmdlet será executada à parte da tarefa do runbook principal. Isto resulta em mais tarefas do que invocar o runbook inline e torna-os mais difíceis de controlar. O principal pode iniciar vários runbooks subordinados no modo assíncrono, sem aguardar por cada concluir. Para esse mesmo tipo de execução paralela de invocar os runbooks subordinados inline, o runbook principal têm de utilizar o [palavra-chave paralela](automation-powershell-workflow.md#parallel-processing).

São fornecidos parâmetros para um runbook subordinado iniciado com um cmdlet como uma tabela hash, conforme descrito em [parâmetros do Runbook](automation-starting-a-runbook.md#runbook-parameters). Apenas os tipos de dados simples podem ser utilizados. Se o runbook tem um parâmetro com um tipo de dados complexas, em seguida, tem de ser chamado inline.

### <a name="example"></a>Exemplo
O exemplo seguinte inicia um runbook subordinado com parâmetros e, em seguida, aguarda pela respetiva conclusão utilizando o AzureRmAutomationRunbook Start-espera o parâmetro. Depois de concluído, o resultado é recolhido do runbook subordinado.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparação de métodos para chamar um runbook subordinado
A tabela seguinte resume as diferenças entre os dois métodos para chamar um runbook a partir de outro runbook.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Tarefa |Runbook subordinado é executado na mesma tarefa que o elemento principal. |É criada uma tarefa separada para o runbook subordinado. |
| Execução |O runbook principal aguarda que o runbook subordinado seja concluído antes de continuar. |O runbook principal continua imediatamente após o início do runbook subordinado *ou* runbook principal aguarda o conclusão do trabalho subordinado. |
| Saída |O runbook principal pode obter resultados diretamente do runbook subordinado. |O runbook principal tem de obter o resultado da tarefa do runbook subordinado *ou* runbook principal pode obter resultados diretamente do runbook subordinado. |
| Parâmetros |Os valores para os parâmetros do runbook subordinado são especificados em separado e podem utilizar qualquer tipo de dados. |Os valores para os parâmetros do runbook subordinado tem de ser combinados numa única tabela hash e só podem incluir simples, matriz e de serialização de JSON que tira partido dos tipos de dados de objeto. |
| Conta de Automatização |O runbook principal só pode utilizar o runbook subordinado na mesma conta de automatização. |O runbook principal pode utilizar o runbook subordinado a partir de qualquer conta de automatização da mesma subscrição do Azure e até mesmo uma subscrição diferente se tiver uma ligação ao mesmo. |
| Publicação |O runbook subordinado tem de ser publicado antes de o runbook principal. |Runbook subordinado tem de ser publicado em qualquer altura antes do runbook principal ser iniciado. |

## <a name="next-steps"></a>Passos seguintes
* [Iniciar um runbook na automatização do Azure](automation-starting-a-runbook.md)
* [Resultados de Runbook e mensagens na automatização do Azure](automation-runbook-output-and-messages.md)

