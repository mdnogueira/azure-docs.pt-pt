---
title: Copiar dados da base de dados do Cosmos do Azure utilizando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de arquivos de dados de origem suportada para a base de dados do Azure Cosmos (ou) da base de dados do Cosmos como arquivos de sink suportadas utilizando o Data Factory.
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 291ca51c83233294d882a94f886bc874e13e97be
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Copiar dados para ou da base de dados do Cosmos do Azure utilizando o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-azure-documentdb-connector.md)
> * [Versão 2 - Pré-visualização](connector-azure-cosmos-db.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de origem e de base de dados do Azure Cosmos (API do DocumentDB). Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [connnector de base de dados do Azure Cosmos no V1](v1/data-factory-azure-documentdb-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados do Azure Cosmos para qualquer arquivo de dados suportados sink ou copiar dados a partir de qualquer arquivo de dados de origem suportada para a base de dados do Azure Cosmos. Para obter uma lista dos arquivos de dados suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de base de dados do Azure Cosmos suporta:

- BD do cosmos [DocumentDB API](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-introduction).
- Importar/exportar documentos JSON como-está ou copiar dados de/para o conjunto de dados em tabela por exemplo, base de dados do SQL Server, ficheiros CSV, etc.

Para copiar documentos como-é para/de ficheiros JSON ou de outra coleção Cosmos DB, consulte [documentos JSON de importação/exportação](#importexport-json-documents).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas à base de dados do Azure Cosmos.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço de base de dados do Cosmos do Azure ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **CosmosDb**. | Sim |
| connectionString |Especifique as informações necessárias para estabelecer ligação à base de dados do Azure Cosmos DB. Tenha em atenção de que tem de especificar as informações da base de dados na cadeia de ligação conforme abaixo de exemplo. Marcar este campo como um SecureString. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados do Azure Cosmos DB.

Para copiar dados de/para a base de dados do Azure Cosmos, defina a propriedade de tipo do conjunto de dados para **DocumentDbCollection**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **DocumentDbCollection** |Sim |
| CollectionName |Nome da coleção de documentos de BD do Cosmos. |Sim |

**Exemplo:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados

Para os arquivos de dados sem esquema, tais como a base de dados do Azure Cosmos, a atividade de cópia infere o esquema de uma das seguintes formas. Por conseguinte, a menos que pretenda [documentos JSON como para importar/exportar-é](#importexport-json-documents), a melhor prática é para especificar a estrutura dos dados no **estrutura** secção.

1. Se especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory honra esta estrutura como o esquema. Neste caso, se uma linha não contém um valor para uma coluna, irá ser fornecido um valor nulo para o mesmo.
2. Se não especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha contém o esquema completo, algumas colunas estará em falta no resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por base de dados do Azure Cosmos origem e dependente.

### <a name="azure-cosmos-db-as-source"></a>BD do Azure do Cosmos como origem

Para copiar dados da base de dados do Azure Cosmos, defina o tipo de origem na atividade de cópia para **DocumentDbCollectionSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **DocumentDbCollectionSource** |Sim |
| consulta |Especifique a consulta de base de dados do Cosmos ler os dados.<br/><br/>Exemplo:`SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a instrução de SQL que é executada:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Caráter especial para indicar que o documento está aninhado e como a flattern o resultado definido.<br/><br/>Por exemplo, se uma consulta de base de dados do Cosmos devolve um resultado aninhado `"Name": {"First": "John"}`, atividade de cópia irá identificar o nome da coluna como "Name.First" com o valor "João" quando o nestedSeparator ponto. |Não (predefinição é dot `.`) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>BD do Azure do Cosmos como sink

Para copiar dados da base de dados do Azure Cosmos, defina o tipo de sink na atividade de cópia para **DocumentDbCollectionSink**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **DocumentDbCollectionSink** |Sim |
| nestingSeparator |É necessário um caráter especial no nome de coluna de origem para indicar que documentos aninhados. <br/><br/>Por exemplo, `Name.First` no conjunto de dados de saída estrutura gera a seguinte estrutura JSON no documento de BD do Cosmos:`"Name": {"First": "[value maps to this column from source]"}` quando o nestedSeparator é ponto. |Não (predefinição é dot `.`) |
| writeBatchTimeout |De tempo de espera para a operação seja concluída antes de atingir o tempo limite.<br/><br/>Valores permitidos são: timespan. Exemplo: "00: 30:00" (30 minutos). |Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Documentos JSON para importar/exportar

Utilizar este conector Cosmos DB, pode facilmente

* Importe documentos JSON de várias origens para Cosmos DB, incluindo Blob do Azure, Azure Data Lake Store e outros arquivos de ficheiros suportados pelo Azure Data Factory.
* Exporte documentos JSON do Cosmos DB collecton em vários arquivos de ficheiros.
* Copiar documentos entre duas coleções de BD do Cosmos como-é.

Para atingir esse cópia desconhecidas do esquema:

- Na base de dados do Cosmos dos DataSets, não especifique a secção "estrutura"; e na base de dados do Cosmos origem/sink de atividade de cópia, especificar a propriedade "nestingSeparator".
- Quando importar do / exportar para ficheiros JSON, no dataset de arquivo correspondente do ficheiro, especifique o tipo de formato como "JsonFormat" e ficheiro de configuração "filePattern" corretamente (consulte [formato JSON](supported-file-formats-and-compression-codecs.md#json-format) secção para obter detalhes), em seguida, não especifique a estrutura" "secção e ignorar as definições do formato de rest.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).