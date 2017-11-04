---
title: Copiar dados da Oracle, utilizando o Data Factory | Microsoft Docs
description: "Saiba como copiar dados do Oracle base de dados que está no local utilizando o Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 09850217018321f67e2e20270aadd054258c90a2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Copiar os dados do Oracle no local utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-onprem-oracle-connector.md)
> * [Versão 2 - Pré-visualização](../connector-oracle.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector Oracle na V2](../connector-oracle.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados para/de uma base de dados de Oracle no local. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **de uma base de dados Oracle** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **para uma base de dados Oracle**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Pré-requisitos
Fábrica de dados suporta a ligar a origens de Oracle no local utilizando o Data Management Gateway. Consulte [Data Management Gateway](data-factory-data-management-gateway.md) artigo para saber mais sobre o Data Management Gateway e [mover os dados no local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como configurar o gateway de um pipeline de dados mover os dados.

É necessário gateway, mesmo que o Oracle estiver alojada numa VM do IaaS do Azure. Pode instalar o gateway na VM do IaaS do mesmo como o arquivo de dados ou numa VM diferente, desde que o gateway consiga estabelecer ligação à base de dados.

> [!NOTE]
> Consulte [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para dicas sobre/gateway de ligação de resolução de problemas relacionados com problemas.

## <a name="supported-versions-and-installation"></a>Versões suportadas e instalação
Este conector Oracle suportar duas versões de controladores:

- **O controlador Microsoft Oracle (recomendado)**: iniciados a partir da Data Management Gateway versão 2.7, um controlador Microsoft para Oracle é automaticamente instalado juntamente com o gateway, pelo que não precisa de adicionalmente lidar com o controlador para estabelecer conectividade para Oracle e também pode ocorrer um melhor desempenho de cópia utilizando este controlador. Abaixo versões do Oracle, são suportadas bases de dados:
    - R1 Oracle 12c (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!IMPORTANT]
> Atualmente o controlador Microsoft Oracle só suporta a cópia de dados da Oracle, mas não escrever Oracle. E tenha em atenção que a capacidade de ligação de teste no separador de diagnóstico do Gateway de gestão de dados não suporta este controlador. Em alternativa, pode utilizar o Assistente para copiar para validar a conectividade.
>

- **Fornecedor de dados Oracle para .NET:** também pode optar por utilizar o fornecedor de dados Oracle para copiar dados de/para Oracle. Este componente está incluído no [Oracle dados acesso componentes para o Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão adequada (32/64 bits) na máquina onde o gateway está instalado. [Fornecedor de dados Oracle .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode aceder à base de dados Oracle 10 g versão 2 ou posterior.

    Se escolher "XCopy instalação", siga os passos no readme.htm. Recomendamos que escolha o instalador com IU (não-XCopy um).

    Depois de instalar o fornecedor, **reiniciar** o serviço de anfitrião do Data Management Gateway no seu computador através de serviços miniaplicação (ou) o Gestor de configuração do Data Management Gateway.  

Se utilizar o Assistente para copiar para criar o pipeline de cópia, o tipo de controlador será determinado para automático. O controlador Microsoft será utilizado por predefinição, a menos que a sua versão do gateway é inferior ao 2.7 ou escolha Oracle como sink.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de/para uma base de dados de Oracle no local utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar um **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines. 
2. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de uma base de dados Oralce para um armazenamento de Blobs do Azure, criar dois serviços ligados para ligar a base de dados Oracle e a conta de armazenamento do Azure à fábrica de dados. As propriedades de serviço ligado que são específicas para Oracle, consulte [ligado propriedades do serviço](#linked-service-properties) secção.
3. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar a tabela na base de dados Oracle que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar o contentor de blob e a pasta que contém os dados copiados da base de dados Oracle. As propriedades do conjunto de dados que são específicas para Oracle, consulte [propriedades do dataset](#dataset-properties) secção.
4. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. No exemplo mencionado anteriormente, utilizar OracleSource como uma origem e BlobSink como um sink para a atividade de cópia. Da mesma forma, se estiver a copiar do Blob Storage do Azure para a base de dados Oracle, utilizar BlobSource e OracleSink na atividade de cópia. Para as propriedades da atividade de cópia que são específicas para a base de dados Oracle, consulte [copiar propriedades da atividade](#copy-activity-properties) secção. Para obter detalhes sobre como utilizar um arquivo de dados como uma origem ou de um receptor de mensagens em fila, clique na ligação na secção anterior para o arquivo de dados. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para/de uma base de dados de Oracle no local, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-oracle-database) secção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço ligado do Oracle.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **OnPremisesOracle** |Sim |
| driverType | Especificar o controlador para utilizar para copiar dados de/para a base de dados Oracle. Valores permitidos são **Microsoft** ou **ODP** (predefinição). Consulte [suportada a instalação e versão](#supported-versions-and-installation) secção nos detalhes do controlador. | Não |
| connectionString | Especifique as informações necessárias para estabelecer ligação à instância de base de dados Oracle para a propriedade connectionString. | Sim |
| gatewayName | Nome do gateway que é utilizada para ligar ao servidor Oracle no local |Sim |

**Exemplo: utilizar o controlador Microsoft:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Exemplo: utilizando o controlador ODP**

Consulte [este site](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) para os formatos permitidos.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Oracle, BLOBs do Azure, a tabela do Azure, etc.).

A secção de typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties do conjunto de dados do tipo OracleTable tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na base de dados Oracle que referencia o serviço ligado. |Não (se **oracleReaderQuery** de **OracleSource** especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia demora apenas uma entrada e produz saída de apenas um.

Enquanto, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

### <a name="oraclesource"></a>OracleSource
Na atividade de cópia, quando a origem é do tipo **OracleSource** as propriedades seguintes estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| oracleReaderQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable <br/><br/>Se não for especificado, a instrução de SQL que é executada: selecionar * de MyTable |Não (se **tableName** de **dataset** especificado) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: 00:30:00 (30 minutos). |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 100) |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia para executar de forma a que os dados de um setor específico é limpa. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia preencher com o identificador de setor automaticamente gerado, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Exemplos JSON para copiar dados para e da base de dados Oracle
O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados de/para uma base de dados Oracle do Blob Storage do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Exemplo: Copiar dados de Oracle para Blob do Azure

O exemplo tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) como origem e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) como sink.

O exemplo copia dados a partir de uma tabela numa base de dados Oracle no local para um blob hora a hora. Para obter mais informações sobre várias propriedades utilizadas no exemplo, consulte a documentação das secções seguintes exemplos.

**Serviço de Oracle ligada:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Serviço ligado do armazenamento de Blobs do Azure:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Conjunto de dados entrado de Oracle:**

O exemplo assume que criou uma tabela "MyTable" Oracle e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

A definição "external": "true" informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
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

**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

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

**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução por hora. No pipeline de definição de JSON, o **origem** tipo está definido como **OracleSource** e **sink** tipo está definido como **BlobSink**.  A consulta de SQL Server especificada com **oracleReaderQuery** propriedade seleciona os dados na última hora para copiar.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Exemplo: Copiar dados de Blobs do Azure para Oracle
Este exemplo mostra como copiar dados a partir de um armazenamento de Blobs do Azure para uma base de dados de Oracle no local. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens de indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.  

O exemplo tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) como origem [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) como sink.

O exemplo copia os dados de um blob para uma tabela numa base de dados Oracle no local a cada hora. Para obter mais informações sobre várias propriedades utilizadas no exemplo, consulte a documentação das secções seguintes exemplos.

**Serviço de Oracle ligada:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Serviço ligado do armazenamento de Blobs do Azure:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Conjunto de dados de entrada Blob do Azure**

Dados são captados um blob de novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome de ficheiro utiliza a parte de hora a hora de início. "external": "true" definição informa o serviço fábrica de dados que esta tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
            "frequency": "Day",
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

**Conjunto de dados de saída Oracle:**

O exemplo assume que criou uma tabela "MyTable" Oracle. Crie a tabela Oracle com o mesmo número de colunas, como espera que o ficheiro CSV de Blob que contém. Novas linhas são adicionadas à tabela de cada hora.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas
### <a name="problem-1-net-framework-data-provider"></a>Problema 1: Fornecedor de dados .NET Framework

Consulte o seguinte **mensagem de erro**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Causas possíveis:**

1. O .NET Framework Data Provider para Oracle não foi instalado.
2. O .NET Framework Data Provider para Oracle foi instalado para o .NET Framework 2.0 e não foi encontrado nas pastas do .NET Framework 4.0.

**Resolução/solução:**

1. Se ainda não instalou o fornecedor de .NET para Oracle, [instalá-lo](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e repita o cenário.
2. Se receber a mensagem de erro, mesmo após a instalação do fornecedor, efetue os seguintes passos:
   1. Abra a configuração da máquina do .NET 2.0 da pasta: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Procurar **Oracle Data Provider para .NET**, e deve ser capazes de localizar uma entrada, conforme mostrado no exemplo seguinte em **data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider para .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Copie esta entrada no ficheiro Machine. config na seguinte pasta v 4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config e, altere a versão para 4.xxx.x.x.
4. Instalar "< ODP.NET instalada caminho > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" para a cache de assemblagem global (GAC) executando `gacutil /i [provider path]`. # # sugestões de resolução de problemas

### <a name="problem-2-datetime-formatting"></a>Problema 2: formatação de datetime

Consulte o seguinte **mensagem de erro**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Resolução/solução:**

Terá de ajustar a cadeia de consulta na sua atividade de cópia com base na forma como as datas são configuradas na base de dados Oracle, conforme mostrado no exemplo seguinte (utilizando a função de to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapeamento de tipos para Oracle
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo atividade de cópia executa conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de passo 2:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Quando move os dados da Oracle, são utilizados os seguintes mapeamentos do tipo de dados Oracle para o tipo de .NET e vice-versa.

| Tipo de dados Oracle | Tipo de dados .NET framework |
| --- | --- |
| BFILE |Byte] |
| BLOB |Byte] |
| CHAR |Cadeia |
| CLOB |Cadeia |
| DATA |DateTime |
| NÚMERO DE VÍRGULA FLUTUANTE |Decimal, cadeia (se precisão > 28) |
| NÚMERO INTEIRO |Decimal, cadeia (se precisão > 28) |
| INTERVALO DE ANO A MÊS |Int32 |
| DIA DE INTERVALO PARA O SEGUNDO |TimeSpan |
| LONGA |Cadeia |
| PERÍODO DE TEMPO EM BRUTO |Byte] |
| NCHAR |Cadeia |
| NCLOB |Cadeia |
| NÚMERO |Decimal, cadeia (se precisão > 28) |
| NVARCHAR2 |Cadeia |
| NÃO PROCESSADO |Byte] |
| ROWID |Cadeia |
| TIMESTAMP |DateTime |
| TIMESTAMP COM O FUSO HORÁRIO LOCAL |DateTime |
| TIMESTAMP COM O FUSO HORÁRIO |DateTime |
| NÚMERO INTEIRO NÃO ASSINADO |Número |
| VARCHAR2 |Cadeia |
| XML |Cadeia |

> [!NOTE]
> Tipo de dados **intervalo para mês** e **intervalo dia para segunda** não são suportados quando utilizar o controlador Microsoft.

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem em colunas no conjunto de dados do sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repetíveis leitura a partir de origens relacionais
Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
