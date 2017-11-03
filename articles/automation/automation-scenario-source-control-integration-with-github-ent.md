---
title: "Integração de controlo de origem da automatização do Azure com o GitHub Enterprise | Microsoft Docs"
description: "Descreve os detalhes sobre como configurar a integração com o GitHub Enterprise para o controlo de origem de runbooks de automatização."
services: automation
documentationCenter: 
authors: eslesar
manager: jwhit
editor: 
ms.assetid: e01d817c-7d38-421c-adf5-647a4b526eb4
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: magoedte
ms.openlocfilehash: 2b3ca90382a1c4fb6446799ad539ab8ee603aefc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Cenário de automatização do Azure – integração de controlo de origem de automatização com o GitHub Enterprise

Automatização atualmente suporta a integração de controlo de origem, que permite-lhe associar runbooks na sua conta de automatização para um repositório de controlo de origem do GitHub.  No entanto, os clientes que implementaram [GitHub Enterprise](https://enterprise.github.com/home) para suportar as respetivas práticas de DevOps, também pretender utilizá-la para gerir o ciclo de vida de runbooks que são desenvolvidos para automatizar processos empresariais e operações de gestão de serviço.  

Neste cenário, tem um computador Windows no seu centro de dados configurado como um Runbook Worker híbrido com os módulos do Azure Resource Manager e instalar as ferramentas de Git.  A máquina de trabalho híbrida tem um clone do repositório de Git local.  Quando o runbook é executado na função de trabalho híbrida, o diretório de Git está sincronizado e os conteúdos do ficheiro de runbook são importados para a conta de automatização.

Este artigo descreve como configurar esta configuração no seu ambiente de automatização do Azure. Vamos começar por configurar a automatização com as credenciais de segurança, necessários para suportar este cenário e a implementação de um Runbook Worker híbrido no seu centro de dados para executar runbooks e aceder ao seu repositório do GitHub Enterprise para sincronizar runbooks com a sua conta de automatização de runbooks.  


## <a name="getting-the-scenario"></a>Obter o cenário

Este cenário é composta por dois runbooks do PowerShell que pode importar diretamente a partir de [Galeria de Runbooks](automation-runbook-gallery.md) no portal do Azure ou transferência a partir do [galeria do PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | Descrição| 
--------|------------|
Exportação RunAsCertificateToHybridWorker | Runbook exporta um certificado de RunAs a uma conta de automatização para uma função de trabalho híbrida para que os runbooks na função de trabalho pode autenticar com o Azure para importar runbooks para a conta de automatização.| 
Sincronização LocalGitFolderToAutomationAccount | Runbook sincroniza-se a pasta de Git local na máquina híbrida e, em seguida, importar os ficheiros do runbook (*.ps1) para a conta de automatização.|

### <a name="credentials"></a>Credenciais

Credencial | Descrição|
-----------|------------|
GitHRWCredential | Recurso de credencial criar para conter o nome de utilizador e palavra-passe para um utilizador com permissões para o worker híbrido.|

## <a name="installing-and-configuring-this-scenario"></a>Instalar e configurar este cenário

### <a name="prerequisites"></a>Pré-requisitos

1. O runbook de sincronização LocalGitFolderToAutomationAccount autentica utilizando o [conta Run As do Azure](automation-sec-configure-azure-runas-account.md). 

2. Também é necessária uma área de trabalho do Microsoft Operations Management Suite (OMS) com a solução de automatização do Azure ativada e configurada.  Se não tiver um que seja associada com a conta de automatização utilizada para instalar e configurar este cenário, é criado e configurado para, quando executar o **New-OnPremiseHybridWorker.ps1** script do runbook worker híbrido.        

    > [!NOTE]
    > Atualmente as seguintes regiões só suportam a integração de automatização com o OMS - **Sudeste da Austrália**, **EUA Leste 2**, **Sudeste asiático**, e **Europa Ocidental**. 

3. Um computador que possa servir como um trabalho de Runbook híbrida dedicado que também irá alojar o software de GitHub e manter os ficheiros do runbook (*runbook*. ps1) num diretório de origem no sistema de ficheiros sejam sincronizadas entre GitHub e a sua conta de automatização.

### <a name="import-and-publish-the-runbooks"></a>Importar e publicar os runbooks

Para importar o *exportação RunAsCertificateToHybridWorker* e *sincronização LocalGitFolderToAutomationAccount* runbooks na galeria do Runbook da sua conta de automatização no portal do Azure, siga os procedimentos [importar Runbook da galeria do Runbook](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Publica os runbooks após terem sido importados com êxito na sua conta de automatização.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Implementar e configurar o Runbook Worker híbrido

Se não tiver um Runbook Worker híbrido já implementada no seu centro de dados, deve rever os requisitos e siga os passos de instalação automática utilizando o procedimento no [Azure automatização de Runbook híbridos - automatizar a instalação e configuração](automation-hybrid-runbook-worker.md#automated-deployment).  Assim que instalou com êxito o worker híbrido no computador, execute os seguintes passos para concluir a respectiva configuração para suportar este cenário.

1. Inicie sessão computador que aloja a função de trabalho de Runbook híbrida com uma conta que possua direitos administrativos locais e criar um diretório para armazenar os ficheiros do runbook de Git.  Clone o repositório de Git interno para o diretório.
2. Se ainda não tiver uma conta RunAs criada ou se quiser criar um novo um dedicado para esta finalidade, do portal do Azure navegue para as contas de automatização, selecione a sua conta de automatização e criar um [recurso de credencial](automation-credentials.md) que contém o nome de utilizador e palavra-passe para um utilizador com permissões para o worker híbrido.  
3. Da sua conta de automatização, [editar o runbook](automation-edit-textual-runbook.md)**exportação RunAsCertificateToHybridWorker** e modifique o valor da variável *$Password* com uma palavra-passe segura.  Depois de modificar o valor, clique em **publicar** ter a versão de rascunho do runbook publicado. 
5. Iniciar o runbook **exportação RunAsCertificateToHybridWorker**e, no **iniciar Runbook** painel, sob a opção **definições de execução** selecionar a opção **Worker híbrido** e na lista pendente, selecione o grupo de trabalho híbridas que criou anteriormente para este cenário.  

    Isto exporta um certificado para o worker híbrido, para que os runbooks no trabalho podem autenticar com o Azure utilizando a ligação Run As para gerir recursos do Azure (em especial para este cenário - importar runbooks para a conta de automatização).

4. A partir da sua conta de automatização, selecione o grupo de trabalho híbrida criado anteriormente e [especifique uma conta RunAs](automation-hrw-run-runbooks.md#runas-account) para o grupo de trabalho híbridas e escolha o recurso de credencial apenas ou já ter criado.  Isto garante que o runbook de sincronização pode executar comandos de Git. 
5. Iniciar o runbook **sincronização LocalGitFolderToAutomationAccount**, forneça as seguintes necessárias valores de parâmetro de entrada e no **iniciar Runbook** painel, sob a opção **definições de execução** selecionar a opção **Worker híbrido** e na lista pendente, selecione o grupo de trabalho híbridas que criou anteriormente para este cenário:
    * *ResourceGroup* -o nome do grupo de recursos associados a sua conta de automatização
    * *AutomationAccountName* -o nome da sua conta de automatização
    * *GitPath* -a pasta local ou o ficheiro no Runbook Worker híbrido onde Git configurar extraia alterações mais recentes para

    Isto irá sincronizar a pasta de Git local no computador de trabalho híbridas e, em seguida, importar os ficheiros. ps1 do diretório de origem para a conta de automatização.

    ![Iniciar sincronização LocalGitFolderToAutomationAccount Runbook](media/automation-scenario-source-control-integration-with-github-ent/start-runbook-synclocalgitfoldertoautoacct.png)<br>

7. Ver detalhes de resumo da tarefa do runbook, selecionando-à partir de **Runbooks** painel na sua conta de automatização e, em seguida, selecione o **tarefas** mosaico.  Confirme que foi concluída com êxito, selecionando o **todos os registos** mosaico e rever o fluxo de registo detalhado.  

## <a name="next-steps"></a>Passos seguintes

-  Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
-  Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
