---
title: "Copiar dados da fábrica de dados de utilização de Table Storage do Azure | Microsoft Docs"
description: Saiba como copiar dados de arquivos de origem suportada para o Table Storage do Azure (ou) de armazenamento de tabelas para lojas de dependente suportados por utilizar o Data Factory.
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
ms.openlocfilehash: 2fa03b82750585454430da0c29392db57b20d3c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Copiar dados de ou para tabelas do Azure utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-azure-table-connector.md)
> * [Versão 2 - Pré-visualização](connector-azure-table-storage.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados e de tabelas do Azure. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector do Table Storage do Azure no V1](v1/data-factory-azure-table-connector.md).

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados a partir de qualquer arquivo de dados de origem suportada para tabelas do Azure ou copiar dados de tabelas do Azure para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de tabelas do Azure suporta a cópia de dados utilizando ambos **chave da conta** e **serviço SAS** autenticações (assinatura de acesso partilhado).

## <a name="get-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas ao Table Storage do Azure.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

### <a name="using-account-key"></a>Utilizando a chave de conta

Pode criar um serviço ligado do Storage do Azure, utilizando a chave de conta, que fornece a fábrica de dados com acesso global para o armazenamento do Azure. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **AzureStorage** |Sim |
| connectionString | Especificar as informações necessárias para ligar ao armazenamento do Azure para a propriedade connectionString. Marcar este campo como SecureString. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>Utilizar a autenticação do serviço SAS

Também pode criar um serviço ligado do Storage do Azure através da utilização de um acesso assinatura partilhado (SAS), que fornece a fábrica de dados com acesso restrito/vínculo de tempo para recursos de todos os/específico no armazenamento.

