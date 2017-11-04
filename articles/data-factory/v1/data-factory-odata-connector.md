---
title: Mover dados do OData origens | Microsoft Docs
description: Saiba mais sobre como mover dados a partir de origens de OData utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a94b02ad2296ba1be6a4194dc49c76bc7332e08
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Mover dados de origem do OData utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-odata-connector.md)
> * [Versão 2 - Pré-visualização](../connector-odata.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector de OData no V2](../connector-odata.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados a partir de uma origem de OData. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados de uma origem de OData para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Fábrica de dados atualmente suporta apenas mover dados a partir de uma origem de OData para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para uma origem de OData. 

## <a name="supported-versions-and-authentication-types"></a>Versões suportadas e tipos de autenticação
Este conector de OData suporta a versão de OData 3.0 e 4.0 e pode copiar dados a partir de ambos os cloud OData e origens de OData no local. Para esta última opção, tem de instalar o Data Management Gateway. Consulte [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter detalhes sobre o Data Management Gateway.

Abaixo autenticação são suportados tipos:

* Para aceder ao **nuvem** feed de OData, pode utilizar anónimo, básico (nome de utilizador e palavra-passe), ou do Azure Active Directory baseado em autenticação OAuth.
* Para aceder ao **no local** feed de OData, pode utilizar anónimo, básico (nome de utilizador e palavra-passe), ou a autenticação do Windows.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move dados a partir de uma origem de OData utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados a partir de uma origem de OData, consulte [exemplo JSON: copiar dados de origem de OData para Blob do Azure](#json-example-copy-data-from-odata-source-to-azure-blob) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas à origem de OData:

## <a name="linked-service-properties"></a>Propriedades do serviço ligadas
A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço OData ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **OData** |Sim |
| URL |URL do serviço OData. |Sim |
| authenticationType |Tipo de autenticação utilizado para ligar à origem de OData. <br/><br/> Para a nuvem OData, os valores possíveis são anónimo, básico e OAuth (tenha em atenção o suporte do Azure Data Factory atualmente, apenas do Azure Active Directory com base em OAuth). <br/><br/> De OData no local, os valores possíveis são anónimo, básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica. |Sim (apenas se estiver a utilizar autenticação básica) |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Sim (apenas se estiver a utilizar autenticação básica) |
| authorizedCredential |Se estiver a utilizar o OAuth, clique em **autorizar** botão no Assistente de cópia do Data Factory ou Editor e introduza as suas credenciais, o valor desta propriedade será gerado automaticamente. |Sim (apenas se estiver a utilizar autenticação OAuth) |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar ao serviço de OData no local. Especifique apenas se estiver a copiar dados a partir da origem de OData no local. |Não |

### <a name="using-basic-authentication"></a>Utilizar a autenticação básica
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Autenticação anónima
```json
{
    "name": "ODataLinkedService",
        "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Através da autenticação do Windows aceder à origem de OData no local
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Utilizando a autenticação do OAuth de nuvem ao aceder à origem de OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **ODataResource** (que inclui o conjunto de dados do OData) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Caminho |Caminho para o recurso de OData |Não |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis na secção typeProperties da atividade por outro lado variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Quando a origem é do tipo **RelationalSource** (que inclui OData) na secção typeProperties, estão disponíveis as seguintes propriedades:

| Propriedade | Descrição | Exemplo | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |"? $select = o nome, descrição & $top = 5" |Não |

## <a name="type-mapping-for-odata"></a>Mapeamento de tipos de OData
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de dois passos.

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Ao mover dados do OData, os seguintes mapeamentos são utilizados de tipos de OData para tipo .NET.

| Tipo de dados de OData | Tipo .NET |
| --- | --- |
| Edm.Binary |Byte] |
| Edm.Boolean |bool |
| Edm.Byte |Byte] |
| Edm.DateTime |DateTime |
| Edm.Decimal |Decimal |
| Edm.Double |duplo |
| Edm.Single |Único |
| Edm.Guid |GUID |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |Cadeia |
| Edm.Time |TimeSpan |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> Por exemplo, tipos de dados complexos de OData objeto não são suportadas.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Exemplo JSON: copiar dados de origem de OData para Blob do Azure
Neste exemplo fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados a partir de uma origem de OData para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory. O exemplo tem as seguintes entidades do Data Factory:

1. Um serviço ligado do tipo [OData](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [ODataResource](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados da consulta em relação a uma origem de OData para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado de OData:** este exemplo utiliza a autenticação anónima. Consulte [serviço ligado de OData](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```json
{
    "name": "ODataLinkedService",
        "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
            }
        }
}
```

**Serviço ligado do Storage do Azure:**

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

**Conjunto de dados entrado de OData:**

A definição "external": "true" informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
                "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3                
        }
    }
}
```

Especificar **caminho** no conjunto de dados, a definição é opcional.

**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Atividade de cópia num pipeline com a origem de OData e o sink de Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **consulta** propriedade seleciona os dados (mais recentes) mais recentes da origem de OData.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

Especificar **consulta** no pipeline de definição é opcional. O **URL** que utiliza o serviço Data Factory para obter dados é: URL especificado no serviço ligado (obrigatório) + caminho especificado no conjunto de dados (opcional) + consulta no pipeline (opcional).


### <a name="type-mapping-for-odata"></a>Mapeamento de tipos de OData
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de passo 2:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Quando armazena dados movimento dos dados de OData, tipos de dados de OData estão mapeados para os tipos de .NET.

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem em colunas no conjunto de dados do sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repetíveis leitura a partir de origens relacionais
Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
