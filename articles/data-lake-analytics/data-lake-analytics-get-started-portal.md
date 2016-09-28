<properties 
   pageTitle="Introdução à Análise do Azure Data Lake com o Portal do Azure Portal | Azure" 
   description="Saiba como utilizar o Portal do Azure para criar uma conta de Data Lake Analytics, criar uma tarefa de Data Lake Analytics utilizando o U-SQL e submeter a tarefa. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# Tutorial: introdução à Análise do Azure Data Lake utilizando o Portal do Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar o Portal do Azure para criar contas de Análise do Azure Data Lake, definir tarefas de Data Lake Analytics no [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter tarefas para contas de Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

Neste tutorial, irá desenvolver uma tarefa que lê um ficheiro de valores separados por tabulações (TSV) e converte-o num ficheiro de valores separados por vírgulas (CSV). Para seguir o mesmo tutorial, utilizando outras ferramentas suportadas, clique nos separadores na parte superior desta secção. Assim que a sua primeira tarefa for bem sucedida, pode começar a escrever transformações de dados mais complexas com o U-SQL.

##Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte:

- **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

##Criar conta de Data Lake Analytics

Tem de ter uma conta do Data Lake Analytics antes de poder executar quaisquer tarefas.

Cada conta de Data Lake Analytics tem uma dependência de conta de [Arquivo Azure Data Lake]().  Esta conta é referida como a conta predefinida do Data Lake Store.  Pode criar a conta de Data Lake Store previamente ou quando criar a conta de Data Lake Analytics. Neste tutorial, irá criar a conta de Data Lake Store com a conta de Data Lake Analytics.

**Para criar uma conta de Data Lake Analytics**

