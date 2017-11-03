---
title: Copiar dados de DB2 utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de DB2 aos arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
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
ms.openlocfilehash: aef796aa4f2604e6cd06452f3d3c6f648850aa38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiar dados de DB2 através da utilização do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-onprem-db2-connector.md)
> * [Versão 2 - Pré-visualização](connector-db2.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados DB2. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector DB2 no V1](v1/data-factory-onprem-db2-connector.md).

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados de base de dados DB2 para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector DB2 suporta as seguintes plataformas de IBM DB2 e versões distribuídas relacional da base de dados arquitetura (DRDA) SQL acesso Manager (SQLAM) versão 9, 10 e 11:

* IBM DB2 para z/SO 11.1
* IBM DB2 para z/SO 10.1
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

> [!TIP]
> Se receber uma mensagem de erro que indica "não foi encontrado o pacote correspondente a um pedido de execução da instrução de SQL. SQLSTATE = 51002 SQLCODE =-805 ", o motivo é um pacote necessário não foi criado para um utilizador normal nesse sistema operativo. Siga estas instruções, de acordo com o tipo de servidor DB2:
> - DB2 para i (AS400): permitir que o utilizador avançado Criar coleção para o utilizador de início de sessão antes de utilizar a atividade de cópia. Comando:`create collection <username>`
> - DB2 para z/OS ou LUW: utilizar uma conta de privilégio elevado - utilizador avançado ou administrador com autoridades de pacote e ENLACE BINDADD, CONCEDA executar para permissões públicas - para executar a atividade de cópia de uma vez, em seguida, o pacote necessário é criado automaticamente durante a cópia. Seguidamente, pode mudar para o utilizador normal para as execuções subsequentes cópia.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar copiar dados de uma base de dados DB2 não está acessível publicamente, terá de configurar um tempo de execução de integração Self-hosted. Para saber mais sobre tempos de execução automática alojada integração, consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo. O tempo de execução de integração fornece um controlador de DB2 incorporado, por conseguinte, não precisa de instalar manualmente a quaisquer controladores quando a cópia de dados DB2.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir o conector DB2 entidades do Data Factory específicas.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço de DB2 ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Db2** | Sim |
| servidor |Nome do servidor DB2. |Sim |
| base de dados |Nome da base de dados DB2. |Sim |
| Esquema |Nome do esquema na base de dados. O nome de esquema é maiúsculas e minúsculas. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados DB2.<br/>Permitido é de valor: **básico**. |Sim |
| o nome de utilizador |Especifique o nome de utilizador para ligar à base de dados DB2. |Sim |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. Marcar este campo como SecureString. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração Self-hosted ou Runtime de integração do Azure (se o arquivo de dados acessível publicamente). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
            "database": "<dbname>",
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados DB2.

Para copiar dados de DB2, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **RelationalTable** | Sim |
| tableName | Nome da tabela na base de dados DB2. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem DB2.

### <a name="db2-as-source"></a>DB2 como origem

Para copiar dados de DB2, defina o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Tipo de dados de mapeamento para DB2

Quando copiar dados de DB2, os seguintes mapeamentos são utilizados DB2 tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de base de dados DB2 | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| BigInt |Int64 |
| Binário |Byte] |
| Blobs |Byte] |
| char |Cadeia |
| CLOB |Cadeia |
| Data |DateTime |
| DB2DynArray |Cadeia |
| DbClob |Cadeia |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| duplo |duplo |
| Número de vírgula flutuante |duplo |
| Gráfico |Cadeia |
| Número inteiro |Int32 |
| LongVarBinary |Byte] |
| LongVarChar |Cadeia |
| LongVarGraphic |Cadeia |
| um valor numérico |Decimal |
| Real |Único |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte] |
| VarChar |Cadeia |
| VarGraphic |Cadeia |
| XML |Byte] |


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).