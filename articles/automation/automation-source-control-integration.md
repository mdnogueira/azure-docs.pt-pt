---
title: "Origem de integração do controlo na automatização do Azure | Microsoft Docs"
description: "Este artigo descreve a integração de controlo de origem com o GitHub na automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 224d7375-9887-44dd-b137-06ffe396a4b4
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;sngun
ms.openlocfilehash: d8f236a012648062e703fa2c4123d139c0e115ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="source-control-integration-in-azure-automation"></a>Integração de controlo de código fonte da Automatização do Azure
Integração de controlo de origem permite-lhe associar os runbooks na sua conta de automatização para um repositório de controlo de origem do GitHub. Controlo de código fonte permite-lhe colaborar com a sua equipa, controlar as alterações, e facilmente reverter para as versões anteriores dos seus runbooks. Por exemplo, controlo de código fonte permite-lhe sincronizar os ramos diferentes no controlo de origem para os desenvolvimento, teste ou de produção as contas de automatização, facilitando a promover o código que tem sido testado no seu ambiente de desenvolvimento para a automatização de produção conta.

Controlo de código fonte permite-lhe emitir o código da automatização do Azure para controlo de origem ou extraia os runbooks do controlo de origem para a automatização do Azure. Este artigo descreve como configurar o controlo de origem no seu ambiente de automatização do Azure. Vamos começar por configurar a automatização do Azure para aceder ao seu repositório do GitHub e guiá operações diferentes que podem ser feitas através da integração do controlo de origem. 

> [!NOTE]
> Controlo de origem suporta extrair e enviar [runbooks do fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) , bem como [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks). [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks) ainda não são suportadas.<br><br>
> 
> 

Existem dois passos simples necessários para configurar o controlo de origem para a sua conta de automatização e apenas um se já tiver uma conta GitHub. São:

