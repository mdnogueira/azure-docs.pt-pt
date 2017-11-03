---
title: "Mover dados do servidor SFTP através do Azure Data Factory | Microsoft Docs"
description: Saiba mais sobre como mover dados de um local ou um servidor SFTP de nuvem utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 48c56e2de40a3166f22db88850c6df2489a35e8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Mover dados de um servidor SFTP através do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-sftp-connector.md)
> * [Versão 2 - Pré-visualização](../connector-sftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [SFTPconnector na V2](../connector-sftp.md).

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para mover dados de um servidor SFTP no local/nuvem para um arquivo de dados suportados sink. Este artigo baseia-se a [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo que apresenta uma descrição geral do movimento de dados com a atividade de cópia e a lista de arquivos de dados suportados como sinks/origens.

Fábrica de dados atualmente suporta apenas mover dados de um servidor SFTP para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um servidor SFTP. Suporta tanto no local e na nuvem servidores SFTP.

> [!NOTE]
> Atividade de cópia não eliminar o ficheiro de origem após ser copiada com êxito para o destino. Se precisar de eliminar o ficheiro de origem depois de uma cópia com êxito, crie uma atividade personalizada para eliminar o ficheiro e utilize a atividade no pipeline. 

## <a name="supported-scenarios-and-authentication-types"></a>Cenários suportados e os tipos de autenticação
Pode utilizar este conector SFTP para copiar dados a partir de **tanto na nuvem SFTP e no local SFTP servidores**. **Básico** e **parâmetros SshPublicKey** tipos de autenticação são suportados quando ligar ao servidor de SFTP.

Quando copiar dados a partir de um servidor SFTP no local, é necessário instalar o Data Management Gateway no local no ambiente de VMS do Azure. Consulte [Data Management Gateway](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como configurar o gateway e utilizá-la.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move dados a partir de uma origem SFTP utilizando ferramentas diferentes/APIs.

- A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

- Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. Para exemplos JSON copiar dados do servidor SFTP para armazenamento de Blobs do Azure, consulte [JSON de exemplo: copiar dados do servidor SFTP para BLOBs do Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) secção deste artigo.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicas do serviço ligado de FTP.

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| tipo | A propriedade de tipo tem de ser definida `Sftp`. |Sim |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta em que o servidor SFTP faz a escuta. O valor predefinido é: 21 |Não |
| authenticationType |Especifique o tipo de autenticação. Valores permitidos: **básico**, **parâmetros SshPublicKey**. <br><br> Consulte [utilizando a autenticação básica](#using-basic-authentication) e [utilizando SSH autenticação de chave pública](#using-ssh-public-key-authentication) secções mais propriedades e exemplos JSON, respetivamente. |Sim |
| skipHostKeyValidation | Especifique se pretende ignorar a validação de chave de anfitrião. | Não. O valor predefinido: false |
| hostKeyFingerprint | Especifique a impressão de cima da situação da chave do anfitrião. | Se Sim o `skipHostKeyValidation` está definido como falso.  |
| gatewayName |Nome do Data Management Gateway para ligar a um servidor SFTP no local. | Sim, se copiar dados a partir de um servidor SFTP no local. |
| encryptedCredential | Credencial encriptada para aceder ao servidor SFTP. Gerado automaticamente quando especifica a autenticação básica (nome de utilizador + palavra-passe) ou autenticação de parâmetros SshPublicKey (nome de utilizador + caminho da chave privado ou conteúdo) no Assistente para copiar ou a caixa de diálogo de pop-up de ClickOnce. | Não. Aplicam-se apenas ao copiar dados a partir de um servidor SFTP no local. |

### <a name="using-basic-authentication"></a>Utilizar a autenticação básica

Para utilizar autenticação básica, defina `authenticationType` como `Basic`e especifique as seguintes propriedades além do conector SFTP aqueles genéricos introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| o nome de utilizador | Utilizador que tenha acesso ao servidor SFTP. |Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

#### <a name="example-basic-authentication"></a>Exemplo: A autenticação básica
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: A autenticação básica com a credencial encriptada

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Utilizar a autenticação de chave pública SSH

Para utilizar a autenticação de chave pública SSH, defina `authenticationType` como `SshPublicKey`e especifique as seguintes propriedades além do conector SFTP aqueles genéricos introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| o nome de utilizador |Utilizador com acesso ao servidor de SFTP |Sim |
| privateKeyPath | Especifique um caminho absoluto para o ficheiro de chave privada pode aceder a este gateway. | Especifique o `privateKeyPath` ou `privateKeyContent`. <br><br> Aplicam-se apenas ao copiar dados a partir de um servidor SFTP no local. |
| privateKeyContent | Uma cadeia serializada do conteúdo da chave privada. O Assistente para copiar pode ler o ficheiro de chave privado e extrair automaticamente o conteúdo da chave privado. Se estiver a utilizar qualquer outra ferramenta/SDK, utilize a propriedade de privateKeyPath em vez disso. | Especifique o `privateKeyPath` ou `privateKeyContent`. |
| frase de acesso | Especifique a passagem frase/palavra-passe para desencriptar a chave privada, se o ficheiro de chave estiver protegido por uma frase de acesso. | Sim, se o ficheiro de chave privada está protegido por uma frase de acesso. |

> [!NOTE]
> Conector SFTP só suportam OpenSSH chave. Certifique-se de que o ficheiro de chave está no formato correto. Pode utilizar o Putty ferramenta para converter de *.ppk OpenSSH formato.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exemplo: Autenticação de parâmetros SshPublicKey utilizando filePath chave privada

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: Autenticação de parâmetros SshPublicKey utilizando o conteúdo da chave privado

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados. Fornece informações que são específicas para o tipo de conjunto de dados. Os typeProperties secção para um conjunto de dados do tipo **FileShare** dataset tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho de subchaves para a pasta. Utilize o caráter de escape ' \ ' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter pasta caminhos com base no setor início/fim tempos de data. |Sim |
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| fileFilter |Especifique um filtro para ser utilizado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (único caráter).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro de dados de séries de tempo. Por exemplo, folderPath parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência de binários. Verdadeiro para o modo binário e ASCII FALSO. Valor predefinido: True. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizados em simultâneo.

### <a name="using-partionedby-property"></a>Utilizar a propriedade partionedBy
Tal como mencionado na secção anterior, pode especificar um folderPath dinâmica, o nome de ficheiro de dados de séries de tempo com partitionedBy. Pode fazê-lo com as macros fábrica de dados e a variável de sistema SliceStart, SliceEnd que indicam o período de tempo de lógica de um setor de dados indicado.

Para mais informações sobre conjuntos de dados de séries de tempo, agendar e setores, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md), [agendamento e execução](data-factory-scheduling-and-execution.md), e [criar Pipelines](data-factory-create-pipelines.md) artigos.

#### <a name="sample-1"></a>Exemplo 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo é substituído {setor} com o valor da variável de sistema do Data Factory SliceStart no formato (YYYYMMDDHH) especificado. SliceStart refere-se a hora do setor. FolderPath é diferente para cada setor. Exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em separado variáveis que são utilizadas pelas propriedades folderPath e nome de ficheiro.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e as políticas estão disponíveis para todos os tipos de atividades.

Enquanto, as propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, as propriedades do tipo variam consoante os tipos de origens e sinks.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e compressão suportados
Consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo em detalhes.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Exemplo JSON: Copiar dados do servidor SFTP para BLOBs do Azure
O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados de origem SFTP para armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

> [!IMPORTANT]
> Este exemplo fornece fragmentos JSON. Não inclui instruções passo a passo para criação da fábrica de dados. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo.

O exemplo tem as seguintes entidades de fábrica de dados:

* Um serviço ligado do tipo [sftp](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Uma entrada [dataset](data-factory-create-datasets.md) do tipo [FileShare](#dataset-properties).
* Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados a partir de um servidor SFTP para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado de SFTP**

Este exemplo utiliza a autenticação básica com o nome de utilizador e palavra-passe em texto simples. Também pode utilizar uma das seguintes formas:

* Autenticação básica com credenciais encriptado
* Autenticação de chave pública SSH

Consulte [serviço ligado de FTP](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Serviço ligado do Armazenamento do Azure**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada de SFTP**

Este conjunto de dados refere-se para a pasta SFTP `mysharedfolder` e ficheiro `test.csv`. O pipeline copia o ficheiro para o destino.

A definição "external": "true" informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Conjunto de dados de saída de Blobs do Azure**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource** e **sink** tipo está definido como **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.
