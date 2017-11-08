---
title: Copiar dados de Teradata utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba mais sobre Teradata conector do serviço fábrica de dados que lhe permite copiar dados de base de dados Teradata aos arquivos de dados suportados pela fábrica de dados como sinks."
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
ms.openlocfilehash: 343facadfec217adaef9a05426e7ae914f4cfd38
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Copiar dados de Teradata utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-onprem-teradata-connector.md)
> * [Versão 2 - Pré-visualização](connector-teradata.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados Teradata. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector Teradata no V1](v1/data-factory-onprem-teradata-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de base de dados Teradata para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Teradata suporta:

- Teradata **versão 12 e acima**.
- Copiar dados utilizando **básico** ou **Windows** autenticação.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector Teradata, tem de:

- Defina um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instalar o [fornecedor de dados .NET para Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) versão 14 ou acima na máquina de tempo de execução de integração.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector Teradata.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço de Teradata ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Teradata** | Sim |
| servidor | Nome do servidor Teradata. | Sim |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados Teradata.<br/>Valores permitidos são: **básico**, e **Windows**. | Sim |
| o nome de utilizador | Especifique o nome de utilizador para ligar à base de dados Teradata. | Sim |
| palavra-passe | Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. Marcar este campo como um SecureString. | Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Um tempo de execução de integração Self-hosted é necessário, tal como mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "Basic",
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados Teradata.

Para copiar dados de Teradata, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **RelationalTable** | Sim |
| tableName | Nome da tabela na base de dados Teradata. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem Teradata.

### <a name="teradata-as-source"></a>Teradata como origem

Para copiar dados de Teradata, defina o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Tipo de dados de mapeamento para Teradata

Quando copiar dados de Teradata, os seguintes mapeamentos são utilizados Teradata tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados Teradata | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| BigInt |Int64 |
| Blobs |Byte] |
| Bytes |Byte] |
| ByteInt |Int16 |
| char |Cadeia |
| CLOB |Cadeia |
| Data |DateTime |
| Decimal |Decimal |
| duplo |duplo |
| Gráfico |Cadeia |
| Número inteiro |Int32 |
| Dia de intervalo |TimeSpan |
| Dia de intervalo hora |TimeSpan |
| Dia de intervalo para minuto |TimeSpan |
| Dia de intervalo para o segundo |TimeSpan |
| Hora de intervalo |TimeSpan |
| Hora de intervalo para minuto |TimeSpan |
| Hora de intervalo para o segundo |TimeSpan |
| Minuto do intervalo |TimeSpan |
| Minuto do intervalo para o segundo |TimeSpan |
| Mês do intervalo |Cadeia |
| Intervalo segundo |TimeSpan |
| Intervalo ano |Cadeia |
| Intervalo de ano a mês |Cadeia |
| Número |duplo |
| Period(Date) |Cadeia |
| Period(Time) |Cadeia |
| Período (Time com fuso horário) |Cadeia |
| Period(Timestamp) |Cadeia |
| Período (Timestamp com o fuso horário) |Cadeia |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Período de tempo com fuso horário |Cadeia |
| Timestamp |DateTime |
| Timestamp com o fuso horário |DateTimeOffset |
| VarByte |Byte] |
| VarChar |Cadeia |
| VarGraphic |Cadeia |
| XML |Cadeia |


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).