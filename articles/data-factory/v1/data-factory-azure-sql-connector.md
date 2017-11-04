---
title: Copiar dados da SQL Database do Azure | Microsoft Docs
description: Saiba como copiar dados do Azure SQL da base de dados utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7e57003582dc6190b79e1b4eea38ec4adc1c521c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Copiar dados para e da base de dados do SQL do Azure utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-azure-sql-connector.md)
> * [Versão 2 - Pré-visualização](../connector-azure-sql-database.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector da SQL Database do Azure na V2](../connector-azure-sql-database.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados para e da SQL Database do Azure. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.  

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **da SQL Database do Azure** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **a SQL Database do Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Tipo de autenticação suportada
Conector de base de dados SQL do Azure suporta a autenticação básica.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de/para uma base de dados do SQL do Azure utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Criar um **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines. 
2. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para uma base de dados SQL do Azure, criar dois serviços ligados para ligar a sua conta de armazenamento do Azure e a SQL database do Azure à fábrica de dados. As propriedades de serviço ligado que são específicas para a SQL Database do Azure, consulte [ligado propriedades do serviço](#linked-service-properties) secção. 
3. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de blob e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a tabela SQL na base de dados SQL do Azure que contém os dados copiados a partir do blob storage. As propriedades do conjunto de dados que são específicas para o Azure Data Lake Store, consulte [propriedades do dataset](#dataset-properties) secção.
4. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. No exemplo mencionado anteriormente, utilizar BlobSource como uma origem e SqlSink como um sink para a atividade de cópia. Da mesma forma, se estiver a copiar da SQL Database do Azure para armazenamento de Blobs do Azure, utilizar SqlSource e BlobSink na atividade de cópia. Para as propriedades da atividade de cópia que são específicas para a SQL Database do Azure, consulte [copiar propriedades da atividade](#copy-activity-properties) secção. Para obter detalhes sobre como utilizar um arquivo de dados como uma origem ou de um receptor de mensagens em fila, clique na ligação na secção anterior para o arquivo de dados.

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para/de uma base de dados do SQL do Azure, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-sql-database) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para a SQL Database do Azure: 

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
Um SQL do Azure serviço ligado liga uma base de dados SQL do Azure à fábrica de dados. A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço ligado SQL do Azure.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **AzureSqlDatabase** |Sim |
| connectionString |Especificar as informações necessárias para ligar à instância do SQL Database do Azure para a propriedade connectionString. Apenas autenticação básica é suportada. |Sim |

> [!IMPORTANT]
> Configurar [Firewall de base de dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) o servidor de base de dados para [permitir que os serviços do Azure aceder ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se estiver a copiar dados para a SQL Database do Azure a partir de fora do Azure incluindo de origens de dados no local com o gateway de fábrica de dados, configure o intervalo de endereços IP adequado para a máquina que está a enviar dados para a SQL Database do Azure.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados para representar os dados de entrada ou saídos numa base de dados SQL do Azure, definir a propriedade de tipo do conjunto de dados para: **AzureSqlTable**. Definir o **linkedServiceName** serviço ligado de propriedade do conjunto de dados com o nome do SQL do Azure.  

Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

A secção de typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **AzureSqlTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância SQL Database do Azure que o serviço ligado refere-se. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia demora apenas uma entrada e produz saída de apenas um.

Enquanto, propriedades disponíveis no **typeProperties** secção da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Se estiver a mover dados de uma base de dados SQL do Azure, defina o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a mover dados para uma base de dados SQL do Azure, definir o tipo de sink na atividade de cópia para **SqlSink**. Esta secção fornece uma lista de propriedades suportadas por SqlSource e SqlSink.

### <a name="sqlsource"></a>SqlSource
Na atividade de cópia, quando a origem é do tipo **SqlSource**, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados a partir da tabela de origem. |Nome do procedimento armazenado. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

Se o **sqlReaderQuery** está especificado para o SqlSource, a atividade de cópia executa esta consulta contra a origem de SQL Database do Azure para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para criar uma consulta (`select column1, column2 from mytable`) para executar a base de dados do SQL do Azure. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

> [!NOTE]
> Quando utiliza **sqlReaderStoredProcedureName**, terá de especificar um valor para o **tableName** propriedade no conjunto de dados JSON. Não existem nenhum validações executadas apesar desta tabela.
>
>

### <a name="sqlsource-example"></a>Exemplo de SqlSource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**A definição de procedimento armazenado:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia para executar de forma a que os dados de um setor específico é limpa. Para obter mais informações, consulte [cópia repetíveis](#repeatable-copy). |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a atividade de cópia preencher com o identificador de setor automaticamente gerado, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. Para obter mais informações, consulte [cópia repetíveis](#repeatable-copy). |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado dados upserts (inserções/atualizações) para a tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia faz com que os dados a ser movidos disponível numa tabela temporária com este tipo de tabela. Código do procedimento armazenado, em seguida, pode intercalar os dados que está a ser copiados com dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="sqlsink-example"></a>Exemplo de SqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Exemplos JSON para copiar dados para e da base de dados do SQL Server
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados para e da SQL Database do Azure e o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exemplo: Copiar dados de SQL Database do Azure para BLOBs do Azure
Define o mesmo as seguintes entidades do Data Factory:

1. Um serviço ligado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureSqlTable](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de séries de tempo (hora a hora, diária, etc.) de uma tabela na base de dados SQL do Azure para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.  

**Serviço ligado do SQL Database do Azure:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Consulte o [serviço ligado de SQL do Azure](#linked-service) secção para obter a lista de propriedades suportadas por este serviço ligado.

**Serviço ligado do armazenamento de Blobs do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Consulte o [Blob do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) artigo para obter a lista de propriedades suportadas por este serviço ligado.


**Conjunto de dados de entrada SQL do Azure:**

O exemplo assume que criou uma tabela "MyTable" SQL do Azure e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

A definição "external": "true" informa o serviço do Azure Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
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

Consulte o [propriedades de tipo de conjunto de dados SQL do Azure](#dataset) secção para obter a lista de propriedades suportado por este tipo de conjunto de dados.  

**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Consulte o [propriedades do tipo de conjunto de dados de Blobs do Azure](data-factory-azure-blob-connector.md#dataset-properties) secção para obter a lista de propriedades suportado por este tipo de conjunto de dados.  

**Uma atividade de cópia num pipeline com a origem SQL e o sink de Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **SqlReaderQuery** propriedade seleciona os dados na última hora para copiar.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
No exemplo, **sqlReaderQuery** especificado para o SqlSource. A atividade de cópia executa esta consulta na origem de SQL Database do Azure para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para criar uma consulta para executar a base de dados do SQL do Azure. Por exemplo: `select column1, column2 from mytable`. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

Consulte o [origem Sql](#sqlsource) secção e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para a lista de propriedades suportadas por SqlSource e BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exemplo: Copiar dados de Blobs do Azure para a SQL Database do Azure
O exemplo define as entidades da fábrica de dados seguintes:  

1. Um serviço ligado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureSqlTable](#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlSink](#copy-activity-properties).

As cópias de exemplo séries de tempo (hora a hora, diária, etc.) de dados do Azure blob para uma tabela no Azure SQL da base de dados a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado SQL do Azure:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Consulte o [serviço ligado de SQL do Azure](#linked-service) secção para obter a lista de propriedades suportadas por este serviço ligado.

**Serviço ligado do armazenamento de Blobs do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Consulte o [Blob do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) artigo para obter a lista de propriedades suportadas por este serviço ligado.


**Conjunto de dados de entrada Blob do Azure:**

Dados são captados um blob de novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome de ficheiro utiliza a parte de hora a hora de início. "external": "true" definição informa o serviço fábrica de dados que esta tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
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
Consulte o [propriedades do tipo de conjunto de dados de Blobs do Azure](data-factory-azure-blob-connector.md#dataset-properties) secção para obter a lista de propriedades suportado por este tipo de conjunto de dados.

**Conjunto de dados de saída de base de dados SQL do Azure:**

O exemplo copia dados para uma tabela com o nome "MyTable" no SQL do Azure. Crie a tabela SQL do Azure com o mesmo número de colunas, como espera que o ficheiro CSV de Blob que contém. Novas linhas são adicionadas à tabela de cada hora.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Consulte o [propriedades de tipo de conjunto de dados SQL do Azure](#dataset) secção para obter a lista de propriedades suportado por este tipo de conjunto de dados.

**Uma atividade de cópia num pipeline com a origem de Blob e o sink do SQL Server:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
Consulte o [Sink do SQL Server](#sqlsink) secção e [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) para a lista de propriedades suportadas por SqlSink e BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados de destino
Esta secção fornece um exemplo para copiar dados a partir de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

```SQL
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Tabela de destino:**

```SQL
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```
Tenha em atenção que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definição de JSON do conjunto de dados de destino**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }    
}
```

Tenha em atenção que, como a tabela de origem e destino têm esquemas diferentes (o destino tem uma coluna adicional com a identidade). Neste cenário, tem de especificar **estrutura** propriedade na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar um procedimento armazenado do sink do SQL Server
Para obter um exemplo de invocar um procedimento armazenado do sink do SQL Server numa atividade de cópia de um pipeline, consulte [invocar um procedimento armazenado para sink do SQL Server na atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md) artigo. 

## <a name="type-mapping-for-azure-sql-database"></a>Mapeamento de tipos para a SQL Database do Azure
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo atividade de cópia executa conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de passo 2:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Ao mover dados para e da SQL Database do Azure, são utilizados os seguintes mapeamentos do tipo SQL para o tipo de .NET e vice-versa. O mapeamento está mesmo que o mapeamento de tipos de dados do SQL Server para ADO.NET.

| Tipo de motor de base de dados do SQL Server | Tipo de .NET framework |
| --- | --- |
| bigint |Int64 |
| Binário |Byte] |
| bits |Valor booleano |
| char |Cadeia, Char [] |
| Data |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte] |
| Número de vírgula flutuante |duplo |
| Imagem |Byte] |
| Int |Int32 |
| dinheiro |Decimal |
| nchar |Cadeia, Char [] |
| ntext |Cadeia, Char [] |
| um valor numérico |Decimal |
| nvarchar |Cadeia, Char [] |
| real |Único |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| em smallmoney |Decimal |
| sql_variant |Objeto * |
| Texto |Cadeia, Char [] |
| hora |TimeSpan |
| carimbo de data/hora |Byte] |
| tinyint |Bytes |
| uniqueidentifier |GUID |
| varbinary |Byte] |
| varchar |Cadeia, Char [] |
| xml |XML |

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem em colunas no conjunto de dados do sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Cópia repetíveis
Quando copiar dados para a base de dados do SQL Server, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Para efetuar um UPSERT em vez disso, consulte [Repeatable escrever SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artigo. 

Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
