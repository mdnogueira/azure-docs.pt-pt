---
title: "Copiar dados de/para Salesforce através da utilização do Azure Data Factory | Microsoft Docs"
description: "Saiba como copiar dados do Salesforce aos arquivos de dados suportados sink (ou) de arquivos de dados de origem suportada para o Salesforce ao utilizar uma atividade de cópia num pipeline do Azure Data Factory."
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
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: 017d03b76bd19a0b3a1e19c22233c61be9067d0d
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-fromto-salesforce-using-azure-data-factory"></a>Copiar dados de/para Salesforce utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-salesforce-connector.md)
> * [Versão 2 - Pré-visualização](connector-salesforce.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de origem e de Salesforce. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector do Salesforce no V1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Salesforce ao arquivo de dados qualquer dependente suportados, ou ou copiar dados a partir de qualquer arquivo de dados de origem suportada para Salesforce. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do Salesforce suporta:

- As seguintes edições do Salesforce: **edição programador, edição Professional, Enterprise Edition ou edição ilimitados**.
- Copiar dados de/para Salesforce **sandbox, de produção e de domínio personalizado**.

## <a name="prerequisites"></a>Pré-requisitos

* Permissão de API tem de estar ativada no Salesforce. Consulte [como ativar o acesso à API no Salesforce pelo conjunto de permissões?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites de pedido do Salesforce

O Salesforce tem limites para o total de pedidos de API e pedidos de API em simultâneo. Tenha em atenção os seguintes pontos:

- Se o número de pedidos simultâneos excede o limite, limitação ocorre e vir falhas aleatórias.
- Se o número total de pedidos excede o limite, a conta do Salesforce é bloqueada durante 24 horas.

Também poderá receber o erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Consulte a secção "Limites de pedidos de API" o [limites de programador do Salesforce](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) artigo para obter detalhes.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector do Salesforce.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Salesforce:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida: **Salesforce**. |Sim |
| environmentUrl | Especifique a instância do URL do Salesforce. <br> -Predefinição é `"https://login.salesforce.com"`. <br> -Para copiar dados a partir de sandbox, especifique `"https://test.salesforce.com"`. <br> -Para copiar dados de domínio personalizado, especificar, por exemplo, `"https://[domain].my.salesforce.com"`. |Não |
| o nome de utilizador |Especifique um nome de utilizador para a conta de utilizador. |Sim |
| palavra-passe |Especifique uma palavra-passe da conta de utilizador.<br/><br/>Pode escolher para marcar este campo como um SecureString armazena de forma segura na ADF ou armazenar a palavra-passe no Cofre de chaves do Azure e permitir que ADF copiar concentre solicitação a partir daí, quando efetuar a cópia de dados – Saiba mais de [armazenar credenciais no Cofre de chaves](store-credentials-in-key-vault.md). |Sim |
| securityToken |Especifique um token de segurança para a conta de utilizador. Consulte [obtenha o token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como repor/obter um token de segurança. Para saber mais sobre os tokens de segurança em geral, consulte [segurança e a API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Pode escolher para marcar este campo como um SecureString armazena de forma segura na ADF ou armazenar o token de segurança no Cofre de chaves do Azure e permitir que ADF copiar concentre solicitação a partir daí, quando efetuar a cópia de dados – Saiba mais de [armazenar credenciais no Cofre de chaves](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. | Não para a origem, Sim para sink |

>[!IMPORTANT]
>Para copiar dados para Salesforce, explicitamente [criar uma resposta a incidentes Azure](create-azure-integration-runtime.md#create-azure-ir) com uma localização perto do Salesforce e associar no serviço ligado do exemplo seguinte.

**Exemplo: armazenamento de credenciais no ADF**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenamento de credenciais no Cofre de chaves do Azure**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados do Salesforce.

Para copiar dados de/para Salesforce, defina a propriedade de tipo do conjunto de dados para **SalesforceObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **SalesforceObject**  | Sim |
| objectApiName | O nome de objeto do Salesforce ao obter dados de. | Não para a origem, Sim para sink |

> [!IMPORTANT]
> A parte "__c" o nome da API é necessário para qualquer objeto personalizado.

![Nome do Data Factory - Salesforce ligação - API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemplo:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Para compatibilidade anterior, quando copiar dados do Salesforce, utilizar o conjunto de dados de tipo anterior "RelationalTable" irá manter a funcionar, enquanto são sugeridos para mudar para o novo tipo de "SalesforceObject".

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **RelationalTable** | Sim |
| tableName | Nome da tabela no Salesforce. | Não (se for especificada "consulta" na origem de atividade) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por Salesforce origem e dependente.

### <a name="salesforce-as-source"></a>Salesforce como origem

Para copiar dados do Salesforce, defina o tipo de origem na atividade de cópia para **SalesforceSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **SalesforceSource** | Sim |
| consulta |Utilize a consulta personalizada para ler os dados. Pode utilizar uma consulta de SQL 92 ou [idioma de consulta de objeto do Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) consulta. Por exemplo: `select * from MyTable__c`. | Não (se for especificado "tableName" no conjunto de dados) |

> [!IMPORTANT]
> A parte "__c" o nome da API é necessário para qualquer objeto personalizado.

![Nome do Data Factory - Salesforce ligação - API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Para compatibilidade anterior, quando copiar dados do Salesforce, utilizando a origem de cópia de tipo "RelationalSource" anterior irá manter a funcionar, enquanto são sugeridos para mudar para o novo tipo de "SalesforceSource".

### <a name="salesforce-as-sink"></a>Salesforce como sink

Para copiar dados para o Salesforce, defina o tipo de sink na atividade de cópia para **SalesforceSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida: **SalesforceSink** | Sim |
| WriteBehavior | O comportamento de escrita para a operação.<br/>Valores permitidos são: **inserir**, e **Upsert**. | Não (a predefinição é Insert) |
| externalIdFieldName | O nome do campo ID externo para a operação de upsert. O campo especificado tem de ser definido como "Campo Id externo" no objeto do Salesforce e não pode ter valores nulos dos dados de entrada correspondente. | Sim para "Upsert" |
| WriteBatchSize | O número de linhas de dados escritos na Salesforce em cada lote. | Não (a predefinição é 5000) |
| ignoreNullValues | Indica se a ignorar valores nulos de entrada durante a escrita de dados operação.<br/>Valores permitidos são: **verdadeiro**, e **falso**.<br>- **Verdadeiro**: mantenha os dados de destino objeto inalterado ao efetuar a operação de atualização/upsert e inserir definido um valor predefinido ao efetuar a operação de inserção.<br/>- **FALSO**: atualizar os dados no objeto de destino para um valor nulo ao efetuar a operação de atualização/upsert e insira um valor nulo ao efetuar a operação de inserção. | Não (a predefinição é false) |

### <a name="example-salesforce-sink-in-copy-activity"></a>Exemplo: Salesforce sink na atividade de cópia

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Sugestões de consulta

### <a name="retrieving-data-from-salesforce-report"></a>Obter dados de relatórios do Salesforce

Pode obter dados de relatórios do Salesforce, especificando a consulta como `{call "<report name>"}`. Exemplo: `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>A obter eliminados registos da Reciclagem do Salesforce

Para consultar os registos eliminados recuperável da Reciclagem do Salesforce, pode especificar **"IsDeleted = 1"** na sua consulta. Por exemplo,

* Para consultar apenas os registos eliminados, especifique "selecionar * de MyTable__c **onde IsDeleted = 1**"
* Para consultar todos os registos, incluindo o existente e o eliminado, especifique "selecionar * de MyTable__c **onde IsDeleted = 0 ou IsDeleted = 1**"

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Obter dados com onde cláusula na coluna de DateTime

Especifique quando a consulta SOQL ou SQL Server, preste atenção à diferença de formato DateTime. Por exemplo:

* **Exemplo SOQL**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Exemplo SQL**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Tipo de dados de mapeamento para o Salesforce

Quando copiar dados do Salesforce, os seguintes mapeamentos são utilizados Salesforce tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do Salesforce | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| Número de automática |Cadeia |
| Caixa de verificação |Valor booleano |
| Moeda |duplo |
| Data |DateTime |
| Data/hora |DateTime |
| E-mail |Cadeia |
| Id |Cadeia |
| Relação de referência |Cadeia |
| Selecionar vários Picklist |Cadeia |
| Número |duplo |
| Percentagem |duplo |
| Telefone |Cadeia |
| Picklist |Cadeia |
| Texto |Cadeia |
| Área de texto |Cadeia |
| Área de texto (longa) |Cadeia |
| Área de texto (avançada) |Cadeia |
| Texto (encriptado) |Cadeia |
| URL |Cadeia |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).