---
title: Mover os dados da base de dados do Azure Cosmos | Microsoft Docs
description: "Saiba como mover dados da coleção de BD do Cosmos Azure utilizando o Azure Data Factory"
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 32b72577002962f049f446d6f3c2353189867e92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Mover dados para e da base de dados do Cosmos do Azure utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-azure-documentdb-connector.md)
> * [Versão 2 - Pré-visualização](../connector-azure-cosmos-db.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector de base de dados do Azure Cosmos no V2](../connector-azure-cosmos-db.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados do Azure Cosmos BD (API do DocumentDB). Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia. 

Pode copiar dados a partir de qualquer arquivo de dados de origem suportada para a base de dados do Azure Cosmos ou da base de dados do Azure Cosmos qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como origens ou sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. 

> [!IMPORTANT]
> Conector Cosmos BD Azure suportam apenas a API do DocumentDB.

Para copiar dados como-é para/de ficheiros JSON ou de outra coleção Cosmos DB, consulte [documentos JSON de importação/exportação](#importexport-json-documents).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados do Azure Cosmos DB utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados da base de dados do Cosmos, consulte [exemplos JSON](#json-examples) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas à base de dados do Cosmos: 

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço de base de dados do Cosmos Azure ligado.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **DocumentDb** |Sim |
| connectionString |Especifique as informações necessárias para estabelecer ligação à base de dados do Azure Cosmos DB. |Sim |

Exemplo:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

A secção de typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **DocumentDbCollection** tem as seguintes propriedades.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| CollectionName |Nome da coleção de documentos de BD do Cosmos. |Sim |

Exemplo:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados
Para os arquivos de dados sem esquema, tais como a base de dados do Azure Cosmos, o serviço Data Factory infere o esquema de uma das seguintes formas:  

1. Se especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory honra esta estrutura como o esquema. Neste caso, se uma linha não contém um valor para uma coluna, irá ser fornecido um valor nulo para o mesmo.
2. Se não especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha contém o esquema completo, algumas colunas estará em falta no resultado da operação de cópia.

Por conseguinte, para origens de dados sem esquema, a melhor prática é especificar a estrutura dos dados utilizando o **estrutura** propriedade.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia demora apenas uma entrada e produz saída de apenas um.

Propriedades disponíveis na secção typeProperties da atividade por outro lado variam com cada tipo de atividade e, em caso de atividade de cópia variam consoante os tipos de origens e sinks.

Em caso de atividade de cópia quando a origem é do tipo **DocumentDbCollectionSource** as propriedades seguintes estão disponíveis no **typeProperties** secção:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler os dados. |Suportado pelo Azure Cosmos DB de cadeia de consulta. <br/><br/>Exemplo:`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a instrução de SQL que é executada:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Caráter especial para indicar que o documento está aninhado |Qualquer caráter. <br/><br/>BD do Cosmos do Azure é um arquivo de NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. O Azure Data Factory permite que o utilizador denota hierarquia através de nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia irá gerar o objeto "Name" com elementos três subordinados em primeiro lugar, média e o último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela. |Não |

**DocumentDbCollectionSink** suporta as seguintes propriedades:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |É necessário um caráter especial no nome de coluna de origem para indicar que documentos aninhados. <br/><br/>Por exemplo acima: `Name.First` na saída tabela produz a seguinte estrutura JSON no documento de BD do Cosmos:<br/><br/>"Nome": {<br/>    "First": "João"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>Valor predefinido é `.` (ponto final). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>Valor predefinido é `.` (ponto final). |
| WriteBatchSize |Número de pedidos paralelos ao serviço de base de dados do Azure Cosmos para criar documentos.<br/><br/>Pode otimizar o desempenho ao copiar dados do Cosmos DB utilizando esta propriedade. Pode esperar um desempenho melhor quando aumenta writeBatchSize porque são enviados mais pedidos paralelos à base de dados do Cosmos. No entanto, terá de evitar limitação que pode acionar a mensagem de erro: "taxa é grande pedido".<br/><br/>Limitação é decidida por um número de fatores, incluindo o tamanho de documentos, número de termos de documentos, a indexação de política de coleção de destino, etc. Para operações de cópia, pode utilizar uma coleção melhor (por exemplo, S3) para ter o maior débito disponível (2,500 pedidos unidades por segundo). |Número inteiro |Não (predefinição: 5) |
| writeBatchTimeout |De tempo de espera para a operação seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

## <a name="importexport-json-documents"></a>Documentos JSON para importar/exportar
Utilizar este conector Cosmos DB, pode facilmente

* Importe documentos JSON de várias origens para Cosmos DB, incluindo o Blob do Azure, Azure Data Lake, sistema de ficheiros no local ou outros arquivos de ficheiros suportados pelo Azure Data Factory.
* Exporte documentos JSON do Cosmos DB collecton em vários arquivos de ficheiros.
* Migrar dados entre duas coleções de BD do Cosmos como-é.

Para obter uma cópia deste tipo desconhecidas do esquema, 
* Ao utilizar o Assistente para copiar, verifique o **"Exportar como-é ficheiros JSON ou coleção de BD do Cosmos"** opção.
* Quando utilizar JSON editar, não especificar a secção "estrutura" dos DataSets de BD do Cosmos nem origem de propriedade de "nestingSeparator" na base de dados do Cosmos/sink na atividade de cópia. Para importar a partir de / exportar para ficheiros JSON, no conjunto de dados de arquivo de ficheiro especificar tipo de formato como "JsonFormat", "filePattern" de configuração e ignorar as definições do formato de rest, consulte [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format) secção em detalhes.

## <a name="json-examples"></a>Exemplos JSON
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados para e da base de dados do Azure Cosmos e armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exemplo: Copiar dados da base de dados do Azure Cosmos para Blob do Azure
O exemplo abaixo mostra:

1. Um serviço ligado do tipo [DocumentDb](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [DocumentDbCollection](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [DocumentDbCollectionSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia os dados na base de dados do Azure Cosmos para Blob do Azure. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**BD do Azure do Cosmos serviço ligado:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
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
**Conjunto de dados de entrada Document DB do Azure:**

O exemplo assume que tem uma coleção designada **pessoa** numa base de dados do Azure Cosmos DB.

A definição "external": "true" e especificando externalData informações da política de serviço do Azure Data Factory que a tabela é externa à fábrica de dados e não são produzidos por uma atividade no factory de dados.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Conjunto de dados de saída do Blob do Azure:**

Dados são copiados para um blob de novo a cada hora com o caminho para o blob ao refletir o datetime específico com granularidade de hora.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Documento JSON de exemplo na coleção de pessoa numa base de dados do Cosmos DB:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
BD do cosmos suporta a consulta de documentos utilizando um SQL Server como a sintaxe por hierárquicos documentos JSON.

Exemplo: 

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

O pipeline seguinte copia dados da coleção na base de dados de base de dados do Azure Cosmos pessoa para um blob do Azure. Como parte da atividade de cópia de entrada e saída conjuntos de dados tem sido especificados.  

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Exemplo: Copiar dados de Blobs do Azure à base de dados do Azure Cosmos 
O exemplo abaixo mostra:

1. Um serviço ligado do tipo [DocumentDb](#azure-documentdb-linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).

O exemplo copia dados a partir do Azure blob BD do Cosmos do Azure. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

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
**BD do Azure do Cosmos serviço ligado:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Conjunto de dados de entrada Blob do Azure:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**BD do Azure do Cosmos o conjunto de dados de saída:**

O exemplo copia dados para uma coleção designada "Pessoa".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
O pipeline seguinte copia dados de Blob do Azure para a coleção de pessoa na base de dados do Cosmos. Como parte da atividade de cópia de entrada e saída conjuntos de dados tem sido especificados.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          }
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Se a entrada de BLOBs de exemplo é como

```
1,John,,Doe
```
Em seguida, a saída JSON na base de dados do Cosmos será como:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
BD do Cosmos do Azure é um arquivo de NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. O Azure Data Factory permite que o utilizador denota hierarquia através de **nestingSeparator**, que é "." Neste exemplo. Com o separador, a atividade de cópia irá gerar o objeto "Name" com elementos três subordinados em primeiro lugar, média e o último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela.

## <a name="appendix"></a>Apêndice
1. **Pergunta:** a atualização de suporte de atividade de cópia de registos existentes?

    **Resposta:** não.
2. **Pergunta:** como funciona uma repetição de uma cópia para o grau de base de dados do Azure Cosmos com já copiados registos?

    **Resposta:** se registos tem um campo de "ID" e a operação de cópia tenta inserir um registo com o mesmo ID, a operação de cópia emite um erro.  
3. **Pergunta:** suporta Data Factory [intervalo ou criação de partições de dados com base em hash](../../cosmos-db/documentdb-partition-data.md)?

    **Resposta:** não.
4. **Pergunta:** posso especificar mais do que uma coleção de BD do Cosmos do Azure para uma tabela?

    **Resposta:** não. Apenas uma coleção pode ser especificada neste momento.

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
