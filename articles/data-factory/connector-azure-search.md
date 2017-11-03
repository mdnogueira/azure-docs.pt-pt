---
title: "Copiar dados para o índice de pesquisa através do Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre como push ou copiar dados para um índice da Azure search utilizando a atividade de cópia num pipeline do Azure Data Factory."
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
ms.date: 09/30/2017
ms.author: jingwang
ms.openlocfilehash: d8db545fc58f3cc2e18bbc9a732ed16ef8b563c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Copiar dados para um índice da Azure Search utilizando o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-azure-search-connector.md)
> * [Versão 2 - Pré-visualização](connector-azure-search.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados para o índice da Azure Search. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector de pesquisa do Azure no V1](v1/data-factory-azure-search-connector.md).

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados a partir de qualquer arquivo de dados de origem suportada para o índice da Azure Search. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector de pesquisa do Azure.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço da Azure Search ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **azuresearch, uma vez** | Sim |
| URL | URL para o serviço de pesquisa do Azure. | Sim |
| key | Chave de administrador para o serviço de pesquisa do Azure. Marcar este campo como um SecureString. | Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

> [!IMPORTANT]
> Quando copiar dados a partir de um arquivo de dados na nuvem para o índice de pesquisa do Azure, na Azure Search serviço ligado, tem de referir-se de um tempo de execução de integração do Azure com a região explícito no connactVia. Defina a região que reside a pesquisa do Azure. Saiba mais de [Runtime de integração do Azure] (conceitos-integração-runtime.md #azure-integração-tempo de execução).

**Exemplo:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados de pesquisa do Azure.

Para copiar dados na Azure Search, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **AzureSearchIndex** | Sim |
| indexName | Nome do índice da Azure Search. Fábrica de dados não crie o índice. O índice tem de existir na Azure Search. | Sim |

**Exemplo:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem de pesquisa do Azure.

### <a name="azure-search-as-sink"></a>Pesquisa do Azure como sink

Para copiar dados na Azure Search, defina o tipo de origem na atividade de cópia para **AzureSearchIndexSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **AzureSearchIndexSink** | Sim |
| WriteBehavior | Especifica se deve intercalar ou substituir quando já existe um documento no índice. Consulte o [WriteBehavior propriedade](#writebehavior-property).<br/><br/>Valores permitidos são: **intercalar** (predefinição), e **carregar**. | Não |
| WriteBatchSize | Carrega dados para o índice da Azure Search, quando o tamanho da memória intermédia atinge writeBatchSize. Consulte o [WriteBatchSize propriedade](#writebatchsize-property) para obter mais detalhes.<br/><br/>Valores permitidos são: número inteiro de 1 a 1000; predefinição é 1000. | Não |

### <a name="writebehavior-property"></a>Propriedade de WriteBehavior

AzureSearchSink upserts ao escrever dados. Por outras palavras, ao escrever um documento, se a chave do documento já existe no índice da Azure Search, pesquisa do Azure atualiza o documento existente, em vez de gerar uma exceção de conflito.

O AzureSearchSink fornece seguintes comportamentos upsert dois (utilizando o SDK azuresearch, uma vez):

- **Intercalar**: combine todas as colunas no novo documento com a já existente. Para colunas com um valor nulo no documento novo, o valor no existentes é preservado.
- **Carregar**: novo documento substitui a já existente. Para colunas não especificadas no documento novo, o valor é definido como nulo se não há um valor não null no documento existente ou não.

O comportamento predefinido é **intercalar**.

### <a name="writebatchsize-property"></a>Propriedade de WriteBatchSize

Serviço de pesquisa do Azure suporta documentos de escrita como um lote. Um lote pode conter ações de 1 a 1000. Uma ação processa um documento para efetuar a operação de carregamento/intercalação.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>Suporte de tipo de dados

A seguinte tabela especifica se um tipo de dados de pesquisa do Azure é suportado ou não.

| Tipo de dados de pesquisa do Azure | Suportado no receptor de pesquisa do Azure |
| ---------------------- | ------------------------------ |
| Cadeia | S |
| Int32 | S |
| Int64 | S |
| duplo | S |
| Valor booleano | S |
| DataTimeOffset | S |
| Matriz de cadeia | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).