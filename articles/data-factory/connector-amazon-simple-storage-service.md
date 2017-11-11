---
title: "Copiar dados do Amazon serviço de armazenamento simples utilizando o Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre como copiar dados a partir do serviço de armazenamento simples (S3) do Amazon aos arquivos de dados dependente suportados através do Azure Data Factory."
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 2bfc59931bbbb08ad1cd0328cf624b7350f92ec0
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copiar dados do Amazon serviço de armazenamento simples utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versão 2 - Pré-visualização](connector-amazon-simple-storage-service.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados para e do armazenamento de Blobs do Azure. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [Amazon S3 connnector no V1](v1/data-factory-amazon-simple-storage-service-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados a partir de qualquer arquivo de dados de origem suportada para o Azure Data Lake Store ou copiar dados do Azure Data Lake Store para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Amazon S3 suporta a cópia de ficheiros como-está ou analisar ficheiros com o [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md).

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados do Amazon S3, certifique-se de que lhe foram concedidas as seguintes permissões:

- `s3:GetObject`e `s3:GetObjectVersion` para operações de objeto do Amazon S3.
- `s3:ListBucket`para operações de registo do Amazon S3. Se estiver a utilizar o Assistente de cópia do Data Factory, `s3:ListAllMyBuckets` também é necessário.

Para obter detalhes sobre a lista completa de permissões do Amazon S3, consulte [especificar permissões numa política](http://docs.aws.amazon.com/amazons3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir o Amazon S3 entidades do Data Factory específicas.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço Amazon S3 ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida **AmazonS3**. | Sim |
| accessKeyId | ID da chave de acesso secreta. |Sim |
| secretAccessKey | A chave de acesso secreta próprio. Marcar este campo como um SecureString. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

>[!NOTE]
>Este conector requer chaves de acesso da conta de IAM copiar dados do Amazon S3. [Credencial de segurança temporário](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) não é suportada.
>

Segue-se um exemplo:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                    "type": "SecureString",
                    "value": "<secret access key>"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados do Amazon S3.

Para copiar dados do Amazon S3, defina a propriedade de tipo do conjunto de dados para **AmazonS3Object**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **AmazonS3Object** |Sim |
| bucketName | O nome do registo de S3. |Sim |
| key | A chave do objeto de S3. Aplica-se apenas ao prefixo não está especificado. |Não |
| prefixo | Prefixo para a chave do objeto de S3. Objetos cujas chaves começar a utilizar este prefixo estão selecionados. Aplica-se apenas quando chave não está especificada. |Não |
| Versão | A versão do objeto S3, se o controlo de versões de S3 estiver ativado. |Não |
| formato | Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ou gerar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formato](supported-file-formats-and-compression-codecs.md#orc-format), e [Parquet formato](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Nenhum (apenas para cenário de cópia binário) |
| Compressão | Especifique o tipo e o nível de compressão de dados. Para obter mais informações, consulte [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Optimal** e **Fastest**. |Não |

> [!NOTE]
> **bucketName + chave** Especifica a localização do objeto S3, onde o registo é o contentor de raiz para objetos de S3 e a chave é o caminho completo para o objeto de S3.

**Exemplo: utilizar o prefixo**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**Exemplo: utilizar a chave e versão (opcional)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pelo Azure Data Lake origem e dependente.

### <a name="amazon-s3-as-source"></a>Amazon S3 como origem

Para copiar dados do Amazon S3, defina o tipo de origem na atividade de cópia para **FileSystemSource** (que inclui o Amazon S3). As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **FileSystemSource** |Sim |
| Recursiva | Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada.<br/>Valores permitidos são: **verdadeiro** (predefinição), **false** | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).