## <a name="step-1--create-a-github-repository"></a>Passo 1 – criar um repositório do GitHub
Se já tiver uma conta GitHub e um repositório de que pretende associar a automatização do Azure, em seguida, iniciar sessão na sua conta existente e iniciar a partir do passo 2 abaixo. Caso contrário, navegue para [GitHub](https://github.com/), inscreva-se numa nova conta e [criar um novo repositório de](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Passo 2 – configurar o controlo de origem na automatização do Azure
1. Na página de conta de automatização no portal do Azure, clique em **segurança controlo de origem.** 
   
    ![Configurar o controlo de origem](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)
2. O **controlo de código fonte** é aberta, onde pode configurar os detalhes da sua conta GitHub a página. Segue-se a lista de parâmetros a configurar:  
   
   | **Parâmetro** | **Descrição** |
   |:--- |:--- |
   | Escolher origem |Selecione a origem. Atualmente, apenas **GitHub** é suportada. |
   | Autorização |Clique em de **autorizar** botão para conceder acesso de automatização do Azure para o seu repositório do GitHub. Se tiver sessão iniciada sua conta GitHub numa janela diferente, em seguida, são utilizadas as credenciais dessa conta. Depois de autorização é efetuada com êxito, a página irá mostrar o seu nome de utilizador do GitHub em **autorização propriedade**. |
   | Escolha o repositório |Selecione um repositório GitHub da lista de repositórios disponíveis. |
   | Selecione o ramo |Selecione um ramo da lista de ramos disponíveis. Apenas o **mestre** ramo é apresentado se ainda não criou quaisquer ramos. |
   | Caminho de pasta do Runbook |O caminho de pasta do runbook Especifica o caminho no repositório de GitHub a partir do qual pretende push ou pull o seu código. Têm de ser introduzido no formato **/nomedapasta/nomedasubpasta**. Apenas os runbooks existentes no caminho da pasta de runbooks serão sincronizados com a sua conta de automatização. Os Runbooks em subpastas do caminho da pasta de runbook será **não** ser sincronizada com êxito. Utilize  **/**  para sincronizar todos os runbooks no repositório. |
3. Por exemplo, se tiver um repositório denominado **PowerShellScripts** que contém uma pasta denominada **RootFolder**, que contém uma pasta denominada **subpasta**. Pode utilizar as seguintes cadeias ao sincronizar a cada nível de pasta:
   
   1. Para sincronizar runbooks **repositório**, é o caminho de pasta do runbook*/*
   2. Para sincronizar runbooks **RootFolder**, caminho de pasta do runbook é */RootFolder*
   3. Para sincronizar runbooks **subpasta**, caminho de pasta do runbook é *RootFolder/subpasta*.
4. Depois de configurar os parâmetros, estes são apresentados os **segurança controlo de origem** página.  
   
    ![Configurar a página de controlo de origem](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. Assim que clicar em **OK**, integração de controlo de origem está agora configurada para a sua conta de automatização e deve ser atualizada com as informações do GitHub. Agora pode clicar nesta parte para ver todos os seu histórico de tarefas de sincronização de controlo de origem.  
   
    ![Valores do repositório](media/automation-source-control-integration/automation_03_RepoValues.png)
6. Depois de configurar o controlo de origem, os seguintes recursos de automatização serão criados na sua conta de automatização:  
   Dois [recursos de variável](automation-variables.md) são criados.  
   
   * A variável **Microsoft.Azure.Automation.SourceControl.Connection** contém os valores de cadeia de ligação, conforme mostrado abaixo.  
     
     | **Parâmetro** | **Valor** |
     |:--- |:--- |
     | Nome |Microsoft.Azure.Automation.SourceControl.Connection |
     | Tipo |Cadeia |
     | Valor |{"Ramo":\<*o nome da sua sucursal*>, "RunbookFolderPath":\<*caminho de pasta do Runbook*>, "ProviderType":\<*tem um valor de 1 para GitHub*>, "Repositório":\<*nome do seu repositório*>, "Nomedeutilizador":\<*nome de utilizador do GitHub*>} |

    * A variável **Microsoft.Azure.Automation.SourceControl.OAuthToken**, contém o valor encriptado seguro da sua OAuthToken.  

    |**Parâmetro**            |**Valor** |
    |:---|:---|
    | Nome  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Tipo | UNKNOWN(Encrypted) |
    | Valor | <*OAuthToken encriptado*> |  

    ![Variáveis](media/automation-source-control-integration/automation_04_Variables.png)  

    * **Controlo de origem de automatização** é adicionado como uma aplicação autorizada à sua conta do GitHub. Para ver a aplicação: sua home page do GitHub, navegue para o **perfil** > **definições** > **aplicações**. Esta aplicação permite que a automatização do Azure para sincronizar o seu repositório do GitHub para uma conta de automatização.  

    ![Aplicação de Git](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Utilizar o controlo de origem na automatização
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Registar um runbook da automatização do Azure ao controlo de origem
Modificação do Runbook no permite-lhe emita as alterações efetuadas a um runbook na automatização do Azure para o repositório de controlo de origem. Seguem-se os passos de verificação num runbook:

1. Da sua conta de automatização, [criar um novo runbook textual](automation-first-runbook-textual.md), ou [editar um runbook existente, textual](automation-edit-textual-runbook.md). Este runbook pode ser um fluxo de trabalho do PowerShell ou um runbook do script do PowerShell.  
2. Depois de editar o runbook, guarde-o e clique em **dar entrada no** do **editar** página.  
   
    ![Botão de Checkin](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Dar entrada da automatização do Azure substitui o código que atualmente existe no controlo de origem. O Git de instrução de linha de comandos equivalente para se registar é **git adicionar + consolidação de git + git push**  

1. Ao clicar em **dar entrada no**, recebem uma mensagem de confirmação, clique em **Sim** para continuar.  
   
    ![Mensagem de Checkin](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. Dar entrada no inicia o runbook de controlo de origem: **sincronização MicrosoftAzureAutomationAccountToGitHubV1**. Este runbook liga-se ao GitHub e pushes alterações da automatização do Azure para o seu repositório. Para ver o marcada no histórico de tarefas, volte atrás para o **integração de controlo de origem** separador e clique para abrir a página de sincronização do repositório. Esta página mostra todas as tarefas de controlo de origem.  Selecione a tarefa que pretende visualizar e clique para ver os detalhes.  
   
    ![Checkin Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Os runbooks do controlo de origem são especiais runbooks de automatização que não é possível ver ou editar. Enquanto que não aparecer na sua lista de runbook, pode ver as tarefas de sincronização que mostra a lista de tarefas.
   > 
   > 
3. O nome do runbook modificado é enviado como um parâmetro de entrada para o marcada no runbook. Pode [ver os detalhes da tarefa](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) expandindo o runbook no **repositório sincronização** página.  
   
    ![Entrada de Checkin](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. Atualize o repositório do GitHub, uma vez concluída a tarefa para ver as alterações.  Deverá haver uma consolidação no seu repositório com uma mensagem de consolidação: **atualizados *nome do Runbook* na automatização do Azure.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Sincronização do controlo de origem a runbooks da automatização do Azure
O botão Sincronizar na página de sincronização do repositório permite-lhe solicitar todos os runbooks no caminho da pasta de runbooks do repositório à sua conta de automatização. Pode ser sincronizadas com o mesmo repositório para mais do que uma conta de automatização. Seguem-se os passos para sincronizar um runbook:

1. A conta de automatização onde configurou o controlo de origem, abra o **sincronização de integração/repositório de controlo de origem** página e clique em **sincronização**.  Recebem uma mensagem de confirmação, clique em **Sim** para continuar.  
   
    ![Botão de sincronização](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. O runbook inicia a sincronização: **sincronização MicrosoftAzureAutomationAccountFromGitHubV1**. Este runbook liga-se ao GitHub e obtém as alterações do seu repositório para automatização do Azure. Deverá ver uma tarefa de novo no **repositório sincronização** página para esta ação. Para ver detalhes sobre a tarefa de sincronização, clique para abrir a página de detalhes da tarefa.  
   
    ![Runbook de sincronização](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > Uma sincronização do controlo de origem substitui a versão de rascunho de runbooks que atualmente existe na sua conta de automatização para **todos os** runbooks que estão atualmente nos controlo de origem. O Git de instrução de linha de comandos equivalente a sincronização é **solicitação de git**


## <a name="troubleshooting-source-control-problems"></a>Resolução de problemas de controlo de origem
Se não existirem erros, com uma verificação no ou tarefa de sincronização, o estado da tarefa deve ser suspensa e pode ver mais detalhes sobre o erro na página da tarefa.  O **todos os registos** parte mostra todos os fluxos de PowerShell associados a essa tarefa. Este procedimento fornece os detalhes necessários para o ajudar a corrigir quaisquer problemas com a verificação em ou sincronização. Mostra também a sequência de ações que ocorreu durante a sincronização ou a verificação num runbook.  

![Imagem de AllLogs](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>Desligar o controlo de código fonte
Para desligar da sua conta do GitHub, abra a página de sincronização de repositório e clique em **desligar**. Depois de desligar o controlo de origem, os runbooks que foram sincronizados anteriormente ainda permanecem na sua conta de automatização, mas não será ativada a página de sincronização do repositório.  

  ![Botão de desligar](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a integração de controlo de origem, consulte os seguintes recursos:  

* [Da automatização do Azure: Integração do controlo de origem na automatização do Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Votos para o seu sistema de controlo de origem favorito](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Da automatização do Azure: Controlo de origem de Runbook utilizando o Visual Studio Team Services a integração](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

