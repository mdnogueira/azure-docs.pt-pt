---
title: "Tipos de Runbook da automatização do Azure | Microsoft Docs"
description: "Descreve os diferentes tipos de runbooks que pode utilizar na automatização do Azure e as considerações que deve ter em consideração ao determinar qual o tipo a utilizar. "
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 9265c975-4281-4819-a84f-d86641277f36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/01/2017
ms.author: bwren
ms.openlocfilehash: 2acf45187894aa3bfcaa4df639becf18605d50a5
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook da automatização do Azure
A automatização do Azure suporta vários tipos de runbooks que por breves instantes são descritos na seguinte tabela.  As seções abaixo fornecem informações adicionais sobre cada tipo, incluindo considerações sobre quando utilizar cada uma.

| Tipo | Descrição |
|:--- |:--- |
| [Gráficos](#graphical-runbooks) |Com base no Windows PowerShell e criado e editado completamente no editor gráfico no portal do Azure. |
| [Fluxo de trabalho do PowerShell gráfico](#graphical-runbooks) |Com base no fluxo de trabalho do Windows PowerShell e criar e editá-lo completamente no editor gráfico no portal do Azure. |
| [PowerShell](#powershell-runbooks) |Runbook de texto com base no script do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks) |Runbook de texto com base no fluxo de trabalho do Windows PowerShell. |
| [python](#python-runbooks) |Runbook de texto com base no Python. |
| [Bash](#bash-runbooks) |Runbook de texto com base no Bash. |

## <a name="graphical-runbooks"></a>Runbooks gráficos
[Gráfico](automation-runbook-types.md#graphical-runbooks) e runbooks do fluxo de trabalho do PowerShell gráfico são criados e editados com o editor gráfico no portal do Azure.  Pode exportá-las para um ficheiro e, em seguida, importe-as para outra conta de automatização, mas não é possível criar ou editá-los com outra ferramenta.  Runbooks gráficos gerar código do PowerShell, mas não é possível ver ou modificar o código diretamente. Não é possível converter um dos runbooks gráficos a [formatos de texto](automation-runbook-types.md), nem um runbook de texto pode ser convertido para o formato do gráfico. Runbooks gráficos pode ser convertidos para runbooks do fluxo de trabalho do PowerShell gráfico durante a importação e vice-versa.

### <a name="advantages"></a>Vantagens
* Visual insert-ligação-configurar o modelo criação  
* Focar-se na forma como os dados fluem através do processo  
* Visualmente representar processos de gestão  
* Incluir outros runbooks como runbooks subordinados para criar fluxos de trabalho de nível elevados  
* Encoraja programação modular  


### <a name="limitations"></a>Limitações
* Não é possível editar o runbook fora do portal do Azure.
* Pode necessitar de uma atividade de código que contém o código do PowerShell para efetuar a lógica complexa.
* Não é possível ver ou editar diretamente o código do PowerShell que é criado pelo fluxo de trabalho gráfico. Tenha em atenção que pode ver o código que cria no quaisquer atividades de código.

## <a name="powershell-runbooks"></a>Runbooks do PowerShell
Os runbooks do PowerShell são baseados no Windows PowerShell.  Editar diretamente o código do runbook com o editor de texto no portal do Azure.  Também pode utilizar qualquer editor de texto offline e [importar o runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) na automatização do Azure.

### <a name="advantages"></a>Vantagens
* Implementa toda a lógica complexa com o código do PowerShell sem os complexidades adicionais do fluxo de trabalho do PowerShell. 
* Runbook inicia-se mais rapidamente do que os runbooks do fluxo de trabalho do PowerShell, uma vez que não precisa de ser compilada antes de executar.

### <a name="limitations"></a>Limitações
* Tem de estar familiarizado com scripts do PowerShell.
* Não é possível utilizar [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para efetuar várias ações em paralelo.
* Não é possível utilizar [pontos de verificação](automation-powershell-workflow.md#checkpoints) retomar runbook em caso de erro.
* Os runbooks do fluxo de trabalho do PowerShell e runbooks gráficos só pode ser incluídos como runbooks subordinados utilizando o cmdlet Start-AzureAutomationRunbook que cria uma tarefa de novo.

### <a name="known-issues"></a>Problemas conhecidos
Seguem-se atuais problemas conhecidos com runbooks do PowerShell.

* Os runbooks do PowerShell não é possível não é possível obter um não encriptada [recurso de variável](automation-variables.md) com um valor nulo.
* Os runbooks do PowerShell não é possível obter um [recurso de variável](automation-variables.md) com  *~*  no nome.
* Get-Process num ciclo num PowerShell runbook pode falhar após cerca de 80 iterações. 
* Um runbook de PowerShell poderá falhar se o mesmo tenta a escrever uma grande quantidade de dados no fluxo de saída de uma só vez.   Normalmente, pode contornar este problema, exportar apenas as informações que necessárias ao trabalhar com objetos grandes.  Por exemplo, em vez de exportar algo semelhante ao seguinte *Get-Process*, o utilizador pode apresentar apenas os campos obrigatórios com *Get-Process | Selecione ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbooks do fluxo de trabalho do PowerShell
Os runbooks do fluxo de trabalho do PowerShell são texto runbooks com base nos [o fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md).  Editar diretamente o código do runbook com o editor de texto no portal do Azure.  Também pode utilizar qualquer editor de texto offline e [importar o runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) na automatização do Azure.

### <a name="advantages"></a>Vantagens
* Implementa toda a lógica complexa com o código de fluxo de trabalho do PowerShell.
* Utilize [pontos de verificação](automation-powershell-workflow.md#checkpoints) retomar runbook em caso de erro.
* Utilize [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para efetuar várias ações em paralelo.
* Pode incluir outros runbooks gráficos e runbooks do fluxo de trabalho do PowerShell como runbooks subordinados para criar fluxos de trabalho de nível elevados.

### <a name="limitations"></a>Limitações
* Autor deve estar familiarizado com o fluxo de trabalho do PowerShell.
* Runbook tem de lidar com a complexidade adicional do fluxo de trabalho do PowerShell como [anular a serialização de objetos](automation-powershell-workflow.md#code-changes).
* Runbook demora mais para iniciar os runbooks do PowerShell, uma vez que tem de ser compilada antes de executar.
* Os runbooks do PowerShell só podem ser incluídos como runbooks subordinados utilizando o cmdlet Start-AzureAutomationRunbook que cria uma tarefa de novo.

## <a name="python-runbooks"></a>Runbooks do Python
Os runbooks do Python compilar em Python 2.  Pode editar diretamente o código do runbook com o editor de texto no portal do Azure, ou pode utilizar qualquer editor de texto offline e [importar o runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) na automatização do Azure.

### <a name="advantages"></a>Vantagens
* Utilize a biblioteca padrão robusta do Python.

### <a name="limitations"></a>Limitações
* Tem de estar familiarizado com o processamento de scripts do Python.
* Apenas Python 2 é suportado neste momento, que significa que funções específicas do Python 3 irão falhar.

### <a name="known-issues"></a>Problemas conhecidos
Seguem-se atuais problemas conhecidos com runbooks do Python.

* Para poder utilizar as bibliotecas de terceiros, o runbook tem de ser executado [Runbook Worker híbrido Windows](https://docs.microsoft.com/en-us/azure/automation/automation-windows-hrw-install) ou [Runbook Worker híbrido Linux](https://docs.microsoft.com/en-us/azure/automation/automation-linux-hrw-install) com bibliotecas já instaladas no computador antes do o runbook é iniciado.

## <a name="considerations"></a>Considerações
Deve ter em conta as seguintes considerações adicionais ao determinar qual o tipo a utilizar para um determinado runbook.

* Não é possível converter os runbooks do gráfico para o tipo de texto ou vice-versa.
* Existem limitações através de runbooks de diferentes tipos de como um runbook subordinado.  Consulte [runbooks subordinados na automatização do Azure](automation-child-runbooks.md) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a criação de um runbook gráfico, consulte [a criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md)
* Para compreender as diferenças entre o PowerShell e o PowerShell fluxos de trabalho para runbooks, consulte [aprendizagem do Windows PowerShell Workflow](automation-powershell-workflow.md)
* Para obter mais informações sobre como criar ou importar um Runbook, consulte [criar ou importar um Runbook](automation-creating-importing-runbook.md)

