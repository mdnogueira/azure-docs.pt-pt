<properties 
    pageTitle="Tutorial: criar um pipeline com a Atividade de Cópia com o Editor do Data Factory | Microsoft Azure" 
    description="Neste tutorial, irá criar um pipeline do Azure Data Factory com uma Atividade de Cópia com o Editor do Data Factory no portal do Azure." 
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

# Tutorial: Criar um pipeline com a Atividade de Cópia com o Editor do Data Factory
> [AZURE.SELECTOR]
- [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Com o Editor do Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Com o PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Com o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Com a API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Com a API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Com o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)


Este tutorial contém os seguintes passos:

Passo | Descrição
-----| -----------
[Criar um Azure Data Factory](#create-data-factory) | Neste passo, irá criar uma fábrica de dados do Azure com o nome **ADFTutorialDataFactory**.  
[Criar serviços ligados](#create-linked-services) | Neste passo, irá criar dois serviços ligados: **AzureStorageLinkedService** e **AzureSqlLinkedService**. O AzureStorageLinkedService liga o armazenamento do Azure e o AzureSqlLinkedService liga a base de dados SQL do Azure a ADFTutorialDataFactory. Os dados de entrada para o pipeline residem num contentor de blobs no armazenamento de blobs do Azure, e os dados de saída são armazenados numa tabela na base de dados SQL do Azure. Por conseguinte, adicione estes dois arquivos de dados como serviços ligados à fábrica de dados.      
[Criar conjuntos de dados de entrada e saída](#create-datasets) | No passo anterior, criou serviços ligados referentes aos arquivos de dados que contêm dados de entrada/saída. Neste passo, vai definir duas tabelas de fábrica de dados – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída armazenados nos arquivos de dados. Para o EmpTableFromBlob, especifique o contentor de blobs que contém um blob com os dados de origem e, para o EmpSQLTable, especifique a tabela SQL que armazena os dados de saída. Especifique também outras propriedades, tais como a estrutura, disponibilidade, e assim sucessivamente. 
[Criar um pipeline](#create-pipeline) | Neste passo, irá criar um pipeline com o nome **ADFTutorialPipeline** no ADFTutorialDataFactory. O pipeline tem uma **Atividade de Cópia** que copia dados de entrada a partir do blob do Azure para a tabela SQL do Azure de saída. A Atividade de Cópia executa o movimento de dados no Azure Data Factory. Utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e escalável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia. 
[Monitorizar o pipeline](#monitor-pipeline) | Neste passo, irá monitorizar setores de tabelas de entrada e de saída com o portal do Azure.

> [AZURE.IMPORTANT] 
> Leia o artigo [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua os passos dos pré-requisitos antes de executar este tutorial.

## Criar fábrica de dados
Neste passo, utilize o portal do Azure para criar uma fábrica de dados do Azure com o nome **ADFTutorialDataFactory**.

1.  Depois de iniciar sessão no [portal do Azure][azure-portal], clique em **NOVO** no canto inferior esquerdo, selecione **Análise de dados** no painel **Criar** e clique em **Data Factory** no painel **Análise de dados**. 

    ![Novo -> DataFactory][image-data-factory-new-datafactory-menu]    

6. No painel **Nova fábrica de dados**:
    1. Introduza **ADFTutorialDataFactory** como **nome**. 
    
        ![Painel Nova fábrica de dados][image-data-factory-getstarted-new-data-factory-blade]
    2. Clique em **NOME DO GRUPO DE RECURSOS** e faça o seguinte:
        1. Clique em **Criar um novo grupo de recursos**.
        2. No painel **Criar grupo de recursos**, introduza **ADFTutorialResourceGroup** como o **nome** do grupo de recursos e clique em **OK**. 

            ![Criar Grupo de Recursos][image-data-factory-create-resource-group]

        Alguns dos passos deste tutorial pressupõe que utiliza o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../resource-group-overview.md).  
7. No painel **Nova fábrica de dados**, repare que a opção **Adicionar ao Startboard** está selecionada.
8. Clique em **Criar** no painel **Nova fábrica de dados**.

    O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory) e tente criá-la novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
    
        Data factory name “ADFTutorialDataFactory” is not available  
     
    ![Nome do Data Factory não disponível][image-data-factory-name-not-available]
    
    > [AZURE.NOTE] O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.  
    > 
    > Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure

9. Clique no hub **NOTIFICAÇÕES** à esquerda e procure notificações do processo de criação. Clique em **X** para fechar o painel **NOTIFICAÇÕES** se estiver aberto. 
10. Após concluir a criação, verá o painel **DATA FACTORY**, como mostrado abaixo.

    ![Home page da fábrica de dados][image-data-factory-get-stated-factory-home-page]

## Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Um arquivo de dados pode ser um Armazenamento do Azure, uma Base de Dados SQL do Azure ou uma base de dados SQL Server no local.

Neste passo, irá criar dois serviços ligados: **AzureStorageLinkedService** e **AzureSqlLinkedService**. O serviço ligado AzureStorageLinkedService liga uma Conta de Armazenamento do Azure e o AzureSqlLinkedService liga uma base de dados SQL do Azure a **ADFTutorialDataFactory**. Mais tarde neste tutorial, irá criar um pipeline que copia dados de um contentor de blobs no AzureStorageLinkedService para uma tabela SQL no AzureSqlLinkedService.

### Criar um serviço ligado para a conta de armazenamento do Azure
1.  No painel **DATA FACTORY**, clique no mosaico **Criar e implementar** para iniciar o **Editor** para a fábrica de dados.

    ![Mosaico Criar e Implementar][image-author-deploy-tile] 

     
5. No **Editor**, clique no botão **Novo arquivo de dados** na barra de ferramentas e selecione ** armazenamento do Azure** no menu pendente. Deverá ver o modelo JSON para criar um serviço ligado de armazenamento do Azure no painel à direita. 

    ![Botão Arquivar dados novos do Editor][image-editor-newdatastore-button]
    
6. Substitua **accountname** e **accountkey** pelo nome da conta e os valores da chave da conta para a sua conta de armazenamento do Azure. 

    ![JSON do Blob Storage do Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
    
    Veja [Referência de Processamento de Scripts JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter mais detalhes sobre as propriedades de JSON.

6. Clique em **Implementar** na barra de ferramentas para implementar o AzureStorageLinkedService. Confirme que vê a mensagem **SERVIÇO LIGADO CRIADO COM ÊXITO** na barra de título.

    ![Implementar o Blob Storage do Editor][image-editor-blob-storage-deploy]

### Criar um serviço ligado para a SQL Database do Azure
1. No **Editor do Data Factory**, clique no botão **Novo arquivo de dados** na barra de ferramentas e selecione **Base de Dados SQL do Azure** no menu pendente. Deverá ver o modelo JSON para criar o serviço ligado SQL do Azure no painel à direita.

    ![Definições do SQL do Azure do Editor][image-editor-azure-sql-settings]

2. Substitua **servername**, **databasename**, **username@servername** e **password** pelos nomes do seu servidor, base de dados, conta de utilizador e palavra-passe do SQL do Azure. 
3. Clique em **Implementar** na barra de ferramentas para criar e implementar o AzureSqlLinkedService. 
   

## Criar conjuntos de dados
No passo anterior, criou serviços ligados **AzureStorageLinkedService** e **AzureSqlLinkedService** para ligar uma Conta de armazenamento do Azure e a SQL Database do Azure a uma fábrica de dados: **ADFTutorialDataFactory**. Neste passo, irá definir duas tabelas de fábrica de dados – **EmpTableFromBlob** e **EmpSQLTable** – que representam os dados de entrada/saída armazenados nos arquivos de dados mencionados pelo AzureStorageLinkedService e AzureSqlLinkedService, respetivamente. Para EmpTableFromBlob, especifique o contentor de blobs que contém um blob com os dados de origem e, para EmpSQLTable, especifique a tabela SQL que armazena os dados de saída. 

### Criar conjunto de dados de entrada 
Uma tabela é um conjunto de dados retangular com um esquema. Neste passo, irá criar uma tabela com o nome **EmpBlobTable** que aponta para um contentor de blobs no Armazenamento do Azure, representado pelo serviço ligado **AzureStorageLinkedService**.

1. No **Editor** do Data Factory, clique no botão **Novo conjunto de dados** na barra de ferramentas e clique em **Tabela de blobs** no menu pendente. 
2. Substitua JSON no painel à direita com o fragmento JSON seguinte: 

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

        
     Tenha em atenção o seguinte: 
    
    - O **type** do conjunto de dados está definido como **AzureBlob**.
    - O **linkedServiceName** está definido como **AzureStorageLinkedService**. Criou este serviço ligado no Passo 2.
    - O **folderPath** está definido para o contentor **adftutorial**. Também pode especificar o nome de um blob na pasta. Uma vez que não está a especificar o nome do blob, os dados de todos os blobs no contentor são considerados como dados de entrada.  
    - O **type** do formato está definido como **TextFormat**
    - Existem dois campos no ficheiro de texto – **FirstName** e **LastName** – separados por vírgula (**columnDelimiter**) 
    - A **disponibilidade** está definida como **de hora a hora** (**a frequência** está definida como **hora** e o **intervalo** está definido como **1**). Por conseguinte, o Data Factory procura dados de entrada a cada hora na pasta raiz do contentor de blobs (**adftutorial**) que especificou. 
    

    Se não especificar um **fileName** para uma **tabela de** **entrada**, todos os ficheiros/blobs da pasta de entrada (**folderPath**) são considerados entradas. Se especificar um fileName no JSON, apenas o ficheiro/blob especificado é considerado uma entrada asn.
 
    Se não especificar um **fileName** para uma **tabela de saída**, os ficheiros gerados no **folderPath** são denominados no seguinte formato: Data.&lt;Guid\&gt;.txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Para definir **folderPath** e **fileName** de forma dinâmica com base no tempo **SliceStart**, utilize a propriedade **partitionedBy**. No exemplo seguinte, o folderPath utiliza o Ano, o Mês e o Dia do SliceStart (hora de início do setor a ser processado) e o fileName utiliza a Hora do SliceStart. Por exemplo, se está a ser produzido um setor para 2014-10-20T08:00:00, folderName está definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName está definido como 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

    Veja [Referência de Processamento de Scripts JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter mais detalhes sobre as propriedades de JSON.

2. Clique em **Implementar** na barra de ferramentas para criar e implementar a tabela **EmpTableFromBlob**. Confirme que vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.

### Criar conjunto de dados de saída
Nesta parte do passo, irá criar um conjunto de dados de saída com o nome **EmpSQLTable**. Este conjunto de dados aponta para uma tabela SQL na base de dados SQL do Azure, representada por **AzureSqlLinkedService** . 

1. No **Editor** do Data Factory, clique no botão **Novo conjunto de dados** na barra de ferramentas e clique em **Tabela SQL do Azure** no menu pendente. 
2. Substitua JSON no painel à direita com o fragmento JSON seguinte:

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

        
     Tenha em atenção o seguinte: 
    
    * O **type** do conjunto de dados está definido como **AzureSQLTable**.
    * O **linkedServiceName** está definido como **AzureSqlLinkedService** (criou este serviço ligado no Passo 2).
    * O **tablename** está definido como **emp**.
    * Existem três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp da base de dados. O ID é uma coluna de identidade, por isso terá de especificar apenas **FirstName** e **LastName** aqui.
    * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory gera um setor de dados de saída a cada hora na tabela **emp** da base de dados SQL do Azure.


3. Clique em **Implementar** na barra de ferramentas para criar e implementar a tabela **EmpSQLTable**.


## Criar pipeline
Neste passo, cria um pipeline com uma **Atividade de Cópia** que utiliza **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1. No **Editor** do Data Factory, clique no botão **Novo pipeline** na barra de ferramentas. Clique em **... (Reticências)** na barra de ferramentas se não vir o botão. Alternativamente, pode clicar com o botão direito em **Pipelines** na vista de árvore e clicar em **Novo pipeline**.

    ![Botão Novo Pipeline do Editor][image-editor-newpipeline-button]
 
2. Substitua JSON no painel à direita com o fragmento JSON seguinte: 
        
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
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z"
          }
        } 

    Tenha em atenção o seguinte:

    - Na secção atividades, existe apenas uma atividade cujo **type** está definido como **CopyActivity**.
    - A entrada da atividade está definida como **EmpTableFromBlob** e a saída da atividade está definida como **EmpSQLTable**.
    - Na secção **transformação**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.

    Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte. Pode especificar apenas a parte da data e ignorar a parte da hora em data e hora. Por exemplo, “2015-02-03”, que é equivalente a “2015-02-03T00:00:00Z”
    
    Tanto o datetime de início como de fim têm de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. 
    
    Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**.
    
    No exemplo anterior existem 24 setores de dados, uma vez que estes são produzidos de hora a hora.
    
    Veja [Referência de Processamento de Scripts JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter mais detalhes sobre as propriedades de JSON.

4. Clique em **Implementar** na barra de ferramentas para criar e implementar o **ADFTutorialPipeline**. Confirme que vê a mensagem **PIPELINE CRIADO COM ÊXITO**.
5. Agora, feche o painel **Editor** clicando em **X**. Clique em **X** novamente para fechar o painel ADFTutorialDataFactory com a barra de ferramentas e a vista de árvore. Se vir a mensagem **as edições não guardadas serão eliminadas**, clique em **OK**.
6. Desta forma, deve regressar ao painel **DATA FACTORY** para **ADFTutorialDataFactory**.

**Parabéns!** Criou um Azure Data Factory, serviços ligados, tabelas e um pipeline com êxito e agendou o pipeline.   
 
### Ver a fábrica de dados numa Vista de Diagrama 
1. No painel **DATA FACTORY**, clique em **Diagrama**.

    ![Painel Data Factory – Mosaico do Diagrama][image-datafactoryblade-diagramtile]

2. Deverá ver o diagrama semelhante ao seguinte: 

    ![Vista de diagrama][image-data-factory-get-started-diagram-blade]

    Pode ampliar, reduzir, ampliar para 100%, fazer zoom para ajustar, posicionar automaticamente pipelines e tabelas e mostrar informações de linhagem (realça os itens a montante e a jusante dos itens selecionados).  Pode fazer duplo clique num objeto (tabela ou pipeline de entrada/saída) para ver as propriedades do mesmo. 
3. Clique com o botão direito do rato em **ADFTutorialPipeline** na Vista de Diagrama e clique em **Abrir pipeline**. Deverá ver as atividades no pipeline juntamente com os conjuntos de dados de entrada e de saída para as atividades. Neste tutorial, tem apenas uma atividade no pipeline (Atividade de Cópia) com EmpTableBlob como conjunto de dados de entrada e EmpSQLTable como conjunto de dados de saída.   

    ![Abrir Pipeline](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)

4. Clique em **Data Factory** no trilho no canto superior esquerdo para regressar à vista de diagrama. A vista de diagrama apresenta todos os pipelines. Neste exemplo, só tem de criar um pipeline.   
 

## Monitorizar o pipeline
Neste passo, irá utilizar o portal do Azure para monitorizar os acontecimentos de uma fábrica de dados do Azure. 

1. Navegue até ao [portal do Azure (Pré-visualização)][azure-portal], se este não estiver aberto. 
2. Se o painel para **ADFTutorialDataFactory** não estiver aberto, abra-o ao clicar em **ADFTutorialDataFactory** no **Startboard**. 
3. Deverá ver a contagem e os nomes das tabelas e do pipeline criados neste painel.

    ![home page com nomes][image-data-factory-get-started-home-page-pipeline-tables]

4. Agora, clique no mosaico **Conjuntos de dados**.
5. No painel **Conjuntos de dados**, clique em **EmpTableFromBlob**. Este conjunto de dados é o conjunto de dados de entrada para **ADFTutorialPipeline**.

    ![Conjuntos de dados com EmpTableFromBlob selecionados][image-data-factory-get-started-datasets-emptable-selected]   
5. Tenha em atenção que os setores de dados até à hora atual já foram produzidos e estão no estado **Pronto**, uma vez que o ficheiro **emp.txt** está sempre presente no contentor do blob: **adftutorial\input**. Confirme que não existem setores na secção **Setores recentemente falhados** na parte inferior.

    Tanto a lista **Setores recentemente atualizados** como a lista **Setores recentemente falhados** estão ordenadas pela **HORA DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de um setor é alterada nas seguintes situações. 
    
    Clique no título das listas ou em **... (reticências)** para ver a lista maior de setores. Clique em **Filtrar** na barra de ferramentas para filtrar os setores.  
    
    Por outro lado, para ver os setores de dados ordenados pelas horas de início/fim dos setores, clique no mosaico **Setores de dados (por hora de setor)**.   

    ![Setores de Dados por Hora do Setor][DataSlicesBySliceTime]   

6. Agora, no painel **Conjuntos de dados**, clique em **EmpSQLTable**. Este conjunto de dados é o conjunto de dados de saída para **ADFTutorialPipeline**.

    ![painel dos conjuntos de dados][image-data-factory-get-started-datasets-blade]

6. Deverá ver o painel **EmpSQLTable**, como mostrado na imagem seguinte:

    ![painel de tabela][image-data-factory-get-started-table-blade]
 
7. Tenha em atenção que os setores de dados até à hora atual já foram produzidos e estão no estado **Pronto**. Nenhum setor aparece na secção **Setores do problema** na parte inferior.
8. Clique em **... (Reticências)** para ver todos os setores.

    ![painel de setores de dados][image-data-factory-get-started-dataslices-blade]

9. Clique em qualquer setor de dados da lista e deverá ver o painel **SETOR DE DADOS**.

    ![painel de setor de dados][image-data-factory-get-started-dataslice-blade]
  
    Se o setor não estiver no estado **Pronto**, pode ver os setores a montante que não estão no estado Pronto e estão a impedir a execução do setor atual na lista **Setores a montante que não estão prontos**. 

11. No painel **SETOR DE DADOS**, deverá ver toda a atividade executada na lista na parte inferior. Clique numa **execução da atividade** para ver o painel **DETALHES DA EXECUÇÃO DA ATIVIDADE**. 

    ![Detalhes da Execução da Atividade][image-data-factory-get-started-activity-run-details]

    
12. Clique em **X** para fechar todos os painéis até regressar ao painel principal para o **ADFTutorialDataFactory**.
14. (opcional) Clique em **Pipelines** na home page para **ADFTutorialDataFactory**, clique em **ADFTutorialPipeline** no painel **Pipelines** e explore as tabelas de entrada (**Consumido**) ou tabelas de saída (**Produzido**).
15. Inicie o **SQL Server Management Studio**, estabeleça ligação à Base de Dados SQL do Azure e verifique se as linhas estão inseridas na tabela **emp** na base de dados.

    ![resultados da consulta SQL][image-data-factory-get-started-sql-query-results]


## Resumo 
Neste tutorial, vai criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o portal do Azure para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1.  Criou uma **fábrica de dados** do Azure.
2.  Criou **serviços ligados**:
    1. Um serviço ligado do ** Storagedo Azure** para ligar a sua Conta de armazenamento do Azure que contém dados de entrada.    
    2. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQL do Azure que contém dados de saída. 
3.  Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4.  Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como sink.  


## Veja Também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de Movimento de Dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia que utilizou no tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory.
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[use-custom-activities]: data-factory-use-custom-activities.md
[resolução de problemas]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png
 


<!--HONumber=ago16_HO4-->


