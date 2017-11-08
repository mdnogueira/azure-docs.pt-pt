---
title: Copiar dados de SAP BW utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados a partir do SAP Business Warehouse aos arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
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
ms.openlocfilehash: 20d6f463d135028bf272c23de9f34be66e73325a
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Copiar dados a partir do SAP Business Warehouse utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-sap-business-warehouse-connector.md)
> * [Versão 2 - Pré-visualização](connector-sap-business-warehouse.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de um SAP Business armazém (BW). Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector SAP BW no V1](v1/data-factory-sap-business-warehouse-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do SAP Business Warehouse para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do SAP Business Warehouse suporta:

- Do SAP Business Warehouse **versão 7. x**.
- Copiar dados a partir de **InfoCubes e QueryCubes** (incluindo BEx consulta) através de consultas MDX.
- A copiar os dados utilizando a autenticação básica.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector do SAP Business Warehouse, tem de:

- Defina um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instalar o **SAP NetWeaver biblioteca** na máquina de tempo de execução de integração. Pode obter a biblioteca do SAP Netweaver ao administrador de SAP ou diretamente a partir de [Centro de transferências de Software do SAP](https://support.sap.com/swdc). Procure o **SAP nota #1025361** para obter a localização de transferência para a versão mais recente. Certifique-se que escolha o **64-bit** SAP NetWeaver biblioteca, que corresponde a instalação de tempo de execução de integração. Em seguida, instale todos os ficheiros incluídos no SDK do SAP NetWeaver RFC, de acordo com a nota SAP. A biblioteca do SAP NetWeaver também está incluída na instalação do SAP ferramentas de cliente.

> [!TIP]
> Coloque os dlls extraídas do NetWeaver RFC SDK na pasta system32.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector do SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço do SAP Business Warehouse (BW) ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **SapBw** | Sim |
| servidor | Nome do servidor no qual reside a instância do SAP BW. | Sim |
| systemNumber | Número de sistema do sistema SAP BW.<br/>Permitido valor: número decimal dois dígitos representado como uma cadeia. | Sim |
| ID de cliente | ID de cliente do cliente no sistema SAP W.<br/>Permitido valor: número decimal de três dígitos representado como uma cadeia. | Sim |
| Nome de utilizador | Nome do utilizador que tem acesso ao servidor do SAP. | Sim |
| palavra-passe | Palavra-passe para o utilizador. Marcar este campo como um SecureString. | Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Um tempo de execução de integração Self-hosted é necessário, tal como mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados SAP BW.

Para copiar dados de SAP BW, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. Enquanto existirem sem propriedades de específicos do tipo suportadas para o conjunto de dados SAP BW do tipo RelationalTable.

**Exemplo:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem de SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW como origem

Para copiar dados de SAP BW, defina o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| consulta | Especifica a consulta MDX para ler os dados na instância do SAP BW. | Sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Mapeamento de tipo de dados para SAP BW

Quando copiar dados a partir do SAP BW, os seguintes mapeamentos são utilizados SAP BW tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados SAP BW | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| ACCP | Int |
| CHAR | Cadeia |
| CLNT | Cadeia |
| CURR | Decimal |
| CUKY | Cadeia |
| DEC | Decimal |
| FLTP | duplo |
| INT1 | Bytes |
| INT2 | Int16 |
| INT4 | Int |
| LANG | Cadeia |
| LCHR | Cadeia |
| LRAW | Byte] |
| PREC | Int16 |
| QUAN | Decimal |
| NÃO PROCESSADO | Byte] |
| RAWSTRING | Byte] |
| CADEIA | Cadeia |
| UNIDADE | Cadeia |
| DATS | Cadeia |
| NUMC | Cadeia |
| TIMS | Cadeia |


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).