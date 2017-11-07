---
title: Copiar dados de SAP HANA utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de SAP HANA aos arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
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
ms.openlocfilehash: 890aa0038710887330ff79ed91d45043bd0afe65
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiar dados de SAP HANA utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-sap-hana-connector.md)
> * [Versão 2 - Pré-visualização](connector-sap-hana.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados SAP HANA. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector de SAP HANA no V1](v1/data-factory-sap-hana-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados SAP HANA para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de SAP HANA suporta:

- Copiar dados a partir de qualquer versão da base de dados SAP HANA.
- Copiar dados a partir de **modelos de informações de HANA** (por exemplo, vistas de registos analíticos e de cálculo) e **tabelas de linha/coluna** através de consultas SQL.
- Copiar dados utilizando **básico** ou **Windows** autenticação.

> [!NOTE]
> Para copiar dados **para** dados SAP HANA armazenar, utilize o conector ODBC genérico. Consulte [sink de SAP HANA](connector-odbc.md#sap-hana-sink) com detalhes. Deste modo, não pode ser reutilizada nota que são os serviços ligados para o conector de SAP HANA e conector ODBC com tipo diferente.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de SAP HANA, tem de:

- Defina um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instale o controlador ODBC do SAP HANA na máquina de tempo de execução de integração. Pode transferir o controlador ODBC do SAP HANA do [Centro de transferências de Software do SAP](https://support.sap.com/swdc). Pesquisa com a palavra-chave **SAP HANA cliente para o Windows**.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector de SAP HANA.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço de SAP HANA ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **SapHana** | Sim |
| servidor | Nome do servidor no qual reside a instância de SAP HANA. Se o servidor estiver a utilizar uma porta personalizada, especifique `server:port`. | Sim |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados SAP HANA.<br/>Valores permitidos são: **básico**, e **Windows** | Sim |
| Nome de utilizador | Nome do utilizador que tem acesso ao servidor do SAP. | Sim |
| palavra-passe | Palavra-passe para o utilizador. Marcar este campo como um SecureString. | Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Um tempo de execução de integração Self-hosted é necessário, tal como mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados SAP HANA.

Para copiar dados de SAP HANA, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. Enquanto existirem sem propriedades de específicos do tipo suportadas para o conjunto de dados SAP HANA do tipo RelationalTable.

**Exemplo:**

```json
{
    "name": "SAPHANADataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem de SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como origem

Para copiar dados de SAP HANA, defina o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| consulta | Especifica a consulta SQL para ler os dados da instância de SAP HANA. | Sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapeamento de tipo de dados para SAP HANA

Quando copiar dados de SAP HANA, os seguintes mapeamentos são utilizados SAP HANA tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados SAP HANA | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| ALPHANUM | Cadeia |
| BIGINT | Int64 |
| BLOB | Byte] |
| VALOR BOOLEANO | Bytes |
| CLOB | Byte] |
| DATA | DateTime |
| DECIMAL | Decimal |
| VALOR DE DUPLO | Único |
| INT | Int32 |
| NVARCHAR | Cadeia |
| REAL | Único |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TEMPO | TimeSpan |
| TIMESTAMP | DateTime |
| TINYINT | Bytes |
| VARCHAR | Cadeia |

## <a name="known-limitations"></a>Limitações conhecidas

Existem algumas limitações conhecidas ao copiar dados de SAP HANA:

- Cadeias NVARCHAR serão truncadas ao comprimento máximo de 4000 carateres Unicode
- SMALLDECIMAL não é suportada
- VARBINARY não é suportada
- Datas válidas são entre 1899/12/30 e 12/9999/31


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).