---
title: Copiar dados de Redshift Amazon utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba mais sobre como copiar dados do Amazon Redshift aos arquivos de dados dependente suportados através do Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 598e7c0c60c82c6f752ec37676dae52488cccb21
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copiar dados de Redshift Amazon utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-amazon-redshift-connector.md)
> * [Versão 2 - Pré-visualização](connector-amazon-redshift.md)


Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de um Redshift Amazon. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [connnector Amazon Redshift no V1](v1/data-factory-amazon-redshift-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Amazon Redshift para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Amazon Redshift suporta obter dados a partir Redshift através de consulta ou suporte de descarregamento Redshift incorporado.

> [!TIP]
> Para obter o melhor desempenho quando copiar grandes quantidades de dados de Redshift, considere utilizar o descarregamento de Redshift incorporada através do Amazon S3. Consulte [descarregamento de utilização para copiar dados do Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) secção para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a copiar para um dados no local do arquivo de dados utilizando [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md), conceder o acesso ao cluster do Amazon Redshift de tempo de execução de integração (endereço IP da utilização da máquina). Consulte [autorizar o acesso ao cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções.
* Se estiver a copiar dados para um arquivo de dados do Azure, consulte o artigo [intervalos de IP de centro de dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) centros de para o endereço IP de computação e os intervalos SQL utilizados pelos dados do Azure.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço de Redshift Amazon ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **AmazonRedshift** | Sim |
| servidor |Nome anfitrião ou endereço IP do servidor Amazon Redshift. |Sim |
| porta |O número da porta TCP que o servidor do Amazon Redshift utiliza para escutar ligações de cliente. |Não, a predefinição é 5439 |
| base de dados |Nome da base de dados Amazon Redshift. |Sim |
| o nome de utilizador |Nome de utilizador que tenha acesso à base de dados. |Sim |
| palavra-passe |Palavra-passe da conta de utilizador. Marcar este campo como um SecureString. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados do Amazon Redshift.

Para copiar dados de Amazon Redshift, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **RelationalTable** | Sim |
| tableName | Nome da tabela em Redshift o Amazon. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift como origem

Para copiar dados do Amazon Redshift, defina o tipo de origem na atividade de cópia para **AmazonRedshiftSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **AmazonRedshiftSource** | Sim |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. |Não (se for especificado "tableName" no conjunto de dados) |
| redshiftUnloadSettings | Grupo de propriedade ao utilizar o Amazon Redshift UNLOAD. | Não |
| s3LinkedServiceName | Refere-se a um Amazon S3 to-be-utilizado como um arquivo intermédio, especificando um nome de serviço ligado do ADF do tipo "AmazonS3". | Sim, se utilizar o descarregamento |
| bucketName | Indique o registo de S3 para armazenar os dados intermédio. Se não for indicado, serviço Data Factory gera automaticamente.  | Sim, se utilizar o descarregamento |

**Exemplo: Origem do Amazon Redshift na atividade de cópia com o descarregamento**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Saiba mais sobre como utilizar o descarregamento para copiar dados do Amazon Redshift eficiente da secção seguinte.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Utilize o descarregamento para copiar dados do Amazon Redshift

[DESCARREGAR](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) é um mecanismo fornecido pelo Redshift Amazon, que pode descarregar os resultados de uma consulta para um ou mais ficheiros no serviço de armazenamento simples (Amazon S3) do Amazon. É a forma recomendada pelo Amazon copiar grande conjunto de dados entre Redshift.

**Exemplo: copiar dados do Amazon Redshift ao Azure SQL Data Warehouse, utilizando o descarregamento, cópia testada e o PolyBase**

Para este exemplo de utilizar as maiúsculas e minúsculas, copiar atividade efetua a descarga dados do Amazon Redshift para Amazon S3 com o configurado nas "redshiftUnloadSettings" e, em seguida, copiar dados do Amazon S3 para Blob do Azure conforme especificado em "stagingSettings", por último utilizam o PolyBase para carregar dados para dados do SQL Server Armazém. O formato intermédio é processado pela atividade de cópia corretamente.

![Redshift para o fluxo de trabalho do armazém de dados do SQL Server cópia](media\copy-data-from-amazon-redshift\redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapeamento de tipo de dados para o Amazon Redshift

Quando copiar dados de Teradata, os seguintes mapeamentos são utilizados Teradata tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do Amazon Redshift | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| BIGINT |Int64 |
| VALOR BOOLEANO |Cadeia |
| CHAR |Cadeia |
| DATA |DateTime |
| DECIMAL |Decimal |
| PRECISÃO DUPLA |duplo |
| NÚMERO INTEIRO |Int32 |
| REAL |Único |
| SMALLINT |Int16 |
| TEXTO |Cadeia |
| TIMESTAMP |DateTime |
| VARCHAR |Cadeia |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).