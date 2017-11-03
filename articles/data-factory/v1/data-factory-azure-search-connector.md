---
title: "Enviar dados para o índice de pesquisa, utilizando o Data Factory | Microsoft Docs"
description: "Saiba mais sobre como enviar dados para o índice da Azure Search utilizando o Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2415850491018f4e27c5ec930b688026cc12b41a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Enviar dados para um índice da Azure Search utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-azure-search-connector.md)
> * [Versão 2 - Pré-visualização](../connector-azure-search.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector de pesquisa do Azure no V2](../connector-azure-search.md).

Este artigo descreve como utilizar a atividade de cópia para enviar dados de um arquivo de dados de origem suportada para o índice da Azure Search. Arquivos de dados de origem suportada estão listados na coluna de origem a [suportados origens e sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Este artigo baseia-se a [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia e combinações de arquivo de dados suportada.

## <a name="enabling-connectivity"></a>Ativar a conectividade
Para permitir que o serviço de ligar a um arquivo de dados no local do Data Factory, instalar o Data Management Gateway no seu ambiente no local. Pode instalar o gateway no mesmo computador que armazenam a origem de dados de anfitriões ou num computador separado para evitar a competir pela recursos com o arquivo de dados.

Gateway de gestão de dados liga-se origens de dados no local a serviços em nuvem de forma segura e gerida. Consulte [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter detalhes sobre o Data Management Gateway.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que envia dados de um arquivo de dados de origem para o índice da Azure Search utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para o índice da Azure Search, consulte [exemplo JSON: copiar dados do SQL Server no local para o índice da Azure Search](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir o índice da Azure Search entidades do Data Factory específicas:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

A tabela seguinte fornece descrições para os elementos JSON que são específicas para o serviço de pesquisa do Azure ligado.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade de tipo tem de ser definida: **azuresearch, uma vez**. | Sim |
| URL | URL para o serviço de pesquisa do Azure. | Sim |
| key | Chave de administrador para o serviço de pesquisa do Azure. | Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e as propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados. O **typeProperties** secção é diferente para cada tipo de conjunto de dados. Os typeProperties secção para um conjunto de dados do tipo **AzureSearchIndex** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade de tipo tem de ser definida **AzureSearchIndex**.| Sim |
| indexName | Nome do índice da Azure Search. Fábrica de dados não crie o índice. O índice tem de existir na Azure Search. | Sim |


## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções e as propriedades disponíveis para definir as atividades, consulte o [Criar pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e tabelas de saída e várias políticas estão disponíveis para todos os tipos de atividades. Enquanto, propriedades disponíveis na secção typeProperties variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Para a atividade de cópia, quando o sink não é do tipo **AzureSearchIndexSink**, na secção typeProperties, estão disponíveis as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve intercalar ou substituir quando já existe um documento no índice. Consulte o [WriteBehavior propriedade](#writebehavior-property).| Intercalar (predefinição)<br/>Carregar| Não |
| WriteBatchSize | Carrega dados para o índice da Azure Search, quando o tamanho da memória intermédia atinge writeBatchSize. Consulte o [WriteBatchSize propriedade](#writebatchsize-property) para obter mais detalhes. | 1 a 1000. Valor predefinido é 1000. | Não |

### <a name="writebehavior-property"></a>Propriedade de WriteBehavior
AzureSearchSink upserts ao escrever dados. Por outras palavras, ao escrever um documento, se a chave do documento já existe no índice da Azure Search, pesquisa do Azure atualiza o documento existente, em vez de gerar uma exceção de conflito.

O AzureSearchSink fornece seguintes comportamentos upsert dois (utilizando o SDK azuresearch, uma vez):

- **Intercalar**: combine todas as colunas no novo documento com a já existente. Para colunas com um valor nulo no documento novo, o valor no existentes é preservado.
- **Carregar**: novo documento substitui a já existente. Para colunas não especificadas no documento novo, o valor é definido como nulo se não há um valor não null no documento existente ou não.

O comportamento predefinido é **intercalar**.

### <a name="writebatchsize-property"></a>Propriedade de WriteBatchSize
Serviço de pesquisa do Azure suporta documentos de escrita como um lote. Um lote pode conter ações de 1 a 1000. Uma ação processa um documento para efetuar a operação de carregamento/intercalação.

### <a name="data-type-support"></a>Suporte de tipo de dados
A seguinte tabela especifica se um tipo de dados de pesquisa do Azure é suportado ou não.

| Tipo de dados de pesquisa do Azure | Suportado no receptor de pesquisa do Azure |
| ---------------------- | ------------------------------ |
| Cadeia | S |
| Int32 | S |
| Int64 | S |
| duplo | S |
| Valor booleano | S |
| DataTimeOffset | S |
| Matriz de cadeia | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Exemplo JSON: copiar dados do SQL Server no local para o índice da Azure Search

O exemplo seguinte mostra:

1.  Um serviço ligado do tipo [azuresearch, uma vez](#linked-service-properties).
2.  Um serviço ligado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureSearchIndex](#dataset-properties).
4.  A [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

O exemplo copia dados de séries de tempo de uma base de dados do SQL Server no local para um índice da Azure Search por hora. As propriedades JSON utilizadas neste exemplo são descritas nas secções seguintes exemplos.

Como primeiro passo, o programa de configuração para o data management gateway no seu computador local. As instruções são no [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado de pesquisa do Azure:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Serviço ligado do SQL Server**

```JSON
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

**Conjunto de dados de entrada do SQL Server**

O exemplo assume que criou uma tabela "MyTable" no SQL Server e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo. Pode consultar através de várias tabelas dentro da mesma base de dados para um único conjunto de dados, mas uma única tabela tem de ser utilizada para typeProperty tableName do conjunto de dados.

A definição "external": "true" informa serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
  "name": "SqlServerDataset",
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

**Conjunto de dados de saída de pesquisa do Azure:**

O exemplo copia dados para um índice de pesquisa do Azure com o nome **produtos**. Fábrica de dados não crie o índice. Para testar o exemplo, crie um índice com este nome. Crie o índice de pesquisa do Azure com o mesmo número de colunas, como o conjunto de dados de entrada. Novas entradas são adicionadas para o índice da Azure Search a cada hora.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**Atividade de cópia de um pipeline com a origem SQL e o sink de índice da Azure Search:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource** e **sink** tipo está definido como **AzureSearchIndexSink**. A consulta de SQL Server especificada para o **SqlReaderQuery** propriedade seleciona os dados na última hora para copiar.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

Se estiver a copiar dados de um arquivo de dados em nuvem na Azure Search, `executionLocation` propriedade é necessária. O fragmento JSON seguinte mostra a alteração necessária na atividade de cópia `typeProperties` como exemplo. Verifique [copiar dados entre os arquivos de dados de nuvem](data-factory-data-movement-activities.md#global) secção para os valores suportados e obter mais detalhes.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Copiar de uma origem de nuvem
Se estiver a copiar dados de um arquivo de dados em nuvem na Azure Search, `executionLocation` propriedade é necessária. O fragmento JSON seguinte mostra a alteração necessária na atividade de cópia `typeProperties` como exemplo. Verifique [copiar dados entre os arquivos de dados de nuvem](data-factory-data-movement-activities.md#global) secção para os valores suportados e obter mais detalhes.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente na definição da atividade de cópia. Para obter mais informações, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização  
Consulte o [guia Otimização e de desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) e de várias formas para otimizar o mesmo.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.
