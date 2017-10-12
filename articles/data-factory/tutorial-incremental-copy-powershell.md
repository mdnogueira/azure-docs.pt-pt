---
title: Copiar dados de forma incremental com o Azure Data Factory | Microsoft Docs
description: Neste tutorial, vai criar um pipeline do Azure Data Factory, que copia dados de forma incremental de uma Base de Dados SQL do Azure para um Armazenamento de Blobs do Azure.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: 91b632b6d2c2917acf17e9d89c1b5a4b0f8b1c33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage"></a>Carregar dados de forma incremental da Base de Dados SQL do Azure para o Armazenamento de Blobs do Azure
O Azure Data Factory é um serviço de integração de dados baseado na cloud que lhe permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados. Com o Azure Data Factory, poderá criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que são capazes de ingerir dados provenientes de diferentes arquivos de dados, processar/transformar os dados através de serviços de computação, tais como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para serem consumidos por aplicações de business intelligence (BI). 

Durante o percurso de integração de dados, um dos cenários mais utilizados consiste em carregar dados de forma incremental periodicamente para atualizar o resultado da análise atualizada após a análise e os carregamentos de dados iniciais. Neste tutorial, vai focar-se em carregar apenas registos novos ou atualizados das origens de dados para os sinks de dados. A execução é mais eficiente quando comparada com carregamentos completos, sobretudo para conjuntos de dados grandes.    

Pode utilizar o Data Factory para criar soluções com limite superior de tamanho para conseguir o carregamento incremental de dados através das atividades de Pesquisa, Cópia e Procedimento Armazenamento num pipeline.  

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Preparar o arquivo de dados para armazenar o valor de limite de tamanho.   
> * Criar uma fábrica de dados.
> * Criar serviços ligados. 
> * Criar conjuntos de dados de origem, de sink e com limite de tamanho.
> * Criar um pipeline.
> * Executar o pipeline.
> * Monitorizar a execução do pipeline. 

## <a name="overview"></a>Descrição geral
O diagrama da solução de alto nível é: 

![Carregar dados de forma incremental](media\tutorial-Incrementally-load-data-from-azure-sql-to-blob\incrementally-load.png)

Eis os passos importantes na criação desta solução: 

1. **Selecionar a coluna de limite de tamanho**.
    Selecione uma coluna no arquivo de dados de origem, que pode ser utilizada para dividir os registos novos ou atualizados para cada execução. Normalmente, os dados nesta coluna selecionada (por exemplo, last_modify_time ou ID) continuam a aumentar quando as linhas são criadas ou atualizadas. O valor máximo nesta coluna é utilizado como limite de tamanho.
2. **Preparar um arquivo de dados para armazenar o valor de limite de tamanho**.   
    Neste tutorial, vai armazenar o valor de limite de tamanho numa base de dados SQL do Azure.
