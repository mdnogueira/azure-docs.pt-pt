---
title: Copiar dados de origem HTTP utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de uma origem HTTP na nuvem ou no local para os arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
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
ms.date: 10/12/2017
ms.author: jingwang
ms.openlocfilehash: 54afc7d993058ac2b3d2990ba131d334e9332555
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Copiar dados de ponto final de HTTP utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-http-connector.md)
> * [Versão 2 - Pré-visualização](connector-http.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de um ponto final de HTTP. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector HTTP no V1](v1/data-factory-http-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados a partir da origem HTTP para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector HTTP suporta:

- Obter dados a partir do ponto final HTTP/s através de HTTP **obter** ou **POST** método.
- Obter dados com as seguintes autenticações: **anónimo**, **básico**, **resumida**, **Windows**, e  **ClientCertificate**.
- Copiar a resposta HTTP como-está ou análise-o com o [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md).

A diferença entre este conector e a [conector Web da tabela](connector-web-table.md) que a última opção é utilizada para extrair conteúdo de tabela de página web HTML.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md)) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector HTTP.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado de HTTP:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **HttpServer**. | Sim |
| URL | URL de base ao servidor Web | Sim |
| enableServerCertificateValidation | Especifique se pretende ativar a validação do certificado SSL de servidor ao ligar ao ponto final de HTTP. | Não, a predefinição é verdadeiro |
| authenticationType | Especifica o tipo de autenticação. Valores permitidos são: **anónimo**, **básico**, **resumida**, **Windows**, **ClientCertificate**. <br><br> Consulte a secções abaixo desta tabela mais propriedades e exemplos JSON para os tipos de autenticação, respetivamente. | Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

### <a name="using-basic-digest-or-windows-authentication"></a>Utilizar a autenticação básica, resumida ou do Windows

Definir a propriedade "authenticationType" para **básico**, **resumida**, ou **Windows**e especifique as seguintes propriedades, juntamente com propriedades genéricas descritas no anterior secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| Nome de utilizador | Nome de utilizador para aceder ao ponto final HTTP. | Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). Marcar este campo como SecureString. | Sim |

**Exemplo**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>Utilizar a autenticação de ClientCertificate

Para utilizar autenticação ClientCertificate, defina a propriedade de "authenticationType" **ClientCertificate**e especifique as seguintes propriedades, juntamente com as propriedades genéricas descritas na secção anterior:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| embeddedCertData | Dados do certificado codificado em Base64. | Especifique o `embeddedCertData` ou `certThumbprint`. |
| CertThumbprint | O thumbprint do certificado que está instalado no arquivo de certificados do seu tempo de execução do Self-hosted integração da máquina. Aplica-se apenas quando personalizada alojado tipo de tempo de execução de integração é especificado no connectVia. | Especifique o `embeddedCertData` ou `certThumbprint`. |
| palavra-passe | Palavra-passe associado ao certificado. Marcar este campo como SecureString. | Não |

Se utilizar "certThumbprint" para a autenticação e o certificado está instalado no arquivo pessoal do computador local, terá de conceder a permissão de leitura para o tempo de execução de integração Self-hosted:

1. Inicie a consola de gestão da Microsoft (MMC). Adicionar o **certificados** snap-in que tenha como destino o **computador Local**.
2. Expanda **certificados**, **pessoais**e clique em **certificados**.
3. O certificado do arquivo pessoal com o botão direito e selecione **todas as tarefas** -> **gerir chaves privadas...**
3. No **segurança** separador, adicione a conta de utilizador na qual o serviço de anfitrião de tempo de execução de integração (DIAHostService) está em execução com o acesso de leitura para o certificado.

**Exemplo 1: utilizar certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: utilizar embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados HTTP.

Para copiar dados de HTTP, defina a propriedade de tipo do conjunto de dados para **HttpFile**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **HttpFile** | Sim |
| relativeUrl | Um URL relativo para o recurso que contém os dados. Quando esta propriedade não for especificada, é utilizado apenas o URL especificado na definição de serviço ligado. | Não |
| requestMethod | Método de HTTP.<br/>Valores permitidos são **obter** (predefinição) ou **Post**. | Não |
| additionalHeaders | Cabeçalhos de pedido HTTP adicionais. | Não |
| RequestBody | Corpo do pedido de HTTP. | Não |
| formato | Se pretender **obter dados a partir do ponto final de HTTP como-é** sem análise e copiar para um arquivo baseado em ficheiros, ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída.<br/><br/>Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de formato de ficheiro: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato Json](supported-file-formats-and-compression-codecs.md#json-format), [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formato](supported-file-formats-and-compression-codecs.md#orc-format), e [Parquet formato](supported-file-formats-and-compression-codecs.md#parquet-format) secções. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Para obter mais informações, consulte [suportado os formatos de ficheiro e compressão codecs](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis suportados são: **Optimal** e **Fastest**. |Não |

**Exemplo 1: utilizar o método Get (predefinição)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Exemplo 2: utilizando o método Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem HTTP.

### <a name="http-as-source"></a>HTTP como origem

Para copiar dados de HTTP, defina o tipo de origem na atividade de cópia para **HttpSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **HttpSource** | Sim |
| httpRequestTimeout | O tempo limite (TimeSpan) para o pedido HTTP obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite de ler os dados de resposta.<br/> Valor predefinido é: 00:01:40  | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).