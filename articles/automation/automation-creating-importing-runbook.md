---
title: "Criar ou importar um runbook na automatização do Azure"
description: "Este artigo descreve como criar um novo runbook na automatização do Azure ou importar um de um ficheiro."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 24414362-b690-4474-8ca7-df18e30fc31d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: ad070333b85b70d911a492a35cc89c8c4a60e5c1
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Criar ou importar um runbook na automatização do Azure
Pode adicionar um runbook da automatização do Azure, [criar uma nova](#creating-a-new-runbook) ou ao importar um runbook existente a partir de um ficheiro ou do [Galeria de Runbooks](automation-runbook-gallery.md). Este artigo fornece informações sobre como criar e importar runbooks a partir de um ficheiro.  Pode obter todos os detalhes em aceder a Comunidade runbooks e módulos no [galleries módulos e Runbooks de automatização do Azure](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Criar um novo runbook
Pode criar um novo runbook na automatização do Azure utilizando um dos portais do Azure ou do Windows PowerShell. Depois do runbook ter sido criado, pode editá-la utilizando as informações no [Learning fluxo de trabalho do PowerShell](automation-powershell-workflow.md) e [a criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Para criar um novo runbook de automatização do Azure com o portal clássico do Azure
Apenas pode trabalhar com [runbooks do fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) no portal do Azure.

1. No portal clássico do Azure, clique em, **novo**, **serviços aplicacionais**, **automatização**, **Runbook**, **criação rápida**.
2. Introduza as informações necessárias e, em seguida, clique em **criar**. O nome do runbook tem de começar com uma letra e pode ter letras, números, carateres de sublinhado e travessões.
3. Se pretender editar o runbook agora, em seguida, clique em **editar Runbook**. Caso contrário, clique em **OK**.
4. O seu runbook novo aparecerá no **Runbooks** separador.

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Para criar um novo runbook de automatização do Azure com o portal do Azure
1. No portal do Azure, abra a sua conta da Automatização.
2. Do Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique em de **adicionar um runbook** botão e, em seguida, **criar um novo runbook**.
4. Escreva um **nome** para o runbook e selecione o [tipo](automation-runbook-types.md). O nome do runbook tem de começar com uma letra e pode ter letras, números, carateres de sublinhado e travessões.
5. Clique em **criar** para criar o runbook e abra o editor.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Para criar um novo runbook de automatização do Azure com o Windows PowerShell
Pode utilizar o [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) cmdlet para criar vazio [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Pode especificar o **nome** parâmetro ao criar um runbook vazio, que pode editar mais tarde ou pode especificar o **caminho** parâmetro para importar um ficheiro de runbook. O **tipo** parâmetro também deve ser incluído para especificar um dos quatro tipos de runbook.

Os comandos de exemplo seguintes mostram como criar um novo runbook vazio.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importar um runbook a partir de um ficheiro para a automatização do Azure
Pode criar um novo runbook na automatização do Azure através da importação de um script do PowerShell ou o fluxo de trabalho do PowerShell (extensão. ps1), um runbook gráfico exportado (. graphrunbook) ou um script de Python 2 (. PY extensão).  Tem de especificar o [tipo de runbook](automation-runbook-types.md) que é criada durante a importação, tendo em conta as seguintes considerações.

* Um ficheiro. graphrunbook só pode ser importado para um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks), e runbooks gráficos só podem ser criados a partir de um ficheiro. graphrunbook.
* Um ficheiro. ps1, que contém um fluxo de trabalho do PowerShell só pode ser importado para um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se o ficheiro contém vários fluxos de trabalho do PowerShell, em seguida, a importação falhará. Tem de guardar cada fluxo de trabalho para o seu próprio ficheiro e importar cada separadamente.
* Um ficheiro. ps1, que não contenha um fluxo de trabalho pode ser importado para o um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se o é importado para um runbook de fluxo de trabalho do PowerShell, em seguida, é convertido para um fluxo de trabalho e os comentários são incluídos no runbook especificando as alterações efetuadas.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Para importar um runbook a partir de um ficheiro com o portal clássico do Azure
Pode utilizar o procedimento seguinte para importar um ficheiro de script para a automatização do Azure.  Tenha em atenção que só pode importar um ficheiro. ps1 para um runbook de fluxo de trabalho do PowerShell com este portal.  Tem de utilizar o portal do Azure para outros tipos.

1. No portal clássico do Azure, selecione **automatização** e, em seguida, selecione uma conta de automatização.
2. Clique em **importar**.
3. Clique em **Procurar ficheiro** e localize o ficheiro de script a importar.
4. Se pretender editar o runbook agora, em seguida, clique em **editar Runbook**. Caso contrário, clique em OK.
5. O runbook novo aparecerá no **Runbooks** separador para a conta de automatização.
6. Tem [publicar o runbook](#publishing-a-runbook) antes de pode executá-lo.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar um runbook a partir de um ficheiro com o portal do Azure
Pode utilizar o procedimento seguinte para importar um ficheiro de script para a automatização do Azure.  

> [!NOTE]
> Tenha em atenção que só pode importar um ficheiro. ps1 para um runbook de fluxo de trabalho do PowerShell através do portal.
> 
> 

1. No portal do Azure, abra a sua conta da Automatização.
2. Do Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique em de **adicionar um runbook** botão e, em seguida, **importação**.
4. Clique em **Runbook ficheiro** para selecionar o ficheiro a importar
5. Se o **nome** campo está ativado, então tem a opção para alterá-la.  O nome do runbook tem de começar com uma letra e pode ter letras, números, carateres de sublinhado e travessões.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas pode alterar o tipo após obter as restrições aplicáveis na conta. 
7. O novo runbook aparece na lista de runbooks da conta de automatização.
8. Tem [publicar o runbook](#publishing-a-runbook) antes de pode executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico ou um runbook gráfico de fluxo de trabalho do PowerShell, tem a opção para converter para o outro tipo, se pretendesse. Não é possível converter para um runbook textual.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar um runbook a partir de um ficheiro de script com o Windows PowerShell
Pode utilizar o [importação AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) cmdlet para importar um ficheiro de script como um rascunho do runbook de fluxo de trabalho do PowerShell. Se o runbook já existir, a importação falha a menos que utilize o *-Force* parâmetro. 

Os comandos de exemplo seguintes mostram como importar um ficheiro de script para um runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publicar um runbook
Quando criar ou importar um runbook novo, tem de o publicar antes de pode executá-lo.  Cada runbook na automatização possui um rascunho e uma versão publicada. Apenas a versão publicada está disponível para ser executado, e apenas a versão de rascunho pode ser editada. A versão publicada não é afetada por quaisquer alterações à versão de rascunho. Quando a versão de rascunho deve ser efetuada disponível, em seguida, publica-o que substitui a versão publicada com a versão de rascunho.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Para publicar um runbook com o portal clássico do Azure
1. Abra o runbook no portal clássico do Azure.
2. Na parte superior do ecrã, clique em **autor**.
3. Na parte inferior do ecrã, clique em **publicar** e, em seguida, **Sim** na mensagem de verificação.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Para publicar um runbook no portal do Azure
1. Abra o runbook no portal do Azure.
2. Clique em de **editar** botão.
3. Clique em de **publicar** botão e, em seguida, **Sim** na mensagem de verificação.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Para publicar um runbook com o Windows PowerShell
Pode utilizar o [publicar AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) cmdlet para publicar um runbook com o Windows PowerShell. Os comandos de exemplo seguintes mostram como publicar um runbook de exemplo.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre como tirar partido do Runbook e Galeria de módulo do PowerShell, consulte [galleries módulos e Runbooks de automatização do Azure](automation-runbook-gallery.md)
* Para saber mais sobre a edição de runbooks do PowerShell e o fluxo de trabalho do PowerShell com um editor de texto, consulte [editar textual runbooks na automatização do Azure](automation-edit-textual-runbook.md)
* Para saber mais sobre a criação de um runbook gráfico, consulte [a criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md)

