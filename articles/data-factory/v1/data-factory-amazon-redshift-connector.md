---
title: "Mover dados do Amazon Redshift através da utilização do Azure Data Factory | Microsoft Docs"
description: "Saiba como mover dados do Amazon Redshift utilizando a atividade de cópia de fábrica de dados do Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d423304c84bd03477f5e9ee2edb4763e2ae8d5b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover dados de Redshift de Amazon utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-amazon-redshift-connector.md)
> * [Versão 2 - Pré-visualização](../connector-amazon-redshift.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector Amazon Redshift na V2](../connector-amazon-redshift.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados do Amazon Redshift. O artigo baseia-se a [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia. 

Fábrica de dados atualmente suporta apenas dados mover da Amazon Redshift para um [arquivo de dados suportados sink](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Mover dados de outros arquivos de dados para Amazon Redshift não é suportada.

> [!TIP]
> Para obter o melhor desempenho quando copiar vastos volumes de dados do Amazon Redshift, considere utilizar o Redshift incorporada **descarregamento** comando através do serviço de armazenamento simples (Amazon S3) do Amazon. Para obter mais informações, consulte [descarregamento de utilização para copiar dados do Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Pré-requisitos
* Se estiver a mover dados para um arquivo de dados no local, instale [Data Management Gateway](data-factory-data-management-gateway.md) numa máquina no local. Conceder acesso para um gateway para o cluster Amazon Redshift utilizando o endereço IP de máquina no local. Para obter instruções, consulte [autorizar o acesso ao cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Para mover dados para um arquivo de dados do Azure, consulte o [endereço IP de computação e os intervalos de SQL Server que são utilizados de centros de dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia para mover dados de uma origem de Amazon Redshift utilizando APIs e ferramentas diferentes.

A forma mais fácil de criar um pipeline consiste em utilizar o Assistente de cópia do Azure Data Factory. Para instruções rápidas sobre como criar um pipeline, utilizando o Assistente de cópia, consulte o [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

Também pode criar um pipeline, utilizando o portal do Azure, o Visual Studio, o Azure PowerShell ou outras ferramentas. Também pode ser utilizada modelos Azure Resource Manager, a API .NET ou a API REST para criar o pipeline. Para obter instruções passo a passo Criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Crie serviços ligados para ligar a entrada e saída de arquivos de dados para a fábrica de dados.
2. Crie conjuntos de dados para representar os dados de entrada e saídos da operação de cópia. 
3. Crie um pipeline com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utiliza o Assistente para copiar, são criadas automaticamente as definições de JSON para estas entidades do Data Factory. Quando utilizar APIs (exceto a API .NET) ou ferramentas, é possível definir as entidades da fábrica de dados utilizando o formato JSON. O [exemplo JSON: copiar dados do Amazon Redshift para armazenamento de Blobs do Azure](#json-example-copy-data-from-amazon-redshift-to-azure-blob) mostra as definições de JSON para entidades da fábrica de dados que são utilizadas para copiar dados de um arquivo de dados do Amazon Redshift.

As secções seguintes descrevem as propriedades JSON que são utilizadas para definir as entidades do Data Factory para Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

A tabela seguinte fornece descrições para os elementos JSON que são específicas para um serviço Redshift Amazon ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |Esta propriedade tem de ser definida **AmazonRedshift**. |Sim |
| **servidor** |O endereço IP ou anfitrião nome do servidor Amazon Redshift. |Sim |
| **porta** |O número da porta TCP que o servidor do Amazon Redshift utiliza para escutar ligações de cliente. |Não (a predefinição é 5439) |
| **base de dados** |O nome da base de dados Amazon Redshift. |Sim |
| **nome de utilizador** |O nome do utilizador que tem acesso à base de dados. |Sim |
| **palavra-passe** |A palavra-passe da conta de utilizador. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista de secções e as propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. O **estrutura**, **disponibilidade**, e **política** secções são semelhantes para todos os tipos de conjunto de dados. Incluem exemplos dos tipos de conjunto de dados SQL do Azure, o Blob storage do Azure e o Table storage do Azure.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo. **O typeProperties** secção para um conjunto de dados do tipo **RelationalTable**, que inclui o conjunto de dados do Amazon Redshift, tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tableName** |O nome da tabela na base de dados do Amazon Redshift que referencia o serviço ligado. |Não (se o **consulta** propriedade de uma atividade de cópia do tipo **RelationalSource** especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista de secções e as propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. O **nome**, **Descrição**, **entradas** tabela, **produz** tabela, e **política** são propriedades está disponível para todos os tipos de atividades. As propriedades que estão disponíveis no **typeProperties** secção variar para cada tipo de atividade. Para a atividade de cópia, as propriedades variam consoante os tipos de origens de dados e sinks.

Para a atividade de cópia, quando a origem é do tipo **AmazonRedshiftSource**, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **consulta** | Utilize a consulta personalizada para ler os dados. |Não (se o **tableName** é especificada a propriedade de um conjunto de dados) |
| **redshiftUnloadSettings** | Contém grupo de propriedades, ao utilizar o Redshift **descarregamento** comando. | Não |
| **s3LinkedServiceName** | O Amazon S3 para utilizar como um arquivo intermédio. O serviço ligado é especificado, utilizando um nome de Azure Data Factory do tipo **AwsAccessKey**. | É necessário quando utilizar o **redshiftUnloadSettings** propriedade |
| **bucketName** | Indica o registo do Amazon S3 a utilizar para armazenar os dados intermédio. Se esta propriedade não for fornecida, atividade de cópia auto-gera um registo. | É necessário quando utilizar o **redshiftUnloadSettings** propriedade |

Em alternativa, pode utilizar o **RelationalSource** tipo, que inclui Amazon Redshift, com a seguinte propriedade no **typeProperties** secção. Tenha em atenção este tipo de origem não suporta o Redshift **descarregamento** comando.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **consulta** |Utilize a consulta personalizada para ler os dados. | Não (se o **tableName** é especificada a propriedade de um conjunto de dados) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Utilize o descarregamento para copiar dados do Amazon Redshift

O Amazon Redshift [ **descarregamento** ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) comando efetua a descarga de resultados de uma consulta para um ou mais ficheiros nos Amazon S3. Este comando é recomendado pelo Amazon copiar grandes conjuntos de dados entre Redshift.

**Exemplo: Copiar dados do Amazon Redshift ao Azure SQL Data Warehouse**

Neste exemplo copia dados da Amazon Redshift ao Azure SQL Data Warehouse. O exemplo utiliza o Redshift **descarregamento** comando, testada copiar dados e o PolyBase de Microsoft.

Neste caso de utilização de exemplo, atividade de cópia primeiro efetua a descarga de dados do Amazon Redshift para Amazon S3 com o configurado no **redshiftUnloadSettings** opção. Em seguida, os dados são copiados do Amazon S3 para armazenamento de Blobs do Azure, conforme especificado no **stagingSettings** opção. Por fim, o PolyBase carrega os dados para o SQL Data Warehouse. Todos os formatos provisórias são processados pela atividade de cópia.

![Copiar o fluxo de trabalho do Amazon Redshift ao SQL Data Warehouse](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Exemplo JSON: copiar dados do Amazon Redshift para armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados de uma base de dados do Amazon Redshift para armazenamento de Blobs do Azure. Dados possam ser copiados diretamente qualquer [suportado sink](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia.  

O exemplo tem as seguintes entidades de fábrica de dados:

* Um serviço ligado do tipo [AmazonRedshift](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](#dataset-properties)
* Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza o [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) propriedades

O exemplo copia dados a partir num resultado de consulta Amazon Redshift para um blob do Azure numa base horária. As propriedades JSON utilizadas no exemplo são descritas nas secções que se seguem as definições de entidade.

**Serviço de Redshift Amazon ligado**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Serviço de ligado do armazenamento de Blobs do Azure**

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
**Conjunto de dados de entrada Amazon Redshift**

O **externo** propriedade está definida como "true" para informar o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados. Esta definição de propriedade indica que o conjunto de dados não é produzido por uma atividade no factory de dados. Defina a propriedade para verdadeiro, um conjunto de dados de entrada que não são produzidos por uma atividade no pipeline.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de dados de saída de Blobs do Azure**

Dados são escritos para um blob de novo a cada hora, definindo o **frequência** propriedade como "Hora" e o **intervalo** propriedade para 1. O **folderPath** propriedade do blob é avaliada dinamicamente. O valor da propriedade baseia-se a hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e em partes de horas a hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Atividade de cópia num pipeline com uma origem de Azure Redshift (do tipo RelationalSource) e um receptor de Blob do Azure**

O pipeline contém uma atividade de cópia está configurada para utilizar os conjuntos de dados de entrada e de saída. O pipeline está agendado para execução a cada hora. Na definição de JSON para o pipeline, a **origem** tipo está definido como **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **consulta** propriedade seleciona os dados ao copiar da hora anterior.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mapeamento de tipo para o Amazon Redshift
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipo automática do tipo de origem para o tipo de sink. Os tipos são convertidos utilizando uma abordagem de dois passos:

1. Converter de um tipo de origem nativo para um tipo .NET
2. Converter de um tipo .NET para o tipo de sink nativo

Os seguintes mapeamentos são utilizados quando a atividade de cópia converte os dados de um tipo de Amazon Redshift para um tipo .NET:

| Tipo de Amazon Redshift | Tipo .NET |
| --- | --- |
| SMALLINT |Int16 |
| NÚMERO INTEIRO |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Único |
| PRECISÃO DUPLA |duplo |
| VALOR BOOLEANO |Cadeia |
| CHAR |Cadeia |
| VARCHAR |Cadeia |
| DATA |DateTime |
| TIMESTAMP |DateTime |
| TEXTO |Cadeia |

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber como mapear colunas no conjunto de dados de origem para colunas no conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetíveis origens relacionais
Quando copiar dados de um arquivo de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a repetição **política** para um conjunto de dados voltar a executar um setor quando ocorre uma falha. Certifique-se de que os mesmos dados é de leitura, independentemente de quantas vezes o setor será novamente executado. Certifique-se também que os mesmos dados é de leitura, independentemente da forma como voltar a executar o setor. Para obter mais informações, consulte [Repeatable lê a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Saiba mais sobre os principais fatores que afetam o desempenho de atividade de cópia e formas para otimizar o desempenho no [guia Otimização e de desempenho de atividade de cópia](data-factory-copy-activity-performance.md). 

## <a name="next-steps"></a>Passos seguintes
Para obter instruções passo a passo para criar um pipeline com atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
