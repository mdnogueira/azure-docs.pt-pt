<properties
   pageTitle="Carregar dados do armazenamento de blobs para o Azure SQL Data Warehouse (Azure Data Factory) | Microsoft Azure"
   description="Saiba como carregar dados com o Azure Data Factory"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Carregar dados do armazenamento de blobs para o Azure SQL Data Warehouse (Azure Data Factory)

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

 Este tutorial mostra como criar um pipeline no Azure Data Factory para mover dados do Blob de Armazenamento do Azure para o SQL Data Warehouse. Com os passos seguintes, irá:

+ Configurar dados de exemplo num Blob de Armazenamento do Azure.
+ Ligar recursos ao Azure Data Factory.
+ Criar um pipeline para mover dados de Armazenamento de Blobs para o SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## Antes de começar

Para se familiarizar com o Azure Data Factory, consulte [Introdução ao Azure Data Factory][].

### Criar ou identificar recursos

Antes de iniciar este tutorial, é necessário ter os seguintes recursos.

   + **Blob de Armazenamento do Azure**: este tutorial utiliza o Blob de Armazenamento do Azure como a origem de dados para o pipeline do Azure Data Factory e, por isso, tem de ter um disponível para armazenar os dados de exemplo. Se ainda não tem um, saiba como [Criar uma conta de armazenamento][].

   + **SQL Data Warehouse**: neste tutorial, os dados são movidos do Blob de Armazenamento do Azure para o SQL Data Warehouse, tendo, por isso, de dispor de um armazém de dados online carregado com os dados de exemplo AdventureWorksDW. Se ainda não tiver um armazém de dados, saiba como [aprovisionar um][Criar um SQL Data Warehouse]. Se tiver um armazém de dados, mas não o tiver aprovisionado com os dados de exemplo, pode [carregá-los manualmente][Carregar dados de exemplo para o SQL Data Warehouse].

   + **Azure Data Factory**: o Azure Data Factory irá concluir o carregamento atual e, por isso, tem de ter um que possa utilizar para criar o pipeline de movimento de dados. Se ainda não tem um, saiba como criar um no passo 1 de [Introdução ao Azure Data Factory (Data Factory Editor)][].

   + **AZCopy**: o AZCopy é necessário para copiar os dados de exemplo do seu cliente local para o Blob de Armazenamento do Azure. Para obter instruções de instalação, consulte [Documentação do AZCopy][].

## Passo 1: copiar dados de exemplo para o Blob de Armazenamento do Azure

Assim que tiver tudo pronto, está pronto para copiar dados de exemplo para o Blob de Armazenamento do Azure.

1. [Transferir os dados de exemplo][]. Estes dados irão adicionar mais três anos de dados de vendas aos dados de exemplo do AdventureWorksDW.

2. Utilize este comando do AZCopy para copiar os três anos de dados para o Blob de Armazenamento do Azure.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## Passo 2. ligar recursos ao Azure Data Factory

Agora que os dados estão no local, podemos criar o pipeline do Azure Data Factory para mover os dados do armazenamento de blobs do Azure para o SQL Data Warehouse.

Para começar, abra o [Portal do Azure][] e selecione a sua fábrica de dados a partir do menu do lado esquerdo.

### Passo 2.1: Criar o Serviço Ligado

Ligue a sua conta de armazenamento do Azure e o SQL Data Warehouse à sua fábrica de dados.  

1. Em primeiro lugar, inicie o processo de registo, clicando na secção “Serviços Ligados” da fábrica de dados e, em seguida, clique em “Novo arquivo de dados”. Escolha um nome para registar o armazenamento no Azure, selecione Armazenamento do Azure como o tipo e, em seguida, introduza o Nome de Conta e a Chave da Conta.

2. Para registar o SQL Data Warehouse, navegue até à secção “Criar e implementar”, selecione “Novo arquivo de dados” e, em seguida, “Azure SQL Data Warehouse”. Copie e cole neste modelo e, em seguida, preencha as suas informações específicas.

