<properties 
    pageTitle="Tutorial: criar um pipeline com a Atividade de Cópia com o Visual Studio | Microsoft Azure" 
    description="Neste tutorial, irá criar um pipeline do Azure Data Factory com uma Atividade de Cópia com o Visual Studio." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/01/2016" 
    ms.author="spelluru"/>


# Tutorial: Criar um pipeline com a Atividade de Cópia com o Visual Studio
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)

Este tutorial mostra-lhe como criar e monitorizar uma fábrica de dados do Azure com o Visual Studio. O pipeline na fábrica de dados utiliza uma Atividade de Cópia para copiar dados do Armazenamento de Blobs do Azure para uma Base de Dados SQL do Azure.

Veja a seguir os passos que deve executar como parte deste tutorial:

1. Criar dois serviços ligados: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. O AzureStorageLinkedService1 liga um armazenamento do Azure e o AzureSqlLinkedService1 liga uma base de dados SQL do Azure à fábrica de dados: **ADFTutorialDataFactoryVS**. Os dados de entrada para o pipeline residem num contentor do blob no armazenamento de blobs do Azure e os dados de saída são armazenados numa tabela na base de dados SQL do Azure. Por conseguinte, adicione estes dois arquivos de dados como serviços ligados à fábrica de dados.
2. Criar duas tabelas de fábrica de dados: **EmpTableFromBlob** e **EmpSQLTable**, que representam os dados de entrada/saída armazenados nos arquivos de dados. Para a EmpTableFromBlob, especifique o contentor de blobs que contém um blob com a origem de dados. Para a EmpSQLTable, especifique a tabela SQL que armazena os dados de saída. Especifique também outras propriedades, tais como a estrutura, a disponibilidade e a política.
3. Criar um pipeline com o nome **ADFTutorialPipeline** no ADFTutorialDataFactoryVS. O pipeline tem uma **Atividade de Cópia** que copia dados de entrada a partir do blob do Azure para a tabela SQL do Azure de saída. A Atividade de Cópia executa o movimento dos dados no Azure Data Factory. A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e escalável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia. 
4. Crie uma fábrica de dados e implemente serviços ligados, tabelas e o pipeline.    

## Pré-requisitos