1. Inicie sessão no novo [Portal Clássico do Azure](https://portal.azure.com).
2. Clique em **Novo**, clique em **Dados + Análise** e, em seguida, clique em **Data Lake Analytics**.
6. Escreva ou selecione o seguinte:

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **Nome**: atribua um nome à conta da Análise.
    - **Data Lake Store**: cada conta do Data Lake Analytics tem uma conta do Data Lake Store dependente. A conta do Data Lake Analytics e a conta do Data Lake Store dependente têm de estar localizadas no centro de dados do Azure. Siga as instruções para criar uma nova conta do Data Lake Store ou selecione uma conta existente.
    - **Subscrição**: selecione a subscrição do Azure utilizada para a conta da Análise.
    - **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo. O Azure Resource Manager (ARM) permite trabalhar com os recursos na sua aplicação como um grupo. Para obter mais informações, consulte o artigo [Descrição geral do Azure Resource Manager](resource-group-overview.md). 
    - **Localização**. Selecione um datacenter do Azure para a conta de Data Lake Analytics. 
7. Selecione **Afixar ao StartBoard**. Esta ação é necessária para seguir este tutorial.
8. Clique em **Criar**. Esta ação leva-o para o StartBoard do portal. Um novo mosaico é adicionado ao StartBoard com a etiqueta que indica "A implementar a Análise do Azure Data Lake". A criação de uma conta de Data Lake Analytics demora alguns instantes. Quando a conta estiver criada, o portal abre a conta num novo painel no portal.

    ![Painel de portal da Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)


Depois de uma conta de Data Lake Analytics ser criada, pode adicionar mais contas de Data Lake Store e contas de Armazenamento do Azure. Para obter instruções, consulte [Gerir origens de dados de contas de Data Lake Analytics](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

##Preparar os dados de origem

Neste tutorial, irá processar alguns registos de pesquisa.  O registo de pesquisa pode ser armazenado no Data Lake Store ou no Armazenamento de Blobs do Azure. 

O Portal do Azure fornece uma interface de utilizador para copiar alguns ficheiros de dados de exemplo para a conta do Data Lake predefinida, que inclui um ficheiro de registo de pesquisa.

**Para copiar ficheiros de dados de exemplo**

1. No Portal do Azure, clique em **Microsoft Azure** no canto superior esquerdo.
2. Clique no mosaico com o nome da sua conta de Data Lake Analytics.  A sua conta foi afixada aqui quando foi criada.
Se a conta não estiver afixada aqui, consulte o artigo [Abrir uma conta de Data Lake Analytics a partir do portal](data-lake-analytics-manage-use-portal.md#access-adla-account) para abrir a conta.
3. Expanda o painel **Princípios Básicos** e, em seguida, clique em **Explorar tarefas de exemplo**. Esta ação abre outro painel com o nome **Tarefas de Exemplo**.
4. Clique em **Copiar Dados de Exemplo** e, em seguida, clique em **OK** para confirmar.
5. Clique em **Notificação**, que é um ícone em forma de sino. Deverá ver um registo que indica **Atualização de dados de exemplo concluída**. Clique em qualquer lugar fora do painel de notificação para fechá-lo.
7. No painel de conta de Data Lake Analytics, clique em **Explorador de Dados** na parte superior. 

    ![Botão do explorador de dados da Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    São abertos dois painéis. Um é o **Explorador de Dados** e o outro é a conta predefinida do Data Lake Store.
8. No painel de conta predefinida do Data Lake Store, clique em **Exemplos** para expandir a pasta e clique em **Dados** para expandir a pasta. Deverá ver os seguintes ficheiros e pastas:

    - AmbulanceData/
    - AdsLog.tsv
    - SearchLog.tsv
    - version.txt
    - WebLog.log
    
    Neste tutorial, irá utilizar SearchLog.tsv.

Na prática, irá programar as suas aplicações para escreverem dados numa conta de armazenamento ligada ou para carregarem dados. Para carregar ficheiros, consulte [Carregar dados para o Data Lake Store](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) ou [Carregar dados para o Armazenamento de Blobs](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

##Criar e submeter tarefas de Data Lake Analytics

Depois de ter preparado os dados de origem, pode começar a desenvolver um script U-SQL.  

**Para submeter a tarefa**

1. A partir do painel de conta de Data Lake Analytics no portal, clique em **Nova Tarefa**. 

    ![Botão de nova tarefa de Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Se não vir o painel, consulte [Abrir uma conta de Data Lake Analytics a partir do portal](data-lake-analytics-manage-use-portal.md#access-adla-account).
4. Introduza o **Nome da Tarefa** e o seguinte script U-SQL:

    ![criar tarefas U-SQL de Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

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
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Este script U-SQL lê o ficheiro de dados de origem, utilizando **Extractors.Tsv()**, e, em seguida, cria um ficheiro csv, utilizando **Outputters.Csv()**. 
    
    Não modifique os dois caminhos, exceto se copiar o ficheiro de origem para uma localização diferente.  A Data Lake Analytics cria a pasta de saída, se não existir.  Neste caso, estamos a utilizar caminhos relativos simples.  
    
    É mais simples utilizar caminhos relativos para ficheiros armazenados em contas do Data Lake predefinidas. Também pode utilizar caminhos absolutos.  Por exemplo 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
      

    Para saber mais sobre o U-SQL, consulte [Introdução à linguagem U-SQL de Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md) e [Referência de linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
     
5. Clique em **Submeter Tarefa** na parte superior. É aberto um novo painel de Detalhes da Tarefa. Na barra de título, é apresentado o estado da tarefa.   
6. Aguarde até que o estado da tarefa seja alterado para **Com êxito**. Quando a tarefa estiver concluída, o portal abre os detalhes da tarefa num novo painel:

    ![Detalhes da tarefa de Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-completed.png)

    Na captura de ecrã anterior, pode ver que a tarefa demorou aproximadamente 1,5 minutos a concluir, desde que foi submetida até ter terminado.
    
    Caso a tarefa tenha falhado, consulte o artigo [Monitorizar e resolver problemas de tarefas de Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorials.md).

7. Na parte inferior do painel **Detalhes da Tarefa**, clique no nome da tarefa em **SearchLog-from-Data-Lake.csv**. Pode pré-visualizar, transferir, mudar o nome e eliminar o ficheiro de saída.

    ![Propriedades do ficheiro de saída da tarefa de Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)
8. Clique em **Pré-visualizar** para ver o ficheiro de saída.

    ![Pré-visualização do ficheiro de saída da tarefa de Análise do Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-output-preview.png)

##Consulte também

- Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicações U-SQL, consulte [Desenvolver scripts SQL-U, utilizando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, consulte [Gerir a Análise do Azure Data Lake através do Portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para uma descrição geral da Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).
- Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
- Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)



<!--HONumber=Sep16_HO3-->


