---
title: "Mover dados de tabela da Web através do Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre como mover dados de uma tabela numa página Web utilizando o Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aaf11636484fb6295c9522795b069839d2783ba9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Mover dados de uma origem de tabela Web utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-web-table-connector.md)
> * [Versão 2 - Pré-visualização](../connector-web-table.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector Web da tabela na V2](../connector-web-table.md).

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma tabela numa página Web para um arquivo de dados suportados sink. Este artigo baseia-se a [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo que apresenta uma descrição geral do movimento de dados com a atividade de cópia e a lista de arquivos de dados suportados como sinks/origens.

Fábrica de dados atualmente suporta apenas mover dados de uma tabela de Web outros arquivos de dados, mas não mover dados de outros dados armazena para um destino de tabela de Web.

> [!IMPORTANT]
> Este conector Web atualmente suporta apenas extrair conteúdo da tabela de uma página HTML. Para obter dados a partir de um ponto final de HTTP/s, utilize [conetor HTTP](data-factory-http-connector.md) em vez disso.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de um arquivo de dados no local Cassandra utilizando ferramentas diferentes/APIs. 

- A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados. 
- Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de uma tabela de web, consulte [exemplo JSON: copiar dados da tabela de Web para Blob do Azure](#json-example-copy-data-from-web-table-to-azure-blob) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para uma tabela de Web:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicas do serviço ligado Web.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **Web** |Sim |
| Url |URL para a origem da Web |Sim |
| authenticationType |Anónimo. |Sim |

### <a name="using-anonymous-authentication"></a>Autenticação anónima

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **WebTable** tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |tipo do conjunto de dados. tem de ser definido como **WebTable** |Sim |
| Caminho |Um URL relativo para o recurso que contém a tabela. |Não. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição de serviço ligado. |
| Índice |O índice da tabela no recurso. Consulte [Get índice de uma tabela numa página HTML](#get-index-of-a-table-in-an-html-page) secção para obter passos para obter o índice de uma tabela numa página HTML. |Sim |

**Exemplo:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
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

Enquanto, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Atualmente, quando a origem na atividade de cópia é do tipo **WebSource**, sem propriedades adicionais são suportadas.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Exemplo JSON: copiar dados da tabela de Web para Blob do Azure
O exemplo seguinte mostra:

1. Um serviço ligado do tipo [Web](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [WebTable](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [WebSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados a partir de uma tabela de Web para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

O exemplo seguinte mostra como copiar dados a partir de uma tabela de Web para um blob do Azure. No entanto, os dados podem ser copiados diretamente a nenhum dos sinks indicados no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo utilizando a atividade de cópia no Azure Data Factory.

**Serviço ligado do Web** este exemplo utiliza o serviço Web ligado com a autenticação anónima. Consulte [Web serviço ligado](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Serviço ligado do Armazenamento do Azure**

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

**Conjunto de dados de entrada WebTable** definição **externo** para **verdadeiro** informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

> [!NOTE]
> Consulte [Get índice de uma tabela numa página HTML](#get-index-of-a-table-in-an-html-page) secção para obter passos para obter o índice de uma tabela numa página HTML.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Conjunto de dados de saída de Blobs do Azure**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1).

```json
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



**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **WebSource** e **sink** tipo está definido como **BlobSink**.

Consulte [propriedades do tipo WebSource](#copy-activity-type-properties) para a lista de propriedades suportadas pelo WebSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
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

## <a name="get-index-of-a-table-in-an-html-page"></a>Obter o índice de uma tabela de uma página HTML
1. Iniciar **Excel 2016** e mude para o **dados** separador.  
2. Clique em **nova consulta** na barra de ferramentas, aponte para **de outras origens** e clique em **da Web**.

    ![Menu de consulta de energia](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. No **da Web** caixa de diálogo, introduza **URL** que pretende utilizar no ligado JSON do serviço (por exemplo: https://en.wikipedia.org/wiki/), juntamente com o caminho tem de especificar o conjunto de dados (por exemplo: AFI 27s_100_Years de %... 100_Movies) e clique em **OK**.

    ![Caixa de diálogo Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL utilizado neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se vir **conteúdo acesso Web** diálogo caixa, selecione o direito **URL**, **autenticação**e clique em **Connect**.

   ![Aceder à caixa de diálogo de conteúdo Web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Clique num **tabela** item na vista de árvore para ver o conteúdo da tabela e, em seguida, clique em **editar** na parte inferior.  

   ![Caixa de diálogo do navegador](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. No **Editor de consultas** janela, clique em **avançadas Editor** botão na barra de ferramentas.

    ![Botão Avançadas do Editor](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo Editor avançadas, o número junto a "Origem" é o índice.

    ![Avançadas Editor - índice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Se estiver a utilizar o Excel 2013, utilize [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obter o índice. Consulte [ligar a uma página web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artigo para obter detalhes. Os passos são semelhantes, se estiver a utilizar [Microsoft Power BI para ambiente de trabalho](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
