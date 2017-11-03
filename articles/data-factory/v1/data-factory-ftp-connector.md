---
title: Mover dados de um servidor de FTP utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba mais sobre como mover dados de um servidor FTP através do Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2bc5ec9b6396850f863c51288f86a99f42db726b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Mover dados de um servidor de FTP utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-ftp-connector.md)
> * [Versão 2 - Pré-visualização](../connector-ftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector FTP na V2](../connector-ftp.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de um servidor de FTP. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um servidor de FTP para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Fábrica de dados atualmente suporta apenas dados mover de um servidor FTP para outros arquivos de dados, mas não mover dados de outros dados armazena um servidor de FTP. Suporta tanto no local e na nuvem servidores FTP.

> [!NOTE]
> A atividade de cópia não eliminar o ficheiro de origem após ser copiada com êxito para o destino. Se precisar de eliminar o ficheiro de origem depois de uma cópia com êxito, crie uma atividade personalizada para eliminar o ficheiro e utilize a atividade no pipeline. 

## <a name="enable-connectivity"></a>Ativar a conectividade
Se estiver a mover dados a partir de um **no local** servidor de FTP para uma nuvem armazenam (por exemplo, para o Blob storage do Azure) de dados, instalarem e utilizam o Data Management Gateway. O Data Management Gateway é um agente de cliente que está instalado no seu computador local e permite que os serviços de nuvem para ligar a um recurso no local. Para obter mais informações, consulte [Data Management Gateway](data-factory-data-management-gateway.md). Para instruções passo a passo sobre como configurar o gateway e utilizá-la, consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md). Utilizar o gateway para ligar a um servidor FTP, mesmo que o servidor está numa infraestrutura do Azure como uma máquina virtual (VM) do serviço (IaaS).

É possível instalar o gateway no mesmo computador local ou VM do IaaS como servidor de FTP. No entanto, recomendamos que instale o gateway num computador separado ou VM do IaaS para evitar a contenção de recursos e para um melhor desempenho. Quando instalar o gateway num computador separado, a máquina deve ser capaz de aceder ao servidor FTP.

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move dados a partir de uma origem FTP utilizando ferramentas diferentes ou APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente de cópia do Data Factory**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado.

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Quando utilizar APIs (exceto .NET API) ou ferramentas, é possível definir estas entidades do Data Factory, utilizando o formato JSON. Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados do FTP, consulte o [exemplo JSON: copiar dados do servidor de FTP para o blob do Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) secção deste artigo.

> [!NOTE]
> Para obter detalhes sobre os formatos de ficheiro e compressão suportados para utilizar, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para FTP.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte descreve os elementos JSON específicos para um serviço FTP ligado.

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| tipo |Defina esta opção para FtpServer. |Sim |&nbsp; |
| anfitrião |Especifique o nome ou endereço IP do servidor de FTP. |Sim |&nbsp; |
| authenticationType |Especifique o tipo de autenticação. |Sim |Anónimo, básico |
| o nome de utilizador |Especifique o utilizador que tem acesso ao servidor de FTP. |Não |&nbsp; |
| palavra-passe |Especifique a palavra-passe para o utilizador (nome de utilizador). |Não |&nbsp; |
| encryptedCredential |Especifique a credencial encriptada para aceder ao servidor FTP. |Não |&nbsp; |
| gatewayName |Especifique o nome do gateway no Data Management Gateway para ligar a um servidor FTP no local. |Não |&nbsp; |
| porta |Especifique a porta em que o servidor FTP está a escutar. |Não |21 |
| enableSsl |Especifique se pretende utilizar FTP através de um canal SSL/TLS. |Não |VERDADEIRO |
| enableServerCertificateValidation |Especifique se pretende ativar a validação do certificado SSL de servidor quando estiver a utilizar FTP através do canal SSL/TLS. |Não |VERDADEIRO |

