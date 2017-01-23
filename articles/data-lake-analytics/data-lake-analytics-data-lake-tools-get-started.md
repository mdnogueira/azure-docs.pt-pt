---
title: Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Microsoft Docs
description: 'Saiba como instalar as Ferramentas do Data Lake para Visual Studio, e como desenvolver e testar scripts U-SQL. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/08/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 3c8406efd9d716df56ed3ca10fc596684945c2f2
ms.openlocfilehash: 3281b001aeab42aace951b5bdd5d4ec901de396f


---
# <a name="tutorial-develop-u-sql-scripts-using-data-lake-tools-for-visual-studio"></a>Tutorial: desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como instalar as Ferramentas do Data Lake para Visual Studio e utilizar as Ferramentas do Data Lake para Visual Studio, para escrever e testar scripts U-SQL.

U-SQL é uma linguagem hiper escalável e altamente extensível para preparar, transformar e analisar todos os dados no Data Lake e além deste. Para obter mais informações, veja [Referência U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2015, Visual Studio 2013 Update 4 ou Visual Studio 2012. As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas; a edição Express não é suportada. O Visual Studio 2017 não é atualmente suportado.**
* **SDK do Microsoft Azure para .NET versão 2.7.1 ou superior**.  Instale-lo através do [Instalador de plataforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
* **[Data Lake Tools para Visual Studio](http://aka.ms/adltoolsvs)**.

    Assim que as Ferramentas do Data Lake para Visual Studio estiverem instaladas, verá um nó de "Data Lake Analytics" no Explorador de Servidores, no nó "Azure" (pode abrir o Explorador de Servidores ao premir Ctrl+Alt+S).

* **Conta do Data Lake Analytics e dados simples** As ferramentas do Data Lake não suportam a criação de contas do Data Lake Analytics. Pode criar uma conta com o portal do Azure, o Azure PowerShell, o SDK do .NET ou a CLI do Azure.
Para sua comodidade, um script do PowerShell para criar um serviço de Data Lake Analytics e carregar o ficheiro de origem de dados pode ser encontrado na [Appx-A PowerShell sample for preparing the tutorial (Amostra de Appx-A PowerShell para preparar o tutorial)](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial).

    Opcionalmente, pode avançar para as duas secções seguintes em [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md) para criar a sua conta e carregar dados manualmente:

    1. [Criar uma conta do Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account).
    2. [Carregue SearchLog.tsv para a conta de Armazenamento do Data Lake predefinida](data-lake-analytics-get-started-portal.md#prepare-source-data).

## <a name="connect-to-azure"></a>Ligar ao Azure
**Para ligar ao Data Lake Analytics**

1. Abra o Visual Studio.
2. No menu **Vista**, clique em **Explorador de Servidores** para abrir o Explorador de Servidores. Ou prima **[CTRL]+[ALT]+S**.
3. Clique com o botão direito do rato em **Azure**, clique em "Ligar a Subscrição do Microsoft Azure" e, em seguida, siga as instruções.
4. No **Explorador de Servidores**, expanda **Azure** e, em seguida, expanda **Data Lake Analytics**. Deve ver uma lista das suas contas do Data Lake Analytics, caso existam. Não é possível criar contas do Data Lake Analytics a partir do Visual Studio. Para criar uma conta, veja [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução ao Azure Data Lake Analytics com a Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="upload-source-data-files"></a>Carregar ficheiros de dados de origem
Carregou alguns dados na secção **Pré-requisitos** anteriormente no tutorial.  

Caso pretenda utilizar os seus dados, eis os procedimentos para carregar dados a partir das Ferramentas do Data Lake.

**Para carregar ficheiros para a conta do Azure Data Lake dependente**

1. No **Explorador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda a sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**. Deverá ver a conta de Armazenamento do Data Lake predefinida e as contas de Armazenamento do Data Lake ligadas, e as contas de Armazenamento do Azure ligadas. A conta do Data Lake predefinida tem uma etiqueta "Conta de Armazenamento Predefinida".
2. Clique com o botão direito do rato na conta de Armazenamento do Data Lake predefinida e, em seguida, clique em **Explorador**.  Abrem-se as Ferramentas do Data Lake do painel do Explorador do Visual Studio.  O lado esquerdo mostra uma vista de árvore; a vista de conteúdo está no lado direito.
3. Procure a pasta para onde pretende carregar ficheiros,
4. Clique com o botão direito do rato em qualquer espaço em branco e, em seguida, clique em **Carregar**.

    ![U-SQL do projeto Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Para carregar ficheiros para uma conta de armazenamento de Blobs do Azure ligada**

1. No **Explorador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda a sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**. Deverá ver a conta de Armazenamento do Data Lake predefinida e as contas de Armazenamento do Data Lake ligadas, e as contas de Armazenamento do Azure ligadas.
2. Expanda a conta de armazenamento do Azure.
3. Clique com o botão direito do rato no contentor para onde pretende carregar ficheiros e, em seguida, clique em **Explorador**. Se não tiver um contentor, primeiro terá de criar um através do portal do Azure, do Azure PowerShell ou de outras ferramentas.
4. Procure a pasta para onde pretende carregar ficheiros,
5. Clique com o botão direito do rato em qualquer espaço em branco e, em seguida, clique em **Carregar**.

## <a name="develop-u-sql-scripts"></a>Desenvolver scripts U-SQL
As tarefas de Data Lake Analytics são escritas em linguagem U-SQL. Para saber mais sobre U-SQL, consulte [Introdução à linguagem U SQL](data-lake-analytics-u-sql-get-started.md) e [Referência de linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para criar e submeter uma tarefa do Data Lake Analytics**

1. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**.
2. Selecione o tipo **Projeto U-SQL** .

    ![novo projeto Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Clique em **OK**. O Visual Studio cria uma solução com um ficheiro **Script.usql**.
4. Introduza o seguinte script no **Script.usql**:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        @res =
            SELECT *
            FROM @searchlog;        

        OUTPUT @res   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Este script U-SQL lê o ficheiro de dados de origem, utilizando **Extractors.Tsv()**, e, em seguida, cria um ficheiro csv, utilizando **Outputters.Csv()**.

    Não modifique os dois caminhos, exceto se copiou o ficheiro de origem para uma localização diferente.  O Data Lake Analytics cria a pasta de saída, se não existir.

    É mais simples utilizar caminhos relativos para ficheiros armazenados em contas do Data Lake predefinidas. Também pode utilizar caminhos absolutos.  Por exemplo

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Tem de utilizar caminhos absolutos para aceder a ficheiros em contas de Armazenamento ligadas.  A sintaxe para ficheiros armazenados numa Conta de Armazenamento do Azure ligada é:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

   > [!NOTE]
   > O contentor de Blobs do Azure com blobs públicos ou permissões de acesso a contentores públicos não são atualmente suportados.  
   >
   >

    Tenha em atenção as seguintes funcionalidades:

   * **IntelliSense**

       A conclusão automática de nome e os membros serão mostrados para Conjunto de Linhas, Classes, Bases de Dados, Esquemas e Objetos Definidos pelo Utilizador (UDOs).

       O IntelliSense para entidades de catálogo (Bases de Dados, Esquemas,Tabelas, UDOs, etc.) está relacionado com a sua conta de computação. Pode verificar a conta de computação ativa, a base de dados e o esquema atuais na barra de ferramentas na parte superior e mudá-los com as listas pendentes.
   * **Expandir * colunas**

       Clique no lado direito de *, deve ver um sublinhado azul sob o *. Faça pairar o cursor do rato no sublinhado azul e, em seguida, clique na seta para baixo.
       ![É possível expandir as ferramentas do Visual Studio para o Data Lake.*](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

       Clique em **Expandir Colunas**, a ferramenta irá substituir * com os nomes das colunas.
   * **Formatação Automática**

       Os utilizadores podem alterar o avanço do script U-SQL com base na estrutura do código em Editar -> Avançadas:

     * Formatar Documento (Ctrl+E, D): Formata todo o documento   
     * Formatar Seleção (Ctrl+K, Ctrl+F): Formata a seleção. Se nada estiver selecionado, este atalho formata a linha onde se encontrar o cursor.  

       Todas as regras de formatação são configuráveis em Ferramentas->Opções->Editor de Texto->SIP->Formatação.  
   * **Avanço Inteligente**

       As Ferramentas do Data Lake para Visual Studio conseguem avançar expressões automaticamente enquanto estiver a escrever scripts. Esta funcionalidade está desativada por predefinição, os utilizadores precisam de ativá-la através da verificação U-SQL-> Opções e Definições ->Parâmetros->Ativar Avanço Inteligente.
   * **Ir para Definição e Localizar Todas as Referências**

       Clicar com o botão direito do rato no nome de um Conjunto de Linhas/parâmetro/coluna/UDO, etc., e clicar em Ir para Definição (F12) permite-lhe navegar para a respetiva definição. Ao clicar em Localizar Todas as Referências (Shift+F12), serão mostradas todas as referências.
   * **Inserir Caminho do Azure**

       Em vez de memorizar o caminho do ficheiro do Azure e digitá-lo manualmente quando escrever o script, as Ferramentas do Data Lake para Visual Studio fornecem uma solução fácil: clique com o botão direito do rato no editor, clique em Inserir Caminho do Azure. Navegue para o ficheiro na caixa de diálogo do Browser de Blobs do Azure. Clique em **OK**. o caminho do ficheiro será inserido no seu código.
5. Especifique a conta do Data Lake Analytics, a Base de Dados e o Esquema. Pode selecionar **(local)** para executar o script localmente, para efeitos de teste. Para obter mais informações, veja [Executar U-SQL localmente](#run-u-sql-locally).

    ![Submeter projeto do Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Para obter mais informações, veja [Utilizar catálogo U-SQL](data-lake-analytics-use-u-sql-catalog.md).
6. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Criar Script**. Verifique o resultado no painel Resultados.
7. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Submeter Script**. Opcionalmente, também pode clicar em **Submeter** a partir do painel Script.usql.  Veja a captura de ecrã anterior.  Clique na seta para baixo junto ao botão Submeter para submeter com as opções de avançar:
8. Especifique o **Nome da Tarefa**, verifique a **Conta de Análise**, e, em seguida, clique em **Submeter**. Os resultados da submissão e a ligação da tarefa ficam disponíveis na janela de Resultados das Ferramentas do Data Lake para Visual Studio quando a submissão estiver concluída.

    ![Submeter projeto do Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
9. Tem de clicar no botão Atualizar para ver o estado da tarefa mais recente e atualizar o ecrã. Quando a tarefa for concluída com êxito, irá mostrar **Gráfico da Tarefa**, **Operações de Metadados**, **Histórico de Estado**, **Diagnóstico**:

    ![Gráfico de desempenho da tarefa do Data Lake Analytics no Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * Resumo da Tarefa. Mostra as informações de resumo da tarefa atual, por exemplo: Estado, Progresso, Tempo de Execução, Nome de Tempo de Execução, Submissor, etc.   
   * Detalhes da Tarefa. São fornecidas informações detalhadas sobre esta tarefa, incluindo script, recursos, Vista de Execução de Vértice.
   * Gráfico da Tarefa. São fornecidos quatro gráficos para visualizar as informações da tarefa: Progresso, Dados Lidos, Dados Escritos, Tempo de Execução, Tempo de Execução Média por Nó, Débito de Entrada, Débito de Saída.
   * Operações de Metadados. Mostra todas as operações de metadados.
   * Histórico de Estado.
   * Diagnóstico. As Ferramentas do Data Lake para Visual Studio irão diagnosticar automaticamente a execução da tarefa. Irá receber alertas quando existirem alguns erros ou problemas de desempenho nas respetivas tarefas. Veja a parte Diagnóstico da Tarefa (ligação TBD) para obter mais informações.

**Para verificar o estado da tarefa**

1. No Explorador de Servidores, expanda **Azure**, expanda **Data Lake Analytics**, expanda o nome de conta do Data Lake Analytics
2. Faça duplo clique **Tarefas** para listar as tarefas.
3. Clique numa tarefa para ver o estado.

**Para ver o resultado da tarefa**

1. No **Explorador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda a sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**, faça duplo clique na conta de Armazenamento do Data Lake predefinida e, em seguida, clique em **Explorador**.
2. Faça duplo clique em **resultado** para abrir a pasta
3. Faça duplo clique em **SearchLog-From-adltools.csv**.

### <a name="job-playback"></a>Reprodução de Tarefa
A reprodução de tarefa permite ver o progresso de execução da tarefa e detetar visualmente anomalias de desempenho e congestionamentos. Esta funcionalidade pode ser utilizada antes de a execução da tarefa ser concluída (ou seja, durante o período de tempo em que a tarefa está ativamente em execução), bem como após a conclusão da execução. Executar a reprodução durante a execução da tarefa permitirá ao utilizador reproduzir o progresso até à hora atual.

**Para ver o progresso de execução da tarefa**  

1. Clique em **Carregar Perfil** no canto superior direito. Veja a captura de ecrã anterior.
2. Clique no botão Reproduzir no canto inferior esquerdo para analisar o progresso de execução da tarefa.
3. Durante a reprodução, clique em **Pausa** para pará-la ou arraste diretamente a barra de progresso para posições específicas.

### <a name="heat-map"></a>Mapa Térmico
As Ferramentas do Data Lake para Visual Studio fornecem sobreposições de cor selecionáveis pelo utilizador na vista de tarefa, para indicar o progresso, E/S de dados, tempo de execução, débito E/S de cada fase. Os utilizadores podem assim descobrir potenciais problemas e a distribuição de propriedades da tarefa, de modo direto e intuitivo. Pode escolher uma origem de dados para apresentar, a partir da lista pendente.  

## <a name="run-u-sql-locally"></a>Executar o U-SQL localmente
Ao utilizar a experiência de execução local do U-SQL no Visual Studio, pode:

* Executar scripts U-SQL localmente, juntamente com as Assemblagens C#.
* Depurar assemblagens C# localmente.
* Crie/elimine/veja bases de dados, assemblagens, esquemas e tabelas locais no Explorador de Servidores, tal como o pode fazer para o serviço do Data Lake Analytics.

Verá uma conta *Local* no Visual Studio e o instalador cria uma pasta *DataRoot* localizada em *C:\LocalRunRoot*. A pasta DataRoot será utilizada para:

* Armazenar metadados, incluindo tabelas, DBs, TVFs, etc.
* Para um determinado script: se um caminho relativo for referenciado em caminhos de entrada/saída, iremos procurar o DataRoot (bem como o caminho do script, se for de entrada)
* A pasta DataRoot NÃO será referenciada se estiver a tentar registar uma assemblagem e utilizar um caminho relativo (veja a parte "Utilizar assemblagens ao executar localmente" para obter mais detalhes)

O vídeo seguinte demonstra a funcionalidade de execução local do U-SQL:

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/USQL-LocalRun/player]
>
>

### <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos
* Não é possível criar tabela/DB etc. no Explorador de Servidores para a conta local.
* Quando um caminho relativo é referenciado:

  * Na entrada de script (EXTRACT * FROM “/path/abc”) - o caminho de DataRoot e o caminho do script serão procurados.
  * Na saída do script (OUTPUT TO “path/abc”): o caminho de DataRoot será utilizado como a pasta de saída.
  * No registo de assemblagem (CRREATE ASSEMBLY xyz FROM “/path/abc”):o caminho do script será procurado, mas não o DataRoot.
  * No TVF/View registado ou outras entidades de metadados: o caminho de DataRoot será procurado, mas não o caminho do script.

    Para os scripts executados no serviço do Data Lake, será utilizada a conta de armazenamento predefinida como pasta raiz e a procura será efetuada em conformidade.

### <a name="test-u-sql-scripts-locally"></a>Testar scripts U-SQL localmente
Para obter instruções sobre como desenvolver scripts U-SQL, veja [Desenvolver scripts U-SQL](#develop-and-test-u-sql-scripts). Para criar e executar scripts U-SQL localmente, selecione **(Local)** no cluster na lista pendente do cluster e, em seguida, clique em **Submeter**. Certifique-se de que tem os dados adequados referenciados - faça referência ao caminho absoluto ou coloque os dados na pasta DataRoot.

![Submeter projeto do Visual Studio do U-SQL localmente](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-local-run.png)

Pode também clicar com o botão direito do rato num script e, em seguida, clicar em **Executar Plano Local** no menu de contexto ou premir **CTRL+F5** para acionar a execução local.

### <a name="use-assemblies-in-local-run"></a>Utilizar assemblagens na execução local
Existem duas formas de executar os ficheiros C# personalizados:

* Escreva assemblagens no ficheiro code-behind e as assemblagens serão automaticamente registadas e removidas depois de terminar o script.
* Crie um projeto de assemblagem C# e registe a dll de saída para a conta local através de um script, como abaixo. Tenha em atenção que o caminho é relativo ao script em vez da pasta DataRoot.

![Utilizar assemblagens na execução u-sql local](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-local-run-assembly.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts e assemblagens C# localmente
Pode depurar assemblagens C# sem as submeter e registar no serviço do Azure Data Lake Analytics. Pode definir pontos de interrupção no ficheiro code-behind e num projeto C# referenciado.

**Para depurar código local no ficheiro code-behind**

1. Configurar pontos de interrupção no ficheiro code-behind.
2. Prima **F5** para depurar o script localmente.

O procedimento seguinte só funciona no Visual Studio 2015. No Visual Studio anterior poderá ter de adicionar manualmente os ficheiros pdb.

**Para depurar código local num projeto C# referenciado**

1. Crie um projeto de Assemblagem C# e crie-o para gerar a dll de saída.
2. Registe a dll com uma instrução U-SQL:

    ```
    CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
    ```
    
3. Defina pontos de interrupção no código C#.
4. Prima **F5** para depurar o script com referência à dll C# localmente.  

## <a name="see-also"></a>Veja também
Para iniciar o Data Lake Analytics com ferramentas diferentes, veja:

* [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Introdução ao Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics com o SDK do .NET](data-lake-analytics-get-started-net-sdk.md)
* [Depuração do código c# nas tarefas U-SQL](data-lake-analytics-debug-u-sql-jobs.md)

Para saber mais sobre as Ferramentas do Data Lake para o Visual Studio Code, veja [Utilizar as Ferramentas do Azure Data Lake para o Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Para ver mais tópicos de desenvolvimento:

* [Analisar blogues com o Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Introdução à linguagem U-SQL do Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
* [Desenvolver operadores U-SQL definidos pelo utilizador para tarefas do Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)

## <a name="appx-a-powershell-sample-for-preparing-the-tutorial"></a>Exemplo de Appx-A PowerShell para preparar o tutorial
O seguinte script do PowerShell prepara uma conta do Azure Data Lake Analytics e a origem de dados para si, para que possa avançar para [Desenvolver scripts U-SQL](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts).

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion



<!--HONumber=Dec16_HO2-->