```JSON
{
    "name": "<Linked Service Name>",
    "properties": {
        "description": "",
        "type": "AzureSqlDW",
        "typeProperties": {
             "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
         }
    }
}
```

### Passo 2.2: definir o conjunto de dados

Depois de criar os serviços ligados, temos de definir os conjuntos de dados.  Neste caso, significa definir a estrutura dos dados que estão a ser movidos do seu armazenamento para o seu armazém de dados.  Pode ler mais sobre como criar

1. Inicie este processo, navegando para a secção “Criar e implementar” da fábrica de dados.

2. Clique em “Novo conjunto de dados” e, em seguida, em “Armazenamento de blobs do Azure” para ligar o seu armazenamento à fábrica de dados.  Pode utilizar o script abaixo para definir os dados no armazenamento de blobs do Azure:

```JSON
{
    "name": "<Dataset Name>",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "<linked storage name>",
        "typeProperties": {
            "folderPath": "<containter name>",
            "fileName": "FactInternetSales.csv",
            "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```


3. Agora, iremos também definir o nosso conjunto de dados para o SQL Data Warehouse.  Vamos começar da mesma forma, clicando em “Novo conjunto de dados” e, em seguida, em “Azure SQL Data Warehouse”.

```JSON
{
    "name": "DWDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "FactInternetSales"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

## Passo 3: criar e executar o pipeline

Por último, iremos configurar e executar o pipeline no Azure Data Factory.  Esta é a operação que irá concluir o movimento de dados.  Pode encontrar uma visão completa das operações que pode realizar com o SQL Data Warehouse e o Azure Data Factory [aqui][Mover dados para e do Azure SQL Data Warehouse, utilizando o Azure Data Factory].

Na secção “Criar e implementar”, clique agora em “Mais comandos” e, em seguida, em “Novo pipeline”.  Depois de criar o pipeline, pode utilizar o código abaixo para transferir os dados para o armazém de dados:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
              }
            ],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## Passos seguintes

Para saber mais, comece por visualizar:

- [Percurso de aprendizagem do Azure Data Factory][].
- [Azure SQL Data Warehouse Connector][]. Este é o tópico de referência principal para utilizar o Azure Data Factory com o Azure SQL Data Warehouse.


Estes tópicos fornecem informações detalhadas sobre o Azure Data Factory. Abrangem a Base de Dados SQL do Azure ou o HDinsight, mas as informações também se aplicam ao Azure SQL Data Warehouse.

- [Tutorial: Introdução ao Azure Data Factory][] Este é o tutorial principal para processamento de dados com o Azure Data Factory. Neste tutorial, irá criar o seu primeiro pipeline, que utiliza o HDInsight para transformar e analisar registos Web mensalmente. Tenha em atenção que não existe nenhuma atividade de cópia neste tutorial.
- [Tutorial: copiar dados do Blob de Armazenamento do Azure para a Base de Dados SQL do Azure][]. Neste tutorial, irá criar um pipeline no Azure Data Factory para copiar dados do Blob de Armazenamento do Azure para a Base de Dados SQL do Azure.


<!--Image references-->

<!--Article references-->
[Documentação do AZCopy]: ../storage/storage-use-azcopy.md
[Azure SQL Data Warehouse Connector]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Criar um SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Criar uma conta de armazenamento]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Introdução ao Azure Data Factory (Data Factory Editor)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introdução ao Azure Data Factory]: ../data-factory/data-factory-introduction.md
[Carregar dados de exemplo para o SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Mover dados para e do Azure SQL Data Warehouse, utilizando o Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Tutorial: copiar dados do Blob de Armazenamento do Azure para a Base de Dados SQL do Azure]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Tutorial: Introdução ao Azure Data Factory]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Percurso de aprendizagem do Azure Data Factory]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Portal do Azure]: https://portal.azure.com
[Transferir os dados de exemplo]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv



<!--HONumber=Sep16_HO3-->


