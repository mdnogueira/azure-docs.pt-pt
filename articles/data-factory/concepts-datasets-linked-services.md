---
title: "Conjuntos de dados e serviços no Azure Data Factory ligados | Microsoft Docs"
description: "Saiba mais sobre os conjuntos de dados e serviços ligados na fábrica de dados. Serviços ligados ligam os arquivos de computação/dados ao data factory. Conjuntos de dados representam dados de entrada/saída."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 
ms.date: 09/05/2017
ms.author: shlo
ms.openlocfilehash: a13e19c7e1a22581b14d1a96e20b8a649c303fc3
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Conjuntos de dados e serviços ligados no Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-create-datasets.md)
> * [Versão 2 - Pré-visualização](concepts-datasets-linked-services.md)

Este artigo descreve os conjuntos de dados são, como definidos no formato JSON, e como são utilizados no Azure Data Factory V2 pipelines. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conjuntos de dados no Data Factory V1](v1/data-factory-create-datasets.md).

Se estiver familiarizado com o Data Factory, consulte o artigo [introdução ao Azure Data Factory](introduction.md) para uma descrição geral. 

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. A **pipeline** é um agrupamento lógico de **atividades** que em conjunto, efetuar uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, poderá utilizar uma atividade de cópia para copiar dados de um servidor de SQL no local para o armazenamento de Blobs do Azure. Em seguida, poderá utilizar uma atividade do ramo de registo que executa um script de ramo de registo num cluster do Azure HDInsight para processar dados de armazenamento de BLOBs para produzir os dados de saída. Por fim, poderá utilizar uma segunda atividade de cópia para copiar os dados de saída para o Azure SQL Data Warehouse, por cima que negócio relatório do intelligence (BI) soluções são criadas. Para obter mais informações sobre pipelines e atividades, consulte [Pipelines e atividades](concepts-pipelines-activities.md) no Azure Data Factory.

Agora, um **dataset** uma vista com o nome dos dados que simplesmente pontos ou referenciar os dados que pretende utilizar na sua **atividades** como entradas e saídas. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de Blobs do Azure Especifica a pasta e o contentor do blob no armazenamento de BLOBs a partir da qual a atividade deve ler os dados.

Antes de criar um conjunto de dados, tem de criar um **serviço ligado** para ligar o seu arquivo de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Considerá-lo desta forma; o conjunto de dados representa a estrutura dos dados nos arquivos de dados ligado e o serviço ligado define a ligação à origem de dados. Por exemplo, um Storage do Azure serviço ligado liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados de Blobs do Azure representa o contentor de blob e a pasta dentro dessa conta de armazenamento do Azure que contém os blobs de entrada a processar.

Eis um cenário de exemplo. Para copiar dados de armazenamento de BLOBs para base de dados SQL, criar dois serviços ligados: armazenamento do Azure e SQL Database do Azure. Em seguida, crie dois conjuntos de dados: conjunto de dados de Blobs do Azure (o que se refere-se ao serviço ligado do Storage do Azure) e o conjunto de dados da tabela de SQL do Azure (que se refere ao serviço ligado de SQL Database do Azure). Os serviços de base de dados do SQL do Azure ligado e o armazenamento do Azure contêm cadeias de ligação que utiliza o Data Factory no tempo de execução para estabelecer ligação com o Storage do Azure e a SQL Database do Azure, respetivamente. O conjunto de dados de Blobs do Azure Especifica a pasta de blob que contém os blobs de entrada no seu armazenamento de BLOBs e contentor de blob. O conjunto de dados de tabela de SQL do Azure Especifica a tabela SQL na base de dados SQL à qual os dados estão a ser copiado.

O diagrama seguinte mostra as relações entre pipeline, atividade, conjunto de dados e o serviço ligado no Factory de dados:

