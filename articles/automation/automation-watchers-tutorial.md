---
title: "Criar uma tarefa de observador na conta de automatização do Azure | Microsoft Docs"
description: "Saiba como criar uma tarefa de observador na conta de automatização do Azure para ver para novos ficheiros criados numa pasta."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 7cd6bebcaa1ed263b9854f7307cf22fba006748e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Tarefas de observador de automatização do Azure permitem-lhe responder a eventos a acontecer no seu centro de dados local

Neste tutorial, irá aprender a criar uma nova tarefa de observador para:

> [!div class="checklist"]
> * Crie um runbook de observador que procura novos ficheiros num diretório.
> * Crie uma variável de automatização para manter a última vez que um ficheiro foi processado de observador.
> * Crie um runbook de ação que é chamado quando o runbook de observador localiza um novo ficheiro.
> * Crie uma tarefa de observador que seleciona o runbook de observador e o runbook de ação.
> * Acione um observador adicionando um novo ficheiro para um diretório.
> * Verifique os resultados do runbook de ação que mostra informações sobre o novo ficheiro.  

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte.
+ Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Conta de automatização](automation-offering-get-started.md) para reter os runbooks de observador e a ação e a tarefa de observador.
+ A [runbook worker híbrido](automation-hybrid-runbook-worker.md) onde é executada a tarefa de observador.

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>Criar um runbook de observador que procura novos ficheiros
1.  Abra a conta de automatização e clique na página de Runbooks.
2.  Clique no botão "Procurar Galeria".
![Lista de Runbook a partir da IU](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  Pesquise "NewFile veja" e importar o runbook para a conta de automatização.
![Publicar o runbook a partir da IU](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  Clique em "Editar" para ver a origem de Runbook e clique no botão "Publicar".

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>Criar uma variável de automatização para manter a última vez que um ficheiro foi processado de observador
1.  Abra a página de variáveis em recursos PARTILHADOS e clique em "Adicionar variável" ![lista as variáveis a partir da IU](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  Introduza "NewFileTimestamp veja" para o nome
3.  Selecione o tipo como valor DateTime e, em seguida, clique no botão "Criar".
![Crie a variável de marca de água da IU](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>Criar um runbook de ação que é chamado quando o runbook de observador localiza um novo ficheiro
1.  Clique na página de Runbooks sob a categoria "AUTOMATIZAÇÃO de processos".
2.  Clique no botão "Procurar Galeria".
3.  Pesquise "Processo NewFile" e importar o runbook para a conta de automatização.
4.  Clique em "Editar" para ver a origem de Runbook e clique no botão "Publicar".
![Observador do processo de IU](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>Criar uma tarefa de observador que seleciona o runbook de observador e o runbook de ação
1.  Abra a página de tarefas do observador e clique no botão "Adicionar uma tarefa de observador".
![Lista de observador a partir da IU](media/automation-watchers-tutorial/WatchersList.png)
2.  Introduza "Ver novos ficheiros" como o nome.
3.  Selecione "Configurar observador" e selecione o runbook "NewFile veja".
![Configurar o observador da IU](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  Introduza os seguintes valores para parâmetros:
    *   FOLDERPATH. Uma pasta na função de trabalho híbrida onde obterem criados novos ficheiros
    *   EXTENSÃO. Deixe em branco para processar todas as extensões de ficheiro.
    *   RECURSE. Deixe a predefinição.
    *   DEFINIÇÕES DE EXECUÇÃO. Escolha o worker híbrido.
5.  Clique em OK e, em seguida, selecione para regressar à página de observador.
6.  Selecione "Configurar action" e selecione o runbook "NewFile de processo".
![Configure a ação de observador a partir da IU](media/automation-watchers-tutorial/ConfigureAction.png)
7.  Introduza os seguintes valores para parâmetros:
    *   EVENTDATA. Deixe em branco. Dados seja transmitidos a partir do runbook de observador.
    *   Definições de execução. Deixe como a Azure como este runbook é executado no serviço de automatização.
8.  Clique em OK e, em seguida, selecione para regressar à página de observador.
9.  Clique em OK para criar a tarefa de observador.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>Acionar um observador adicionando um novo ficheiro para um diretório
1.  Remoto para o worker híbrido
2.  Adicione um novo ficheiro de texto para a pasta que está a ser monitorizada pela tarefa de observador.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>Verifique os resultados do runbook de ação que mostra informações sobre o novo ficheiro
1.  Clique na tarefa de observador para "Veja novos ficheiros"
2.  Clique em fluxos"observador de vista" que o observador encontrado o novo ficheiro e iniciar o runbook de ação.
3.  Clique em "ver observador ação tarefas" para ver a tarefa de runbook de ação.
![Tarefas de ação de observador da IU](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>Passos seguintes:

Para obter mais informações, consulte [o meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).








