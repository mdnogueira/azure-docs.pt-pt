---
title: Copiar dados a partir do servidor SFTP utilizando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre o conector de MySQL no Azure Data Factory que lhe permite copiar dados de um servidor SFTP para um arquivo de dados suportado como um sink.
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
ms.openlocfilehash: 6726198687b9fa32930a7861bde6f9b994e2a3ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Copiar dados a partir do servidor SFTP utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-sftp-connector.md)
> * [Versão 2 - Pré-visualização](connector-sftp.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados a partir de um servidor SFTP. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector SFTP no V1](v1/data-factory-sftp-connector.md).

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados do servidor SFTP para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector SFTP suporta:

- Copiar ficheiros utilizando **básico** ou **parâmetros SshPublicKey** autenticação.
- Copiar ficheiros como-está ou analisar ficheiros com o [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas ao SFTP.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço SFTP ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Sftp**. |Sim |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Sim |
| porta | Porta em que o servidor SFTP faz a escuta.<br/>Valores permitidos são: inteiro, valor de defualt é **22**. |Não |
| skipHostKeyValidation | Especifique se pretende ignorar a validação de chave de anfitrião.<br/>Valores permitidos são: **verdadeiro**, **falso** (predefinição).  | Não |
| hostKeyFingerprint | Especifique a impressão de cima da situação da chave do anfitrião. | Sim, se o "skipHostKeyValidation" estiver definido como false.  |
| authenticationType | Especifique o tipo de autenticação.<br/>Valores permitidos são: **básico**, **parâmetros SshPublicKey**. Consulte [utilizando a autenticação básica](#using-basic-authentication) e [utilizando SSH autenticação de chave pública](#using-ssh-public-key-authentication) secções mais propriedades e exemplos JSON, respetivamente. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

### <a name="using-basic-authentication"></a>Utilizar a autenticação básica

Para utilizar autenticação básica, defina a propriedade de "authenticationType" como **básico**e especifique as seguintes propriedades além do conector SFTP aqueles genéricos introduzidas na última secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| Nome de utilizador | Utilizador que tenha acesso ao servidor SFTP. |Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). Marcar este campo como um SecureString. | Sim |

**Exemplo:**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
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

### <a name="using-ssh-public-key-authentication"></a>Utilizar a autenticação de chave pública SSH

Para utilizar a autenticação de chave pública SSH, definir a propriedade de "authenticationType" como **parâmetros SshPublicKey**e especifique as seguintes propriedades além do conector SFTP aqueles genéricos introduzidas na última secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| Nome de utilizador | Utilizador com acesso ao servidor de SFTP |Sim |
| privateKeyPath | Especifique um caminho absoluto para o ficheiro de chave privado que podem aceder a integração do tempo de execução. Aplica-se apenas quando personalizada alojada de tempo de execução de integração for especificado em "connectVia". | Especifique o `privateKeyPath` ou `privateKeyContent`.  |
| privateKeyContent | Conteúdo da chave privado SSH de codificados Base64. A chave privada SSH deve ter o formato de OpenSSH. Marcar este campo como um SecureString. | Especifique o `privateKeyPath` ou `privateKeyContent`. |
| frase de acesso | Especifique a passagem frase/palavra-passe para desencriptar a chave privada, se o ficheiro de chave estiver protegido por uma frase de acesso. Marcar este campo como um SecureString. | Sim, se o ficheiro de chave privada está protegido por uma frase de acesso. |

> [!NOTE]
> Conector SFTP suporta apenas OpenSSH chave. Certifique-se de que o ficheiro de chave está no formato correto. Pode utilizar o Putty ferramenta para converter de *.ppk OpenSSH formato.

**Exemplo 1: Autenticação de parâmetros SshPublicKey utilizar filePath chave privada**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: Autenticação de parâmetros SshPublicKey utilizando o conteúdo da chave privado**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados SFTP.

Para copiar dados de SFTP, defina a propriedade de tipo do conjunto de dados para **FileShare**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **partilha de ficheiros** |Sim |
| folderPath | Caminho para a pasta. Por exemplo: pasta/subpasta / |Sim |
| fileName | Especifique o nome do ficheiro no **folderPath** se pretender copiar a partir de um ficheiro específico. Se não for especificado qualquer valor para esta propriedade, o conjunto de dados de pontos a todos os ficheiros na pasta como origem. |Não |
| fileFilter | Especifique um filtro para ser utilizado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros. Aplica-se apenas ao nome de ficheiro não está especificado. <br/><br/>Permitidos carateres universais são: `*` (vários carateres) e `?` (único caráter).<br/>-Exemplo 1:`"fileFilter": "*.log"`<br/>-Exemplo 2:`"fileFilter": 2017-09-??.txt"` |Não |
| formato | Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar ficheiros com um formato específico, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formato](supported-file-formats-and-compression-codecs.md#orc-format), e [Parquet formato](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Nenhum (apenas para cenário de cópia binário) |
| Compressão | Especifique o tipo e o nível de compressão de dados. Para obter mais informações, consulte [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Optimal** e **Fastest**. |Não |

**Exemplo:**

```json
{
    "name": "SFTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
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

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem SFTP.

### <a name="sftp-as-source"></a>SFTP como origem

Para copiar dados de SFTP, defina o tipo de origem na atividade de cópia para **FileSystemSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **FileSystemSource** |Sim |
| Recursiva | Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada.<br/>Valores permitidos são: **verdadeiro** (predefinição), **false** | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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