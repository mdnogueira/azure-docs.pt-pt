---
title: "Mover dados do serviço de armazenamento simples Amazon utilizando o Data Factory | Microsoft Docs"
description: "Saiba mais sobre como mover dados a partir do serviço de armazenamento simples (S3) do Amazon através da utilização do Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4b0af784ad8f18e7dba49a32320dd6a6a7c5ad99
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Mover dados do serviço de armazenamento simples Amazon através da utilização do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-amazon-simple-storage-service-connector.md)
> * [Versão 2 - Pré-visualização](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector Amazon S3 na V2](../connector-amazon-simple-storage-service.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados do serviço de armazenamento simples (S3) do Amazon. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados do Amazon S3 para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Fábrica de dados atualmente suporta apenas mover dados do Amazon S3 outros arquivos de dados, mas não mover dados de outros dados armazena para Amazon S3.

## <a name="required-permissions"></a>Permissões necessárias
Para copiar dados do Amazon S3, certifique-se de que lhe foram concedidas as seguintes permissões:

* `s3:GetObject`e `s3:GetObjectVersion` para operações de objeto do Amazon S3.
* `s3:ListBucket`para operações de registo do Amazon S3. Se estiver a utilizar o Assistente de cópia do Data Factory, `s3:ListAllMyBuckets` também é necessário.

Para obter detalhes sobre a lista completa de permissões do Amazon S3, consulte [especificar permissões numa política](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move dados a partir de uma origem de Amazon S3, utilizando ferramentas diferentes ou APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Para instruções rápidas, consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Para obter instruções passo a passo Criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se utilizar ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado.

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Quando utilizar APIs (exceto .NET API) ou ferramentas, é possível definir estas entidades do Data Factory, utilizando o formato JSON. Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados do Amazon S3, consulte o [exemplo JSON: copiar dados do Amazon S3 para Blob do Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) secção deste artigo.

> [!NOTE]
> Para obter detalhes sobre os formatos de ficheiro e compressão suportados para uma atividade de cópia, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir o Amazon S3 entidades do Data Factory específicas.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
Um serviço ligado liga um arquivo de dados para uma fábrica de dados. Criar um serviço ligado do tipo **AwsAccessKey** para ligar o seu arquivo de dados do Amazon S3 à fábrica de dados. A tabela seguinte fornece uma descrição para os elementos JSON específicos para o Amazon S3 (AwsAccessKey) serviço ligado.

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| accessKeyID |ID da chave de acesso secreta. |Cadeia |Sim |
| secretAccessKey |A chave de acesso secreta próprio. |Cadeia secreta encriptada |Sim |

>[!NOTE]
>Este conector requer chaves de acesso da conta de IAM copiar dados do Amazon S3. [Credencial de segurança temporário](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) não é suportada.
>

Segue-se um exemplo:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados para representar os dados de entrada no Blob storage do Azure, defina a propriedade de tipo do conjunto de dados para **AmazonS3**. Definir o **linkedServiceName** propriedade do conjunto de dados com o nome do Amazon S3 serviço ligado. Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). 

As secções, tais como a estrutura, a disponibilidade e a política são semelhantes para todos os tipos de conjunto de dados (por exemplo, SQL database, blob do Azure e tabela do Azure). O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para um conjunto de dados do tipo **AmazonS3** (que inclui o conjunto de dados do Amazon S3) tem as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| bucketName |O nome do registo de S3. |Cadeia |Sim |
| key |A chave do objeto de S3. |Cadeia |Não |
| prefixo |Prefixo para a chave do objeto de S3. Objetos cujas chaves começar a utilizar este prefixo estão selecionados. Aplica-se apenas quando o chave está vazia. |Cadeia |Não |
| Versão |A versão do objeto S3, se o controlo de versões de S3 estiver ativado. |Cadeia |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte o [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender copiar ficheiros como-está entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não | |
| Compressão | Especifique o tipo e o nível de compressão de dados. Os tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não | |


> [!NOTE]
> **bucketName + chave** Especifica a localização do objeto S3, onde o registo é o contentor de raiz para objetos de S3 e a chave é o caminho completo para o objeto de S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de dados de exemplo com prefixo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Conjunto de dados de exemplo (com a versão)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Caminhos de dinâmicos para S3
O exemplo anterior utiliza valores fixos para o **chave** e **bucketName** propriedades no conjunto de dados do Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Pode ter o Data Factory calcular estas propriedades dinamicamente em runtime, utilizando variáveis de sistema, tais como SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Pode efetuar as mesmas o **prefixo** propriedade de um conjunto de dados do Amazon S3. Para obter uma lista de funções suportadas e as variáveis, consulte [funções de Data Factory e variáveis do sistema](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte [Criar pipelines](data-factory-create-pipelines.md). Propriedades, tais como o nome, descrição e de saída, tabelas e as políticas estão disponíveis para todos os tipos de atividades. Propriedades disponíveis no **typeProperties** secção da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, propriedades variam consoante os tipos de origens e sinks. Quando uma origem na atividade de cópia é do tipo **FileSystemSource** (que inclui o Amazon S3), da seguinte propriedade está disponível no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Especifica se recursivamente lista S3 objetos sob o diretório. |Verdadeiro/Falso |Não |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Exemplo JSON: copiar dados do Amazon S3 para armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados do Amazon S3 para um Blob storage do Azure. No entanto, os dados podem ser copiados diretamente para [qualquer um dos sinks que são suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia numa fábrica de dados.

O exemplo fornece definições de JSON para as seguintes entidades do Data Factory. Pode utilizar estas definições para criar um pipeline para copiar dados do Amazon S3 para armazenamento de BLOBs, utilizando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Um serviço ligado do tipo [AwsAccessKey](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AmazonS3](#dataset-properties).
* Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados do Amazon S3 para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

### <a name="amazon-s3-linked-service"></a>Serviço ligado do Amazon S3

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="amazon-s3-input-dataset"></a>Conjunto de dados de entrada Amazon S3

Definição **"external": true** informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados. Defina esta propriedade para verdadeiro, um conjunto de dados de entrada que não são produzidos por uma atividade no pipeline.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e em partes de horas a hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Atividade de cópia de um pipeline com uma origem de Amazon S3 e um receptor de blob

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource**, e **sink** tipo está definido como **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Para mapear colunas de um conjunto de dados de origem para colunas de um conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* Para saber mais sobre os principais fatores que desempenho impacto de movimento de dados (atividade de cópia) na fábrica de dados e várias formas para otimizar o mesmo, consulte o [copiar guia Otimização e de desempenho de atividade](data-factory-copy-activity-performance.md).

* Para obter instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
