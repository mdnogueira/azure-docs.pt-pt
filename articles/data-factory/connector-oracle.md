---
title: Copiar dados da Oracle, utilizando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de arquivos de origem suportada para a base de dados Oracle (ou) do Oracle para lojas de dependente suportados por utilizar o Data Factory.
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
ms.date: 11/01/2017
ms.author: jingwang
ms.openlocfilehash: df9ba6367b2d2ce1b2c281ce483e63b0baa9feb6
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-from-and-to-oracle-using-azure-data-factory"></a>Copiar dados de origem e de Oracle utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-onprem-oracle-connector.md)
> * [Versão 2 - Pré-visualização](connector-oracle.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de origem e de uma base de dados Oracle. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector Oracle no V1](v1/data-factory-onprem-oracle-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de base de dados Oracle para qualquer arquivo de dados suportados sink ou copiar dados a partir de qualquer arquivo de dados de origem suportada para a base de dados Oracle. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Oracle suporta as seguintes versões de base de dados Oracle e suporta Basic ou OID de autenticações.

    - R1 Oracle 12c (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de/para uma base de dados Oracle que não está acessível publicamente, terá de configurar um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes sobre o tempo de execução de integração. O tempo de execução de integração fornece um controlador de Oracle incorporado, por conseguinte, não precisa de instalar manualmente a quaisquer controladores ao copiar dados de/para Oracle.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector do Oracle.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço de Oracle ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Oracle** | Sim |
| connectionString | Especifique as informações necessárias para estabelecer ligação à instância de base de dados Oracle. Marcar este campo como um SecureString. | Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração Self-hosted ou Runtime de integração do Azure (se o arquivo de dados acessível publicamente). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados Oracle.

Para copiar dados de/para Oracle, defina a propriedade de tipo do conjunto de dados para **OracleTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **OracleTable** | Sim |
| tableName |Nome da tabela na base de dados Oracle que referencia o serviço ligado. | Sim |

**Exemplo:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por Oracle origem e dependente.

### <a name="oracle-as-source"></a>Oracle como origem

Para copiar dados da Oracle, defina o tipo de origem na atividade de cópia para **OracleSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **OracleSource** | Sim |
| oracleReaderQuery | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não |

Se não especificar "oracleReaderQuery", as colunas definidas na secção "estrutura" do conjunto de dados são utilizadas para construir uma consulta (`select column1, column2 from mytable`) para executar a base de dados Oracle. Se a definição do conjunto de dados não tiver a estrutura"", todas as colunas são selecionadas da tabela.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle como sink

Para copiar dados para Oracle, defina o tipo de sink na atividade de cópia para **OracleSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida: **OracleSink** | Sim |
| WriteBatchSize | Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize.<br/>Valores permitidos são: número inteiro (número de linhas). |Não (a predefinição é 10000) |
| writeBatchTimeout | De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite.<br/>Valores permitidos são: Timespan. Exemplo: 00:30:00 (30 minutos). | Não |
| preCopyScript | Especifique uma consulta de SQL Server ser executado antes de escrever dados para Oracle em cada execução de atividade de cópia. Pode utilizar esta propriedade para limpar os dados previamente carregados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Tipo de dados de mapeamento para Oracle

Quando copiar dados de/para Oracle, os seguintes mapeamentos são utilizados Oracle tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados Oracle | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| BFILE |Byte] |
| BLOB |Byte] |
| CHAR |Cadeia |
| CLOB |Cadeia |
| DATA |DateTime |
| NÚMERO DE VÍRGULA FLUTUANTE |Decimal, cadeia (se precisão > 28) |
| NÚMERO INTEIRO |Decimal, cadeia (se precisão > 28) |
| LONGA |Cadeia |
| PERÍODO DE TEMPO EM BRUTO |Byte] |
| NCHAR |Cadeia |
| NCLOB |Cadeia |
| NÚMERO |Decimal, cadeia (se precisão > 28) |
| NVARCHAR2 |Cadeia |
| NÃO PROCESSADO |Byte] |
| ROWID |Cadeia |
| TIMESTAMP |DateTime |
| TIMESTAMP COM O FUSO HORÁRIO LOCAL |Cadeia |
| TIMESTAMP COM O FUSO HORÁRIO |Cadeia |
| NÚMERO INTEIRO NÃO ASSINADO |Número |
| VARCHAR2 |Cadeia |
| XML |Cadeia |

> [!NOTE]
> SEGUNDO intervalo ano mês e para do dia de intervalo do tipo de dados não são suportadas.


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).