1. Leia o artigo [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua os passos de **pré-requisitos**. 
2. Tem de ser um **administrador da subscrição do Azure** para conseguir publicar entidades do Data Factory no Azure Data Factory.  
3. Tem de ter o seguinte instalado no computador: 
    - Visual Studio 2013 ou Visual Studio 2015
    - Transfira o Azure SDK para o Visual Studio 2013 ou Visual Studio 2015. Navegue até à [Página de Transferências do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET**.
    - Transfira o plug-in mais recente do Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se estiver a utilizar o Visual Studio 2013, também poderá atualizar o plug-in. Para tal, realize os seguintes passos: no menu, clique em **Ferramentas** -> **Extensões e Atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **Atualizar**. 
 


## Criar projeto do Visual Studio 
1. Inicie o **Visual Studio 2013**. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**. A caixa de diálogo **Novo Projeto** deve aparecer.  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto do Data Factory Vazio**. Se não vir o modelo DataFactory, feche o Visual Studio, instale o Azure SDK para o Visual Studio 2013 e volte a abrir o Visual Studio.  

    ![Caixa de diálogo Novo projeto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Introduza um **nome** para o projeto, a **localização** e um nome para a **solução** e, em seguida, clique em **OK**.

    ![Explorador de Soluções](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png) 

## Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Um arquivo de dados pode ser um Storage do Azure, SQL Database do Azure ou uma base de dados SQL Server no local.

Neste passo, irá criar dois serviços ligados: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. O serviço ligado AzureStorageLinkedService1 liga uma Conta de Armazenamento do Azure e o AzureSqlLinkedService liga uma base de dados SQL do Azure à fábrica de dados: **ADFTutorialDataFactory**. 

### Criar o serviço ligado do Storage do Azure

4. Clique com o botão direito do rato em **Serviços Ligados** no explorador de soluções, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.      
5. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Ligado do Storage do Azure** na lista e clique em **Adicionar**. 

    ![Novo Serviço Ligado](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. Substitua **accountname** e **accountkey** pelo nome da sua conta de armazenamento do Azure e pela respetiva chave. 

    ![Serviço Ligado do Storage do Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Guarde o ficheiro **AzureStorageLinkedService1.json**.

### Criar o serviço ligado SQL do Azure

5. Clique com o botão direito do rato no nó **Serviços Ligados** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**. 
6. Desta vez, selecione o **Serviço Ligado SQL do Azure** e clique em **Adicionar**. 
7. No **ficheiro AzureSqlLinkedService1.json**, substitua **servername**, **databasename**, **username@servername** e **password** pelos nomes do seu servidor, base de dados, conta de utilizador e palavra-passe do SQL do Azure.    
8.  Guarde o ficheiro **AzureSqlLinkedService1.json**. 


## Criar conjuntos de dados
No passo anterior, criou serviços ligados **AzureStorageLinkedService1** e **AzureSqlLinkedService1** para ligar uma Conta de armazenamento do Azure e a SQL Database do Azure a uma fábrica de dados: **ADFTutorialDataFactory**. Neste passo, irá definir duas tabelas de fábrica de dados – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída armazenados nos arquivos de dados mencionados pelo AzureStorageLinkedService1 e AzureSqlLinkedService1, respetivamente. Para EmpTableFromBlob, especifique o contentor de blobs que contém um blob com a origem de dados. Para a EmpSQLTable, especifique a tabela SQL que armazena os dados de saída.

### Criar conjunto de dados de entrada

9. Clique com o botão direito do rato em **Tabelas** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.
10. Na caixa de diálogo **Adicionar Novo Item**, selecione **Blob do Azure** e clique em **Adicionar**.   
10. Substitua o texto JSON com o seguinte texto e guarde o ficheiro **AzureBlobLocation1.json**. 

        {
          "name": "EmpTableFromBlob",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "adftutorial/",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

### Criar conjunto de dados de saída

11. Clique novamente com o botão direito em **Tabelas** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.
12. Na caixa de diálogo **Adicionar Novo Item**, selecione **SQL do Azure** e clique em **Adicionar**. 
13. Substitua o texto JSON com o seguinte JSON e guarde o ficheiro **AzureSqlTableLocation1.json**.

        {
          "name": "EmpSQLTable",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

## Criar pipeline 
Até ao momento, criou serviços ligados de entrada/saída e tabelas. Agora, irá criar um pipeline com uma **Atividade de Cópia** para copiar dados a partir do blob do Azure para a base de dados SQL do Azure. 


1. Clique com o botão direito do rato em **Pipelines** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.  
15. Selecione **Copiar Pipeline de Dados** na caixa de diálogo **Adicionar Novo Item** e clique em **Adicionar**. 
16. Substitua o JSON pelo seguinte JSON e guarde o ficheiro **CopyActivity1.json**.
            
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "EmpTableFromBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "EmpSQLTable"
                  }
                ],
                "typeProperties": {
                  "source": {
                    "type": "BlobSource"
                  },
                  "sink": {
                    "type": "SqlSink",
                    "writeBatchSize": 10000,
                    "writeBatchTimeout": "60:00:00"
                  }
                },
                "Policy": {
                  "concurrency": 1,
                  "executionPriorityOrder": "NewestFirst",
                  "style": "StartOfInterval",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z",
            "isPaused": false
          }
        }

## Publicar/implementar entidades do Data Factory
  
18. Clique com o botão direito do rato no Explorador de Soluções e, em seguida, clique em **Publicar**. 
19. Se vir a caixa de diálogo **Iniciar sessão na sua conta Microsoft**, introduza as credenciais da conta com a subscrição do Azure e clique em **iniciar sessão**.
20. Deverá ver a seguinte caixa de diálogo:

    ![Caixa de diálogo Publicar](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)

21. Na página Configurar fábrica de dados, realize os seguintes passos: 
    1. Selecione a opção **Criar Nova Fábrica de Dados**.
    2. Introduza **VSTutorialFactory** para **Nome**.  
    
        > [AZURE.NOTE]  
        > O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber um erro relativo ao nome da fábrica de dados durante a publicação, altere o nome (por exemplo, seunomeVSTutorialFactory) e tente publicar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
        
    3. Selecione a subscrição correta no campo **Subscrição**. 
    4. Selecione o **grupo de recursos** para criar a fábrica de dados. 
    5. Selecione a **região** da fábrica de dados. 
    6. Clique em **Seguinte** para mudar para a página **Publicar Itens**. 
23. Na página **Publicar Itens**, confirme que todas as entidades dos Data Factories estão selecionadas e clique em **Seguinte** para mudar para a página **Resumo**.     
24. Reveja o resumo e clique em **Seguinte** para iniciar o processo de implementação e ver o **Estado da Implementação**.
25. Na página **Estado da Implementação**, deve ver o estado do processo de implementação. Clique em Concluir após a conclusão da implementação. 

Tenha em atenção os seguintes pontos: 

- Se receber o erro: "**Esta subscrição não está registada para utilizar o espaço de nomes Microsoft.DataFactory**", realize um dos seguintes procedimentos e tente publicar novamente: 

    - No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado. 
    
            Get-AzureRmResourceProvider
    - Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.
-   O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
-   Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure

## Resumo
Neste tutorial, irá criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o Visual Studio para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1.  Criou uma **fábrica de dados** do Azure.
2.  Criou **serviços ligados**:
    1. Um serviço ligado do ** Storagedo Azure** para ligar a sua Conta de armazenamento do Azure que contém dados de entrada.    
    2. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQL do Azure que contém dados de saída. 
3.  Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4.  Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como sink. 


## Utilize o Explorador de Servidores para ver as fábricas de dados

1. No **Visual Studio**, clique em **Ver** no menu e, em seguida, clique em **Explorador de Servidores**.
2. Na janela do Explorador de Servidores, expanda **Azure** e **Data Factory**. Se vir **Iniciar Sessão no Visual Studio**, introduza a **conta** associada à sua subscrição do Azure e clique em **Continuar**. Introduza a **palavra-passe** e clique em **Iniciar sessão**. O Visual Studio tenta obter informações sobre todas as fábricas de dados do Azure na sua subscrição. Verá o estado desta operação na janela **Lista de Tarefas do Data Factory**.
    ![Explorador de Servidores](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Pode clicar com o botão direito numa fábrica de dados e selecione Exportar Data Factory para Novo Projeto para criar um projeto do Visual Studio com base numa fábrica de dados existente.
    ![Exportar a fábrica de dados para um projeto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## Atualizar as ferramentas do Data Factory para Visual Studio
Para atualizar as ferramentas do Azure Data Factory para Visual Studio, realize os seguintes passos:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**. 
2. Selecione **Atualizações** no painel esquerdo e, em seguida, selecione **Galeria do Visual Studio**.
4. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se não vir esta entrada, significa que já tem a versão mais recente das ferramentas. 

Consulte [Monitor datasets and pipeline (Monitorizar os conjuntos de dados e o pipeline)](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) para obter instruções sobre como utilizar o Portal do Azure para monitorizar o pipeline e os conjuntos de dados que criou neste tutorial.

## Consultar também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de Movimento de Dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia que utilizou no tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory.
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. 



<!--HONumber=Sep16_HO3-->


