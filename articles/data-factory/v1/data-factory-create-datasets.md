---
title: Criar conjuntos de dados no Azure Data Factory | Microsoft Docs
description: Saiba como criar conjuntos de dados no Azure Data Factory, com exemplos que utilizam propriedades, tais como o desvio e anchorDateTime.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3e4b73f432f2695fa8b66b4d2bca23d32bfa9f3a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados de Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-create-datasets.md)
> * [Versão 2 - Pré-visualização](../concepts-datasets-linked-services.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conjuntos de dados na V2](../concepts-datasets-linked-services.md).

Este artigo descreve os conjuntos de dados são, como definidos no formato JSON, e como são utilizados no Azure Data Factory pipelines. Fornece detalhes sobre cada secção (por exemplo, estrutura, disponibilidade e política) na definição de JSON do conjunto de dados. O artigo também fornece exemplos para utilizar o **desvio**, **anchorDateTime**, e **estilo** propriedades numa definição de JSON do conjunto de dados.

> [!NOTE]
> Se estiver familiarizado com o Data Factory, consulte o artigo [introdução ao Azure Data Factory](data-factory-introduction.md) para uma descrição geral. Se não tiver experiência prática com criar fábricas de dados, pode obter uma melhor compreensão ao ler o [tutorial de transformação de dados](data-factory-build-your-first-pipeline.md) e [tutorial de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. A **pipeline** é um agrupamento lógico de **atividades** que em conjunto, efetuar uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, poderá utilizar uma atividade de cópia para copiar dados de um servidor de SQL no local para o armazenamento de Blobs do Azure. Em seguida, poderá utilizar uma atividade do ramo de registo que executa um script de ramo de registo num cluster do Azure HDInsight para processar dados de armazenamento de BLOBs para produzir os dados de saída. Por fim, poderá utilizar uma segunda atividade de cópia para copiar os dados de saída para o Azure SQL Data Warehouse, por cima que negócio relatório do intelligence (BI) soluções são criadas. Para obter mais informações sobre pipelines e atividades, consulte [Pipelines e atividades no Azure Data Factory](data-factory-create-pipelines.md).

Uma atividade pode demorar entrada zero ou mais **conjuntos de dados**e produzir uma ou mais conjuntos de dados de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline, e um conjunto de dados de saída representa a saída da atividade. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de Blobs do Azure Especifica a pasta e o contentor do blob no armazenamento de BLOBs a partir do qual o pipeline deve ler os dados. 

Antes de criar um conjunto de dados, criar um **serviço ligado** para ligar o seu arquivo de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Conjuntos de dados identificam os dados dentro de arquivos de dados ligadas, como tabelas de SQL Server, ficheiros, pastas e documentos. Por exemplo, um Storage do Azure serviço ligado liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados de Blobs do Azure representa o contentor de blob e a pasta que contém os blobs de entrada a processar. 

Eis um cenário de exemplo. Para copiar dados de armazenamento de BLOBs para base de dados SQL, criar dois serviços ligados: armazenamento do Azure e SQL Database do Azure. Em seguida, crie dois conjuntos de dados: conjunto de dados de Blobs do Azure (o que se refere-se ao serviço ligado do Storage do Azure) e o conjunto de dados da tabela de SQL do Azure (que se refere ao serviço ligado de SQL Database do Azure). Os serviços de base de dados do SQL do Azure ligado e o armazenamento do Azure contêm cadeias de ligação que utiliza o Data Factory no tempo de execução para estabelecer ligação com o Storage do Azure e a SQL Database do Azure, respetivamente. O conjunto de dados de Blobs do Azure Especifica a pasta de blob que contém os blobs de entrada no seu armazenamento de BLOBs e contentor de blob. O conjunto de dados de tabela de SQL do Azure Especifica a tabela SQL na base de dados SQL à qual os dados estão a ser copiado.

O diagrama seguinte mostra as relações entre pipeline, atividade, conjunto de dados e o serviço ligado no Factory de dados: 

![Relação entre o pipeline, atividade, conjunto de dados, serviços ligados](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON do conjunto de dados
Um conjunto de dados no Data Factory está definido no formato JSON da seguinte forma:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

A tabela seguinte descreve as propriedades no JSON acima:   

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| nome |Nome do conjunto de dados. Consulte [do Azure Data Factory - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura. |Sim |ND |
| tipo |tipo do conjunto de dados. Especifique um dos tipos suportados pela fábrica de dados (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter mais informações, consulte [tipo do conjunto de dados](#Type). |Sim |ND |
| estrutura |Esquema do conjunto de dados.<br/><br/>Para obter mais informações, consulte [estrutura do conjunto de dados](#Structure). |Não |ND |
| typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: Azure Blob, tabela SQL do Azure). Para obter detalhes sobre os tipos suportados e as respetivas propriedades, consulte [tipo do conjunto de dados](#Type). |Sim |ND |
| externo | Sinalizador booleano para especificar se um conjunto de dados explicitamente é produzido por um pipeline de fábrica de dados ou não. Se o conjunto de dados de entrada para uma atividade não é produzido pelo pipeline atual, defina este sinalizador como verdadeiro. Defina este sinalizador como true para o conjunto de dados de entrada da primeira atividade no pipeline.  |Não |FALSO |
| disponibilidade | Define a janela de processamento (por exemplo, hora ou diária) ou o modelo slicing para o conjunto de dados de produção. Cada unidade de dados consumidos e produzidos por uma execução de atividade é chamada um setor de dados. Se a disponibilidade de um conjunto de dados de saída está definida como diariamente (frequência - dia, o intervalo de-1), um setor é produzido diariamente. <br/><br/>Para obter mais informações, consulte [disponibilidade do conjunto de dados](#Availability). <br/><br/>Para obter detalhes sobre o conjunto de dados repartir modelo, consulte o [agendamento e execução](data-factory-scheduling-and-execution.md) artigo. |Sim |ND |
| política |Define os critérios ou a condição que os setores de conjunto de dados tem de cumprir. <br/><br/>Para obter mais informações, consulte o [política de conjunto de dados](#Policy) secção. |Não |ND |

## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo seguinte, o conjunto de dados representa uma tabela com o nome **MyTable** numa base de dados do SQL Server.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Tenha em atenção os seguintes pontos:

* **tipo** está definido como AzureSqlTable.
* **tableName** propriedade de tipo (específico para o tipo de AzureSqlTable) está definida como MyTable.
* **linkedServiceName** refere-se a um serviço ligado do tipo AzureSqlDatabase, que é definido no fragmento JSON seguinte. 
* **frequência de disponibilidade** está definido como dia, e **intervalo** está definida como 1. Isto significa que o setor de conjunto de dados é produzido diariamente.  

**AzureSqlLinkedService** é definidos do seguinte modo:

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

* **tipo** está definido como AzureSqlDatabase.
* **connectionString** propriedade do tipo Especifica as informações para ligar a uma base de dados do SQL Server.  

Como pode ver, o serviço ligado define como ligar a uma base de dados do SQL Server. Define o conjunto de dados que a tabela é utilizada como uma entrada e saída da atividade num pipeline.   

> [!IMPORTANT]
> A menos que um conjunto de dados está a ser produzido pelo pipeline, deverá ser marcado como **externo**. Esta definição aplica-se, geralmente, entradas da primeira atividade num pipeline.   


## <a name="Type"></a>Tipo de conjunto de dados
O tipo do conjunto de dados depende do arquivo de dados que utiliza. Consulte a tabela seguinte para obter uma lista dos arquivos de dados suportada pela fábrica de dados. Clique num arquivo de dados para saber como criar um serviço ligado e um conjunto de dados para este arquivo de dados.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Armazena os dados com * pode estar no local ou na infraestrutura do Azure como um serviço (IaaS). Estes arquivos de dados exigem que instale [Data Management Gateway](data-factory-data-management-gateway.md).

O exemplo na secção anterior, o tipo do conjunto de dados está definido como **AzureSqlTable**. Da mesma forma, para um conjunto de dados de Blobs do Azure, o tipo do conjunto de dados está definido como **AzureBlob**, conforme mostrado no seguinte JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>Estrutura do conjunto de dados
O **estrutura** secção é opcional. Define o esquema do conjunto de dados, que contém uma coleção de nomes e tipos de dados das colunas. Utilize a secção de estrutura para fornecer informações de tipo que são utilizadas para converter os tipos de modelo e mapeie as colunas de origem para o destino. No exemplo seguinte, o conjunto de dados tem três colunas: `slicetimestamp`, `projectname`, e `pageviews`. São do tipo String, String e Decimal, respetivamente.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Cada coluna na estrutura de contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna.  |Não |
| Cultura |. Idioma baseado em NET a ser utilizado quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. |Não |
| formato |Formato de cadeia a ser utilizado quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. |Não |

As seguintes diretrizes ajudam a determinar quando deve incluir informações de estrutura e o que incluir no **estrutura** secção.

* **Para origens de dados estruturados**, especificar a secção de estrutura apenas se pretender mapear colunas de origem para sink colunas e os respetivos nomes não são iguais. Este tipo de origem de dados estruturados armazena informações de esquema e o tipo de dados, juntamente com dados propriamente ditos. Exemplos de origens de dados estruturados incluem o SQL Server, Oracle e tabela do Azure. 
  
    Como informações sobre o tipo já se encontra disponível para origens de dados estruturados, não deve incluir informações sobre o tipo ao incluir a secção de estrutura.
* **Para o esquema em origens de dados de leitura (especificamente o armazenamento de BLOBs)**, pode optar por armazenar os dados sem armazenar quaisquer informações de esquema ou tipo com os dados. Para estes tipos de origens de dados, inclua estrutura quando pretende mapear colunas de origem para sink colunas. Também inclua estrutura quando o conjunto de dados for uma entrada para uma atividade de cópia e tipos de dados do conjunto de dados de origem devem ser convertidos para tipos de nativos para o sink. 
    
    Fábrica de dados suporta os seguintes valores para fornecer informações de tipo na estrutura: **Int16, Int32, Int64, único, Double, Decimal, Byte [], booleano, String, Guid, Datetime, Datetimeoffset e Timespan**. Estes valores são especificação de idioma comum (CLS)-em conformidade,. Valores do tipo baseada em NET.

Fábrica de dados efetua automaticamente conversões de tipo quando mover dados de um arquivo de dados de origem para um arquivo de dados do sink. 
  

## <a name="dataset-availability"></a>Disponibilidade do conjunto de dados
O **disponibilidade** secção um conjunto de dados define a janela de processamento (por exemplo, a hora a hora, diariamente, ou a semanal) para o conjunto de dados. Para obter mais informações sobre janelas de atividade, consulte [agendamento e execução](data-factory-scheduling-and-execution.md).

A secção de disponibilidade seguinte especifica que o conjunto de dados de saída está é produzido de hora a hora, ou o conjunto de dados de entrada está disponível por hora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Se o pipeline tem os seguinte início e a hora de fim:  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

O conjunto de dados de saída é produzido hora a hora no pipeline de início e de fim. Por conseguinte, existem cinco setores de conjunto de dados produzidos por este pipeline, um para cada janela de atividade (12: 00 - 1 AM, 1: 00 - 2 AM, 2 AM - 3 AM, AM de 3 - 4 AM, 4 AM - 5: 00). 

A tabela seguinte descreve as propriedades que pode utilizar a secção de disponibilidade:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para produção de setor de conjunto de dados.<br/><br/><b>Suportado frequência</b>: minuto, hora, dia, semana, mês |Sim |ND |
| intervalo |Especifica um multiplicador para a frequência.<br/><br/>"Intervalo de frequência x" determina com que frequência o setor é produzido. Por exemplo, se precisar do conjunto de dados segmentados numa base horária, defina <b>frequência</b> para <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/>Tenha em atenção que se especificar **frequência** como **minuto**, deve definir o intervalo para não inferior a 15. |Sim |ND |
| Estilo |Especifica se deve ser produzido do setor no início ou fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Se **frequência** está definido como **mês**, e **estilo** está definido como **EndOfInterval**, o setor é produzido no último dia do mês. Se **estilo** está definido como **StartOfInterval**, o setor é produzido no primeiro dia do mês.<br/><br/>Se **frequência** está definido como **dia**, e **estilo** está definido como **EndOfInterval**, o setor é produzido na última hora do dia.<br/><br/>Se **frequência** está definido como **hora**, e **estilo** está definido como **EndOfInterval**, o setor é produzido no fim da hora. Por exemplo, para um setor para o período de PM 1 PM - 2, o setor é produzido em 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição na hora utilizado pelo programador para limites de setor de conjunto de dados de computação absoluta. <br/><br/>Tenha em atenção que, se este propoerty tem partes de data que são mais granulares do que a frequência especificada, as partes mais granulares serão ignoradas. Por exemplo, se o **intervalo** é **hora a hora** (frequência: horas e intervalo: 1) e o **anchorDateTime** contém **minutos e segundos**, em seguida, as partes minutos e segundos, da **anchorDateTime** são ignorados. |Não |01/01/0001 |
| deslocamento |TimeSpan através do qual são desviados o início e de fim de todos os setores de conjunto de dados. <br/><br/>Tenha em atenção que se ambos os **anchorDateTime** e **desvio** especificado, o resultado é o shift combinada. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por predefinição, diariamente (`"frequency": "Day", "interval": 1`) setores iniciar às 12 AM (meia-noite) Hora Universal Coordenada (UTC). Se pretender que a hora de início, a hora UTC de 6: 00 em vez disso, defina o deslocamento conforme mostrado no seguinte fragmento: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>exemplo de anchorDateTime
No exemplo seguinte, o conjunto de dados é produzido cada 23 horas. Primeiro setor é iniciada no momento especificado pelo **anchorDateTime**, que está definido para `2017-04-19T08:00:00` (UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>exemplo de deslocamento/estilo
O seguinte conjunto de dados é mensal e é produzido no 3rd de cada mês às 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Política de conjunto de dados
O **política** secção na definição do conjunto de dados define os critérios ou a condição que os setores de conjunto de dados tem de cumprir.

### <a name="validation-policies"></a>Políticas de validação
| Nome da política | Descrição | Aplicar a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que os dados no **Blob storage do Azure** cumpre os requisitos de tamanho mínimo (em megabytes). |Armazenamento de Blobs do Azure |Não |ND |
| minimumRows |Valida que os dados de um **SQL database do Azure** ou um **tabela do Azure** contém o número mínimo de linhas. |<ul><li>Base de dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

#### <a name="examples"></a>Exemplos
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Conjuntos de dados externos
Conjuntos de dados externos são aqueles que não são produzidos por um pipeline na fábrica de dados em execução. Se o conjunto de dados está marcado como **externo**, a **ExternalData** pode ser definida a política de modo a influenciar o comportamento de disponibilidade de setor o conjunto de dados.

A menos que um conjunto de dados está a ser produzido pela fábrica de dados, deverá ser marcado como **externo**. Esta definição geralmente aplica-se para as entradas de atividade primeiro num pipeline, a menos que está a ser utilizada a atividade ou encadeamento do pipeline.

| Nome | Descrição | Necessário | Valor predefinido |
| --- | --- | --- | --- |
| dataDelay |O tempo de atraso de verificação na disponibilidade dos dados externos para o setor especificado. Por exemplo, pode atrasar uma verificação horária ao utilizar esta definição.<br/><br/>A definição só se aplica à hora presente.  Por exemplo, se for 1:00 PM agora e este valor é 10 minutos, a validação é iniciada à 1: as 22: 00.<br/><br/>Tenha em atenção que esta definição não afeta a setores no passado. Setores com **hora de fim de setor** + **dataDelay** < **agora** são processadas sem qualquer atrasos.<br/><br/>Vezes superior ao 23:59 horas devem ser especificadas utilizando o `day.hours:minutes:seconds` formato. Por exemplo, para especificar a 24 horas, não utilize o 24:00:00. Em alternativa, utilize 1.00:00:00. Se utilizar 24:00:00, esta é tratada como 24 dias (24.00:00:00). Para 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| RetryInterval |O tempo de espera entre uma falha e a tentativa seguinte. Esta definição aplica-se a hora presente. Se anterior tente falhado, tente o seguinte é após o **retryInterval** período. <br/><br/>Se for 1:00 PM agora, vamos começar a primeira tentativa. Se a duração para concluir a primeira verificação de validação é de 1 minuto e a operação falhou, a tentativa seguinte ocorrerá é 1:00 + 1 min (duração) + 1min (intervalo de repetição) = 1:02 PM. <br/><br/>Setores no passado, não há nenhum atraso. Nova tentativa ocorre imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se esta propriedade é definida para 10 minutos, a validação deve ser concluída entre 10 minutos. Se demora mais de 10 minutos para efetuar a validação, a repetir o tempo limite.<br/><br/>Se todas as tentativas para o tempo limite de validação, o setor está marcado como **ServiceHost**. |Não |00:10:00 (10 minutos) |
| maximumRetry |O número de vezes para procurar a disponibilidade dos dados externos. O valor máximo permitido é de 10. |Não |3 |


## <a name="create-datasets"></a>Criar conjuntos de dados
Pode criar conjuntos de dados utilizando um destas ferramentas ou SDKs: 

- Assistente de Cópia 
- Portal do Azure
- Visual Studio
- PowerShell
- Modelo Azure Resource Manager
- API REST
- API .NET

Consulte os tutoriais seguintes para obter instruções passo a passo para criar pipelines e conjuntos de dados utilizando um destas ferramentas ou SDKs:
 
- [Build a pipeline with a data transformation activity](data-factory-build-your-first-pipeline.md) (Criar um pipeline cum uma atividade de transformação de dados)
- [Criar um pipeline com uma atividade de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Depois de um pipeline é criado e implementado, pode gerir e monitorizar os seus pipelines utilizando os painéis do portais do Azure ou para a aplicação de monitorização e gestão. Consulte os tópicos seguintes para obter instruções passo a passo: 

- [Monitorizar e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md)
- [Monitorizar e gerir pipelines com a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Âmbito de conjuntos de dados
Pode criar conjuntos de dados que estão no âmbito de um pipeline, utilizando o **conjuntos de dados** propriedade. Estes conjuntos de dados só podem ser utilizados por atividades dentro deste pipeline e não pela atividades em outros pipelines. O exemplo seguinte define um pipeline com dois conjuntos de dados (InputDataset rdc e OutputDataset rdc) para ser utilizado dentro do pipeline.  

> [!IMPORTANT]
> Âmbito de conjuntos de dados só são suportados com pipelines única (onde **pipelineMode** está definido como **OneTime**). Consulte [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) para obter mais detalhes.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Passos seguintes
- Para mais informações sobre pipelines, consulte [Criar pipelines](data-factory-create-pipelines.md). 
- Para obter mais informações sobre como pipelines são agendadas e executadas, consulte [agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md). 