3. **Criar um pipeline com o seguinte fluxo de trabalho:** 
    
    O pipeline nesta solução tem as seguintes atividades:
  
    1. Criar duas atividades de **pesquisa**. Utilize a primeira atividade de pesquisa para obter o último valor de limite de tamanho. Utilize a segunda atividade de pesquisa para obter o novo valor de limite de tamanho. Estes valores de limite de tamanho são transmitidos para a atividade de cópia. 
    2. Criar uma **atividade de cópia** que copia linhas do arquivo de dados de origem com o valor da coluna de limite de tamanho superior ao valor de limite de tamanho antigo e inferior ao novo valor de limite de tamanho. Em seguida, copia os dados delta do arquivo de dados de origem para um armazenamento de blobs como um ficheiro novo. 
    3. Criar uma **atividade de procedimento armazenado** que atualiza o valor de limite de tamanho para o pipeline que vai ser executado da próxima vez. 


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Base de Dados SQL do Azure**. A base de dados é utilizada como o arquivo de dados de **origem**. Se não tiver uma Base de Dados SQL do Azure, veja o artigo [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para obter os passos para criar uma.
* **Conta de Armazenamento do Azure**. O armazenamento de blobs é utilizado como arquivo de dados de **sink**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter os passos para criar uma. Crie um contentor com o nome **adftutorial**. 
* **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Criar uma tabela de origem de dados na base de dados SQL do Azure
1. Abra o **SQL Server Management Studio**, em **Explorador de Servidores**, faça clique com o botão direito do rato na base de dados e escolha **Nova Consulta**.
2. Execute o seguinte comando SQL na base de dados SQL do Azure para criar uma tabela com o nome `data_source_table` como arquivo da origem de dados.  
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Neste tutorial, vai utilizar **LastModifytime** como a coluna de **limite de tamanho**.  Os dados no arquivo da origem de dados são apresentados na tabela seguinte:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela na base de dados SQL para armazenar o valor de limite superior de tamanho
1. Execute o seguinte comando SQL na base de dados SQL do Azure para criar uma tabela com o nome `watermarktable` para armazenar o valor de limite de tamanho.  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
3. Defina o **valor** predefinido de limite de tamanho com o nome da tabela do arquivo de dados de origem.  (Neste tutorial, o nome da tabela é: **data_source_table**)

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
4. Reveja os dados na tabela: `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Saída: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Criar um procedimento armazenado na base de dados SQL do Azure 

Execute o seguinte comando para criar um procedimento armazenado na base de dados SQL do Azure.

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.

    Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Execute o comando seguinte para ver todas as subscrições desta conta:

    ```powershell
    Get-AzureRmSubscription
    ```
    Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **SubscriptionId** pelo ID da sua subscrição do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. Execute o cmdlet **Set-AzureRmDataFactoryV2** para criar uma fábrica de dados. Substitua os marcadores de posição pelos seus próprios valores antes de executar o comando.

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName "<your resource group to create the factory>" -Location "East US" -Name "<specify the name of data factory to create. It must be globally unique.>" 
    ```

    Tenha em atenção os seguintes pontos:

    * O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

        ```
        The specified Data Factory name '<data factory name>' is already in use. Data Factory names must be globally unique.
        ```

    * Para criar instâncias do Data Factory, tem de ser contribuidor ou administrador da subscrição do Azure.
    * Atualmente, o Data Factory V2 só permite criar uma fábrica de dados na região E.U.A. Leste. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.


## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Nesta secção, vai criar serviços ligados para a sua conta de Armazenamento do Azure e a base de dados SQL do Azure. 

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Armazenamento do Azure.
1. Crie um ficheiro JSON com o nome **AzureStorageLinkedService.json** na pasta **C:\ADF** com o seguinte conteúdo: (crie a pasta ADF, caso ainda não exista.). Substitua `<accountName>`,  `<accountKey>` pelo nome e chave da sua conta de armazenamento do Azure antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. No **Azure PowerShell**, mude para a pasta **ADF**.
3. Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço ligado: **AzureStorageLinkedService**. No exemplo seguinte, vai transmitir os valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Criar o serviço ligado da Base de Dados SQL do Azure.
1. Crie um ficheiro JSON com o nome **AzureSQLDatabaseLinkedService.json** na pasta **C:\ADF** com o seguinte conteúdo: (crie a pasta ADF, caso ainda não exista.). Substitua **&lt;server&gt;, &lt;user id&gt; e &lt;password&gt;** pelo nome do seu Azure SQL Server, ID de utilizador e palavra-passe antes de guardar o ficheiro. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. No **Azure PowerShell**, mude para a pasta **ADF**.
3. Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço ligado: **AzureSQLDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de origem e sink. 

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. Crie um ficheiro JSON com o nome SourceDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    Neste tutorial, utilizamos o nome de tabela: **data_source_table**. Substitua-o se estiver a utilizar uma tabela com um nome diferente. 
2.  Execute o cmdlet Set-AzureRmDataFactoryV2Dataset para criar o conjunto de dados: SourceDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de sink

1. Crie um ficheiro JSON com o nome SinkDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Este fragmento parte do princípio de que tem um contentor de blobs denominado **adftutorial** no Armazenamento de Blobs do Azure. Crie o contentor se ainda não existir ou defina-o com o nome de um contentor existente. A pasta de saída `incrementalcopy` é criada automaticamente se não existir no contentor. Neste tutorial, o nome de ficheiro é gerado dinamicamente através da expressão: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.
2.  Execute o cmdlet Set-AzureRmDataFactoryV2Dataset para criar o conjunto de dados: SinkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-watermark"></a>Criar um conjunto de dados para o limite de tamanho
Neste passo, vai criar um conjunto de dados para armazenar um valor de limite superior de tamanho. 

1. Crie um ficheiro JSON com o nome WatermarkDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Execute o cmdlet Set-AzureRmDataFactoryV2Dataset para criar o conjunto de dados: WatermarkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste tutorial, vai criar um pipeline com duas atividades de pesquisa, um atividade de cópia e uma atividade de procedimento armazenado encadeadas num pipeline. 


1. Crie um ficheiro JSON: IncrementalCopyPipeline.json na mesma pasta com o seguinte conteúdo. 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "sp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ],
            
        }
    }
    ```json

    If you are using a source table with a name different from the one used in the tutorial (**data_source_table**), replace **data_source_table** in the sqlReaderQuery with the name of your source table. 
    

2. Run the Set-AzureRmDataFactoryV2Pipeline cmdlet to create the pipeline: IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Segue-se o resultado do exemplo: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Execute o pipeline: **IncrementalCopyPipeline** através do cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Substitua os marcadores de posição pelos seus nomes do grupo de recursos e da fábrica de dados.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ``` 
2. Verifique o estado do pipeline, executando o cmdlet Get-AzureRmDataFactoryV2ActivityRun até ver todas as atividades a serem executadas com êxito. Substitua os marcadores de posição pela sua hora apropriada para o parâmetro RunStartedAfter e RunStartedBefore.  Neste tutorial, utilizamos -RunStartedAfter "2017/09/14" -RunStartedBefore "2017/09/15"

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Segue-se o resultado do exemplo:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Rever os resultados

