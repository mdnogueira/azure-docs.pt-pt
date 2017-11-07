---
title: Mover dados para e do SQL Server | Microsoft Docs
description: "Saiba mais sobre como mover dados para/a partir da base de dados de SQL Server que está no local ou numa VM do Azure utilizando o Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 37eb7b728bebcec5c389a8bdf68be6baf97f3c38
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Mover dados para e do SQL Server no local ou no IaaS (VM do Azure) utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-sqlserver-connector.md)
> * [Versão 2 - Pré-visualização](../connector-sql-server.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector do SQL Server no V2](../connector-sql-server.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados para/de uma base de dados do SQL Server no local. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia. 

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **de uma base de dados do SQL Server** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **para uma base de dados do SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Versões suportadas do SQL Server
Este suporte de conector do SQL Server copiar dados de/para as seguintes versões de instância alojado no local ou no IaaS do Azure utilizando a autenticação do SQL Server e a autenticação do Windows: SQL Server 2016, o SQL Server 2014, o SQL Server 2012, o SQL Server 2008 R2, o SQL Server 2008, do SQL Server 2005

## <a name="enabling-connectivity"></a>Ativar a conectividade
Os conceitos e os passos necessários para estabelecer a ligação com SQL Server alojado no local ou em VMs do IaaS do Azure (infraestrutura-como-um-serviço) são os mesmos. Em ambos os casos, terá de utilizar o Data Management Gateway para a conectividade.

Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway. Configurar uma instância de gateway é um pré-requisito para a ligação com o SQL Server.

Enquanto pode instalar o gateway no mesmo computador local ou instância VM na nuvem como o SQL Server para um melhor desempenho, recomendamos que instalá-los em computadores separados. Ter o gateway e o SQL Server em máquinas separadas reduz a contenção de recursos.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de/para uma base de dados do SQL Server no local utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Criar um **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines. 
2. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de uma base de dados do SQL Server para um armazenamento de Blobs do Azure, criar dois serviços ligados para ligar a base de dados do SQL Server e a conta do storage do Azure à fábrica de dados. As propriedades de serviço ligado que são específicas para a base de dados do SQL Server, consulte [ligado propriedades do serviço](#linked-service-properties) secção. 
3. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar a tabela SQL na base de dados do SQL Server que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar o contentor de blob e a pasta que contém os dados copiados da base de dados do SQL Server. As propriedades do conjunto de dados que são específicas para a base de dados do SQL Server, consulte [propriedades do dataset](#dataset-properties) secção.
4. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. No exemplo mencionado anteriormente, utilizar SqlSource como uma origem e BlobSink como um sink para a atividade de cópia. Da mesma forma, se estiver a copiar do Blob Storage do Azure para a base de dados do SQL Server, utilizar BlobSource e SqlSink na atividade de cópia. Para as propriedades da atividade de cópia que são específicas para a base de dados do SQL Server, consulte [copiar propriedades da atividade](#copy-activity-properties) secção. Para obter detalhes sobre como utilizar um arquivo de dados como uma origem ou de um receptor de mensagens em fila, clique na ligação na secção anterior para o arquivo de dados. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para/de uma base de dados do SQL Server no local, consulte [exemplos JSON](#json-examples-for-copying-data-from-and-to-sql-server) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para o SQL Server: 

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
Criar um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server no local a uma fábrica de dados. A tabela seguinte fornece uma descrição para os elementos JSON específicas do serviço do SQL Server ligado no local.

A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço ligado do SQL Server.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especificar as informações de connectionString necessárias para ligar à base de dados de SQL Server no local, utilizando a autenticação SQL ou autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar a autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |

Pode encriptar as credenciais utilizando o **New-AzureRmDataFactoryEncryptValue** cmdlet e utilizá-los na cadeia de ligação, conforme mostrado no exemplo seguinte (**EncryptedCredential** propriedade):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Amostras
**JSON para utilizar a autenticação do SQL Server**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON para utilizar a autenticação do Windows**

O Data Management Gateway será representar a conta de utilizador especificada para ligar à base de dados do SQL Server no local. 

```json
{
     "Name": " MyOnPremisesSQLDB",
     "Properties":
     {
         "type": "OnPremisesSqlServer",
         "typeProperties": {
             "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
             "username": "<domain\\username>",
             "password": "<password>",
             "gatewayName": "<gateway name>"
        }
     }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Nos exemplos, utilizou um conjunto de dados do tipo **SqlServerTable** para representar uma tabela numa base de dados do SQL Server.  

Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL Server, BLOBs do Azure, a tabela do Azure, etc.).

A secção de typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **SqlServerTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância da base de dados do SQL Server que o serviço ligado refere-se. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Se estiver a mover dados de uma base de dados do SQL Server, defina o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a mover dados para uma base de dados do SQL Server, definir o tipo de sink na atividade de cópia para **SqlSink**. Esta secção fornece uma lista de propriedades suportadas por SqlSource e SqlSink.

Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e as políticas estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia demora apenas uma entrada e produz saída de apenas um.

Enquanto, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

### <a name="sqlsource"></a>SqlSource
Quando a origem de uma atividade de cópia é do tipo **SqlSource**, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. Pode referenciar várias tabelas da base de dados referenciada pelo conjunto de dados de entrada. Se não for especificado, a instrução de SQL que é executada: selecione a partir de MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados a partir da tabela de origem. |Nome do procedimento armazenado. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

Se o **sqlReaderQuery** está especificado para o SqlSource, a atividade de cópia executa esta consulta contra a origem de base de dados do SQL Server para obter os dados.

Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura serão utilizadas para criar uma consulta Selecione para executar a base de dados do SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

> [!NOTE]
> Quando utiliza **sqlReaderStoredProcedureName**, terá de especificar um valor para o **tableName** propriedade no conjunto de dados JSON. Não existem nenhum validações executadas apesar desta tabela.

### <a name="sqlsink"></a>SqlSink
**SqlSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpa. Para obter mais informações, consulte [cópia repetíveis](#repeatable-copy) secção. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia preencher com o identificador de setor automaticamente gerado, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. Para obter mais informações, consulte [cópia repetíveis](#repeatable-copy) secção. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado dados upserts (inserções/atualizações) para a tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |
| sqlWriterTableType |Especifique o nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia faz com que os dados a ser movidos disponível numa tabela temporária com este tipo de tabela. Código do procedimento armazenado, em seguida, pode intercalar os dados que está a ser copiados com dados existentes. |Um nome de tipo de tabela. |Não |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Exemplos JSON para copiar dados de e para o SQL Server
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos seguintes mostram como copiar dados de e para o SQL Server e o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exemplo: Copiar dados do SQL Server para o Blob do Azure
O exemplo seguinte mostra:

1. Um serviço ligado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [SqlServerTable](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de séries de tempo de uma tabela do SQL Server para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

Como primeiro passo, configure o data management gateway. As instruções são no [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado do SQL Server**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Serviço de ligado do armazenamento de Blobs do Azure**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do SQL Server**

O exemplo assume que criou uma tabela "MyTable" no SQL Server e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo. Pode consultar através de várias tabelas dentro da mesma base de dados para um único conjunto de dados, mas uma única tabela tem de ser utilizada para typeProperty tableName do conjunto de dados.

A definição "external": "true" informa serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Conjunto de dados de saída de Blobs do Azure**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **SqlReaderQuery** propriedade seleciona os dados na última hora para copiar.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
Neste exemplo, **sqlReaderQuery** especificado para o SqlSource. A atividade de cópia executa esta consulta na origem de base de dados do SQL Server para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros). O sqlReaderQuery pode fazer referência a várias tabelas na base de dados referenciada pelo conjunto de dados de entrada. Não é limitado para a tabela só definir como typeProperty tableName do conjunto de dados.

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura serão utilizadas para criar uma consulta Selecione para executar a base de dados do SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

Consulte o [origem Sql](#sqlsource) secção e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para a lista de propriedades suportadas por SqlSource e BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exemplo: Copiar dados de Blobs do Azure para o SQL Server
O exemplo seguinte mostra:

1. O serviço ligado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. O serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlSink](#sql-server-copy-activity-type-properties).

As cópias de exemplo séries de tempo dados a partir de um Azure blob para um SQL Server a tabela a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado do SQL Server**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Serviço de ligado do armazenamento de Blobs do Azure**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada Blob do Azure**

Dados são captados um blob de novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome de ficheiro utiliza a parte de hora a hora de início. "external": "true" definição informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Conjunto de dados de saída de SQL Server**

O exemplo copia dados para uma tabela com o nome "MyTable" no SQL Server. Crie a tabela no SQL Server com o mesmo número de colunas como espera que o ficheiro CSV de Blob que contém. Novas linhas são adicionadas à tabela de cada hora.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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

## <a name="troubleshooting-connection-issues"></a>Resolução de problemas de ligação
1. Configure o servidor de SQL para aceitar ligações remotas. Iniciar **SQL Server Management Studio**, faça duplo clique **servidor**e clique em **propriedades**. Selecione **ligações** na lista e verificação **permitir ligações remotas ao servidor**.

    ![Ativar ligações remotas](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Consulte [configurar a opção de configuração do servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx) para obter passos detalhados.
2. Iniciar **Gestor de configuração do SQL Server**. Expanda **configuração de rede do SQL Server** para a instância que pretende e selecione **protocolos para MSSQLSERVER**. Deverá ver protocolos no painel direito. Ative TCP/IP clicando **TCP/IP** e clicando em **ativar**.

    ![Ative TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Consulte [ativar ou desativar um protocolo de rede do servidor](https://msdn.microsoft.com/library/ms191294.aspx) para obter detalhes e alternativas formas de ativar o protocolo TCP/IP.
3. Na janela do mesma, faça duplo clique em **TCP/IP** iniciar **propriedades de TCP/IP** janela.
4. Mudar para o **endereços IP** separador. Desloque para baixo para ver **IPAll** secção. Tome nota o * * a porta TCP * * (predefinição é **1433**).
5. Criar um **regra da Firewall do Windows** no computador para permitir tráfego de entrada através desta porta.  
6. **Verificar ligação**: para ligar ao SQL Server com o nome completamente qualificado, utilize o SQL Server Management Studio de um computador diferente. Por exemplo: "<machine>.<domain>.corp.<company>.com,1433."

   > [!IMPORTANT]

   > Consulte [mover dados entre origens no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obter informações detalhadas.
   >
   > Consulte [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para dicas sobre/gateway de ligação de resolução de problemas relacionados com problemas.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados de destino
Esta secção fornece um exemplo que copia dados a partir de uma tabela de origem com nenhuma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Tabela de destino:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Tenha em atenção que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definição de JSON do conjunto de dados de destino**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Tenha em atenção que, como a tabela de origem e destino têm esquemas diferentes (o destino tem uma coluna adicional com a identidade). Neste cenário, tem de especificar **estrutura** propriedade na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar um procedimento armazenado do sink do SQL Server
Consulte [invocar um procedimento armazenado para sink do SQL Server na atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md) artigo para obter um exemplo de invocar um procedimento armazenado do sink do SQL Server numa atividade de cópia de um pipeline.

## <a name="type-mapping-for-sql-server"></a>Mapeamento de tipos para o SQL server
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de passo 2:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Ao mover dados para & do SQL server, são utilizados os seguintes mapeamentos de tipo SQL para o tipo de .NET e vice-versa.

O mapeamento está mesmo que o mapeamento de tipos de dados do SQL Server para ADO.NET.

| Tipo de motor de base de dados do SQL Server | Tipo de .NET framework |
| --- | --- |
| bigint |Int64 |
| Binário |Byte] |
| bits |Valor booleano |
| char |Cadeia, Char [] |
| Data |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte] |
| Número de vírgula flutuante |duplo |
| Imagem |Byte] |
| Int |Int32 |
| dinheiro |Decimal |
| nchar |Cadeia, Char [] |
| ntext |Cadeia, Char [] |
| um valor numérico |Decimal |
| nvarchar |Cadeia, Char [] |
| real |Único |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| em smallmoney |Decimal |
| sql_variant |Objeto * |
| Texto |Cadeia, Char [] |
| hora |TimeSpan |
| carimbo de data/hora |Byte] |
| tinyint |Bytes |
| uniqueidentifier |GUID |
| varbinary |Byte] |
| varchar |Cadeia, Char [] |
| xml |XML |

## <a name="mapping-source-to-sink-columns"></a>Origem de mapeamento para colunas de sink
Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Cópia repetíveis
Quando copiar dados para a base de dados do SQL Server, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Para efetuar um UPSERT em vez disso, consulte [Repeatable escrever SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artigo. 

Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
