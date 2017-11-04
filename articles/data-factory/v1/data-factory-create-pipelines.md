---
title: Criar/agenda Pipelines, Encadeiam atividades no Data Factory | Microsoft Docs
description: "Saiba como criar um pipeline de dados no Azure Data Factory para mover e transforme dados. Crie um fluxo de trabalho baseados nos dados para produzir preparada para utilizar as informações."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8bfdd9d69b6172ff4fae82be6db4a459dd516716
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines e atividades de Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-create-pipelines.md)
> * [Versão 2 - Pré-visualização](../concepts-pipelines-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [Pipelines V2](../concepts-pipelines-activities.md).

Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e a utilizá-los para construir fluxos de dados completos e orientados por dados para os seus cenários de movimento de dados e processamento de dados.  

> [!NOTE]
> Este artigo pressupõe que já leu [introdução ao Azure Data Factory](data-factory-introduction.md). Se não tiver hands-on-experiência com criar fábricas de dados, passar [tutorial de transformação de dados](data-factory-build-your-first-pipeline.md) e/ou [tutorial de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) iria ajudar a compreender melhor neste artigo.  

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um SQL Server no local para um Armazenamento de Blobs do Azure. Em seguida, utilize uma atividade do Hive que execute um script do Hive num cluster do Azure HDInsight para processar/transformar os dados do armazenamento de blobs para produzir dados de saída. Por fim, utilize uma segunda atividade de cópia para copiar os dados de saída para um armazém do Azure SQL Data Warehouse sobre o qual são criadas soluções de relatórios de business intelligence (BI). 

Uma atividade pode ter zero ou mais [conjuntos de dados](data-factory-create-datasets.md) de entrada e produzir um ou mais [conjuntos de dados](data-factory-create-datasets.md) de saída. O diagrama seguinte mostra a relação entre pipelines, atividades e conjuntos de dados no Data Factory: 

![Relação entre pipeline, a atividade e o conjunto de dados](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Um pipeline permite-lhe gerir atividades como um conjunto em vez de cada um deles individualmente. Por exemplo, pode implementar, agendar, suspender e retomar um pipeline, em vez de lidar com atividades no pipeline de forma independente.

O Data Factory suporta dois tipos de atividades -- atividades de movimento de dados e atividades de transformação de dados. Cada atividade pode ter entrada de zero ou mais [conjuntos de dados](data-factory-create-datasets.md) e produzir uma ou mais conjuntos de dados de saída.

Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Depois de criar um conjunto de dados, pode utilizá-lo com atividades num pipeline. Por exemplo, um conjunto de dados pode ser um conjunto de dados de entrada/saída de uma atividade Cópia ou de uma atividade HDInsightHive. Para obter mais informações sobre os conjuntos de dados, veja o artigo [Datasets in Azure Data Factory](data-factory-create-datasets.md) (Conjuntos de Dados no Azure Data Factory).

### <a name="data-movement-activities"></a>Atividades de movimento de dados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

Para obter mais informações, veja o artigo [Data Movement Activities (Atividades de Movimento de Dados)](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Para obter mais informações, veja o artigo [Data Transformation Activities (Atividades de Transformação de Dados)](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Atividades .NET personalizadas 
Se precisar de mover o arquivo de dados para/de um dado que a atividade de cópia não suportar, ou transformar dados utilizando a sua própria lógica, crie um **atividade personalizada do .NET**. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Pipelines de agenda
Um pipeline está ativo apenas entre o **iniciar** tempo e **final** tempo. Não foi executada antes da data de início ou após a hora de fim. Se o pipeline está em pausa, não ser executada independentemente da respetiva hora de início e de fim. Para um pipeline executar, que deve não ser pausado. Consulte [agendamento e execução](data-factory-scheduling-and-execution.md) para compreender como funciona o agendamento e execução no Azure Data Factory.

## <a name="pipeline-json"></a>JSON dos pipelines
Vamos ver mais de perto a definição dos pipelines no formato JSON. A estrutura genérica de um pipeline tem o aspeto seguinte:

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets": 
        [
        ]
    }
}
```

| Etiqueta | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome do pipeline. Especifique um nome que represente a ação que o pipeline realiza. <br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com uma letra, um número ou um caráter de sublinhado (_)</li><li>Os seguintes carateres não são permitidos: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Sim |
| descrição | Especifique o texto que descreve para o que é utilizado o pipeline. |Sim |
| atividades | A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Consulte a secção seguinte para obter detalhes sobre o elemento JSON de atividades. | Sim |  
| start | Data-hora de início para o pipeline. Tem de constar [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: `2016-10-14T16:32:41Z`. <br/><br/>É possível especificar uma hora local, por exemplo, um período de tempo EST. Eis um exemplo: `2016-02-27T06:00:00-05:00`", que é 6 AM EST.<br/><br/>As propriedades de início e de fim em conjunto especifique o período ativo para o pipeline. Setores de saída só são produzidos neste período de Active Directory. |Não<br/><br/>Se especificar um valor para a propriedade end, tem de especificar o valor da propriedade de início.<br/><br/>Os tempos de início e de fim podem de estar vazios para criar um pipeline. Tem de especificar ambos os valores para definir um período ativo do pipeline ser executada. Se não especificar os tempos de início e de fim quando criar um pipeline, pode configurá-los utilizando o cmdlet Set-AzureRmDataFactoryPipelineActivePeriod mais tarde. |
| Fim | Data-hora de fim para o pipeline. Se for especificado tem de estar no formato ISO. Por exemplo: `2016-10-14T17:32:41Z` <br/><br/>É possível especificar uma hora local, por exemplo, um período de tempo EST. Eis um exemplo: `2016-02-27T06:00:00-05:00`, que é 6 AM EST.<br/><br/>Para executar o pipeline de forma indefinida, especifique 9999-09-09 como o valor para a propriedade end. <br/><br/> Um pipeline está ativo apenas entre a hora de início e a hora de fim. Não foi executada antes da data de início ou após a hora de fim. Se o pipeline está em pausa, não ser executada independentemente da respetiva hora de início e de fim. Para um pipeline executar, que deve não ser pausado. Consulte [agendamento e execução](data-factory-scheduling-and-execution.md) para compreender como funciona o agendamento e execução no Azure Data Factory. |Não <br/><br/>Se especificar um valor para a propriedade de início, tem de especificar o valor da propriedade end.<br/><br/>Consulte as notas para o **iniciar** propriedade. |
| isPaused | Se definido como true, o pipeline não é executado. Trata-se no Estado em pausa. Valor predefinido = false. Pode utilizar esta propriedade para ativar ou desativar um pipeline. |Não |
| pipelineMode | O método de agendamento é executado para o pipeline. Valores permitidos são: agendada (predefinição), onetime.<br/><br/>'Agendada' indica que o pipeline é executado num intervalo de tempo especificado, de acordo com o período ativo (hora de início e fim). 'Onetime' indica que o pipeline é executado apenas uma vez. Pipelines onetime depois de criado não podem ser modificado/atualizar atualmente. Consulte [Onetime pipeline](#onetime-pipeline) para obter detalhes sobre a definição onetime. |Não |
| expirationTime | Duração de tempo após a criação para o qual o [pipeline Monouso](#onetime-pipeline) é válido e deve permanecer aprovisionado. Se não tem nenhum Active Directory, falha, ou pendentes é executado, o pipeline é automaticamente uma vez eliminada atingir o tempo de expiração. O valor predefinido:`"expirationTime": "3.00:00:00"`|Não |
| Conjuntos de dados |Lista de conjuntos de dados a ser utilizada por atividades definidas no pipeline. Esta propriedade pode ser utilizada para definir os conjuntos de dados que são específicas neste pipeline e não definido no factory de dados. Conjuntos de dados definidos no âmbito deste pipeline só podem ser utilizados por este pipeline e não podem ser partilhados. Consulte [âmbito conjuntos de dados](data-factory-create-datasets.md#scoped-datasets) para obter mais detalhes. |Não |

## <a name="activity-json"></a>JSON da Atividade
A secção **atividades** pode ter uma ou mais atividades definidas na mesma. Cada atividade tem a seguinte estrutura de nível superior:

```json
{
    "name": "ActivityName",
    "description": "description", 
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

A tabela seguinte descreve as propriedades na definição JSON da atividade:

| Etiqueta | Descrição | Necessário |
| --- | --- | --- |
| nome | Nome da atividade. Especifique um nome que represente a ação que a atividade realiza. <br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com uma letra, um número ou um caráter de sublinhado (_)</li><li>Os seguintes carateres não são permitidos: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Sim |
| descrição | Texto que descreve para o que é utilizada a atividade |Sim |
| tipo | Tipo de atividade. Consulte o [atividades de movimentos de dados](#data-movement-activities) e [atividades de transformação de dados](#data-transformation-activities) secções para diferentes tipos de atividades. |Sim |
| Entradas |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Sim |
| saídas |Tabelas de saída utilizadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para a atividade do HDInsight e do Azure Machine Learning atividade de classificação de lote <br/><br/>Não para todas as outras. |
| typeProperties |Propriedades de **typeProperties** secção dependem do tipo da atividade. Para ver as propriedades do tipo de uma atividade, clique nas ligações para a atividade na secção anterior. | Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, políticas predefinidas são utilizadas. |Não |
| Programador | propriedade de "Programador" é utilizada para definir o agendamento pretendido para a atividade. Os subproperties são as mesmas que na [propriedade de disponibilidade de um conjunto de dados](data-factory-create-datasets.md#dataset-availability). |Não |


### <a name="policies"></a>Políticas
As políticas afetam o comportamento de tempo de execução de uma atividade, especificamente quando o setor de uma tabela é processado. A tabela seguinte fornece os detalhes.

| Propriedade | Valores permitidos | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| Simultaneidade |Número inteiro <br/><br/>O valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções de actividade paralela que pode acontecer em diferentes setores. Por exemplo, se uma atividade tem de passar por um grande conjunto de dados disponíveis, que tenham um valor de concorrência maior acelera o processamento de dados. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina a ordenação de setores de dados que estão a ser processados.<br/><br/>Por exemplo, se tiver setores de 2 (uma acontecer em 4 pm e outra nas 17: 00) e ambos são pendentes execução. Se definir o executionPriorityOrder ser NewestFirst, o setor nas 17: 00 é processado primeiro. Da mesma forma se definir o executionPriorityORder ser OldestFIrst, em seguida, o setor no 4 PM é processado. |
| retry |Número inteiro<br/><br/>O valor máximo possível 10 |0 |Número de tentativas antes do processamento de dados para o setor está marcado como falha. Execução da atividade para um setor de dados é repetida até o número especificado de tentativas. Nova tentativa é efetuada logo que possível após a falha. |
| tempo limite |TimeSpan |00:00:00 |Tempo limite para a atividade de mensagens em fila. Exemplo: 00:10:00 (indica o tempo limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite é infinito.<br/><br/>Se a hora de processamento de dados de um setor excede o valor de tempo limite, será cancelado e o sistema tenta repetir o processamento. O número de tentativas depende a propriedade de repetição. Quando ocorre a tempo limite, o estado é definido como ServiceHost. |
| Atraso |TimeSpan |00:00:00 |Especifique o atraso antes do processamento de dados de é iniciado o setor.<br/><br/>A execução da atividade para um setor de dados é iniciada depois do atraso é posterior à hora de execução esperada.<br/><br/>Exemplo: 00:10:00 (implica atraso de 10 minutos) |
| longRetry |Número inteiro<br/><br/>O valor máximo: 10 |1 |O número de tentativas de repetição muito antes da execução do setor falhou.<br/><br/>tentativas de longRetry são espaçamento por longRetryInterval. Por isso, se tem de especificar um período de tempo entre as tentativas de repetição, utilize o longRetry. Se repetir e o longRetry forem especificados, cada tentativa de longRetry incluir as tentativas de repetição e o número máximo de tentativas de repetição * longRetry.<br/><br/>Por exemplo, se as seguintes definições na política de atividade:<br/>Repita: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Partem do princípio de houver apenas um setor para executar (estado está a aguardar a) e a execução da atividade falha sempre. Inicialmente seria possível 3 tentativas de execução consecutivos. Após cada tentativa, o estado do setor seria repetição. Após serem primeiro 3 tentativas sobre, o estado do setor seria LongRetry.<br/><br/>Depois de uma hora (ou seja, o valor do longRetryInteval), seria possível outro conjunto de 3 tentativas de execução consecutivos. Depois disso, seria possível executar o estado do setor e não iriam ser tentadas nenhuma mais tentativas. Por conseguinte, global 6 foram efetuadas tentativas.<br/><br/>Se qualquer execução for bem sucedida, o estado do setor seria preparado e não existem mais tentativas estão tentadas.<br/><br/>longRetry pode ser utilizada em situações onde dados dependentes chega vezes não determinística ou o ambiente geral flaky em que o processamento de dados ocorre. Nestes casos, fazer várias tentativas não umas a seguir podem ajudar e se o fizer, após um intervalo de tempo resultados no resultado pretendido.<br/><br/>Palavra de advertência: não definir valores elevados para longRetry ou longRetryInterval. Normalmente, os valores superiores implica outros problemas systemic. |
| longRetryInterval |TimeSpan |00:00:00 |O atraso entre tentativas de repetição longo |

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. No exemplo, a [atividade de cópia](data-factory-data-movement-activities.md) copia os dados de um armazenamento de blobs do Azure para uma base de dados SQL do Azure. 

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
} 
```

Tenha em atenção os seguintes pontos:

* Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
* A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**. Veja o artigo [Conjuntos de dados](data-factory-create-datasets.md) para saber como definir conjuntos de dados em JSON. 
* Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. No [atividades de movimentos de dados](#data-movement-activities) secção, clique em armazenam dos dados que pretende utilizar como uma origem ou de um receptor para obter mais informações sobre como mover os dados desse arquivo de dados. 

Para instruções completas de criar este pipeline, consulte [Tutorial: copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplos
No pipeline de exemplo seguinte, existe uma atividade do tipo **HDInsightHive** na secção **activities**. Neste exemplo, a [atividade Hive do HDInsight](data-factory-hive-activity.md) transforma dados de um armazenamento de Blobs do Azure mediante a execução de um ficheiro de script de Hive num cluster do Hadoop para o Azure HDInsight. 

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Tenha em atenção os seguintes pontos: 

* Na secção “activities”, existe apenas uma atividade cujo **type** está definido como **HDInsightHive**.
* O ficheiro do Script de ramo de registo **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.
* O `defines` secção é utilizada para especificar as definições de tempo de execução que são transmitidas ao script de ramo de registo como valores de configuração do ramo de registo (por exemplo `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

A secção **typeProperties** é diferente para cada atividade de transformação. Para saber mais sobre as propriedades de tipo suportado para uma atividade de transformação, clique na atividade de transformação no [atividades de transformação de dados](#data-transformation-activities) tabela. 

Para instruções completas de criar este pipeline, consulte [Tutorial: criar o seu primeiro pipeline para processar dados utilizando o cluster de Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Os dois pipelines de exemplo anteriores só contêm uma atividade. Pode ter mais de uma atividade num pipeline.  

Se tiver várias atividades num pipeline e saída de uma atividade não é uma entrada de outra atividade, as atividades poderão ser executadas em paralelo se setores de dados de entrada para as atividades estão prontos. 

Pode encadeiam duas atividades, fazendo com que o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada de outra atividade. A segunda atividade executa apenas quando primeiro for concluído com êxito.

![Encadeamento de atividades no pipeline mesmo](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Neste exemplo, o pipeline com duas atividades: Activity1 e Activity2. O Activity1 demora Dataset1 como entrada e produz uma saída Dataset2. A atividade demora Dataset2 como entrada e produz uma saída Dataset3. Desde a saída de Activity1 (Dataset2) é a entrada de Activity2, executa a Activity2 apenas depois da atividade seja concluída com êxito e produz o setor Dataset2. Se o Activity1 falhar por algum motivo e não produz o setor Dataset2, a atividade 2 não é executado para esse setor (por exemplo: 09: 00 para AM 10). 

Também pode encadeiam atividades que estão em diferentes pipelines.

![Encadeamento de atividades em duas pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Neste exemplo, Pipeline1 tem apenas uma atividade que demora Dataset1 como entrada e produz Dataset2 como resultado. O Pipeline2 também tem apenas uma atividade que aceita Dataset2 como uma entrada e Dataset3 como resultado. 

Para obter mais informações, consulte [agendamento e execução](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="create-and-monitor-pipelines"></a>Criar e monitorizar pipelines
Pode criar pipelines utilizando um destas ferramentas ou SDKs. 

- Assistente para copiar. 
- Portal do Azure
- Visual Studio
- Azure PowerShell
- Modelo Azure Resource Manager
- API REST
- API .NET

Consulte os tutoriais seguintes para obter instruções passo a passo para criar pipelines utilizando um destas ferramentas ou SDKs.
 
- [Build a pipeline with a data transformation activity](data-factory-build-your-first-pipeline.md) (Criar um pipeline cum uma atividade de transformação de dados)
- [Criar um pipeline com uma atividade de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Depois de um pipeline criado/implementada, pode gerir e monitorizar os seus pipelines utilizando o painéis do portal do Azure ou monitorizar e gerir aplicações. Consulte os tópicos seguintes para obter instruções passo a passo. 

- [Monitorizar e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorizar e gerir pipelines com a monitorizar e gerir aplicações](data-factory-monitor-manage-app.md)


## <a name="onetime-pipeline"></a>Onetime pipeline
Pode criar e agendar um pipeline para executar periodicamente (por exemplo: hora a hora ou diariamente) dentro os tempos de início e de fim que especificar na definição do pipeline. Consulte [agendamento atividades](#scheduling-and-execution) para obter mais detalhes. Também pode criar um pipeline que é executada apenas uma vez. Para tal, defina o **pipelineMode** propriedade na definição do pipeline para **onetime** conforme mostrado no seguinte exemplo JSON. O valor predefinido para esta propriedade é **agendada**.

```json
{
    "name": "CopyPipeline",
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
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ]
                "name": "CopyActivity-0"
            }
        ]
        "pipelineMode": "OneTime"
    }
}
```

Tenha em atenção o seguinte:

* **Iniciar** e **final** não são especificadas horas para o pipeline.
* **Disponibilidade** especificado conjuntos de dados de entrada e saída (**frequência** e **intervalo**), apesar de fábrica de dados não utiliza os valores.  
* Vista de diagrama mostra pipelines única. Este comportamento é propositado.
* Não não possível atualizar o uso pipelines. Pode clonar um único pipeline, mude o nome, atualizar propriedades e implementá-la para criar uma nova.


## <a name="next-steps"></a>Passos Seguintes
- Para mais informações sobre conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md) artigo. 
- Para obter mais informações sobre como pipelines são agendadas e executadas, consulte [agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md) artigo. 
  

