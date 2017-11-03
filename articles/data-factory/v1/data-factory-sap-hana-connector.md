---
title: Mover dados de SAP HANA utilizando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados de SAP HANA utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e1ca5bd3abd66eb7a7b8e802dc193c1993a78afb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Mover dados de SAP HANA utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-sap-hana-connector.md)
> * [Versão 2 - Pré-visualização](../connector-sap-hana.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector de SAP HANA no V2](../connector-sap-business-warehouse.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de um local SAP HANA. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados SAP HANA no local para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Fábrica de dados atualmente suporta apenas mover dados a partir de um SAP HANA para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um SAP HANA.

## <a name="supported-versions-and-installation"></a>Versões suportadas e instalação
Este conector suporta qualquer versão da base de dados SAP HANA. Suporta a cópia de dados de modelos de informações de HANA (por exemplo, vistas de registos analíticos e de cálculo) e tabelas de linha/coluna através de consultas SQL.

Para ativar a conectividade com a instância de SAP HANA, instale os seguintes componentes:
- **O Data Management Gateway**: a ligar aos dados no local suporta a serviço Data Factory arquivos (incluindo SAP HANA) através de um componente chamado Data Management Gateway. Para saber mais sobre o Data Management Gateway e instruções passo a passo para configurar o gateway, consulte [do arquivo de mover dados entre dados no local para o arquivo de dados de nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo. É necessário gateway, mesmo se o SAP HANA estiver alojada numa máquina virtual IaaS do Azure (VM). Pode instalar o gateway na mesma VM como o arquivo de dados ou numa VM diferente, desde que o gateway consiga estabelecer ligação à base de dados.
- **O controlador ODBC do SAP HANA** no computador gateway. Pode transferir o controlador ODBC do SAP HANA do [Centro de transferências de Software do SAP](https://support.sap.com/swdc). Pesquisa com a palavra-chave **SAP HANA cliente para o Windows**. 

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de um arquivo de dados SAP HANA no local utilizando ferramentas diferentes/APIs. 

- A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados. 
- Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de SAP HANA no local, consulte [exemplo JSON: copiar dados de SAP HANA para Blob do Azure](#json-example-copy-data-from-sap-hana-to-azure-blob) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para um arquivo de dados SAP HANA:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço ligado do SAP HANA.

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância de SAP HANA. Se o servidor estiver a utilizar uma porta personalizada, especifique `server:port`. | Cadeia | Sim
authenticationType | Tipo de autenticação. | Cadeia. "Básico" ou "Windows" | Sim 
o nome de utilizador | Nome do utilizador que tem acesso ao servidor do SAP | Cadeia | Sim
palavra-passe | Palavra-passe para o utilizador. | Cadeia | Sim
gatewayName | Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à instância de SAP HANA no local. | Cadeia | Sim
encryptedCredential | A cadeia de credencial encriptada. | Cadeia | Não

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Não estão propriedades específicos do tipo suportadas para o conjunto de dados SAP HANA do tipo **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, tabelas de entrada e de saída, são as políticas estão disponíveis para todos os tipos de atividades.

Enquanto, propriedades disponíveis no **typeProperties** secção da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Quando a origem de atividade de cópia é do tipo **RelationalSource** (que inclui o SAP HANA), na secção typeProperties, estão disponíveis as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta SQL para ler os dados da instância de SAP HANA. | Consulta SQL. | Sim |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Exemplo JSON: copiar dados de SAP HANA para Blob do Azure
O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Este exemplo mostra como copiar dados de SAP HANA um local para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos sinks listado [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.  

> [!IMPORTANT]
> Este exemplo fornece fragmentos JSON. Não inclui instruções passo a passo para criação da fábrica de dados. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo.

O exemplo tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [SapHana](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados a partir de uma instância de SAP HANA para um blob do Azure numa base horária. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

Como primeiro passo, configure o data management gateway. As instruções são no [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.

### <a name="sap-hana-linked-service"></a>Serviço de SAP HANA ligado
Este serviço ligado liga a instância de SAP HANA à fábrica de dados. A propriedade type é definida como **SapHana**. A secção de typeProperties fornece informações de ligação para a instância de SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Este serviço ligado liga a conta do Storage do Azure à fábrica de dados. A propriedade type é definida como **AzureStorage**. A secção de typeProperties fornece informações de ligação para a conta de armazenamento do Azure.

```json
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

### <a name="sap-hana-input-dataset"></a>Conjunto de dados de entrada de SAP HANA

Este conjunto de dados define o conjunto de dados SAP HANA. Definir o tipo do conjunto de dados do Data Factory para **RelationalTable**. Atualmente, não especifique quaisquer propriedades de tipo específicas para um conjunto de dados SAP HANA. A consulta na definição da atividade de cópia Especifica quais os dados para ler da instância de SAP HANA. 

Definir a propriedade externa como true informa o serviço fábrica de dados que a tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

Propriedades de intervalo de frequência e define a agenda. Neste caso, os dados é lida a instância de SAP HANA hora a hora. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure
Este conjunto de dados define o conjunto de dados de Blobs do Azure de saída. A propriedade type é definida para AzureBlob. A secção de typeProperties fornece armazenar os dados copiados da instância de SAP HANA. Os dados são escritos para um blob de novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline com atividade de cópia

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **RelationalSource** (para a origem de SAP HANA) e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **consulta** propriedade seleciona os dados na última hora para copiar.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Mapeamento de tipos para SAP HANA
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de dois passos:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Quando mover dados de SAP HANA, os seguintes mapeamentos são utilizados de tipos de SAP HANA para tipos de .NET.

SAP HANA tipo | .NET com base em tipo
------------- | ---------------
TINYINT | Bytes
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Único
VALOR DE DUPLO | Único
DECIMAL | Decimal
VALOR BOOLEANO | Bytes
VARCHAR | Cadeia
NVARCHAR | Cadeia
CLOB | Byte]
ALPHANUM | Cadeia
BLOB | Byte]
DATA | DateTime
TEMPO | TimeSpan
TIMESTAMP | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Limitações conhecidas
Existem algumas limitações conhecidas ao copiar dados de SAP HANA:

- Cadeias NVARCHAR serão truncadas ao comprimento máximo de 4000 carateres Unicode
- SMALLDECIMAL não é suportada
- VARBINARY não é suportada
- Datas válidas são entre 1899/12/30 e 12/9999/31

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem em colunas no conjunto de dados do sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repetíveis leitura a partir de origens relacionais
Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
