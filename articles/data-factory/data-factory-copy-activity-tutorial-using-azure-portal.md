---
title: "Tutorial: Criar um pipeline com a Atividade de Cópia com o Portal do Azure | Microsoft Docs"
description: "Neste tutorial, irá criar um pipeline do Azure Data Factory com uma Atividade de Cópia com o Editor do Data Factory no portal do Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/14/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 4b29fd1c188c76a7c65c4dcff02dc9efdf3ebaee
ms.openlocfilehash: 299a55865c1c91e664d67095de76708f444d30b9


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-portal"></a>Tutorial: Criar um pipeline com a Atividade de Cópia com o Portal do Azure
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Este tutorial mostra-lhe como criar e monitorizar uma fábrica de dados do Azure com o Portal do Azure. O pipeline na fábrica de dados utiliza uma Atividade de Cópia para copiar dados do Armazenamento de Blobs do Azure para uma Base de Dados SQL do Azure.

> [!NOTE]
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Não transforma dados de entrada para produzir dados de saída. Para ver um tutorial sobre como transformar dados através do Azure Data Factory, consulte [Tutorial: Build a pipeline to transform data using Hadoop cluster (Tutorial: Criar um pipeline para transformar dados com o cluster do Hadoop)](data-factory-build-your-first-pipeline.md).
> 
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 

Veja a seguir os passos que deve executar como parte deste tutorial:

