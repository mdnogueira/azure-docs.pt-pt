---
title: 'Tutorial: criar um pipeline com a Atividade de Cópia com o Visual Studio | Microsoft Docs'
description: Neste tutorial, irá criar um pipeline do Azure Data Factory com uma Atividade de Cópia com o Visual Studio.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/01/2016
ms.author: spelluru

---
# Tutorial: Criar um pipeline com a Atividade de Cópia com o Visual Studio
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> 
> 

Este tutorial mostra-lhe como criar e monitorizar uma fábrica de dados do Azure com o Visual Studio. O pipeline na fábrica de dados utiliza uma Atividade de Cópia para copiar dados do Armazenamento de Blobs do Azure para uma Base de Dados SQL do Azure.

Veja a seguir os passos que deve executar como parte deste tutorial:

1. Criar dois serviços ligados: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. 
   
    O AzureStorageLinkedService1 liga um armazenamento do Azure e o AzureSqlLinkedService1 liga uma base de dados SQL do Azure à fábrica de dados: **ADFTutorialDataFactoryVS**. Os dados de entrada para o pipeline residem num contentor do blob no armazenamento de blobs do Azure e os dados de saída são armazenados numa tabela na base de dados SQL do Azure. Por conseguinte, adicione estes dois arquivos de dados como serviços ligados à fábrica de dados.
2. Crie dois conjuntos de dados, **InputDataset** e **OutputDataset**, que representam os dados de entrada/saída que estão armazenados nos arquivos de dados. 
   
    Para InputDataset, especifique o contentor de blobs que contém um blob com a origem de dados. Para OutputDataset, especifique a tabela SQL que armazena os dados de saída. Especifique também outras propriedades, tais como a estrutura, a disponibilidade e a política.
3. Criar um pipeline com o nome **ADFTutorialPipeline** no ADFTutorialDataFactoryVS. 
   
    O pipeline tem uma **Atividade de Cópia** que copia dados de entrada a partir do blob do Azure para a tabela SQL do Azure de saída. A Atividade de Cópia executa o movimento dos dados no Azure Data Factory. A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e escalável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia. 
4. Crie uma fábrica de dados com o nome **VSTutorialFactory**. Implemente a fábrica de dados e todas as entidades do Data Factory (serviços ligados, tabelas e o pipeline).    

