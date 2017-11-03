---
title: Mover dados de PostgreSQL utilizando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados de base de dados PostgreSQL utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 46a72a15ba35119ecb5640cb0b22cd2a0fc56a27
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Mover dados de PostgreSQL utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-onprem-postgresql-connector.md)
> * [Versão 2 - Pré-visualização](../connector-postgresql.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector PostgreSQL no V2](../connector-postgresql.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma base de dados de PostgreSQL no local. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados PostgreSQL no local para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, a fábrica de dados suporta mover dados a partir de uma base de dados PostgreSQL para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para uma base de dados PostgreSQL. 

## <a name="prerequisites"></a>Pré-requisitos

O serviço de fábrica de dados suporta a ligar a origens de PostgreSQL no local utilizando o Data Management Gateway. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway.

É necessário gateway, mesmo se uma VM do IaaS do Azure está alojada a base de dados PostgreSQL. Pode instalar o gateway na VM do IaaS do mesmo como o arquivo de dados ou numa VM diferente, desde que o gateway consiga estabelecer ligação à base de dados.

> [!NOTE]
> Consulte [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para dicas sobre/gateway de ligação de resolução de problemas relacionados com problemas.

## <a name="supported-versions-and-installation"></a>Versões suportadas e instalação
Para o Data Management Gateway para ligar à base de dados PostgreSQL, instale o [Ngpsql data provider para PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) com a versão entre 2.0.12 e 3.1.9 no mesmo sistema que o Data Management Gateway. PostgreSQL versão 7.4 e posterior é suportado.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de um arquivo de dados PostgreSQL no local utilizando ferramentas diferentes/APIs. 

- A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados. 
- Também pode utilizar as ferramentas seguintes para criar um pipeline: 
    - Portal do Azure
    - Visual Studio
    - Azure PowerShell
    - Modelo Azure Resource Manager
    - API .NET
    - API REST

     Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados PostgreSQL no local, consulte [exemplo JSON: copiar dados de PostgreSQL para Blob do Azure](#json-example-copy-data-from-postgresql-to-azure-blob) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para um arquivo de dados PostgreSQL:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço de PostgreSQL ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **OnPremisesPostgreSql** |Sim |
| servidor |Nome do servidor PostgreSQL. |Sim |
| base de dados |Nome da base de dados PostgreSQL. |Sim |
| Esquema |Nome do esquema na base de dados. O nome de esquema é maiúsculas e minúsculas. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados PostgreSQL. Os valores possíveis são: anónimo, básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados de PostgreSQL no local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A secção de typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados PostgreSQL) tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados PostgreSQL pelo serviço ligado refere-se. TableName é maiúsculas e minúsculas. |Não (se **consulta** de **RelationalSource** especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

Enquanto, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Quando a origem é do tipo **RelationalSource** (que inclui PostgreSQL), na secção typeProperties, estão disponíveis as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `"query": "select * from \"MySchema\".\"MyTable\""`. |Não (se **tableName** de **dataset** especificado) |

> [!NOTE]
> Os nomes de tabela e esquema são maiúsculas e minúsculas. Coloque-os na `""` (aspas) na consulta.  

**Exemplo:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Exemplo JSON: copiar dados de PostgreSQL para Blob do Azure
Neste exemplo fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados de base de dados PostgreSQL para armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.   

> [!IMPORTANT]
> Este exemplo fornece fragmentos JSON. Não inclui instruções passo a passo para criação da fábrica de dados. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo.

O exemplo tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados a partir de um resultado de consulta na base de dados PostgreSQL para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

Como primeiro passo, configure o data management gateway. As instruções são no [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**PostgreSQL serviço ligado:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Serviço ligado do armazenamento de Blobs do Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
    }
    }
}
```
**Conjunto de dados entrado de PostgreSQL:**

O exemplo assume que criou uma tabela "MyTable" PostgreSQL e contém uma coluna chamada "timestamp" para dados de séries de tempo.

Definição `"external": true` informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução por hora. No pipeline de definição de JSON, o **origem** tipo está definido como **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **consulta** propriedade seleciona os dados da tabela public.usstates na base de dados PostgreSQL.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Mapeamento de tipos para PostgreSQL
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo atividade de cópia executa conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de passo 2:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Ao mover dados para PostgreSQL, os seguintes mapeamentos são utilizados do tipo PostgreSQL para tipo .NET.

| Tipo de base de dados PostgreSQL | PostgresSQL aliases | Tipo de .NET framework |
| --- | --- | --- |
| abstime | |DateTime | &nbsp;
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte [], cadeia | &nbsp;
| bit variando [(n)] |varbit |Byte [], cadeia |
| Valor booleano |bool |Valor booleano |
| Caixa | |Byte [], cadeia |&nbsp;
| bytea | |Byte [], cadeia |&nbsp;
| caráter [(n)] |char [(n)] |Cadeia |
| caráter vários [(n)] |varchar [(n)] |Cadeia |
| CID | |Cadeia |&nbsp;
| CIDR | |Cadeia |&nbsp;
| Círculo | |Byte [], cadeia |&nbsp;
| Data | |DateTime |&nbsp;
| daterange | |Cadeia |&nbsp;
| precisão dupla |FLOAT8 |duplo |
| inet | |Byte [], cadeia |&nbsp;
| intarry | |Cadeia |&nbsp;
| int4range | |Cadeia |&nbsp;
| int8range | |Cadeia |&nbsp;
| número inteiro |int, int4 |Int32 |
| intervalo [campos] [(p)] | |Timespan |&nbsp;
| JSON | |Cadeia |&nbsp;
| jsonb | |Byte] |&nbsp;
| linha | |Byte [], cadeia |&nbsp;
| lseg | |Byte [], cadeia |&nbsp;
| macaddr | |Byte [], cadeia |&nbsp;
| dinheiro | |Decimal |&nbsp;
| numérica [(p, s)] |decimal [(p, s)] |Decimal |
| numrange | |Cadeia |&nbsp;
| OID | |Int32 |&nbsp;
| Caminho | |Byte [], cadeia |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| ponto de | |Byte [], cadeia |&nbsp;
| polígono | |Byte [], cadeia |&nbsp;
| real |FLOAT4 |Único |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| Série |serial4 |Int32 |
| Texto | |Cadeia |&nbsp;

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem em colunas no conjunto de dados do sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repetíveis leitura a partir de origens relacionais
Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
