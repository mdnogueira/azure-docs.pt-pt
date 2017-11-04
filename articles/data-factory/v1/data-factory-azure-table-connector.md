---
title: Mover os dados da tabela do Azure | Microsoft Docs
description: Saiba como mover dados do armazenamento de tabelas do Azure utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1379cbb0ea9b01a20d1974ed08e93b4872ffd92b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover dados para e da tabela do Azure utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-azure-table-connector.md)
> * [Versão 2 - Pré-visualização](../connector-azure-table-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector do Table Storage do Azure no V2](../connector-azure-table-storage.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados do Table Storage do Azure. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia. 

Pode copiar dados a partir de qualquer arquivo de dados de origem suportada para o Table Storage do Azure ou a partir do armazenamento de tabelas do Azure para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como origens ou sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. 

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de um Table Storage do Azure utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um Table Storage do Azure, consulte [exemplos JSON](#json-examples) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas ao Table Storage do Azure: 

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
Existem dois tipos de serviços ligados, que pode utilizar para ligar um armazenamento de Blobs do Azure a um Azure data factory. São: **AzureStorage** serviço ligado e **AzureStorageSas** serviço ligado. O serviço ligado do Storage do Azure fornece a fábrica de dados com acesso global para o armazenamento do Azure. Enquanto, ligados a SAS de armazenamento do Azure (assinatura de acesso partilhado) o serviço fornece a fábrica de dados com acesso restrito/vínculo de tempo para o armazenamento do Azure. Não existem não existem outras diferenças entre esses dois serviços ligados. Escolha o serviço ligado que se adapta às suas necessidades. As secções seguintes fornecem mais detalhes sobre estes dois serviços ligados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

A secção de typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **AzureTable** tem as seguintes propriedades.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados do Azure tabela pelo serviço ligado refere-se. |Sim. Quando um tableName for especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se um azureTableSourceQuery também está especificado, os registos da tabela que satisfaça a consulta são copiados para o destino. |

### <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados
Para os arquivos de dados sem esquema, tais como tabelas do Azure, o serviço Data Factory infere o esquema de uma das seguintes formas:

1. Se especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory honra esta estrutura como o esquema. Neste caso, se uma linha não contém um valor para uma coluna, é fornecido um valor nulo para o mesmo.
2. Se não especificar a estrutura dos dados ao utilizar o **estrutura** propriedade na definição do conjunto de dados, o Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha contém o esquema completo, são omitidas algumas colunas nos resultados da operação de cópia.

Por conseguinte, para origens de dados sem esquema, a melhor prática é especificar a estrutura dos dados utilizando o **estrutura** propriedade.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, conjuntos de dados e as políticas estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis na secção typeProperties da atividade por outro lado variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

**AzureTableSource** suporta as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta de tabela do Azure. Veja exemplos na secção seguinte: |Não. Quando um tableName for especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se um azureTableSourceQuery também está especificado, os registos da tabela que satisfaça a consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indica se swallow a exceção da tabela não existe. |VERDADEIRO<br/>FALSO |Não |

### <a name="azuretablesourcequery-examples"></a>Exemplos de azureTableSourceQuery
Se a coluna de tabelas do Azure é do tipo de cadeia:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Se a coluna de tabelas do Azure é do tipo datetime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** suporta as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Partição chave valor predefinido que pode ser utilizado pelo sink. |Um valor de cadeia. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como chaves de partição. Se não for especificado, AzureTableDefaultPartitionKeyValue é utilizado como a chave de partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores da coluna são utilizados como chave de linha. Se não for especificado, utilize um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo de inserir os dados na tabela do Azure.<br/><br/>Esta propriedade controla se as linhas existentes na tabela de saída com correspondentes chaves de partição e a linha tem os respetivos valores substituído ou intercaladas. <br/><br/>Para saber mais sobre como funcionam estas definições (intercalação e substitua), consulte [inserção ou entidade de intercalação](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) tópicos. <br/><br> Esta definição aplica-se ao nível da linha, não o nível de tabela e nenhuma opção elimina as linhas na tabela de saída que não existem na entrada. |Intercalar (predefinição)<br/>Substituir |Não |
| WriteBatchSize |Insere dados na tabela do Azure quando é atingido o writeBatchSize ou writeBatchTimeout. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure, quando é atingido o writeBatchSize ou writeBatchTimeout |TimeSpan<br/><br/>Exemplo: "00: 20:00" (20 minutos) |Não (seg 90 de valor predefinido para o tempo limite de predefinição do cliente armazenamento) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapear uma coluna de origem para uma coluna de destino utilizando o tradutor propriedade JSON antes de poder utilizar a coluna de destino como o azureTablePartitionKeyName.

No exemplo seguinte, a coluna de origem DivisionID está mapeada para a coluna de destino: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
O DivisionID está especificada como a chave de partição.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Exemplos JSON
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados de e para o Table Storage do Azure e a base de dados de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens para qualquer um dos suportado sinks. Para obter mais informações, consulte a secção "arquivos de dados suportadas e formatos" em [mover dados utilizando a atividade de cópia](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exemplo: Copiar dados de tabelas do Azure para BLOBs do Azure
O exemplo seguinte mostra:

1. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (utilizado para a tabela & blob).
2. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureTable](#dataset-properties).
3. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [AzureTableSource](#activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia os dados que pertencem à partição do predefinido, uma tabela do Azure para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado do storage do Azure:**

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
O Azure Data Factory suportam dois tipos de serviços de armazenamento do Azure ligado: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de ligação que inclua a chave de conta e para um posterior, especificar o Uri de assinatura de acesso partilhado (SAS). Consulte [serviços ligados](#linked-service-properties) secção para obter detalhes.  

**Conjunto de dados de entrada tabela do Azure:**

O exemplo assume que criou uma tabela "MyTable" na tabela do Azure.

A definição "external": "true" informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Atividade de cópia de um pipeline com AzureTableSource e BlobSink:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **AzureTableSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada com **AzureTableSourceQuery** propriedade seleciona os dados da partição de predefinição a cada hora para copiar.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                      {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                      {
                            "name": "AzureBlobOutput"
                      }
                ],
                "typeProperties": {
                      "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exemplo: Copiar dados de Blobs do Azure para tabelas do Azure
O exemplo seguinte mostra:

1. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (utilizado para a tabela & blob)
2. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureTable](#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureTableSink](#copy-activity-properties).

As cópias de exemplo séries de tempo dados a partir de um Azure blob para um Azure hora a hora de tabela. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado do storage do Azure (para tabelas do Azure e BLOBs):**

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

O Azure Data Factory suportam dois tipos de serviços de armazenamento do Azure ligado: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de ligação que inclua a chave de conta e para um posterior, especificar o Uri de assinatura de acesso partilhado (SAS). Consulte [serviços ligados](#linked-service-properties) secção para obter detalhes.

**Conjunto de dados de entrada Blob do Azure:**

Dados são captados um blob de novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome de ficheiro utiliza a parte de hora a hora de início. "external": "true" definição informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Conjunto de dados de saída do tabela do Azure:**

O exemplo copia dados para uma tabela com o nome "MyTable" na tabela do Azure. Crie uma tabela do Azure com o mesmo número de colunas como espera que o ficheiro CSV de Blob que contém. Novas linhas são adicionadas à tabela de cada hora.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Atividade de cópia de um pipeline com BlobSource e AzureTableSink:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **AzureTableSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Mapeamento de tipos para a tabela do Azure
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de dois passos.

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Quando mover dados para & tabela do Azure, o seguinte [mapeamentos definidos pelo serviço de Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) são utilizadas de tipos de OData de tabela do Azure para o tipo de .NET e vice-versa.

| Tipo de dados de OData | Tipo .NET | Detalhes |
| --- | --- | --- |
| Edm.Binary |Byte] |Uma matriz de bytes até 64 KB. |
| Edm.Boolean |bool |Valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits, expressado como Hora Universal Coordenada (UTC). Início do intervalo suportado de DateTime de 12:00, 1 de Janeiro de 1601 a.d. (E.C.), UTC. O intervalo de termina a 31 de Dezembro de 9999. |
| Edm.Double |duplo |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |GUID |Um identificador exclusivo global de 128 bits. |
| Edm.Int32 |Int32 |Um número inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um número inteiro de 64 bits. |
| Edm.String |Cadeia |Um valor com codificação UTF-16. Valores de cadeia poderão ser até 64 KB. |

### <a name="type-conversion-sample"></a>Exemplo de conversão de tipo
O exemplo seguinte é para copiar dados a partir de um Blob do Azure para tabelas do Azure com conversões de tipo.

Suponha que o conjunto de dados de Blob está num formato CSV e contém três colunas. Uma delas é uma coluna de datetime com um formato de datetime personalizado utilizando abreviados nomes francês para um dia da semana.

Defina o conjunto de dados de origem do Blob da seguinte forma juntamente com as definições de tipo para as colunas.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
         "structure":
          [
                { "name": "userid", "type": "Int64"},
                { "name": "name", "type": "String"},
                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Tendo em conta o mapeamento do tipo do tipo de OData de tabela do Azure para o tipo de .NET, seriam definidos a tabela em tabelas do Azure com o esquema seguinte.

**Esquema da tabela do Azure:**

| Nome da coluna | Tipo |
| --- | --- |
| ID de utilizador |Edm.Int64 |
| nome |Edm.String |
| lastlogindate |Edm.DateTime |

Em seguida, defina o conjunto de dados de tabelas do Azure da seguinte forma. Não é necessário especificar a secção "estrutura" com as informações de tipo uma vez que as informações de tipo já foi especificadas no arquivo de dados subjacente.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

Neste caso, Data Factory automaticamente escreva conversões, incluindo o campo de Datetime com o formato de datetime personalizado utilizando a cultura "fr-fr" ao mover os dados a partir do Blob para tabelas do Azure.

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Para saber mais sobre os principais fatores que desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo, consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md).