### <a name="use-anonymous-authentication"></a>Utilize a autenticação anónima

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Utilize o nome de utilizador e palavra-passe em texto simples para a autenticação básica

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Utilizar porta, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Utilizar encryptedCredential para autenticação e de gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados. Fornece informações que são específicas para o tipo de conjunto de dados. O **typeProperties** secção para um conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Subcaminho para a pasta. Utilize o caráter de escape ' \ ' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar esta propriedade com **partitionBy** caminhos de pastas com base no início do setor e tempos de data de fim. |Sim |
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando **fileName** não está especificado para um conjunto de dados de saída é o nome do ficheiro gerado no seguinte formato: <br/><br/>Dados. <Guid>. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro para ser utilizado para selecionar um subconjunto de ficheiros a **folderPath**, em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (único caráter).<br/><br/>Exemplo 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com distribuído ficheiro sistema Hadoop (HDFS). |Não |
| partitionedBy |Utilizado para especificar um dinâmico **folderPath** e **fileName** para dados de séries de tempo. Por exemplo, pode especificar um **folderPath** que é parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte o [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender copiar ficheiros conforme forem entre arquivos baseados em ficheiros (cópia binário), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**, e níveis suportados são **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se pretende utilizar o modo de transferência de binários. Os valores são verdadeiras para modo binário (este é o valor predefinido) ou FALSO para ASCII. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> **nome de ficheiro** e **fileFilter** não podem ser utilizados em simultâneo.

### <a name="use-the-partionedby-property"></a>Utilize a propriedade partionedBy
Tal como mencionado na secção anterior, pode especificar um dinâmico **folderPath** e **fileName** para dados de séries de tempo com o **partitionedBy** propriedade.

Para mais informações sobre conjuntos de dados de séries de tempo, agendar e setores, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md), [agendamento e execução](data-factory-scheduling-and-execution.md), e [Criar pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo, {setor} é substituída pelo valor da variável de sistema do Data Factory SliceStart, no formato especificado (YYYYMMDDHH). SliceStart refere-se a hora do setor. O caminho da pasta é diferente para cada setor. (Por exemplo, wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104)

#### <a name="sample-2"></a>Exemplo 2

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
Neste exemplo, o ano, mês, dia e hora do SliceStart são extraídos em separado variáveis que são utilizadas pelo **folderPath** e **fileName** propriedades.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte [Criar pipelines](data-factory-create-pipelines.md). Propriedades, tais como o nome, descrição e de saída, tabelas e as políticas estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis no **typeProperties** secção da atividade, por outro lado, variar com cada tipo de atividade. Para a atividade de cópia, as propriedades do tipo variam consoante os tipos de origens e sinks.

Na atividade de cópia, quando a origem é do tipo **FileSystemSource**, da seguinte propriedade está disponível no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é de leitura em modo recursivo das subpastas, ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo JSON: copiar dados do servidor de FTP para o Blob do Azure
Este exemplo mostra como copiar dados a partir de um servidor FTP para o Blob storage do Azure. No entanto, os dados podem ser copiados diretamente a nenhum dos sinks indicados no [arquivos de dados e formatos suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats), utilizando a atividade de cópia numa fábrica de dados.  

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Um serviço ligado do tipo [FtpServer](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Uma entrada [dataset](data-factory-create-datasets.md) do tipo [partilha de ficheiros](#dataset-properties)
* Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

O exemplo copia dados a partir de um servidor FTP para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

### <a name="ftp-linked-service"></a>Serviço ligado de FTP

Este exemplo utiliza a autenticação básica, com o nome de utilizador e palavra-passe em texto simples. Também pode utilizar uma das seguintes formas:

* Autenticação anónima
* Autenticação básica com credenciais encriptado
* FTP por SSL/TLS (FTPS)

Consulte o [serviço ligado de FTP](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

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
### <a name="ftp-input-dataset"></a>Conjunto de dados de entrada de FTP

Este conjunto de dados refere-se para a pasta FTP `mysharedfolder` e ficheiro `test.csv`. O pipeline copia o ficheiro para o destino.

Definição **externo** para **verdadeiro** informa o serviço fábrica de dados que o conjunto de dados externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta do blob é avaliado dinamicamente, com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e em partes de horas a hora de início.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Uma atividade de cópia num pipeline com o sink de origem e de blob do sistema de ficheiros

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource**e o **sink** tipo está definido como **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
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
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* Para saber mais sobre os principais fatores que desempenho impacto de movimento de dados (atividade de cópia) na fábrica de dados e várias formas para otimizar o mesmo, consulte o [copiar guia Otimização e de desempenho de atividade](data-factory-copy-activity-performance.md).

* Para obter instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
