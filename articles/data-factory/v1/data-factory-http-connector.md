---
title: Mover dados a partir de uma origem HTTP - Azure | Microsoft Docs
description: Saiba mais sobre como mover dados de um local ou uma origem HTTP de nuvem com o Azure Data Factory.
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
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b03be0df05f85ec9ecd1fca4042e87c838022c7
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Mover dados a partir de uma origem HTTP utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-http-connector.md)
> * [Versão 2 - Pré-visualização](../connector-http.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conetor HTTP na V2](../connector-http.md).


Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para mover dados de um ponto de final HTTP no local/nuvem para um arquivo de dados suportados sink. Este artigo baseia-se a [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo que apresenta uma descrição geral do movimento de dados com a atividade de cópia e a lista de arquivos de dados suportados como sinks/origens.

Fábrica de dados atualmente suporta apenas mover dados de uma origem HTTP outros arquivos de dados, mas não mover dados de outros dados armazena para um destino de HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários suportados e os tipos de autenticação
Pode utilizar este conector HTTP para obter dados de **ponto final HTTP/s de nuvem e no local** através de HTTP **obter** ou **POST** método. São suportados os seguintes tipos de autenticação: **anónimo**, **básico**, **resumida**, **Windows**, e  **ClientCertificate**. Tenha em atenção a diferença entre este conector e a [conector Web da tabela](data-factory-web-table-connector.md) é: a última opção é utilizada para extrair conteúdo de tabela de página web HTML.

Quando copiar dados a partir de um ponto de final HTTP no local, é necessário instalar o Data Management Gateway no local no ambiente de VMS do Azure. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move dados a partir de uma origem HTTP utilizando ferramentas diferentes/APIs.

- A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

- Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. Para exemplos JSON copiar dados de origem HTTP para o Blob Storage do Azure, consulte [exemplos JSON](#json-examples) secção este artigo.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos HTTP serviço ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo | A propriedade de tipo tem de ser definida: `Http`. | Sim |
| URL | URL de base ao servidor Web | Sim |
| authenticationType | Especifica o tipo de autenticação. Valores permitidos são: **anónimo**, **básico**, **resumida**, **Windows**, **ClientCertificate**. <br><br> Consulte a secções abaixo desta tabela mais propriedades e exemplos JSON para os tipos de autenticação, respetivamente. | Sim |
| enableServerCertificateValidation | Especifique se pretende ativar a validação do certificado SSL de servidor, se a origem é o servidor de Web de HTTPS | Não, a predefinição é verdadeiro |
| gatewayName | Nome do Data Management Gateway para ligar a uma origem HTTP no local. | Sim, se copiar dados a partir de uma origem HTTP no local. |
| encryptedCredential | Credencial encriptada para aceder ao ponto final HTTP. Gerado automaticamente quando configura as informações de autenticação no Assistente para copiar ou a caixa de diálogo de pop-up de ClickOnce. | Não. Aplicam-se apenas ao copiar dados a partir de um servidor HTTP no local. |

Consulte [mover dados entre origens no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre como definir credenciais para origem de dados de conector HTTP no local.

### <a name="using-basic-digest-or-windows-authentication"></a>Utilizar a autenticação básica, resumida ou do Windows

Definir `authenticationType` como `Basic`, `Digest`, ou `Windows`e especifique as seguintes propriedades além do conector HTTP aqueles genéricos introduzidas acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Nome de utilizador para aceder ao ponto final HTTP. | Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemplo: utilizar a autenticação básica, resumida ou do Windows

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Utilizar a autenticação de ClientCertificate

Para utilizar autenticação básica, defina `authenticationType` como `ClientCertificate`e especifique as seguintes propriedades além do conector HTTP aqueles genéricos introduzidas acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| embeddedCertData | O conteúdo dos dados binários do ficheiro Personal Information (Exchange PFX) com codificação Base64. | Especifique o `embeddedCertData` ou `certThumbprint`. |
| CertThumbprint | O thumbprint do certificado que foi instalado no arquivo de certificados do computador do gateway. Aplicam-se apenas ao copiar dados a partir de uma origem HTTP no local. | Especifique o `embeddedCertData` ou `certThumbprint`. |
| palavra-passe | Palavra-passe associado ao certificado. | Não |

Se utilizar `certThumbprint` para autenticação e o certificado está instalado no arquivo pessoal do computador local, tem de conceder a permissão de leitura para o serviço de gateway:

1. Inicie a consola de gestão da Microsoft (MMC). Adicionar o **certificados** snap-in que tenha como destino o **computador Local**.
2. Expanda **certificados**, **pessoais**e clique em **certificados**.
3. O certificado do arquivo pessoal com o botão direito e selecione **todas as tarefas**->**gerir chaves privadas...**
3. No **segurança** separador, adicione a conta de utilizador na qual o serviço de anfitrião do Data Management Gateway está em execução com o acesso de leitura para o certificado.  

#### <a name="example-using-client-certificate"></a>Exemplo: utilizar o certificado de cliente
Este serviço ligado liga a fábrica de dados para um servidor de web HTTP no local. Utiliza um certificado de cliente é instalado na máquina com o Data Management Gateway instalado.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exemplo: utilizar o certificado de cliente num ficheiro
Este serviço ligado liga a fábrica de dados para um servidor de web HTTP no local. Utiliza um ficheiro de certificado de cliente na máquina com o Data Management Gateway instalado.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **Http** tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | Especificar o tipo do conjunto de dados. tem de ser definido como `Http`. | Sim |
| relativeUrl | Um URL relativo para o recurso que contém os dados. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição de serviço ligado. <br><br> Para construir o URL dinâmico, pode utilizar [funções de Data Factory e variáveis do sistema](data-factory-functions-variables.md), por exemplo, "relativeUrl": "$$Text.Format (" os meus/relatório? mês = {0:yyyy}-{0:MM} & fmt = csv', SliceStart) ". | Não |
| requestMethod | Método de HTTP. Valores permitidos são **obter** ou **POST**. | Não. Predefinição é `GET`. |
| additionalHeaders | Cabeçalhos de pedido HTTP adicionais. | Não |
| RequestBody | Corpo do pedido de HTTP. | Não |
| formato | Se pretender simplesmente **obter os dados do ponto final de HTTP como-é** sem análise-lo, ignore este definições do formato. <br><br> Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="example-using-the-get-default-method"></a>Exemplo: utilizando o método GET (predefinição)

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

### <a name="example-using-the-post-method"></a>Exemplo: utilizando o método POST

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis no **typeProperties** secção da atividade por outro lado variar com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Atualmente, quando a origem na atividade de cópia é do tipo **HttpSource**, são suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (TimeSpan) para o pedido HTTP obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite de ler os dados de resposta. | Não. Valor predefinido: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e compressão suportados
Consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo em detalhes.

## <a name="json-examples"></a>Exemplos JSON
O exemplo a seguir fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados a partir da origem HTTP para o Blob Storage do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Exemplo: Copiar dados a partir da origem HTTP para o Blob Storage do Azure
A solução de fábrica de dados para este exemplo contém as seguintes entidades do Data Factory:

1. Um serviço ligado do tipo [HTTP](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [Http](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [HttpSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados a partir de uma origem HTTP para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

### <a name="http-linked-service"></a>Serviço ligado de HTTP
Este exemplo utiliza o serviço ligado de HTTP com a autenticação anónima. Consulte [serviço ligado de HTTP](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
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

### <a name="http-input-dataset"></a>Conjunto de dados de entrada de HTTP
Definição **externo** para **verdadeiro** informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1).

```JSON
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-with-copy-activity"></a>Pipeline com atividade de cópia

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **HttpSource** e **sink** tipo está definido como **BlobSink**.

Consulte [HttpSource](#copy-activity-properties) para a lista de propriedades suportadas pelo HttpSource.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
