---
title: "Definições de Runbooks | Microsoft Docs"
description: "Descreve as definições de configuração para um runbook na automatização do Azure e como alterá-los utilizando o Portal de gestão do Azure e o Windows PowerShell."
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: a726f20c-a952-48b8-88ee-36d76aa3ac61
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: bwren
ms.openlocfilehash: 534ea7e3f2f8e5640db4d351c2bb3245f29b6eec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="runbook-settings"></a>Definições de runbook
Cada runbook na automatização do Azure tem várias definições que ajudam a ser identificados e alterar o respetivo comportamento de registo. Cada uma destas definições é descrita abaixo seguida pelos procedimentos sobre como modificá-las.

## <a name="settings"></a>Definições
### <a name="name-and-description"></a>Nome e descrição
Não é possível alterar o nome de um runbook depois de terem sido criadas. A descrição é opcional e pode ter até 512 carateres.

### <a name="tags"></a>Etiquetas
As etiquetas permitem atribuir palavras e expressões diferentes para ajudar a identificar um runbook. Por exemplo, quando submeter um runbook para o [galeria do PowerShell](https://www.powershellgallery.com/), especifique etiquetas específicas para identificar as categorias o runbook deve estar listado no. Pode especificar várias etiquetas para um runbook, separando-as com vírgulas.

### <a name="logging"></a>Registo
Por predefinição, os registos verboso e progresso não são escritos no histórico da tarefa. Pode alterar as definições para um determinado runbook para efetuar estes registos. Para obter mais informações sobre estes registos, consulte [Runbook resultados e mensagens](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Alterar definições de runbooks

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Alterar definições de runbooks com o portal do Azure
Pode alterar as definições para um runbook no portal do Azure a partir de **definições** painel do runbook.

1. No portal do Azure, selecione **automatização** e, em seguida, em seguida, clique no nome de uma conta de automatização.
2. Selecione o **Runbooks** separador.
3. Clique no nome de um runbook e o utilizador é direcionado para o painel de definições para o runbook. Aqui pode especificar ou modificar etiquetas, a descrição de runbook, configurar definições de rastreio e de registo e as ferramentas de suporte para o ajudar a resolver problemas de acesso.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Alterar definições de runbooks com o Windows PowerShell
Pode utilizar o [conjunto AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) cmdlet para alterar as definições para um runbook. Se pretender especificar várias etiquetas, pode optar por fornecer uma matriz ou uma cadeia única com valores de delimitado por vírgulas para o parâmetro de etiquetas. Pode obter as etiquetas atuais com o [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx).

Os comandos de exemplo seguintes mostram como definir as propriedades de um runbook. Este exemplo adiciona três etiquetas para as etiquetas existentes e especifica que registos verbosos devem ser registados.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Passos seguintes
* Para saber como criar e obter mensagens de erro e de saída a partir de runbooks, consulte [Runbook resultados e mensagens](automation-runbook-output-and-messages.md) 
* Para compreender como adicionar um runbook que já foi desenvolvido pela Comunidade ou outra origem ou criar o seus próprios Consulte runbook [criar ou importar um Runbook](automation-creating-importing-runbook.md) 