| Passo | Descrição |
| --- | --- |
| [Criar um Azure Data Factory](#create-data-factory) |Neste passo, irá criar uma fábrica de dados do Azure com o nome **ADFTutorialDataFactory**. |
| [Criar serviços ligados](#create-linked-services) |Neste passo, irá criar dois serviços ligados: **AzureStorageLinkedService** e **AzureSqlLinkedService**. <br/><br/>O AzureStorageLinkedService liga o armazenamento do Azure e o AzureSqlLinkedService liga a base de dados SQL do Azure a ADFTutorialDataFactory. Os dados de entrada para o pipeline residem num contentor de blobs no armazenamento de blobs do Azure, e os dados de saída são armazenados numa tabela na base de dados SQL do Azure. Por conseguinte, adicione estes dois arquivos de dados como serviços ligados à fábrica de dados. |
| [Criar conjuntos de dados de entrada e saída](#create-datasets) |No passo anterior, criou serviços ligados referentes aos arquivos de dados que contêm dados de entrada/saída. Neste passo, vai definir dois conjuntos de dados -- **InputDataset** e **OutputDataset** -- que representam os dados de entrada/saída que estão armazenados nos arquivos de dados. <br/><br/>Para InputDataset, especifique o contentor de blobs que contém um blob com os dados de origem e, para OutputDataset, especifique a tabela SQL que armazena os dados de saída. Especifique também outras propriedades, tais como a estrutura, a disponibilidade e a política. |
| [Criar um pipeline](#create-pipeline) |Neste passo, irá criar um pipeline com o nome **ADFTutorialPipeline** no ADFTutorialDataFactory. <br/><br/>Adiciona uma **Atividade de Cópia** ao pipeline que copia os dados de entrada a partir do blob do Azure para a tabela SQL do Azure de saída. A Atividade de Cópia executa o movimento de dados no Azure Data Factory. Utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e escalável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia. |
| [Monitorizar o pipeline](#monitor-pipeline) |Neste passo, irá monitorizar setores de tabelas de entrada e de saída com o portal do Azure. |

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir este tutorial, conclua os pré-requisitos listados na [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, irá utilizar o Portal do Azure para criar uma fábrica de dados do Azure com o nome **ADFTutorialDataFactory**.

1. Depois de iniciar sessão no [portal do Azure](https://portal.azure.com/), clique em **Nova**, selecione **Informações + Análises** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. No painel **Nova fábrica de dados**:
   
   1. Introduza **ADFTutorialDataFactory** como **nome**. 
      
         ![Painel Nova fábrica de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory) e tente criá-la novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Nome do Data Factory não disponível](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Selecione a sua **subscrição** do Azure.
   3. No Grupo de Recursos, siga um destes passos:
      
      - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
      - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
          Alguns dos passos deste tutorial pressupõe que utiliza o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
   4. Selecione a **localização** da fábrica de dados. A lista pendente só mostra as regiões que o serviço Data Factory suporta.
   5. Selecione **Afixar ao StartBoard**.     
   6. Clique em **Criar**.
      
      > [!IMPORTANT]
      > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
      > 
      > O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.                
      > 
      > 
3. Para ver as mensagens de estado/notificação, clique no ícone de sino na barra de ferramentas. 
   
   ![Mensagens de notificação](./media/data-factory-copy-activity-tutorial-using-azure-portal/Notifications.png) 
4. Depois de concluída a criação, vai ver o painel **Data Factory**, conforme mostrado na imagem.
   
   ![Home page da fábrica de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Veja [Arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) relativamente a todas as origens e sinks que a Atividade de Cópia suporta. Veja [Compute linked services (Serviços ligados de computação)](data-factory-compute-linked-services.md) para obter a lista dos serviços de computação que o Data Factory suporta. Neste tutorial, não é utilizado nenhum serviço de computação. 

Neste passo, irá criar dois serviços ligados: **AzureStorageLinkedService** e **AzureSqlLinkedService**. O serviço ligado AzureStorageLinkedService liga uma Conta de Armazenamento do Azure e o AzureSqlLinkedService liga uma base de dados SQL do Azure a **ADFTutorialDataFactory**. Mais tarde neste tutorial, irá criar um pipeline que copia dados de um contentor de blobs no AzureStorageLinkedService para uma tabela SQL no AzureSqlLinkedService.

### <a name="create-a-linked-service-for-the-azure-storage-account"></a>Criar um serviço ligado para a conta de armazenamento do Azure
1. No painel **Data Factory**, clique no mosaico **Criar e implementar** para iniciar o **Editor** da fábrica de dados.
   
   ![Mosaico Criar e Implementar](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. No **Editor**, clique no botão **Novo arquivo de dados** na barra de ferramentas e selecione ** armazenamento do Azure** no menu pendente. Deverá ver o modelo JSON para criar um serviço ligado de armazenamento do Azure no painel à direita. 
   
    ![Botão Arquivar dados novos do Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Substitua `<accountname>` e `<accountkey>` pelo nome da conta e pelos valores da chave de conta da sua conta de armazenamento do Azure. 
   
    ![JSON do Blob Storage do Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Clique em **Implementar**, na barra de ferramentas. Deverá ver agora o **AzureStorageLinkedService** implementado na vista de árvore. 
   
    ![Implementar o Blob Storage do Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

> [!NOTE]
> Veja [Move data from/to Azure Blob (Mover dados do/para o Blob do Azure)](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre as propriedades de JSON.
> 
> 

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Criar um serviço ligado para a SQL Database do Azure
1. No **Editor do Data Factory**, clique no botão **Novo arquivo de dados**, na barra de ferramentas, e selecione **Base de Dados SQL do Azure** no menu pendente. Deverá ver o modelo JSON para criar o serviço ligado SQL do Azure no painel à direita.
2. Substitua `<servername>`, `<databasename>`, `<username>@<servername>` e `<password>` pelos nomes do servidor, da base de dados, da conta de utilizador e a palavra-passe do SQL do Azure. 
3. Clique em **Implementar**, na barra de ferramentas, para criar e implementar o **AzureSqlLinkedService**.
4. Confirme que consegue ver o **AzureSqlLinkedService** na vista de árvore. 

> [!NOTE]
> Veja [Move data from/to Azure SQL Database (Mover dados do/para a Base de Dados SQL do Azure)](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter detalhes sobre as propriedades de JSON.
> 
> 

## <a name="create-datasets"></a>Criar conjuntos de dados
No passo anterior, criou serviços ligados **AzureStorageLinkedService** e **AzureSqlLinkedService** para ligar uma Conta de armazenamento do Azure e a SQL Database do Azure a uma fábrica de dados: **ADFTutorialDataFactory**. Neste passo, vai definir dois conjuntos de dados, **InputDataset** e **OutputDataset**, que representam os dados de entrada/saída que são armazenados nos arquivos de dados mencionados por AzureStorageLinkedService e AzureSqlLinkedService, respetivamente. Para InputDataset, especifique o contentor de blobs que contém um blob com os dados de origem e, para OutputDataset, especifique a tabela SQL que armazena os dados de saída. 

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Neste passo, vai criar um conjunto de dados com o nome **InputDataset** que aponta para um contentor de blobs no Armazenamento do Azure representado pelo serviço ligado **AzureStorageLinkedService**.

1. No **Editor** do Data Factory, clique em **... Mais**, clique em **Novo conjunto de dados** e clique em **Armazenamento de Blobs do Azure**, no menu pendente. 
   
    ![Menu Novo conjunto de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Substitua JSON no painel à direita com o fragmento JSON seguinte: 
   
    ```JSON
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
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
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
    ```   
    Tenha em atenção os seguintes pontos: 
   
   * O **type** do conjunto de dados está definido como **AzureBlob**.
   * O **linkedServiceName** está definido como **AzureStorageLinkedService**. Criou este serviço ligado no Passo 2.
   * O **folderPath** está definido para o contentor **adftutorial**. Também pode especificar o nome de um blob na pasta com a propriedade **fileName**. Uma vez que não está a especificar o nome do blob, os dados de todos os blobs no contentor são considerados como dados de entrada.  
   * O **type** do formato está definido como **TextFormat**
   * Existem dois campos no ficheiro de texto – **FirstName** e **LastName** – separados por vírgula (**columnDelimiter**)    
   * A **disponibilidade** está definida como **de hora a hora** (**a frequência** está definida como **hora** e o **intervalo** está definido como **1**). Por conseguinte, o Data Factory procura dados de entrada a cada hora na pasta raiz do contentor de blobs (**adftutorial**) que especificou. 
     
     Se não especificar um **fileName** para um conjunto de dados de **entrada**, todos os ficheiros/blobs da pasta de entrada (**folderPath**) são considerados entradas. Se especificar um fileName no JSON, apenas o ficheiro/blob especificado é considerado uma entrada asn.
     
     Se não especificar um **fileName** para uma **tabela de saída**, os ficheiros gerados no **folderPath** recebem um nome com o seguinte formato: Data.&lt;Guid&gt;.txt (example: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).
     
     Para definir **folderPath** e **fileName** de forma dinâmica com base no tempo **SliceStart**, utilize a propriedade **partitionedBy**. No exemplo seguinte, o folderPath utiliza o Ano, o Mês e o Dia do SliceStart (hora de início do setor a ser processado) e o fileName utiliza a Hora do SliceStart. Por exemplo, se estiver a ser produzido um setor para 2016-09-20T08:00:00, folderName é definido como wikidatagateway/wikisampledataout/2016/09/20 e fileName como 08.csv. 

    ```JSON     
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
       { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
       { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
       { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
       { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],
    ```
3. Clique em **Implementar**, na barra de ferramentas, para criar e implementar o conjunto de dados **InputDataset**. Confirme que consegue ver **InputDataset** na vista de árvore.

> [!NOTE]
> Veja [Mover dados do/para o Blob do Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obter detalhes sobre as propriedades de JSON.
> 
> 

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Nesta parte do passo, vai criar um conjunto de dados de saída com o nome **OutputDataset**. Este conjunto de dados aponta para uma tabela SQL na Base de Dados SQL do Azure, representada por **AzureSqlLinkedService**. 

1. No **Editor** do Data Factory, clique em **... Mais**, clique em **Novo conjunto de dados** e clique em **SQL Azure**, no menu pendente. 
2. Substitua JSON no painel à direita com o fragmento JSON seguinte:

    ```JSON   
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
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```       
    Tenha em atenção os seguintes pontos: 
   
   * O **type** do conjunto de dados está definido como **AzureSQLTable**.
   * **linkedServiceName** está definido como **AzureSqlLinkedService** (criou este serviço ligado no Passo 2).
   * O **tablename** está definido como **emp**.
   * Existem três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp da base de dados. O ID é uma coluna de identidade, por isso terá de especificar apenas **FirstName** e **LastName** aqui.
   * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory gera um setor de dados de saída a cada hora na tabela **emp** da base de dados SQL do Azure.
3. Clique em **Implementar**, na barra de ferramentas, para criar e implementar o conjunto de dados **OutputDataset**. Confirme que consegue ver **OutputDataset** na vista de árvore. 

> [!NOTE]
> Veja [Move data from/to Azure SQL Database (Mover dados do/para a Base de Dados SQL do Azure)](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter detalhes sobre as propriedades de JSON.
> 
> 

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, vai criar um pipeline com uma **Atividade de Cópia** que utiliza **InputDataset** como entrada e **OutputDataset** como saída.

1. No **Editor** do Data Factory, clique em **... Mais** e clique em **Novo pipeline**. Alternativamente, pode clicar com o botão direito em **Pipelines** na vista de árvore e clicar em **Novo pipeline**.
2. Substitua JSON no painel à direita com o fragmento JSON seguinte: 

    ```JSON   
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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    } 
    ```   
    
    Tenha em atenção os seguintes pontos:
   
   * Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
   * A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**.
   * Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.
     
     Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte. Pode especificar apenas a parte da data e ignorar a parte da hora em data e hora. Por exemplo, "2016-02-03", que é equivalente a "2016-02-03T00:00:00Z"
     
     Tanto o datetime de início como de fim têm de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. 
     
     Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**.
     
     No exemplo anterior existem 24 setores de dados, uma vez que estes são produzidos de hora a hora.
3. Clique em **Implementar** na barra de ferramentas para criar e implementar o **ADFTutorialPipeline**. Verifique se vê o pipeline na vista de árvore. 
4. Agora, feche o painel **Editor** clicando em **X**. Clique em **X** novamente para ver a home page do **Data Factory** relativa a **ADFTutorialDataFactory**.

**Parabéns!** Criou um Azure Data Factory, serviços ligados, tabelas e um pipeline com êxito e agendou o pipeline.   

### <a name="view-the-data-factory-in-a-diagram-view"></a>Ver a fábrica de dados numa Vista de Diagrama
1. No painel **Data Factory**, clique em **Diagrama**.
   
    ![Painel Data Factory – Mosaico do Diagrama](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Deverá ver um diagrama semelhante à seguinte imagem: 
   
    ![Vista de diagrama](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)
   
    Pode ampliar, reduzir, ampliar para 100%, fazer zoom para ajustar, posicionar automaticamente pipelines e tabelas e mostrar informações de linhagem (realça os itens a montante e a jusante dos itens selecionados).  Pode fazer duplo clique num objeto (tabela ou pipeline de entrada/saída) para ver as propriedades do mesmo. 
3. Clique com o botão direito do rato em **ADFTutorialPipeline** na Vista de Diagrama e clique em **Abrir pipeline**. 
   
    ![Abrir Pipeline](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)
4. Deverá ver as atividades no pipeline juntamente com os conjuntos de dados de entrada e de saída para as atividades. Neste tutorial, tem apenas uma atividade no pipeline (Atividade de Cópia) com InputDataset como conjunto de dados de entrada e OutputDataset como conjunto de dados de saída.   
   
    ![Vista Pipeline aberto](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenedPipeline.png)
5. Clique em **Data Factory** no trilho no canto superior esquerdo para regressar à vista de diagrama. A vista de diagrama apresenta todos os pipelines. Neste exemplo, só tem de criar um pipeline.   

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o portal do Azure para monitorizar os acontecimentos de uma fábrica de dados do Azure. 

### <a name="monitor-pipeline-using-diagram-view"></a>Monitorizar o pipeline com a Vista de Diagrama
1. Clique em **X** para fechar a vista **Diagrama** para ver a home page do Data Factory relativa à fábrica de dados. Se tiver fechado o browser, siga estes passos: 
   1. Navegue para o [portal do Azure](https://portal.azure.com/). 
   2. Faça duplo clique em **ADFTutorialDataFactory** no **Startboard** (ou) clique em **Fábricas de dados**, no menu à esquerda, e procure ADFTutorialDataFactory. 
2. Deverá ver a contagem e os nomes das tabelas e do pipeline criados neste painel.
   
    ![home page com nomes](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png)
3. Agora, clique no mosaico **Conjuntos de dados**.
4. No painel **Conjuntos de dados**, clique em **InputDataset**. Este conjunto de dados é o conjunto de dados de entrada para **ADFTutorialPipeline**.
   
    ![Conjuntos de dados com InputDataset selecionado](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Clique em **... (reticências)** para ver todos os setores de dados.
   
    ![Todos os setores de dados de entrada](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Repare que todos os setores de dados até à hora atual estão no estado **Pronto** porque o ficheiro **emp.txt** está sempre presente no contentor de blobs **adftutorial\input**. Confirme que não existem setores na secção **Setores recentemente falhados** na parte inferior.
   
    Tanto a lista **Setores recentemente atualizados** como a lista **Setores recentemente falhados** estão ordenadas pela **HORA DA ÚLTIMA ATUALIZAÇÃO**. 
   
    Clique em **Filtrar** na barra de ferramentas para filtrar os setores.  
   
    ![Filtrar setores de entrada](./media/data-factory-copy-activity-tutorial-using-azure-portal/filter-input-slices.png)
6. Feche os painéis até ver o painel **Conjuntos de dados**. Clique em **OutputDataset**. Este conjunto de dados é o conjunto de dados de saída para **ADFTutorialPipeline**.
   
    ![painel dos conjuntos de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png)
7. Deverá ver o painel **OutputDataset**, como mostrado na imagem seguinte:
   
    ![painel de tabela](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png) 
8. Tenha em atenção que os setores de dados até à hora atual já foram produzidos e estão no estado **Pronto**. Nenhum setor aparece na secção **Setores do problema** na parte inferior.
9. Clique em **... (Reticências)** para ver todos os setores.
   
    ![painel de setores de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png)
10. Clique em qualquer setor de dados da lista e deverá ver o painel **Setor de Dados**.
    
     ![painel de setor de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Se o setor não estiver no estado **Pronto**, pode ver os setores a montante que não estão no estado Pronto e estão a impedir a execução do setor atual na lista **Setores a montante que não estão prontos**.
11. No painel **SETOR DE DADOS**, deverá ver toda a atividade executada na lista na parte inferior. Clique numa **execução de atividade** para ver o painel **Detalhes da execução de atividade**. 
    
    ![Detalhes da Execução da Atividade](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)
12. Clique em **X** para fechar todos os painéis até regressar ao painel principal para o **ADFTutorialDataFactory**.
13. (opcional) Clique em **Pipelines** na home page para **ADFTutorialDataFactory**, clique em **ADFTutorialPipeline** no painel **Pipelines** e explore as tabelas de entrada (**Consumido**) ou tabelas de saída (**Produzido**).
14. Inicie o **SQL Server Management Studio**, estabeleça ligação à Base de Dados SQL do Azure e verifique se as linhas estão inseridas na tabela **emp** na base de dados.
    
    ![resultados da consulta SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorizar o pipeline com a Aplicação de Monitorização e Gestão
Pode ainda utilizar a aplicação de Monitorização e Gestão para monitorizar os seus pipelines. Para obter detalhes sobre a utilização desta aplicação, veja [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App (Monitorizar e gerir pipelines do Azure Data Factory com a Aplicação de Monitorização e Gestão)](data-factory-monitor-manage-app.md).

1. Clique no mosaico **Monitorizar e Gerir** na home page do data factory.
   
    ![Mosaico Monitorizar e Gerir](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Deverá ver a **Aplicação de Monitorização e Gestão**. Altere a **Hora de início** e a **Hora de fim** para incluir as horas de início (12-07-2016) e de fim (13-02-2016) do seu pipeline e clique em **Aplicar**. 
   
    ![Aplicação Monitorizar e Gerir](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png) 
3. Selecione uma janela de atividade na lista **Janelas de Atividade** para ver os respetivos detalhes. 
    ![Detalhes da janela de atividade](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

## <a name="summary"></a>Resumo
Neste tutorial, vai criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o portal do Azure para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1. Criou uma **fábrica de dados** do Azure.
2. Criou **serviços ligados**:
   1. Um serviço ligado do ** Storagedo Azure** para ligar a sua Conta de armazenamento do Azure que contém dados de entrada.     
   2. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQL do Azure que contém dados de saída. 
3. Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4. Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como sink.  

## <a name="see-also"></a>Veja Também
| Tópico | Descrição |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |



<!--HONumber=Feb17_HO1-->


