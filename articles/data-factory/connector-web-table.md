---
title: "Copiar dados da tabela da Web através do Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre Web tabela conector do Azure Data Factory que lhe permite copiar dados de uma tabela de web para os arquivos de dados suportados pela fábrica de dados como sinks."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: fba3de916fc3fb0b83b300cc2cf78ef556b35556
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Copiar os dados da tabela da Web através do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-web-table-connector.md)
> * [Versão 2 - Pré-visualização](connector-web-table.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados de tabela de Web. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.


> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector Web tabela no V1](v1/data-factory-web-table-connector.md).

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados da base de dados de tabela de Web para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de tabela Web suporta **extrair conteúdo de tabela a partir de uma página HTML**. Para obter dados a partir de um ponto final de HTTP/s, utilize [conetor HTTP](connector-http.md) em vez disso.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](create-self-hosted-integration-runtime.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector de tabela de Web.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado de tabela Web:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Web** |Sim |
| URL | URL para a origem da Web |Sim |
| authenticationType | Permitido é de valor: **anónimo**. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados de tabela de Web.

Para copiar dados da tabela da Web, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **WebTable** | Sim |
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
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem de tabela de Web.

### <a name="web-table-as-source"></a>Tabela de Web como origem

Para copiar dados da tabela da Web, defina o tipo de origem na atividade de cópia para **WebSource**, sem propriedades adicionais são suportadas.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Obter o índice de uma tabela de uma página HTML

1. Iniciar **Excel 2016** e mude para o **dados** separador.
2. Clique em **nova consulta** na barra de ferramentas, aponte para **de outras origens** e clique em **da Web**.

    ![Menu de consulta de energia](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. No **da Web** caixa de diálogo, introduza **URL** que pretende utilizar no ligado JSON do serviço (por exemplo: https://en.wikipedia.org/wiki/), juntamente com o caminho tem de especificar o conjunto de dados (por exemplo: AFI 27s_100_Years de %... 100_Movies) e clique em **OK**.

    ![Caixa de diálogo Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL utilizado neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se vir **conteúdo acesso Web** diálogo caixa, selecione o direito **URL**, **autenticação**e clique em **Connect**.

   ![Aceder à caixa de diálogo de conteúdo Web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Clique num **tabela** item na vista de árvore para ver o conteúdo da tabela e, em seguida, clique em **editar** na parte inferior.  

   ![Caixa de diálogo do navegador](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. No **Editor de consultas** janela, clique em **avançadas Editor** botão na barra de ferramentas.

    ![Botão Avançadas do Editor](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo Editor avançadas, o número junto a "Origem" é o índice.

    ![Avançadas Editor - índice](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Se estiver a utilizar o Excel 2013, utilize [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obter o índice. Consulte [ligar a uma página web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artigo para obter detalhes. Os passos são semelhantes, se estiver a utilizar [Microsoft Power BI para ambiente de trabalho](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).