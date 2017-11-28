---
title: "Ferramentas do Azure Data Lake: A utilização do Azure Data Lake Tools para Visual Studio Code | Microsoft Docs"
description: 'Saiba como utilizar o Azure Data Lake Tools para Visual Studio Code para criar, testar e executar scripts U-SQL. '
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: jejiang
ms.openlocfilehash: 60307b8b16718fdc947bde7616532fa6a0920cf0
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Utilize as ferramentas do Azure Data Lake para Visual Studio Code

Obter as ferramentas do Azure Data Lake para Visual Studio Code (VS Code) para criar, testar e executar scripts U-SQL. As informações também são abrangidas no vídeo seguinte:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Pré-requisitos

Ferramentas do Azure Data Lake para VSCode suporta o Windows, Linux e MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

Para MacOS e Linux:
- [.NET core SDK 2.0](https://www.microsoft.com/net/download/core). 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Instalar as ferramentas do Data Lake

Depois de instalar os pré-requisitos, pode instalar as ferramentas do Data Lake para o VS Code.

**Para instalar as ferramentas do Data Lake**

1. Abra o Visual Studio Code.
2. Clique em **extensões** no painel esquerdo. Introduza **do Azure Data Lake** na caixa de pesquisa.
3. Clique em **instalar** junto a **ferramentas do Azure Data Lake**. Após alguns segundos, o **instalar** botão será alterado para **recarregar**.
4. Clique em **recarregar** para ativar o **ferramentas do Azure Data Lake** extensão.
5. Clique em **recarregar janela** para confirmar. Pode ver **ferramentas do Azure Data Lake** no painel de extensões.

    ![Ferramentas do Data Lake para Painel de extensões de código do Visual Studio](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Ativar as ferramentas do Azure Data Lake
Criar um novo ficheiro de .usql ou abra um ficheiro .usql existente para ativar a extensão. 

## <a name="open-the-sample-script"></a>Abra o script de exemplo
Abra a paleta de comando (Ctrl + Shift + P) e introduza **ADL: Abra o Script de exemplo**. Abrir outra instância deste exemplo. Também pode editar, configurar e submeter script nesta instância.

## <a name="work-with-u-sql"></a>Trabalhar com o U-SQL

É necessário abrir um ficheiro de U-SQL ou uma pasta para trabalhar com o U-SQL.

**Para abrir uma pasta para o seu projeto U-SQL**

1. A partir do Visual Studio Code, selecione o **ficheiro** e, em seguida, selecione **Abrir pasta**.
2. Especifique uma pasta e, em seguida, selecione **selecionar pasta**.
3. Selecione o **ficheiro** e, em seguida, selecione **novo**. Um ficheiro de Untitled-1 é adicionado ao projeto.
4. Introduza o seguinte código no ficheiro Untitled-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    O script cria um ficheiro de departments.csv com alguns dados incluídos na pasta /output.

5. Guarde o ficheiro como **myUSQL.usql** na pasta aberta. Um ficheiro de configuração xxx_settings.json também é adicionado à pasta.
6. Abra e configurar xxx_settings.json com as seguintes propriedades:

    - : Uma análise do Data Lake conta na sua subscrição do Azure que é necessário para compilar e executar tarefas U-SQL, pelo que tem de configurar a conta de computador antes de o compilar e executar tarefas U-SQL.
    - Base de dados: Uma base de dados na sua conta. A predefinição é **mestre**.
    - Esquema: Um esquema em sua base de dados. A predefinição é **dbo**.
    - Definições opcionais:
        - Prioridade: O intervalo de prioridade é de 1 a 1000 1 como a prioridade mais elevada. O valor predefinido é **1000**.
        - Paralelismo: O intervalo de paralelismo é entre 1 e 150. O valor predefinido é o paralelismo máximo permitido na sua conta do Azure Data Lake Analytics. 
        
        ![Ferramentas do Data Lake para o ficheiro de configuração do Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)
      
        > [!NOTE] 
        > Depois da configuração é guardada, a conta, base de dados e as informações de esquema aparecem na barra de estado no canto inferior esquerdo do ficheiro .usql correspondente.

**Para compilar um script U-SQL**

1. Selecione Ctrl + Shift + P para abrir a paleta de comando. 
2. Introduza **ADL: compilar o Script**. Os resultados de compilação são apresentados no **saída** janela. Pode também com o botão direito um ficheiro de script e, em seguida, selecione **ADL: compilar o Script** para compilar uma tarefa de U-SQL. O resultado da compilação é apresentado no **saída** painel.
 

**Para submeter um script U-SQL**

1. Selecione Ctrl + Shift + P para abrir a paleta de comando. 
2. Introduza **ADL: submeter a tarefa**.  Pode também com o botão direito um ficheiro de script e, em seguida, selecione **ADL: Submeter tarefa**. 

Depois de submeter uma tarefa de U-SQL, os registos de submissão apresentado o **saída** janela no VS Code. Se a submissão for bem sucedida, a tarefa URL parece bem. Pode abrir o URL de tarefa num web browser para controlar o estado da tarefa em tempo real.

Para ativar a saída dos detalhes da tarefa, defina **jobInformationOutputPath** no **vs code para u-sql_settings.json** ficheiro.
 
## <a name="use-python-r-and-csharp-code-behind-file"></a>Utilizar o Python, R e CSharp ficheiro code-behind
Ferramenta de Lake de dados do Azure suporta vários código personalizado, consulte as instruções [desenvolver U-SQL com o Python, R e CSharp para o Azure Data Lake Analytics no VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="use-assemblies"></a>Utilizar assemblagens

Para obter informações sobre o desenvolvimento das assemblagens, consulte [assemblagens de desenvolver U-SQL para tarefas do Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Pode utilizar as ferramentas do Data Lake para registar as assemblagens de código personalizado no catálogo do Data Lake Analytics.

**Para registar uma assemblagem**

Pode registar a assemblagem através de **ADL: registar a assemblagem** ou **ADL: registar a assemblagem através da configuração** comandos.

**Para registar através de ADL: o comando de registar a assemblagem**
1.  Selecione Ctrl + Shift + P para abrir a paleta de comando.
2.  Introduza **ADL: registar a assemblagem**. 
3.  Especifique o caminho da assemblagem local. 
4.  Selecione uma conta de Data Lake Analytics.
5.  Selecione uma base de dados.

Resultados: O portal é aberto num browser e mostra o processo de registo de assemblagem.  

Outra maneira conveniente para acionar o **ADL: registar a assemblagem** comando é o ficheiro. dll no Explorador de ficheiros com o botão direito. 

**Para registar, embora o ADL: registar a assemblagem através do comando de configuração**
1.  Selecione Ctrl + Shift + P para abrir a paleta de comando.
2.  Introduza **ADL: registar a assemblagem através da configuração**. 
3.  Especifique o caminho da assemblagem local. 
4.  O ficheiro JSON é apresentado. Rever e editar as dependências de assemblagem e os parâmetros do recurso, se necessário. As instruções apresentadas no **saída** janela. Para prosseguir para o registo de assemblagem, guarde, por exemplo, o ficheiro JSON (Ctrl + S).

![Ferramentas do Data Lake para Visual Studio Code code-behind](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dependências de assemblagem: autodetects de ferramentas do Data Lake do Azure indica se a DLL tem quaisquer dependências. As dependências são apresentadas no ficheiro de JSON depois de estes forem detetados. 
>- Recursos: Pode carregar os DLL recursos (por exemplo,. txt, PNG e. csv) como parte do registo de assemblagem. 

Outra forma para acionar o **ADL: registar a assemblagem através da configuração** comando é o ficheiro. dll no Explorador de ficheiros com o botão direito. 

O código de U-SQL seguinte demonstra como chamar uma assemblagem. No exemplo, o nome da assemblagem é *testar*.

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```

## <a name="connect-to-azure"></a>Ligar ao Azure

Antes de poder compilar e executar scripts U-SQL no Data Lake Analytics, tem de ligar a sua conta do Azure.

**Para ligar ao Azure**

1.  Selecione Ctrl + Shift + P para abrir a paleta de comando. 
2.  Introduza **ADL: início de sessão**. As informações de início de sessão é apresentado no **saída** painel.

    ![Data Lake Tools para paleta de comandos do Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![ferramentas do Data Lake para obter informações de início de sessão de dispositivo do Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Selecione Ctrl + clique no URL de início de sessão: https://aka.ms/devicelogin para abrir a página Web de início de sessão. Introduza o código **G567LX42V** na caixa de texto e, em seguida, selecione **continuar**.

   ![Ferramentas do Data Lake para início de sessão do Visual Studio Code cole o código](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Siga as instruções para iniciar sessão a partir da página Web. Quando estiver ligado, o nome da sua conta do Azure é apresentado na barra de estado no canto inferior esquerdo do **VS Code** janela. 

    > [!NOTE] 
    > Se a sua conta tiver dois fatores ativadas, recomendamos que utilize autenticação de telefone, em vez de utilizar um PIN.

Para terminar sessão, introduza o comando **ADL: fim de sessão**.

## <a name="list-your-data-lake-analytics-accounts"></a>Lista as contas de Data Lake Analytics

Para testar a ligação, obter uma lista das suas contas do Data Lake Analytics.

**Para listar as contas de Data Lake Analytics na sua subscrição do Azure**

1. Selecione Ctrl + Shift + P para abrir a paleta de comando.
2. Introduza **ADL: lista de contas**. As contas são apresentados no **saída** painel.


## <a name="access-the-data-lake-analytics-catalog"></a>Aceder ao catálogo de Data Lake Analytics

Depois de ligar para o Azure, pode utilizar os seguintes passos para aceder ao catálogo U-SQL.

**Para aceder o metadados de Azure Data Lake Analytics**

1.  Selecione Ctrl + Shift + P e, em seguida, introduza **ADL: lista de tabelas**.
2.  Selecione uma das contas de Data Lake Analytics.
3.  Selecione uma das bases de dados do Data Lake Analytics.
4.  Selecione uma dos esquemas. Pode ver a lista de tabelas.

## <a name="view-data-lake-analytics-jobs"></a>Tarefas do Vista Data Lake Analytics

**Para ver as tarefas de Data Lake Analytics**
1.  Abra a paleta de comando (Ctrl + Shift + P) e selecione **ADL: tarefa mostrar**. 
2.  Selecione uma conta local ou de Data Lake Analytics. 
3.  Aguarde que a lista de tarefas para a conta a aparecer.
4.  Selecione uma tarefa a partir da lista de tarefas, as ferramentas do Data Lake abre os detalhes da tarefa no portal do Azure e apresenta o ficheiro de JobInfo no VS Code.

    ![Tipos de objeto de ferramentas do Data Lake para Visual Studio para o IntelliSense de código](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Integração de armazenamento do Azure Data Lake

Pode utilizar comandos relacionados com o armazenamento do Azure Data Lake para:
 - Procure os recursos de armazenamento do Azure Data Lake. [Listar o caminho de armazenamento](#list-the-storage-path). 
 - Pré-visualize o ficheiro de armazenamento do Azure Data Lake. [Pré-visualize o ficheiro de armazenamento](#preview-the-storage-file). 
 - Carregue o ficheiro diretamente ao armazenamento do Azure Data Lake no VS Code. [Carregar o ficheiro ou pasta](#upload-file-or-folder).
 - Transfira o ficheiro diretamente a partir do armazenamento do Azure Data Lake no VS Code. [Transferir o ficheiro](#download-file).

## <a name="list-the-storage-path"></a>Listar o caminho de armazenamento 

**Para listar o caminho de armazenamento através da paleta do comando**

O editor de scripts com o botão direito e selecione **ADL: caminho de lista**.

Selecione a pasta na lista ou clique em **introduza caminho** ou **procurar a partir da raiz** (utiliza introduza um caminho como um exemplo). -> Selecione o **ADLA conta**. -> Navegue ou introduza o caminho de pasta de armazenamento (por exemplo: / saída /). -> As listas de paleta de comando as informações de caminho com base nas suas entradas.

![Ferramentas do Data Lake para Visual Studio Code lista de resultados de caminho de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Uma forma mais conveniente para listar o caminho relativo é efetuada através do menu de contexto de contexto.

**Para listar o caminho de armazenamento através de contexto**

Faça duplo clique para selecionar a cadeia do caminho **lista caminho** para continuar.

![Ferramentas do Data Lake para Visual Studio Code clique no menu de contexto](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>Pré-visualize o ficheiro de armazenamento

O editor de scripts com o botão direito e selecione **ADL: pré-visualização do ficheiro**.

Selecione o **ADLA conta**. -> Introduza um caminho de ficheiro de armazenamento do Azure (por exemplo, /output/SearchLog.txt). -> Resultado: para abrir o ficheiro no VSCode.

   ![Ferramentas do Data Lake para Visual Studio Code pré-visualizar resultados de ficheiro](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Outra forma de ficheiros de pré-visualização é efetuada através do menu de contexto no caminho completo do ficheiro ou o caminho relativo do ficheiro no editor de scripts. 

## <a name="upload-file-or-folder"></a>Carregar o ficheiro ou pasta

1. O editor de scripts com o botão direito e selecione **carregar o ficheiro** ou **carregar pasta**.

2. Escolha um ficheiro de vários ficheiros, se selecionar carregar ficheiros, ou escolha a pasta de toda, se selecionar carregar pastas, em seguida, clique em **carregar**. -> Escolha a pasta de armazenamento na lista ou clique em **introduza caminho** ou **procurar a partir da raiz** (utiliza introduza um caminho como um exemplo). -> Selecione o **ADLA conta**. -> Navegue ou introduza o caminho de pasta de armazenamento (por exemplo: / saída /). -> clique **escolha a pasta atual** para especificar o destino de carregamento.

   ![Ferramentas do Data Lake para Visual Studio Code carregar o Estado](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Outra forma de carregamento de ficheiros de armazenamento é através do menu de contexto no caminho completo do ficheiro ou o caminho relativo do ficheiro no editor de scripts.

Como o mesmo tempo, pode monitorizar o [Carregar estado](#check-storage-tasks-status).


## <a name="download-file"></a>Transferir o ficheiro 
Pode transferir os ficheiros, introduzindo os comandos **ADL: Transferir ficheiro** ou **ADL: Transferir o ficheiro (avançado)**.

**Para transferir ficheiros apesar de ADL: Transferir o ficheiro (avançado)**
1. O editor de scripts com o botão direito e, em seguida, selecione **transferir ficheiros (avançado)**.
2. O VS Code apresenta um ficheiro JSON. Pode introduzir caminhos de ficheiro e transferi vários ficheiros ao mesmo tempo. As instruções apresentadas no **saída** janela. Para continuar a transferir o ficheiro, guarde, por exemplo, o ficheiro JSON (Ctrl + S).

    ![Ferramentas do Data Lake para Visual Studio Code transferir ficheiros com a configuração](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Resultados: O **saída** janela apresenta o estado de carregamento de ficheiros.

    ![Data Lake Tools para Visual Studio Code transferir vários ficheiros resultados](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Como o mesmo tempo, pode monitorizar o [Transferir estado](#check-storage-tasks-status).

**Para transferir ficheiros apesar do ADL: Transferir o ficheiro**

1. O editor de scripts com o botão direito, selecione **transferir ficheiro**e, em seguida, selecione a pasta de destino a partir de **selecionar pasta** caixa de diálogo.

2. Selecione a pasta na lista ou clique em **introduza caminho** ou **procurar a partir da raiz** (utiliza introduza um caminho como um exemplo). -> Selecione o **ADLA conta**. -> Navegue ou introduza o caminho de pasta de armazenamento (por exemplo: / saída) -> Escolha um ficheiro para transferir.

   ![Estado de transferir as ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Outra forma de transferência de ficheiros de armazenamento é através do menu de contexto no caminho completo do ficheiro ou o caminho relativo do ficheiro no editor de scripts.

Como o mesmo tempo, pode monitorizar o [Transferir estado](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Verificar o estado de tarefas de armazenamento
Apresenta o estado na parte inferior da barra de estado quando concluir a transferência e carregar.
1. Clique a barra de estado abaixo e, em seguida, a transferência e carregar o estado mostram em **saída** painel.

   ![Ferramentas do Data Lake para o estado do Visual Studio Code verificar armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>Integração de VSCode Explorer com o Azure Data Lake
1. Após o início de sessão, irá ver todas as contas do Azure estão listadas no painel da esquerda do **DataLake Explorer**. Expanda uma base de dados, pode ver o **esquemas**, **tabelas**, **assemblagens** e assim sucessivamente, sob o nó.

   ![Explorador do DataLake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

2. Pode executar o comando **registar a assemblagem** clicando no **assemblagens** nós.

    ![Explorador do DataLake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

3. Navegue para **conta de armazenamento**, pode carregar ou transferir ficheiro clicando na pasta ou ficheiro. E também **pré-visualização** um ficheiro, **transferir**, **copiar o caminho relativo**, **caminho completo de cópia** pelo menu de contexto.

   ![Explorador do DataLake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Abrir Explorador de armazenamento ADL no portal
1. Selecione Ctrl + Shift + P para abrir a paleta de comando.
2. Introduza **Abrir Explorador de armazenamento do Azure Web** ou um caminho relativo ou o caminho completo no editor de scripts com o botão direito e, em seguida, selecione **Abrir Explorador de armazenamento do Azure Web**.
3. Selecione uma conta de Data Lake Analytics.

Ferramentas do Data Lake abre-se o caminho de armazenamento do Azure no portal do Azure. Pode localizar o caminho e pré-visualizar o ficheiro web.

## <a name="local-run-and-local-debug-for-windows-users"></a>Execução local e o local de depuração para o Windows utilizadores
Execução local do U-SQL testa os seus dados locais e valida o script localmente, antes do seu código é publicado para o Data Lake Analytics. A funcionalidade de depuração local permite-lhe concluir as seguintes tarefas antes do seu código foi submetido para o Data Lake Analytics: 
- Depurar o c# por detrás do código. 
- Percorra o código. 
- Valide o script localmente.

Para obter instruções sobre como executar local e de depuração local, consulte [execução local do U-SQL e de depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Funcionalidades adicionais

Ferramentas do Data Lake para o VS Code suporta as seguintes funcionalidades:

-   O IntelliSense conclusão automática: sugestões são apresentados no pop-up windows em torno de itens, tais como palavras-chave, os métodos e variáveis. Ícones diferentes representam os diferentes tipos de objetos:

    - Tipo de dados de scala
    - tipo de dados complexo
    - UDTs incorporadas
    - Classes e de coleção de .NET
    - Expressões de c#
    - UDFs incorporadas c#, UDOs e UDAAGs 
    - Funções de U-SQL
    - Função de modos de janela U-SQL
 
    ![Tipos de objeto de ferramentas do Data Lake para Visual Studio para o IntelliSense de código](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   O IntelliSense a conclusão automática no Data Lake Analytics metadados: ferramentas do Data Lake transfere as informações de metadados do Data Lake Analytics localmente. A funcionalidade de IntelliSense preenche automaticamente objetos, incluindo a base de dados, de esquema, tabela, vista, função valorizada por tabela, procedimentos e assemblagens c#, dos metadados do Data Lake Analytics.
 
    ![Ferramentas do Data Lake para Visual Studio Code o IntelliSense metadados](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Marcador de erro IntelliSense: ferramentas do Data Lake underlines os erros de edição para U-SQL e c#. 
-   Destaques de sintaxe: ferramentas do Data Lake utiliza cores diferentes para diferenciar itens, tais como as variáveis, palavras-chave, tipo de dados e funções. 

    ![Ferramentas do Data Lake para Visual Studio Code sintaxe destaques](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Passos seguintes
- [Desenvolver U-SQL com o Python, R e CSharp para o Azure Data Lake Analytics no VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Execução local do U-SQL e de depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desenvolver scripts U-SQL, utilizando ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Desenvolver assemblagens de U-SQL para tarefas do Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)