1. No armazenamento de blobs do Azure (arquivo sink), deverá ver que os dados foram copiados para o ficheiro definido em SinkDataset.  Neste tutorial, o nome de ficheiro é `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`.  Abra o ficheiro. Poderá ver que os registos no ficheiro são iguais aos dados na base de dados SQL do Azure.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. Verifique o valor mais recente de `watermarktable`. Poderá ver que o valor de limite de tamanho foi atualizado.

    ```sql
    Select * from watermarktable
    ```
    
    Segue-se o resultado do exemplo:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table   2017-09-05  8:06:00.000

### <a name="insert-data-into-data-source-store-to-verify-delta-data-loading"></a>Inserir dados no arquivo de dados de origem para verificar o carregamento de dados delta

1. Insira novos dados na base de dados SQL do Azure (arquivo de dados de origem):

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    Os dados atualizados na base de dados SQL do Azure são os seguintes:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Execute o pipeline: **IncrementalCopyPipeline** novamente com o cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Substitua os marcadores de posição pelos seus nomes do grupo de recursos e da fábrica de dados.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ```
3. Verifique o estado do pipeline, executando o cmdlet **Get-AzureRmDataFactoryV2ActivityRun** até ver todas as atividades a serem executadas com êxito. Substitua os marcadores de posição pela sua hora apropriada para o parâmetro RunStartedAfter e RunStartedBefore.  Neste tutorial, utilizamos -RunStartedAfter "2017/09/14" -RunStartedBefore "2017/09/15"

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Segue-se o resultado do exemplo:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4.  No armazenamento de blobs do Azure, deverá ver que foi criado outro ficheiro. Neste tutorial, o novo nome de ficheiro é `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`.  Abra esse ficheiro. Verá dois registos de linhas no mesmo:
5.  Verifique o valor mais recente de `watermarktable`. Poderá ver que o valor de limite de tamanho foi atualizado novamente.

    ```sql
    Select * from watermarktable
    ```
    saída de exemplo: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Passos seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Definir uma coluna de **limite de tamanho** e armazená-la na Base de Dados SQL do Azure.  
> * Criar uma fábrica de dados.
> * Criar serviços ligados para a Base de Dados SQL e o Armazenamento de Blobs. 
> * Criar conjuntos de dados de origem e sink.
> * Criar um pipeline.
> * Executar o pipeline.
> * Monitorizar a execução do pipeline. 

Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados com um cluster do Spark na cloud](tutorial-transform-data-spark-powershell.md)



