---
title: "Mover dados de DB2 através da utilização do Azure Data Factory | Microsoft Docs"
description: "Saiba como mover dados de uma base de dados de DB2 no local utilizando a atividade de cópia de fábrica de dados do Azure"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 727041edf457ef55a39eb91ba2369c163f5b4712
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Mover dados de DB2 utilizando a atividade de cópia de fábrica de dados do Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-onprem-db2-connector.md)
> * [Versão 2 - Pré-visualização](../connector-db2.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector DB2 no V2](../connector-db2.md).


Este artigo descreve como pode utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados de DB2 no local para um arquivo de dados. Pode copiar dados para qualquer arquivo que está listado como um sink suportado no [atividades de movimentos de dados do Data Factory](data-factory-data-movement-activities.md#supported-data-stores-and-formats) artigo. Este tópico baseia-se o artigo de fábrica de dados, que apresenta uma descrição geral do movimento de dados utilizando a atividade de cópia e lista as combinações de arquivo de dados suportada. 

Fábrica de dados atualmente suporta apenas dados mover de uma base de dados DB2 para um [arquivo de dados suportados sink](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Mover dados de outros dados armazena para um DB2 base de dados não é suportada.

## <a name="prerequisites"></a>Pré-requisitos
Fábrica de dados suporta a ligação a uma base de dados de DB2 no local utilizando o [o data management gateway](data-factory-data-management-gateway.md). Para obter instruções passo a passo configurar o pipeline de dados do gateway para mover os dados, consulte o [mover os dados no local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.

É necessário um gateway, mesmo que o DB2 está alojado numa VM do IaaS do Azure. Pode instalar o gateway na VM do IaaS do mesmo como o arquivo de dados. Se o gateway se pode ligar à base de dados, pode instalar o gateway numa VM diferente.

O data management gateway fornece um controlador de DB2 incorporado, pelo que não precisa de instalar manualmente um controlador para copiar dados de DB2.

> [!NOTE]
> Para sugestões de resolução de problemas de gateway e de ligação, consulte o [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) artigo.


## <a name="supported-versions"></a>Versões suportadas
O conector DB2 da fábrica de dados suporta as seguintes plataformas de IBM DB2 e versões com versões do Gestor de acesso do SQL Server distribuídas relacional da base de dados arquitetura (DRDA) 9, 10 e 11:

* IBM DB2 para z/SO 11.1
* IBM DB2 para z/SO 10.1
* IBM DB2 versão i (AS400) 7.2
* IBM DB2 versão i (AS400) 7.1
* IBM DB2 para Linux, UNIX e o Windows (LUW) versão 11
* IBM DB2 para LUW versão 10.5
* IBM DB2 para LUW versão 10.1

> [!TIP]
> Se receber a mensagem de erro "não foi encontrado o pacote correspondente a um pedido de execução da instrução de SQL. SQLSTATE = 51002 SQLCODE =-805, "o motivo é um pacote necessário não foi criado para o utilizador normal no sistema operativo. Para resolver este problema, siga estas instruções para o tipo de servidor DB2:
> - DB2 para i (AS400): permitir que um utilizador avançado criar a coleção para o utilizador normal antes de executar a atividade de cópia. Para criar a coleção, utilize o comando:`create collection <username>`
> - DB2 para z/OS ou LUW: utilizar uma conta de privilégio elevado – um utilizador avançado ou administrador que tenha autoridades de pacote e ENLACE, BINDADD, conceder a executar para permissões públicas – para executar a cópia de uma vez. O pacote necessário é criado automaticamente durante a cópia. Posteriormente, pode voltar a mudar para o utilizador normal para as execuções subsequentes cópia.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia para mover dados de um arquivo de dados DB2 no local através de APIs e ferramentas diferentes: 

- A forma mais fácil de criar um pipeline consiste em utilizar o Assistente de cópia do Azure Data Factory. Para instruções rápidas sobre como criar um pipeline, utilizando o Assistente de cópia, consulte o [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md). 
- Também pode utilizar ferramentas para criar um pipeline, incluindo o portal do Azure, Visual Studio, Azure PowerShell, um modelo Azure Resource Manager, a API .NET e a API REST. Para obter instruções passo a passo Criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Crie serviços ligados para ligar a entrada e saída de arquivos de dados para a fábrica de dados.
2. Crie conjuntos de dados para representar os dados de entrada e saídos da operação de cópia. 
3. Crie um pipeline com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utiliza o Assistente para copiar, definições de JSON para a fábrica de dados ligada serviços, conjuntos de dados e pipeline entidades são criadas automaticamente para si. Quando utilizar APIs (exceto a API .NET) ou ferramentas, é possível definir as entidades da fábrica de dados utilizando o formato JSON. O [exemplo JSON: copiar dados de DB2 para armazenamento de Blobs do Azure](#json-example-copy-data-from-db2-to-azure-blob) mostra as definições de JSON para entidades da fábrica de dados que são utilizadas para copiar dados de um arquivo de dados DB2 no local.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir as entidades da fábrica de dados que são específicas para um arquivo de dados DB2.

## <a name="db2-linked-service-properties"></a>Propriedades do serviço ligado de DB2
A tabela seguinte lista as propriedades JSON que são específicas para um serviço DB2 ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |Esta propriedade tem de ser definida **OnPremisesDb2**. |Sim |
| **servidor** |O nome do servidor DB2. |Sim |
| **base de dados** |O nome da base de dados DB2. |Sim |
| **esquema** |O nome do esquema na base de dados DB2. Esta propriedade é maiúsculas e minúsculas. |Não |
| **authenticationType** |O tipo de autenticação que é utilizada para ligar à base de dados DB2. Os valores possíveis são: anónimo, básico e Windows. |Sim |
| **nome de utilizador** |O nome da conta de utilizador, se utilizar a autenticação básica ou do Windows. |Não |
| **palavra-passe** |A palavra-passe da conta de utilizador. |Não |
| **gatewayName** |O nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados de DB2 no local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista de secções e as propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Secções, tais como **estrutura**, **disponibilidade**e o **política** para um conjunto de dados JSON, são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Blob storage do Azure, Table storage do Azure e assim sucessivamente).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para um conjunto de dados do tipo **RelationalTable**, que inclui o conjunto de dados DB2, tem a seguinte propriedade:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tableName** |O nome da tabela na instância de base de dados DB2 que referencia o serviço ligado. Esta propriedade é maiúsculas e minúsculas. |Não (se o **consulta** propriedade de uma atividade de cópia do tipo **RelationalSource** especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista de secções e as propriedades disponíveis para definir as atividades de cópia, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Copiar as propriedades da atividade, tal como **nome**, **Descrição**, **entradas** tabela, **produz** tabela, e **política**, estão disponíveis para todos os tipos de atividades. As propriedades que estão disponíveis no **typeProperties** secção da atividade variar para cada tipo de atividade. Para a atividade de cópia, as propriedades variam consoante os tipos de origens de dados e sinks.

Para a atividade de cópia, quando a origem é do tipo **RelationalSource** (que inclui DB2), as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **consulta** |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""` |Não (se o **tableName** é especificada a propriedade de um conjunto de dados) |

> [!NOTE]
> Os nomes de tabela e esquema são maiúsculas e minúsculas. A instrução de consulta, coloque os nomes de propriedade utilizando "" (aspas).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Exemplo JSON: copiar dados de DB2 para armazenamento de Blobs do Azure
Neste exemplo fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). O exemplo mostra como copiar dados de uma base de dados DB2 para armazenamento de Blobs. No entanto, os dados podem ser copiados para [todos os dados suportados armazenam o tipo de sink](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia de fábrica de dados do Azure.

O exemplo tem as seguintes entidades do Data Factory:

- Serviço do tipo de ligado um DB2 [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Serviço do tipo de ligado um armazenamento de Blobs do Azure [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- A [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza o [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) propriedades

O exemplo copia dados de um resultado de consulta na base de dados DB2 para um blob do Azure numa base horária. As propriedades JSON utilizadas no exemplo são descritas nas secções que se seguem as definições de entidade.

Como primeiro passo, instalar e configurar um gateway de dados. Instruções são no [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado de DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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

**Serviço de ligado do armazenamento de Blobs do Azure**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Conjunto de dados de entrada DB2**

O exemplo assume que criou uma tabela em DB2 com o nome "MyTable" que tem uma coluna intitulada "timestamp" para os dados de séries de tempo.

O **externo** propriedade está definida como "true". Esta definição informa o serviço fábrica de dados que este conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados. Tenha em atenção que o **tipo** propriedade está definida como **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

**Conjunto de dados de saída de Blobs do Azure**

Dados são escritos para um blob de novo a cada hora, definindo o **frequência** propriedade como "Hora" e o **intervalo** propriedade para 1. O **folderPath** propriedade para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e partes de hora a hora de início.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline para a atividade de cópia**

O pipeline contém uma atividade de cópia que esteja configurado para utilizar a entrada especificada e conjuntos de dados de saída e que esteja agendada para execução a cada hora. Na definição de JSON para o pipeline, a **origem** tipo está definido como **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **consulta** propriedade seleciona os dados da tabela "Ordens".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Mapeamento de tipos para DB2
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipo automática do tipo de origem para sink do tipo utilizando a seguinte abordagem de dois passos:

1. Converter de um tipo de origem nativo para um tipo .NET
2. Converter de um tipo .NET para o tipo de sink nativo

Os seguintes mapeamentos são utilizados quando a atividade de cópia converte os dados de um tipo de DB2 para um tipo .NET:

| Tipo de base de dados DB2 | Tipo de .NET framework |
| --- | --- |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Único |
| duplo |duplo |
| Número de vírgula flutuante |duplo |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| um valor numérico |Decimal |
| Data |DateTime |
| Hora |TimeSpan |
| Timestamp |DateTime |
| XML |Byte] |
| char |Cadeia |
| VarChar |Cadeia |
| LongVarChar |Cadeia |
| DB2DynArray |Cadeia |
| Binário |Byte] |
| VarBinary |Byte] |
| LongVarBinary |Byte] |
| Gráfico |Cadeia |
| VarGraphic |Cadeia |
| LongVarGraphic |Cadeia |
| CLOB |Cadeia |
| Blobs |Byte] |
| DbClob |Cadeia |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Único |
| duplo |duplo |
| Número de vírgula flutuante |duplo |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| um valor numérico |Decimal |
| Data |DateTime |
| Hora |TimeSpan |
| Timestamp |DateTime |
| XML |Byte] |
| char |Cadeia |

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber como mapear colunas no conjunto de dados de origem para colunas no conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetíveis origens relacionais
Quando copiar dados de um arquivo de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a repetição **política** propriedade para um conjunto de dados voltar a executar um setor quando ocorre uma falha. Certifique-se de que os mesmos dados é de leitura, independentemente de quantas vezes o setor será novamente executado e independentemente da forma como voltar a executar o setor. Para obter mais informações, consulte [Repeatable lê a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Saiba mais sobre os principais fatores que afetam o desempenho de atividade de cópia e formas para otimizar o desempenho no [guia Otimização e de desempenho de atividade de cópia](data-factory-copy-activity-performance.md).