Uma assinatura de acesso partilhado (SAS) concede acesso delegado a recursos na sua conta de armazenamento. Permite-lhe conceder que um cliente limitada permissões para objetos na sua conta de armazenamento para um determinado período de tempo e com um conjunto especificado de permissões, sem ter de partilhar as chaves de acesso da conta. O SAS é um URI que abrange nos respetivos parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a SAS, o cliente só tem de passar a SAS para o método ou construtor adequado. Para obter informações detalhadas sobre SAS, consulte [assinaturas de acesso partilhado: compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory agora só suporta **serviço SAS** , mas não a conta SAS. Consulte [tipos de assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obter detalhes sobre estes dois tipos e de como construir. O URL de SAS generable a partir do portal do Azure ou no Explorador de armazenamento é um SAS de conta, que não é suportado.
>

Para utilizar a autenticação do serviço SAS, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **AzureStorage** |Sim |
| sasUri | Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento do Azure como BLOBs, contentor ou tabela. Marcar este campo como SecureString. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ao criar um **URI de SAS**, considerar os seguintes pontos:

- Conjunto adequada de leitura/escrita **permissões** em objetos com base na forma como o serviço ligado (leitura, escrita, leitura/escrita) é utilizado na fábrica de dados.
- Definir **hora de expiração** adequadamente. Certifique-se de que o acesso a objetos de armazenamento do Azure não expira dentro do período ativo do pipeline.
- URI deve ser criado ao nível da tabela da direita em necessário.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados de tabelas do Azure.

Para copiar dados da tabela do Azure, defina a propriedade de tipo do conjunto de dados para **AzureTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **AzureTable** |Sim |
| tableName |Nome da tabela na instância da base de dados do Azure tabela pelo serviço ligado refere-se. |Sim |

**Exemplo:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados

Para os arquivos de dados sem esquema, tais como tabelas do Azure, o serviço Data Factory infere o esquema de uma das seguintes formas:

1. Se especificar a estrutura de dados utilizando o **estrutura** propriedade na definição do conjunto de dados, o serviço Data Factory honra esta estrutura como o esquema. Neste caso, se uma linha não contém um valor para uma coluna, é fornecido um valor nulo para o mesmo.
2. Se não especificar a estrutura dos dados ao utilizar o **estrutura** propriedade na definição do conjunto de dados, o Data Factory infere o esquema utilizando a primeira linha dos dados. Neste caso, se a primeira linha contém o esquema completo, são omitidas algumas colunas nos resultados da operação de cópia.

Por conseguinte, para origens de dados sem esquema, a melhor prática é especificar a estrutura dos dados utilizando o **estrutura** propriedade.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem de tabelas do Azure e o sink.

### <a name="azure-table-as-source"></a>Tabela do Azure como origem

Para copiar dados de tabelas do Azure, defina o tipo de origem na atividade de cópia para **AzureTableSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **AzureTableSource** |Sim |
| azureTableSourceQuery |Utilize a consulta de tabela do Azure personalizado para ler os dados. Veja exemplos na secção seguinte: |Não |
| azureTableSourceIgnoreTableNotFound |Indica se swallow a exceção da tabela não existe.<br/>Valores permitidos são: **verdadeiro**, e **falso** (predefinição). |Não |

### <a name="azuretablesourcequery-examples"></a>Exemplos de azureTableSourceQuery

Se a coluna de tabelas do Azure é do tipo de cadeia:

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

Se a coluna de tabelas do Azure é do tipo datetime:

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>Tabela do Azure como sink

Para copiar dados de tabelas do Azure, defina o tipo de origem na atividade de cópia para **AzureTableSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **AzureTableSink** |Sim |
| azureTableDefaultPartitionKeyValue |Partição chave valor predefinido que pode ser utilizado pelo sink. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como chaves de partição. Se não for especificado, "AzureTableDefaultPartitionKeyValue" é utilizado como a chave de partição. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores da coluna são utilizados como chave de linha. Se não for especificado, utilize um GUID para cada linha. |Não |
| azureTableInsertType |O modo de inserir os dados na tabela do Azure. Esta propriedade controla se as linhas existentes na tabela de saída com correspondentes chaves de partição e a linha tem os respetivos valores substituído ou intercaladas. <br/><br/>Valores permitidos são: **intercalação** (predefinição), e **substituir**. <br/><br> Esta definição aplica-se ao nível da linha, não o nível de tabela e nenhuma opção elimina as linhas na tabela de saída que não existem na entrada. Para saber mais sobre como funcionam estas definições (intercalação e substitua), consulte [inserção ou entidade de intercalação](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) tópicos. |Não |
| WriteBatchSize |Insere dados na tabela do Azure quando é atingido o writeBatchSize ou writeBatchTimeout.<br/>Valores permitidos são: número inteiro (número de linhas) |Não (a predefinição é 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando é atingido o writeBatchSize ou writeBatchTimeout.<br/>Valores permitidos são: timespan. Exemplo: "00: 20:00" (20 minutos) |Não (a predefinição é 90 seg - tempo limite predefinido do cliente de armazenamento) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Mapear uma coluna de origem para uma coluna de destino utilizando a propriedade de "tradutor" antes de poder utilizar a coluna de destino como o azureTablePartitionKeyName.

No exemplo seguinte, a coluna de origem DivisionID está mapeada para a coluna de destino DivisionID.

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" está especificada como a chave de partição.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Tipo de dados de mapeamento de tabelas do Azure

Quando copiar dados de/para tabelas do Azure, os seguintes mapeamentos são utilizados de tipos de dados de tabelas do Azure para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

Quando mover dados para & tabela do Azure, o seguinte [mapeamentos definidos pelo serviço de Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) são utilizadas de tipos de OData de tabela do Azure para o tipo de .NET e vice-versa.

| Tipo de dados de tabela do Azure | Tipo de dados intermédio de fábrica de dados | Detalhes |
|:--- |:--- |:--- |
| Edm.Binary |Byte] |Uma matriz de bytes até 64 KB. |
| Edm.Boolean |bool |Valor booleano. |
| Edm.DateTime |DateTime |Um valor de 64 bits, expressado como Hora Universal Coordenada (UTC). Início do intervalo suportado de DateTime de 12:00, 1 de Janeiro de 1601 a.d. (E.C.), UTC. O intervalo de termina a 31 de Dezembro de 9999. |
| Edm.Double |duplo |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |GUID |Um identificador exclusivo global de 128 bits. |
| Edm.Int32 |Int32 |Um número inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um número inteiro de 64 bits. |
| Edm.String |Cadeia |Um valor com codificação UTF-16. Valores de cadeia poderão ser até 64 KB. |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).