![Relação entre o pipeline, atividade, conjunto de dados, serviços ligados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON do conjunto de dados
Um conjunto de dados no Data Factory está definido no formato JSON da seguinte forma:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                 "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}

```
A tabela seguinte descreve as propriedades no JSON acima:

Propriedade | Descrição | Necessário | Predefinição
-------- | ----------- | -------- | -------
nome | Nome do conjunto de dados. | Consulte [do Azure Data Factory - as regras de nomenclatura](naming-rules.md). | Sim | ND
tipo | tipo do conjunto de dados. | Especifique um dos tipos suportados pela fábrica de dados (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter mais informações, consulte [tipos de conjunto de dados](#dataset-types). | Sim | ND
estrutura | Esquema do conjunto de dados. | Para obter mais informações, consulte [estrutura do conjunto de dados](#dataset-structure). | Não | ND
typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: Azure Blob, tabela SQL do Azure). Para obter detalhes sobre os tipos suportados e as respetivas propriedades, consulte [tipo do conjunto de dados](#dataset-type). | Sim | ND

## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo seguinte, o conjunto de dados representa uma tabela com o nome MyTable numa base de dados do SQL Server.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                 "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Tenha em atenção os seguintes pontos:

- tipo está definido como AzureSqlTable.
- propriedade de tipo tableName (específica para o tipo de AzureSqlTable) está definida como MyTable.
- linkedServiceName refere-se a um serviço ligado do tipo AzureSqlDatabase, que é definido no fragmento JSON seguinte.

## <a name="linked-service-example"></a>Exemplo de serviço ligado
AzureSqlLinkedService é definido do seguinte modo:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```
No fragmento JSON anterior:

- **tipo** está definido como AzureSqlDatabase.
- **connectionString** propriedade do tipo Especifica as informações para ligar a uma base de dados do SQL Server.

Como pode ver, o serviço ligado define como ligar a uma base de dados do SQL Server. Define o conjunto de dados que a tabela é utilizada como uma entrada e saída da atividade num pipeline.

## <a name="dataset-type"></a>Tipo de conjunto de dados
Existem vários tipos de conjuntos de dados, consoante o arquivo de dados que utiliza. Consulte a tabela seguinte para obter uma lista dos arquivos de dados suportada pela fábrica de dados. Clique num arquivo de dados para saber como criar um serviço ligado e um conjunto de dados para este arquivo de dados.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

O exemplo na secção anterior, o tipo do conjunto de dados está definido como **AzureSqlTable**. Da mesma forma, para um conjunto de dados de Blobs do Azure, o tipo do conjunto de dados está definido como **AzureBlob**, conforme mostrado no seguinte JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                 "type": "LinkedServiceReference",
        }, 
 
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```
## <a name="dataset-structure"></a>Estrutura do conjunto de dados
O **estrutura** secção é opcional. Define o esquema do conjunto de dados, que contém uma coleção de nomes e tipos de dados das colunas. Utilize a secção de estrutura para fornecer informações de tipo que são utilizadas para converter os tipos de modelo e mapeie as colunas de origem para o destino. No exemplo seguinte, o conjunto de dados tem três colunas: timestamp, projectname e pageviews. São do tipo String, String e Decimal, respetivamente.

```json
[
    { "name": "timestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Cada coluna na estrutura de contém as seguintes propriedades:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da coluna. | Sim
tipo | Tipo de dados da coluna. | Não
Cultura | . Idioma baseado em NET a ser utilizado quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. | Não
formato | Formato de cadeia a ser utilizado quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. | Não

As seguintes diretrizes ajudam a determinar quando deve incluir informações de estrutura e o que incluir no **estrutura** secção.

- **Para origens de dados estruturados**, especificar a secção de estrutura apenas se pretender mapear colunas de origem para sink colunas e os respetivos nomes não são iguais. Este tipo de origem de dados estruturados armazena informações de esquema e o tipo de dados, juntamente com dados propriamente ditos. Exemplos de origens de dados estruturados incluem o SQL Server, Oracle e SQL Database do Azure.<br/><br/>Como informações sobre o tipo já se encontra disponível para origens de dados estruturados, não deve incluir informações sobre o tipo ao incluir a secção de estrutura.
- **Para o esquema em origens de dados de leitura (especificamente o armazenamento de BLOBs)**, pode optar por armazenar os dados sem armazenar quaisquer informações de esquema ou tipo com os dados. Para estes tipos de origens de dados, inclua estrutura quando pretende mapear colunas de origem para sink colunas. Também inclua estrutura quando o conjunto de dados for uma entrada para uma atividade de cópia e tipos de dados do conjunto de dados de origem devem ser convertidos para tipos de nativos para o sink.<br/><br/> Fábrica de dados suporta os seguintes valores para fornecer informações de tipo na estrutura: `Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset, and Timespan`. 

Saiba mais sobre como o factory de dados mapeia os dados de origem para sink do [esquema e o mapeamento do tipo]( copy-activity-schema-and-type-mapping.md) e especificar as informações de estrutura.

## <a name="create-datasets"></a>Criar conjuntos de dados
Pode criar conjuntos de dados utilizando um destas ferramentas ou SDKs: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), modelo do Azure Resource Manager e o portal do Azure

## <a name="v1-vs-v2-datasets"></a>V1 vs. Conjuntos de dados v2

Seguem-se algumas diferenças entre conjuntos de dados de Data Factory, v1 e v2: 

- A propriedade externa não é suportada na v2. Está a ser substituído por um [acionador](concepts-pipeline-execution-triggers.md).
- As propriedades de política e a disponibilidade não são suportadas na V2. A hora de início de um pipeline depende [acionadores](concepts-pipeline-execution-triggers.md).
- Âmbito conjuntos de dados (conjuntos de dados definidos num pipeline) não são suportados na V2. 

## <a name="next-steps"></a>Passos seguintes
Consulte o tutorial seguinte para obter instruções passo a passo para criar pipelines e conjuntos de dados utilizando um destas ferramentas ou SDKs. 

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Início rápido: criar uma fábrica de dados com o PowerShell](quickstart-create-data-factory-powershell.md)
- [Início rápido: criar uma fábrica de dados utilizando a REST API](quickstart-create-data-factory-rest-api.md)
- Início rápido: criar uma fábrica de dados através do portal do Azure
