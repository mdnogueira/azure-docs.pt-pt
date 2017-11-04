---
title: Copiar dados de PostgreSQL utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de PostgreSQL aos arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
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
ms.date: 10/12/2017
ms.author: jingwang
ms.openlocfilehash: c9395ab077a112e64811b860dd5aa2345a3206fc
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Copiar dados de PostgreSQL através da utilização do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-onprem-postgresql-connector.md)
> * [Versão 2 - Pré-visualização](connector-postgresql.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados PostgreSQL. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.


> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector PostgreSQL no V1](v1/data-factory-onprem-postgresql-connector.md).

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados de base de dados PostgreSQL para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector PostgreSQL suporta PostgreSQL **versão 7.4 e acima**.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector PostgreSQL, tem de:

- Defina um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instalar o [Ngpsql data provider para PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) com a versão entre 2.0.12 e 3.1.9 na máquina de tempo de execução de integração.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector PostgreSQL.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço de PostgreSQL ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **PostgreSql** | Sim |
| servidor | Nome do servidor PostgreSQL. |Sim |
| base de dados | Nome da base de dados PostgreSQL. |Sim |
| Esquema | Nome do esquema na base de dados. O nome de esquema é maiúsculas e minúsculas. |Não |
| o nome de utilizador | Especifique o nome de utilizador para ligar à base de dados PostgreSQL. |Sim |
| palavra-passe | Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. Marcar este campo como um SecureString. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Um tempo de execução de integração Self-hosted é necessário, tal como mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados PostgreSQL.

Para copiar dados de PostgreSQL, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **RelationalTable** | Sim |
| tableName | Nome da tabela na base de dados PostgreSQL. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL como origem

Para copiar dados de PostgreSQL, defina o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Não (se for especificado "tableName" no conjunto de dados) |

> [!NOTE]
> Os nomes de tabela e esquema são maiúsculas e minúsculas. Coloque-os na `""` (aspas) na consulta.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-postgresql"></a>Tipo de dados de mapeamento para PostgreSQL

Quando copiar dados de PostgreSQL, os seguintes mapeamentos são utilizados PostgreSQL tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados PostgreSQL | PostgresSQL aliases | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |:--- |
| `abstime` | |`Datetime` | &nbsp;
| `bigint` | `int8` | `Int64` |
| `bigserial` | `serial8` | `Int64` |
| `bit [ (n) ]` | | `Byte[], String` | &nbsp;
| `bit varying [ (n) ]` | ' varbit |Byte [] e String' |
| `boolean` | `bool` | `Boolean` |
| `box` | | `Byte[], String` | &nbsp;
| `bytea` | | `Byte[], String` |&nbsp;
| `character [ (n) ]` | `char [ (n) ]` | `String` |
| `character varying [ (n) ]` | `varchar [ (n) ]` | `String` |
| `cid` | | `String` |&nbsp;
| `cidr` | | `String` |&nbsp;
| `circle` | |`Byte[], String` |&nbsp;
| `date` | |`Datetime` |&nbsp;
| `daterange` | |`String` |&nbsp;
| `double precision` |`float8` |`Double` |
| `inet` | |`Byte[], String` |&nbsp;
| `intarry` | |`String` |&nbsp;
| `int4range` | |`String` |&nbsp;
| `int8range` | |`String` |&nbsp;
| `integer` | ' int, int4 |Int32' |
| `interval [ fields ] [ (p) ]` | | `Timespan` |&nbsp;
| `json` | | `String` |&nbsp;
| `jsonb` | | `Byte[]` |&nbsp;
| `line` | | `Byte[], String` |&nbsp;
| `lseg` | | `Byte[], String` |&nbsp;
| `macaddr` | | `Byte[], String` |&nbsp;
| `money` | | `Decimal` |&nbsp;
| `numeric [ (p, s) ]`|`decimal [ (p, s) ]` |`Decimal` |
| `numrange` | |`String` |&nbsp;
| `oid` | |`Int32` |&nbsp;
| `path` | |`Byte[], String` |&nbsp;
| `pg_lsn` | |`Int64` |&nbsp;
| `point` | |`Byte[], String` |&nbsp;
| `polygon` | |`Byte[], String` |&nbsp;
| `real` |`float4` |`Single` |
| `smallint` |`int2` |`Int16` |
| `smallserial` |`serial2` |`Int16` |
| `serial` |`serial4` |`Int32` |
| `text` | |`String` |&nbsp;


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).