## Pré-requisitos
1. Leia o artigo [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua os passos de **pré-requisitos**. 
2. Tem de ser um **administrador da subscrição do Azure** para conseguir publicar entidades do Data Factory no Azure Data Factory.  
3. Tem de ter o seguinte instalado no computador: 
   * Visual Studio 2013 ou Visual Studio 2015
   * Transfira o Azure SDK para o Visual Studio 2013 ou Visual Studio 2015. Navegue até à [Página de Transferências do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET**.
   * Transfira o plug-in mais recente do Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Também pode atualizar o plug-in ao seguir estes passos: no menu, clique em **Ferramentas** -> **Extensões e Atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **Atualizar**.

## Criar projeto do Visual Studio
1. Inicie o **Visual Studio 2013**. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**. A caixa de diálogo **Novo Projeto** deve aparecer.  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto do Data Factory Vazio**. Se não vir o modelo DataFactory, feche o Visual Studio, instale o Azure SDK para o Visual Studio 2013 e volte a abrir o Visual Studio.  
   
    ![Caixa de diálogo Novo projeto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Introduza um **nome** para o projeto, a **localização** e um nome para a **solução** e, em seguida, clique em **OK**.
   
    ![Explorador de Soluções](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png) 

## Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Veja [Arquivos de dados suportados](data-factory-data-movement-activities.md##supported-data-stores-and-formats) relativamente a todas as origens e sinks que a Atividade de Cópia suporta. Veja [Compute linked services (Serviços ligados de computação)](data-factory-compute-linked-services.md) para obter a lista dos serviços de computação que o Data Factory suporta. Neste tutorial, não é utilizado nenhum serviço de computação. 

Neste passo, irá criar dois serviços ligados: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. O serviço ligado AzureStorageLinkedService1 liga uma Conta de Armazenamento do Azure e o AzureSqlLinkedService liga uma base de dados SQL do Azure à fábrica de dados: **ADFTutorialDataFactory**. 

### Criar o serviço ligado do Storage do Azure
1. Clique com o botão direito do rato em **Serviços Ligados** no explorador de soluções, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.      
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Ligado do Storage do Azure** na lista e clique em **Adicionar**. 
   
    ![Novo Serviço Ligado](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Substitua `<accountname>` e `<accountkey>`* pelo nome da sua conta de armazenamento do Azure e pela respetiva chave. 
   
    ![Serviço Ligado do Storage do Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Guarde o ficheiro **AzureStorageLinkedService1.json**.

> Veja [Move data from/to Azure Blob (Mover dados do/para o Blob do Azure)](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre as propriedades de JSON.
> 
> 

### Criar o serviço ligado SQL do Azure
1. Clique com o botão direito do rato no nó **Serviços Ligados** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**. 
2. Desta vez, selecione o **Serviço Ligado SQL do Azure** e clique em **Adicionar**. 
3. No **ficheiro AzureSqlLinkedService1.json**, substitua `<servername>`, `<databasename>`, `<username@servername>` e `<password>` pelos nomes do servidor, da base de dados, da conta de utilizador e a palavra-passe do SQL do Azure.    
4. Guarde o ficheiro **AzureSqlLinkedService1.json**. 

> [!NOTE]
> Veja [Move data from/to Azure SQL Database (Mover dados do/para a Base de Dados SQL do Azure)](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter detalhes sobre as propriedades de JSON.
> 
> 

## Criar conjuntos de dados
No passo anterior, criou serviços ligados **AzureStorageLinkedService1** e **AzureSqlLinkedService1** para ligar uma Conta de armazenamento do Azure e a SQL Database do Azure a uma fábrica de dados: **ADFTutorialDataFactory**. Neste passo, vai definir dois conjuntos de dados, **InputDataset** and **OutputDataset**, que representam os dados de entrada/saída armazenados nos arquivos de dados mencionados por AzureStorageLinkedService1 e AzureSqlLinkedService1, respetivamente. Para InputDataset, especifique o contentor de blobs que contém um blob com a origem de dados. Para OutputDataset, especifique a tabela SQL que armazena os dados de saída.

### Criar conjunto de dados de entrada
Neste passo, vai criar um conjunto de dados com o nome **InputDataset** que aponta para um contentor de blobs no Armazenamento do Azure representado pelo serviço ligado **AzureStorageLinkedService1**. Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados suportado atualmente. 

1. Clique com o botão direito do rato em **Tabelas** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Blob do Azure** e clique em **Adicionar**.   
3. Substitua o texto JSON com o seguinte texto e guarde o ficheiro **AzureBlobLocation1.json**. 
   
       {
         "name": "InputDataset",
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
   
    Tenha em atenção os seguintes pontos: 
   
   * O **type** do conjunto de dados está definido como **AzureBlob**.
   * O **linkedServiceName** está definido como **AzureStorageLinkedService**. Criou este serviço ligado no Passo 2.
   * O **folderPath** está definido para o contentor **adftutorial**. Também pode especificar o nome de um blob na pasta com a propriedade **fileName**. Uma vez que não está a especificar o nome do blob, os dados de todos os blobs no contentor são considerados como dados de entrada.  
   * O **type** do formato está definido como **TextFormat**
   * Existem dois campos no ficheiro de texto – **FirstName** e **LastName** – separados por vírgula (**columnDelimiter**) 
   * A **disponibilidade** está definida como **de hora a hora** (**a frequência** está definida como **hora** e o **intervalo** está definido como **1**). Por conseguinte, o Data Factory procura dados de entrada a cada hora na pasta raiz do contentor de blobs (**adftutorial**) que especificou. 
   
   Se não especificar um **fileName** para um conjunto de dados de **entrada**, todos os ficheiros/blobs da pasta de entrada (**folderPath**) são considerados entradas. Se especificar um fileName no JSON, apenas o ficheiro/blob especificado é considerado uma entrada asn.
   
   Se não especificar um **fileName** para uma **tabela de saída**, os ficheiros gerados no **folderPath** são denominados no seguinte formato: Data.&lt;Guid\&gt;.txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).
   
   Para definir **folderPath** e **fileName** de forma dinâmica com base no tempo **SliceStart**, utilize a propriedade **partitionedBy**. No exemplo seguinte, o folderPath utiliza o Ano, o Mês e o Dia do SliceStart (hora de início do setor a ser processado) e o fileName utiliza a Hora do SliceStart. Por exemplo, se estiver a ser produzido um setor para 2016-09-20T08:00:00, folderName é definido como wikidatagateway/wikisampledataout/2016/09/20 e fileName como 08.csv. 
   
           "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
           "fileName": "{Hour}.csv",
           "partitionedBy": 
           [
               { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
               { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
               { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
               { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 

> [!NOTE]
> Veja [Mover dados do/para o Blob do Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obter detalhes sobre as propriedades de JSON.
> 
> 

### Criar conjunto de dados de saída
Neste passo, vai criar um conjunto de dados de saída com o nome **OutputDataset**. Este conjunto de dados aponta para uma tabela SQL na Base de Dados SQL do Azure representada por **AzureSqlLinkedService1**. 

1. Clique novamente com o botão direito em **Tabelas** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **SQL do Azure** e clique em **Adicionar**. 
3. Substitua o texto JSON com o seguinte JSON e guarde o ficheiro **AzureSqlTableLocation1.json**.
   
       {
         "name": "OutputDataset",
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
   
    Tenha em atenção os seguintes pontos: 
   
   * O **type** do conjunto de dados está definido como **AzureSQLTable**.
   * **linkedServiceName** está definido como **AzureSqlLinkedService** (criou este serviço ligado no Passo 2).
   * O **tablename** está definido como **emp**.
   * Existem três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp da base de dados. O ID é uma coluna de identidade, por isso terá de especificar apenas **FirstName** e **LastName** aqui.
   * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory gera um setor de dados de saída a cada hora na tabela **emp** da base de dados SQL do Azure.

> [!NOTE]
> Veja [Move data from/to Azure SQL Database (Mover dados do/para a Base de Dados SQL do Azure)](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter detalhes sobre as propriedades de JSON.
> 
> 

## Criar pipeline
Até ao momento, criou serviços ligados de entrada/saída e tabelas. Agora, irá criar um pipeline com uma **Atividade de Cópia** para copiar dados a partir do blob do Azure para a base de dados SQL do Azure. 

1. Clique com o botão direito do rato em **Pipelines** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.  
2. Selecione **Copiar Pipeline de Dados** na caixa de diálogo **Adicionar Novo Item** e clique em **Adicionar**. 
3. Substitua o JSON pelo seguinte JSON e guarde o ficheiro **CopyActivity1.json**.
   
       {
         "name": "ADFTutorialPipeline",
         "properties": {
           "description": "Copy data from a blob to Azure SQL table",
           "activities": [
             {
               "name": "CopyFromBlobToSQL",
               "type": "Copy",
               "inputs": [
                 {
                   "name": "InputDataset"
                 }
               ],
               "outputs": [
                 {
                   "name": "OutputDataset"
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
   
   Tenha em atenção os seguintes pontos:
   
   * Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
   * A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**.
   * Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.
   
   Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte. Pode especificar apenas a parte da data e ignorar a parte da hora em data e hora. Por exemplo, "2016-02-03", que é equivalente a "2016-02-03T00:00:00Z"
   
   Tanto o datetime de início como de fim têm de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. 
   
   Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**.
   
   No exemplo anterior existem 24 setores de dados, uma vez que estes são produzidos de hora a hora.

## Publicar/implementar entidades do Data Factory
Neste passo, vai publicar as entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) que criou anteriormente. Também vai especificar o nome da nova fábrica de dados a ser criada para conter estas entidades.  

1. Clique com o botão direito do rato no Explorador de Soluções e, em seguida, clique em **Publicar**. 
2. Se vir a caixa de diálogo **Iniciar sessão na sua conta Microsoft**, introduza as credenciais da conta com a subscrição do Azure e clique em **iniciar sessão**.
3. Deverá ver a seguinte caixa de diálogo:
   
   ![Caixa de diálogo Publicar](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Na página Configurar fábrica de dados, realize os seguintes passos: 
   
   1. Selecione a opção **Criar Nova Fábrica de Dados**.
   2. Introduza **VSTutorialFactory** para **Nome**.  
      
      > [!IMPORTANT]
      > O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber um erro relativo ao nome da fábrica de dados durante a publicação, altere o nome (por exemplo, seunomeVSTutorialFactory) e tente publicar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.     
      > 
      > 
   3. Selecione a sua subscrição do Azure no campo **Subscrição**.
      
      > [!IMPORTANT]
      > Se não vir nenhuma subscrição, verifique se iniciou sessão com uma conta de administrador ou coadministrador da subscrição.  
      > 
      > 
   4. Selecione o **grupo de recursos** para criar a fábrica de dados. 5. Selecione a **região** da fábrica de dados. A lista pendente só mostra as regiões que o serviço Data Factory suporta.
5. Clique em **Seguinte** para mudar para a página **Publicar Itens**.
   
        ![Página Configurar fábrica de dados](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
6. Na página **Publicar Itens**, confirme que todas as entidades dos Data Factories estão selecionadas e clique em **Seguinte** para mudar para a página **Resumo**.
   
   ![Página Publicar itens](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
7. Reveja o resumo e clique em **Seguinte** para iniciar o processo de implementação e ver o **Estado da Implementação**.
   
   ![Página Resumo da publicação](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
8. Na página **Estado da Implementação**, deve ver o estado do processo de implementação. Clique em Concluir após a conclusão da implementação. 
   ![Página de estado da implementação](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png) Tenha em atenção os seguintes pontos: 

* Se receber o erro: "**Esta subscrição não está registada para utilizar o espaço de nomes Microsoft.DataFactory**", realize um dos seguintes procedimentos e tente publicar novamente: 
  
  * No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory. 
    
          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
      Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado. 
    
          Get-AzureRmResourceProvider
  * Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.
* O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.

> [!IMPORTANT]
> Para criar instâncias do Data Factory, tem de ser administrador/coadministrador da subscrição do Azure
> 
> 

## Resumo
Neste tutorial, irá criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o Visual Studio para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1. Criou uma **fábrica de dados** do Azure.
2. Criou **serviços ligados**:
   1. Um serviço ligado do ** Storagedo Azure** para ligar a sua Conta de armazenamento do Azure que contém dados de entrada.    
   2. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQL do Azure que contém dados de saída. 
3. Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4. Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como sink. 

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
3. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se não vir esta entrada, significa que já tem a versão mais recente das ferramentas. 

Consulte [Monitor datasets and pipeline (Monitorizar os conjuntos de dados e o pipeline)](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) para obter instruções sobre como utilizar o Portal do Azure para monitorizar o pipeline e os conjuntos de dados que criou neste tutorial.

## Consultar também
| Tópico | Descrição |
|:--- |:--- |
| [Atividades de Movimento de Dados](data-factory-data-movement-activities.md) |Este artigo fornece informações detalhadas sobre a Atividade de Cópia que utilizou no tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. |

<!--HONumber=Sep16_HO4-->


