---
title: Atividade de procedimento de armazenados do SQL Server
description: Saiba como pode utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado numa SQL Database do Azure ou do Azure SQL Data Warehouse a partir de um pipeline do Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: f490aeef07b142b6a28319581b01c6cfc00054ba
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="sql-server-stored-procedure-activity"></a>Atividade de procedimento de armazenados do SQL Server
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade do ramo de registo](data-factory-hive-activity.md) 
> * [Atividade do PIg](data-factory-pig-activity.md)
> * [Atividade de MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se versão 1 do Azure Data Factory, que é geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [transformar dados utilizando a atividade de procedimento armazenado na versão 2 do Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Descrição geral
Utilize atividades de transformação de dados numa fábrica de dados [pipeline](data-factory-create-pipelines.md) para transformar e processar dados não processados para predições e das informações. A atividade de procedimento armazenado é uma das atividades de transformação que suporte a fábrica de dados. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportado na fábrica de dados.

Pode utilizar a atividade de procedimento armazenado para invocar um procedimento armazenado dos arquivos de dados seguintes na sua empresa ou numa máquina virtual do Azure (VM): 

- Base de Dados SQL do Azure
- Azure SQL Data Warehouse
- Base de dados do SQL Server.  Se estiver a utilizar o SQL Server, instale o Data Management Gateway no mesmo computador que aloja a base de dados ou num computador separado que tenha acesso à base de dados. O Data Management Gateway é um componente que liga dados origens no local/no VM do Azure com serviços em nuvem de forma segura e gerida. Consulte [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter detalhes.

> [!IMPORTANT]
> Quando copiar dados para a SQL Database do Azure ou SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando o **sqlWriterStoredProcedureName** propriedade. Para obter mais informações, consulte [invocar um procedimento armazenado de atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para obter detalhes sobre a propriedade, consulte os artigos de conector: [SQL Database do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Invocar um procedimento armazenado ao copiar dados para um Azure SQL Data Warehouse, utilizando uma atividade de cópia não é suportada. No entanto, pode utilizar a atividade de procedimento armazenado para invocar um procedimento armazenado num armazém de dados SQL. 
>  
> Quando copiar dados de SQL Database do Azure ou SQL Server ou o Azure SQL Data Warehouse, pode configurar **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler os dados da base de dados de origem utilizando o  **sqlReaderStoredProcedureName** propriedade. Para obter mais informações, consulte os seguintes artigos de conector: [SQL Database do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          


As instruções seguintes utilizam a atividade de procedimento armazenados num pipeline para invocar um procedimento armazenado numa base de dados SQL do Azure. 

## <a name="walkthrough"></a>Instruções
### <a name="sample-table-and-stored-procedure"></a>Tabela de exemplo e o procedimento armazenado
1. Crie o seguinte **tabela** na SQL Database do Azure utilizando o SQL Server Management Studio ou qualquer outra ferramenta estiver familiarizado com. A coluna de datetimestamp é a data e hora quando é gerada a ID correspondente.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    O ID é exclusivo identificados e a coluna de datetimestamp é a data e hora quando é gerada a ID correspondente.
    
    ![Dados de exemplo](./media/data-factory-stored-proc-activity/sample-data.png)

    Neste exemplo, o procedimento armazenado é uma base de dados do SQL do Azure. Se tiver o procedimento armazenado um Azure SQL Data Warehouse e a base de dados do SQL Server, a abordagem é semelhante. Para uma base de dados do SQL Server, tem de instalar um [Data Management Gateway](data-factory-data-management-gateway.md).
2. Crie o seguinte **procedimento armazenado** que inserem dados para o **sampletable**.

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Nome** e **tem maiúsculas e minúsculas** do parâmetro (DateTime neste exemplo) tem de corresponder do parâmetro especificado no pipeline/JSON de atividade. Na definição do procedimento armazenado, certifique-se de que  **@**  é utilizado como um prefixo para o parâmetro.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **novo** no menu da esquerda, clique em **Intelligence + análise**e clique em **Data Factory**.

    ![Nova fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. No **nova fábrica de dados** painel, introduza **SProcDF** para o nome. Os nomes de fábrica de dados do Azure são **globalmente exclusivo**. Terá de preceder o nome do data factory com o nome, para permitir a criação da fábrica de com êxito.

   ![Nova fábrica de dados](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Selecione o **subscrição do Azure**.
5. Para **grupo de recursos**, efetue um dos seguintes passos:
   1. Clique em **criar nova** e introduza um nome para o grupo de recursos.
   2. Clique em **utilizar existente** e selecione um grupo de recursos existente.  
6. Selecione a **localização** da fábrica de dados.
7. Selecione **afixar ao dashboard** para que possa ver a fábrica de dados no dashboard próxima vez que iniciar sessão.
8. Clique em **Criar** no painel **Nova fábrica de dados**.
9. Consulte a fábrica de dados a ser criada no **dashboard** do portal do Azure. Depois de ter criado a fábrica de dados com êxito, vê a página da fábrica de dados e o respetivo conteúdo.

   ![Home page da fábrica de dados](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Criar um serviço ligado SQL do Azure
Depois de criar a fábrica de dados, criar um Azure SQL serviço ligado que liga a base de dados SQL do Azure, que contém a tabela de sampletable e sp_sample armazenados procedimento, à fábrica de dados.

1. Clique em **autor e implementar** no **Data Factory** painel **SProcDF** para iniciar o Editor do Data Factory.
2. Clique em **novo arquivo de dados** no comando barra e escolha **SQL Database do Azure**. Deverá ver o script JSON para criar um serviço ligado SQL do Azure no editor.

   ![Novo arquivo de dados](media/data-factory-stored-proc-activity/new-data-store.png)
3. O script de JSON, efetue as seguintes alterações:

   1. Substitua `<servername>` com o nome do seu servidor de SQL Database do Azure.
   2. Substitua `<databasename>` com a base de dados que criou a tabela e o procedimento armazenado.
   3. Substitua `<username@servername>` com a conta de utilizador que tenha acesso à base de dados.
   4. Substitua `<password>` com a palavra-passe da conta de utilizador.

      ![Novo arquivo de dados](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Para implementar o serviço ligado, clique em **implementar** na barra de comandos. Confirme se vê o AzureSqlLinkedService na vista de árvore à esquerda.

    ![Vista de árvore com o serviço ligado](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Tem de especificar um conjunto de dados de saída para uma atividade de procedimento armazenado, mesmo se o procedimento armazenado não produz quaisquer dados. Isto acontece porque é o conjunto de dados de saída que pauta a agenda da atividade (frequência a atividade é executada - numa base horária, diária, etc.). O conjunto de dados de saída tem de utilizar um **serviço ligado** que se refere a uma base de dados do SQL do Azure ou um Azure SQL Data Warehouse ou uma base de dados SQL Server na qual pretende que o procedimento armazenado para ser executada. O conjunto de dados de saída pode servir como uma forma de passar o resultado do procedimento armazenado para processamento por outra atividade subsequentes ([encadeamento atividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no pipeline. No entanto, a fábrica de dados não automaticamente escrever a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve a uma tabela SQL que o conjunto de dados de saída aponta para. Em alguns casos, o conjunto de dados de saída pode ser um **dataset fictício** (um conjunto de dados que aponta para uma tabela que realmente não espera saída do procedimento armazenado). Este conjunto de dados fictício é utilizado apenas para especificar o agendamento para execução da atividade de procedimento armazenado. 

1. Clique em **... Mais** na barra de ferramentas, clique em **novo conjunto de dados**e clique em **SQL do Azure**. **Novo conjunto de dados** na barra de comandos e selecione **SQL do Azure**.

    ![Vista de árvore com o serviço ligado](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copie/cole o seguinte script JSON para o editor de JSON.

    ```JSON
    {                
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Para implementar o conjunto de dados, clique em **implementar** na barra de comandos. Confirme se vê o conjunto de dados na vista de árvore.

    ![Vista de árvore com serviços ligados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Criar um pipeline com atividade SqlServerStoredProcedure
Agora, vamos criar um pipeline com uma atividade de procedimento armazenado. 

Tenha em atenção as seguintes propriedades: 

- O **tipo** propriedade está definida como **SqlServerStoredProcedure**. 
- O **storedProcedureName** propriedades do tipo é definido como **sp_sample** (nome do procedimento armazenado).
- O **storedProcedureParameters** secção contém um parâmetro com o nome **DateTime**. Nome e a maiúsculas e minúsculas do parâmetro no JSON tem de corresponder ao nome e a maiúsculas e minúsculas do parâmetro na definição do procedimento armazenado. Se precisar de passar nulo para um parâmetro, utilize a sintaxe: `"param1": null` (todo em minúsculas).
 
1. Clique em **... Mais** na barra de comandos e clique em **novo pipeline**.
2. Copie/cole o fragmento JSON seguinte:   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
             "start": "2017-04-02T00:00:00Z",
             "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Para implementar o pipeline, clique em **implementar** na barra de ferramentas.  

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. Clique em **X** para fechar os painéis do Editor do Data Factory e para regressar ao painel Data Factory e, em seguida, clique em **Diagrama**.

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. Na **Vista de Diagrama**, verá uma descrição geral dos pipelines e dos conjuntos de dados utilizados neste tutorial.

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Na vista de diagrama, faça duplo clique o conjunto de dados `sprocsampleout`. Verá o setor no estado pronto. Deverá haver cinco setores porque é produzido um setor para cada hora entre a hora de início e a hora de fim de JSON.

    ![Mosaico do diagrama](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando um setor estiver no **pronto** Estado, execute um `select * from sampletable` consulta na base de dados SQL do Azure para verificar que os dados inseridos à tabela através do procedimento armazenado.

   ![dados de saída](./media/data-factory-stored-proc-activity/output.png)

   Consulte [monitorizar o pipeline](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre a monitorização do Azure Data Factory pipelines.  


## <a name="specify-an-input-dataset"></a>Especifique um conjunto de dados de entrada
A explicação passo a passo, a atividade de procedimento armazenado não tem qualquer conjuntos de dados de entrada. Se especificar um conjunto de dados de entrada, a atividade de procedimento armazenado não é executada até o setor de conjunto de dados de entrada está disponível (no estado pronto). O conjunto de dados pode ser um conjunto de dados externo (que não é produzido por outra atividade no pipeline mesmo) ou um conjunto de dados interno que é produzido por uma atividade a montante (a atividade que é executado antes desta atividade). Pode especificar vários conjuntos de dados de entrada para a atividade de procedimento armazenado. Se o fizer, a atividade de procedimento armazenado é executado apenas quando todos os setores de conjunto de dados de entrada estão disponíveis (no estado pronto). O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Só é utilizada para verificar a dependência antes de iniciar a atividade de procedimento armazenado.

## <a name="chaining-with-other-activities"></a>Encadeamento com outras atividades
Se pretender encadeiam uma atividade a montante com esta atividade, especifique a saída da atividade a montante como uma entrada desta atividade. Se o fizer, a atividade de procedimento armazenado não é executado até concluir a atividade a montante e o conjunto de dados de saída da atividade a montante não está disponível (no estado pronto). Pode especificar os conjuntos de dados de saída de múltiplas atividades a montante como conjuntos de dados de entrada da atividade de procedimento armazenado. Se o fizer, a atividade de procedimento armazenado é executado apenas quando todos os setores de conjunto de dados de entrada estão disponíveis.  

No exemplo seguinte, é a saída da atividade de cópia: OutputDataset, que é uma entrada da atividade de procedimento armazenado. Por conseguinte, a atividade de procedimento armazenado não é executado até concluir a atividade de cópia e o setor OutputDataset está disponível (no estado pronto). Se especificar vários conjuntos de dados de entrada, a atividade de procedimento armazenado não é executado, até que todos os setores de conjunto de dados de entrada estão disponíveis (no estado pronto). Os conjuntos de dados de entrada não podem ser utilizados diretamente como parâmetros para a atividade de procedimento armazenado. 

Para obter mais informações sobre o encadeamento atividades, consulte [múltiplas atividades num pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{

    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }

        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Da mesma forma, para ligar a atividade de procedimento de arquivo com **atividades a jusante** (as atividades que são executadas depois de concluída a atividade de procedimento armazenado), especifique o conjunto de dados de saída da atividade de procedimento armazenado como uma entrada do a jusante atividade no pipeline.

> [!IMPORTANT]
> Quando copiar dados para a SQL Database do Azure ou SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando o **sqlWriterStoredProcedureName** propriedade. Para obter mais informações, consulte [invocar um procedimento armazenado de atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para obter detalhes sobre a propriedade, consulte os seguintes artigos de conector: [SQL Database do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
>  
> Quando copiar dados de SQL Database do Azure ou SQL Server ou o Azure SQL Data Warehouse, pode configurar **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler os dados da base de dados de origem utilizando o  **sqlReaderStoredProcedureName** propriedade. Para obter mais informações, consulte os seguintes artigos de conector: [SQL Database do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          

## <a name="json-format"></a>Formato JSON
Eis o formato JSON para definir uma atividade de procedimento armazenado:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":  
        {
            "param1": "param1Value"
            …
        }
    }
}
```

A tabela seguinte descreve estas propriedades JSON:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome | Nome da atividade |Sim |
| descrição |Texto que descreve o que é utilizada a atividade para |Não |
| tipo | Tem de ser definido como: **SqlServerStoredProcedure** | Sim |
| Entradas | Opcional. Se especificar um conjunto de dados de entrada, tem de estar disponível (no estado "Pronto") para a atividade de procedimento armazenado ser executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Só é utilizada para verificar a dependência antes de iniciar a atividade de procedimento armazenado. |Não |
| saídas | Tem de especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. Especifica o conjunto de dados de saída a **agenda** para a atividade de procedimento armazenado (hora a hora, semanais, mensais, etc.). <br/><br/>O conjunto de dados de saída tem de utilizar um **serviço ligado** que se refere a uma base de dados do SQL do Azure ou um Azure SQL Data Warehouse ou uma base de dados SQL Server na qual pretende que o procedimento armazenado para ser executada. <br/><br/>O conjunto de dados de saída pode servir como uma forma de passar o resultado do procedimento armazenado para processamento por outra atividade subsequentes ([encadeamento atividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) no pipeline. No entanto, a fábrica de dados não automaticamente escrever a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve a uma tabela SQL que o conjunto de dados de saída aponta para. <br/><br/>Em alguns casos, o conjunto de dados de saída pode ser um **dataset fictício**, que é utilizada apenas para especificar a agenda de execução da atividade de procedimento armazenado. |Sim |
| storedProcedureName |Especifique o nome do procedimento armazenado na base de dados SQL do Azure ou do armazém de dados do SQL do Azure ou SQL Server da base de dados que é representada pelo serviço ligado que utilize a tabela de saída. |Sim |
| storedProcedureParameters |Especifique os valores dos parâmetros de procedimento armazenado. Se tiver de passar nulo para um parâmetro, utilize a sintaxe: "param1": um valor nulo (todas as minúsculas). Consulte o exemplo seguinte para saber mais sobre como utilizar esta propriedade. |Não |

## <a name="passing-a-static-value"></a>Transmissão de um valor estático
Agora, vamos considere adicionar outra coluna com o nome 'Cenário de' na tabela que contém um valor estático chamado 'Documento exemplo'.

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabela:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Procedimento armazenado:**

```SQL
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Agora, passar o **cenário** parâmetro e o valor da atividade de procedimento armazenado. O **typeProperties** secção anterior exemplo aparente ser o seguinte fragmento:

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Conjunto de dados do Data Factory:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline de fábrica de dados**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```