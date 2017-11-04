---
title: "Ferramentas do Azure do Data Lake: Execução local do U-SQL e depuração local com o Visual Studio Code | Microsoft Docs"
description: "Saiba como utilizar as ferramentas do Azure Data Lake para Visual Studio Code para execução local e de depuração local."
Keywords: "VScode, ferramentas do Azure Data Lake, execução, ficheiros de armazenamento de pré-visualização de depuração Local, depurar Local, Local carregar para o caminho de armazenamento"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: 78a5efb19f73192dcc95103abc70c14a3ce2e29a
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="u-sql-local-run-and-local-debug-for-windows-with-visual-studio-code"></a>Execução local do U-SQL e de depuração local para o Windows com o Visual Studio Code
Neste documento, irá aprender a executar tarefas U-SQL numa máquina de desenvolvimento local para acelerar as fases de codificação antecipados ou para depurar código localmente no Visual Studio Code. Para obter instruções sobre como ferramenta do Azure Data Lake para Visual Studio Code, consulte [utilização do Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md). 


## <a name="set-up-the-u-sql-local-run-environment"></a>Configurar o ambiente de execução local do U-SQL

1. Selecione Ctrl + Shift + P para abrir a paleta de comando e, em seguida, introduza **ADL: Transferir Local dependência executar** para transferir os pacotes.  

   ![Transferir os pacotes de ADL LocalRun dependência](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Localize os pacotes de dependência do caminho de mostrado no **saída** painel e, em seguida, instalar BuildTools e Win10SDK 10240. Eis um caminho de exemplo:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![Localize os pacotes de dependência](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 para instalar **BuildTools**, clique em visualcppbuildtools_full.exe na pasta LocalRunDependency, em seguida, siga as instruções do assistente.   

    ![Instalar BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 para instalar **Win10SDK 10240**, clique em sdksetup.exe na pasta LocalRunDependency/Win10SDK_10.0.10240_2, em seguida, siga as instruções do assistente.  

    ![Instalar Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Configure a variável de ambiente. Definir o **SCOPE_CPP_SDK** variável de ambiente para:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
4. Reinicie o sistema operativo para se certificar de que as definições de variável de ambiente em vigor.  

   ![Certifique-se de que a variável de ambiente de SCOPE_CPP_SDK está instalada](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Inicie o serviço de execução local e submeter a tarefa de U-SQL para uma conta local 
Para o utilizador da primeira vez, utilize **ADL: Transferir Local dependência executar** para transferir os pacotes de execução locais, se não tiver [configurou o ambiente de execução local U-SQL](#set-up-the-u-sql-local-run-environment).

1. Selecione Ctrl + Shift + P para abrir a paleta de comando e, em seguida, introduza **ADL: iniciar o serviço de execução Local**.   
2. Selecione **aceitar** para aceitar os termos de licenciamento de Software Microsoft pela primeira vez. 

   ![Aceite os termos de licenciamento de Software da Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Abre a consola cmd. Para os utilizadores da primeira vez, tem de introduzir **3**e, em seguida, localize o caminho de pasta local para os dados de entrada e saída. Para outras opções, pode utilizar os valores predefinidos. 

   ![Ferramentas do Data Lake para Visual Studio Code cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Selecione Ctrl + Shift + P para abrir a paleta de comandos, introduza **ADL: Submeter tarefa**e, em seguida, selecione **Local** para submeter a tarefa à sua conta local.

   ![Ferramentas do Data Lake para Visual Studio Code selecione local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Depois de submeter a tarefa, pode ver os detalhes de submissão. Para ver os detalhes de submissão, selecione **jobUrl** no **saída** janela. Também pode ver o estado de submissão da tarefa da consola do cmd. Introduza **7** na consola do cmd se quiser saber mais detalhes da tarefa.

   ![Data Lake Tools para execução a saída de local de Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![ferramentas do Data Lake para locais de Visual Studio Code executar cmd Estado](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Iniciar uma depuração local para a tarefa de U-SQL  
Para o utilizador do primeiro tempo:

1. Utilize **ADL: Transferir Local dependência executar** para transferir os pacotes de execução locais, se não tiver [configurou o ambiente de execução local U-SQL](#set-up-the-u-sql-local-run-environment).
2. Instale o .NET Core SDK 2.0 como sugerido na caixa de mensagem, se não instalado.
 
  ![o lembrete instala Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instalação c# para Visual Studio Code, como sugerido na caixa de mensagem se não estiver instalado. Clique em **instalar** para continuar e, em seguida, reinicie VSCode.

    ![Lembrete para instalar o C #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Siga os passos abaixo para efetuar a depuração local:
  
1. Selecione Ctrl + Shift + P para abrir a paleta de comando e, em seguida, introduza **ADL: iniciar o serviço de execução Local**. Abre a consola cmd. Certifique-se de que o **DataRoot** está definido.
2. Defina um ponto de interrupção no seu c# code-behind.
3. Para o editor de scripts, rato e selecione **ADL: depurar Local**.
    
   ![Ferramentas do Data Lake para resultados de local de depuração do Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Passos seguintes
- Para utilizar as ferramentas do Azure Data Lake para Visual Studio Code, consulte [utilização do Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- Para obter informações de introdução no Data Lake Analytics, consulte [Tutorial: introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Para informações sobre as ferramentas do Data Lake para Visual Studio, consulte [Tutorial: desenvolver scripts SQL-U, utilizando ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para informações sobre o desenvolvimento das assemblagens, consulte [assemblagens de desenvolver U-SQL para tarefas do Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).
