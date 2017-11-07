---
title: Copiar dados de armazenamento de Blobs do Azure utilizando o Data Factory / | Microsoft Docs
description: Saiba como copiar dados de arquivos de dados de origem suportada para o Blob Storage do Azure (ou) do armazenamento de BLOBs para sink suportados arquivos de dados utilizando o Data Factory.
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 10/13/2017
ms.author: jingwang
ms.openlocfilehash: 76b44766780a730092b31a0c44396f9851dd411a
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Copiar os dados de ou para armazenamento de Blobs do Azure utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-azure-blob-connector.md)
> * [Versão 2 - Pré-visualização](connector-azure-blob-storage.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados para e do armazenamento de Blobs do Azure. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [connnector de armazenamento do blogue do Azure no V1](v1/data-factory-azure-blob-connector.md).


## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados a partir de qualquer arquivo de dados de origem suportada para o Blob Storage do Azure ou copiar dados do Blob Storage do Azure para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens / ou sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md) tabela.

Especificamente, este conector de Blob do Azure suporta:

- Copiar os blobs de/para contas de armazenamento do Azure para fins gerais e de armazenamento de BLOBs de frequente/útil. 
- Copiar a blobs com ambos **chave da conta** e **serviço SAS** autenticações (assinatura de acesso partilhado).
- Copiar os blobs **do bloco, acrescentar ou blobs de páginas**e a cópia dos dados **para apenas blobs de blocos**. Armazenamento Premium do Azure não é suportado como um sink porque esteja associada a blobs de páginas.
- Copiar os blobs como-está ou analisar/gerar blobs com o [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas ao Blob Storage do Azure.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

### <a name="using-account-key"></a>Utilizando a chave de conta

Pode criar um serviço ligado do Storage do Azure, utilizando a chave de conta, que fornece a fábrica de dados com acesso global para o armazenamento do Azure. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **AzureStorage** |Sim |
| connectionString | Especificar as informações necessárias para ligar ao armazenamento do Azure para a propriedade connectionString. Marcar este campo como um SecureString. |Sim |
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
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>Utilizar a autenticação do serviço SAS

Também pode criar um serviço ligado do Storage do Azure através da utilização de um acesso assinatura partilhado (SAS), que fornece a fábrica de dados com acesso restrito/vínculo de tempo específico/todos os recursos (/ contentor do blob) no armazenamento.

Uma assinatura de acesso partilhado (SAS) concede acesso delegado a recursos na sua conta de armazenamento. Através da SAS, pode conceder que um cliente limitada permissões para objetos na sua conta de armazenamento para um determinado período de tempo, sem ter de partilhar as chaves de acesso da conta. O SAS é um URI que abrange nos respetivos parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a SAS, o cliente só tem de passar a SAS para o método ou construtor adequado. Para obter informações detalhadas sobre SAS, consulte [assinaturas de acesso partilhado: compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Azure Data Factory agora só suporta **serviço SAS** , mas não a conta SAS. Consulte [tipos de assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obter detalhes sobre estes dois tipos e de como construir. O URL de SAS gerado a partir do portal do Azure ou no Explorador de armazenamento é um SAS de conta, que não é suportado.
>

Para utilizar a autenticação do serviço SAS, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **AzureStorage** |Sim |
| sasUri | Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento do Azure como BLOBs, contentor ou tabela. Marcar este campo como um SecureString. |Sim |
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
- URI deve ser criado no contentor/blob correto ou com base na necessidade de nível da tabela. Um Uri de SAS para um blob do Azure permite que o serviço Data Factory aceder a esse blob específico. Um Uri de SAS a um contentor de Blobs do Azure permite que o serviço Data Factory iterar através de blobs no contentor. Se precisar de fornecer um acesso mais/menos objetos mais tarde, ou atualizar o URI de SAS, não se esqueça de atualizar o serviço ligado com o URI de novo.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados de Blobs do Azure.

Para copiar dados para/de Blob do Azure, defina a propriedade de tipo do conjunto de dados para **AzureBlob**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **AzureBlob** |Sim |
| folderPath | Caminho para o contentor e a pasta no armazenamento de Blobs. Exemplo: myblobcontainer/myblobfolder / |Sim |
| fileName | Especifique o nome do blob no **folderPath** se pretende copiar para/de um blob específico. Se não for especificado qualquer valor para esta propriedade, o conjunto de dados aponta para todos os blobs na pasta.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída e **preserveHierarchy** não foram especificadas no receptor de atividade, atividade de cópia gera automaticamente o nome do blob com o seguinte formato: `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Por exemplo: `Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz`. |Não |
| formato | Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formato](supported-file-formats-and-compression-codecs.md#orc-format), e [Parquet formato](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Nenhum (apenas para cenário de cópia binário) |
| Compressão | Especifique o tipo e o nível de compressão de dados. Para obter mais informações, consulte [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Optimal** e **Fastest**. |Não |

**Exemplo:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem do Blob do Azure e o sink.

### <a name="azure-blob-as-source"></a>Blob do Azure como origem

Para copiar dados de Blobs do Azure, defina o tipo de origem na atividade de cópia para **BlobSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **BlobSource** |Sim |
| Recursiva | Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada.<br/>Valores permitidos são: **verdadeiro** (predefinição), **false** | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-blob-as-sink"></a>Blob do Azure como sink

Para copiar dados para o Blob do Azure, defina o tipo de sink na atividade de cópia para **BlobSink**. São suportadas as seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida: **BlobSink** |Sim |
| copyBehavior | Define o comportamento de cópia quando a origem é ficheiros de arquivo de dados baseada em ficheiros.<br/><br/>Valores permitidos são:<br/><b>-PreserveHierarchy (predefinição)</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro de destino para a pasta de destino.<br/><b>-FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível da pasta de destino. Os ficheiros de destino tem o nome de automaticamente gerado. <br/><b>-MergeFiles</b>: une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro/Blob, o nome de ficheiro intercalado seria o nome especificado; caso contrário, seria nome de ficheiro gerada automaticamente. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior

Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| Recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| VERDADEIRO |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| VERDADEIRO |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | o destino Pasta1 é criado com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| VERDADEIRO |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | o destino Pasta1 é criado com a estrutura seguinte: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + ficheiro 5 é intercalado conteúdo para um ficheiro com nome de ficheiro gerado automaticamente |
| FALSO |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |
| FALSO |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |
| FALSO |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdos são intercalados para um ficheiro com nome de ficheiro gerada automaticamente. nome gerado automaticamente para File1<br/><br/>Não Subfolder1 com File3, File4 e File5 captado. |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).