---
title: "Fábrica de dados do Azure - referência de scripts JSON | Microsoft Docs"
description: Fornece os esquemas JSON para entidades do Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: a437c369ac48fd4ac71dee2a85547d787d9dd210
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-data-factory---json-scripting-reference"></a>Fábrica de dados do Azure - referência de scripts JSON
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA).


Este artigo fornece os esquemas JSON e exemplos para definir entidades do Azure Data Factory (pipeline, atividade, conjunto de dados e o serviço ligado).  

## <a name="pipeline"></a>Pipeline 
A estrutura de alto nível para uma definição de pipeline é o seguinte: 

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

A tabela seguinte descreve as propriedades no pipeline de definição JSON:

| Propriedade | Descrição | Necessário
-------- | ----------- | --------
| nome | Nome do pipeline. Especifique um nome que representa a ação que está configurado para a atividade ou o pipeline fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com uma letra, um número ou um caráter de sublinhado (_)</li><li>Os seguintes carateres não são permitidos: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Sim |
| descrição |Texto que descreve o que a atividade ou o pipeline é utilizado para | Não |
| atividades | Contém uma lista de atividades. | Sim |
| start |Data-hora de início para o pipeline. Tem de constar [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41. <br/><br/>É possível especificar uma hora local, por exemplo, um período de tempo EST. Eis um exemplo: `2016-02-27T06:00:00**-05:00`, que é 6 AM EST.<br/><br/>As propriedades de início e de fim em conjunto especifique o período ativo para o pipeline. Setores de saída só são produzidos neste período de Active Directory. |Não<br/><br/>Se especificar um valor para a propriedade end, tem de especificar o valor da propriedade de início.<br/><br/>Os tempos de início e de fim podem de estar vazios para criar um pipeline. Tem de especificar ambos os valores para definir um período ativo do pipeline ser executada. Se não especificar os tempos de início e de fim quando criar um pipeline, pode configurá-los utilizando o cmdlet Set-AzureRmDataFactoryPipelineActivePeriod mais tarde. |
| Fim |Data-hora de fim para o pipeline. Se for especificado tem de estar no formato ISO. Por exemplo: 2014-10-14T17:32:41 <br/><br/>É possível especificar uma hora local, por exemplo, um período de tempo EST. Eis um exemplo: `2016-02-27T06:00:00**-05:00`, que é 6 AM EST.<br/><br/>Para executar o pipeline de forma indefinida, especifique 9999-09-09 como o valor para a propriedade end. |Não <br/><br/>Se especificar um valor para a propriedade de início, tem de especificar o valor da propriedade end.<br/><br/>Consulte as notas para o **iniciar** propriedade. |
| isPaused |Se definido como verdadeiro pipeline não é executado. Valor predefinido = false. Pode utilizar esta propriedade para ativar ou desativar. |Não |
| pipelineMode |O método de agendamento é executado para o pipeline. Valores permitidos são: agendada (predefinição), onetime.<br/><br/>'Agendada' indica que o pipeline é executado num intervalo de tempo especificado, de acordo com o período ativo (hora de início e fim). 'Onetime' indica que o pipeline é executado apenas uma vez. Pipelines onetime depois de criado não podem ser modificado/atualizar atualmente. Consulte [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) para obter detalhes sobre a definição onetime. |Não |
| expirationTime |Duração de tempo após a criação, para que o pipeline é válido e deve permanecer aprovisionado. Se não tem nenhum Active Directory, falha, ou pendentes é executado, o pipeline é eliminado automaticamente assim que atingir o tempo de expiração. |Não |


## <a name="activity"></a>Atividade 
A estrutura de alto nível para uma atividade dentro de uma definição de pipeline (elemento atividades) é o seguinte:

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
    }
    "scheduler":
    {
    }
}
```

Os seguintes tabela descrevem as propriedades dentro da definição JSON da atividade:

| Etiqueta | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da atividade. Especifique um nome que representa a ação que a atividade está configurada para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com uma letra, um número ou um caráter de sublinhado (_)</li><li>Os seguintes carateres não são permitidos: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Sim |
| descrição |Texto que descreve o que é utilizada a atividade para. |Não |
| tipo |Especifica o tipo da atividade. Consulte o [ARQUIVOS de dados](#data-stores) e [atividades de transformação de dados](#data-transformation-activities) secções para diferentes tipos de atividades. |Sim |
| Entradas |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Não HDInsightStreaming e SqlServerStoredProcedure atividades <br/> <br/> Sim para todas as outras pessoas |
| saídas |Tabelas de saída utilizadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para atividades do HDInsight, atividades do Azure Machine Learning e atividade de procedimento armazenado. <br/><br/>Não para todas as outras. |
| typeProperties |Propriedades na secção typeProperties dependem do tipo da atividade. |Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, políticas predefinidas são utilizadas. |Não |
| Programador |propriedade de "Programador" é utilizada para definir o agendamento pretendido para a atividade. Os subproperties são as mesmas que na [propriedade de disponibilidade de um conjunto de dados](data-factory-create-datasets.md#dataset-availability). |Não |

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

### <a name="typeproperties-section"></a>secção typeProperties
A secção de typeProperties é diferente para cada atividade. Atividades de transformação tem apenas as propriedades de tipo. Consulte [atividades de transformação de dados](#data-transformation-activities) secção neste artigo para exemplos JSON que definem as atividades de transformação num pipeline. 

**Atividade de cópia** tem dois subsecções na secção typeProperties: **origem** e **sink**. Consulte [ARQUIVOS de dados](#data-stores) secção deste artigo para exemplos JSON que mostram como utilizar os dados de arquivo como uma origem de e/ou o sink. 

### <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. Neste exemplo, o [atividade de cópia](data-factory-data-movement-activities.md) copia dados a partir de um armazenamento de Blobs do Azure para uma base de dados SQL do Azure. 

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
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Tenha em atenção os seguintes pontos:

* Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
* A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**.
* Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.

Consulte [ARQUIVOS de dados](#data-stores) secção deste artigo para exemplos JSON que mostram como utilizar os dados de arquivo como uma origem de e/ou o sink.    

Para instruções completas de criar este pipeline, consulte [Tutorial: copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

### <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplos
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
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Tenha em atenção os seguintes pontos: 

* Na secção “activities”, existe apenas uma atividade cujo **type** está definido como **HDInsightHive**.
* O ficheiro do Script de ramo de registo **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.
* O **define** secção é utilizada para especificar as definições de tempo de execução que são transmitidas ao script de ramo de registo como valores de configuração do ramo de registo (por exemplo `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Consulte [atividades de transformação de dados](#data-transformation-activities) secção neste artigo para exemplos JSON que definem as atividades de transformação num pipeline.

Para instruções completas de criar este pipeline, consulte [Tutorial: criar o seu primeiro pipeline para processar dados utilizando o cluster de Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="linked-service"></a>Serviço ligado
A estrutura de alto nível para uma definição de serviço ligado é o seguinte:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Os seguintes tabela descrevem as propriedades dentro da definição JSON da atividade:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- | 
| nome | Nome do serviço ligado. | Sim | 
| Propriedades - tipo | Tipo de serviço ligado. Por exemplo: armazenamento do Azure, SQL Database do Azure. |
| typeProperties | A secção de typeProperties tem elementos que são diferentes para cada arquivo de dados ou ambiente de computação. Consulte [arquivos de dados](#datastores) secção para todos os dados armazenam serviços ligados e [ambientes de computação](#compute-environments) para todos os a capacidade de cálculo serviços ligados |   

## <a name="dataset"></a>Conjunto de dados 
Um conjunto de dados no Azure Data Factory é definido do seguinte modo:

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
| nome | Nome do conjunto de dados. Consulte [do Azure Data Factory - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura. |Sim |ND |
| tipo | tipo do conjunto de dados. Especifique um dos tipos suportados pelo Azure Data Factory (por exemplo: AzureBlob, AzureSqlTable). Consulte [ARQUIVOS de dados](#data-stores) secção para todos os arquivos de dados e os tipos de conjunto de dados suportados pela fábrica de dados. | 
| estrutura | Esquema do conjunto de dados. Contém colunas, os respetivos tipos, etc. | Não |ND |
| typeProperties | Propriedades correspondente ao tipo selecionado. Consulte [ARQUIVOS de dados](#data-stores) na secção tipos suportados e as respetivas propriedades. |Sim |ND |
| externo | Sinalizador booleano para especificar se um conjunto de dados explicitamente é produzido por um pipeline de fábrica de dados ou não. |Não |FALSO |
| disponibilidade | Define a janela de processamento ou o modelo slicing para o conjunto de dados de produção. Para obter detalhes sobre o conjunto de dados repartir modelo, consulte [agendamento e execução](data-factory-scheduling-and-execution.md) artigo. |Sim |ND |
| política |Define os critérios ou a condição que os setores de conjunto de dados tem de cumprir. <br/><br/>Para obter mais informações, consulte [política de conjunto de dados](#Policy) secção. |Não |ND |

Cada coluna no **estrutura** secção contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna.  |Não |
| Cultura |.NET com base em idioma a ser utilizado quando o tipo for especificado, não sendo tipo .NET `Datetime` ou `Datetimeoffset`. Predefinição é `en-us`. |Não |
| formato |Formato de cadeia a ser utilizado quando o tipo for especificado, não sendo tipo .NET `Datetime` ou `Datetimeoffset`. |Não |

No exemplo seguinte, o conjunto de dados tem três colunas `slicetimestamp`, `projectname`, e `pageviews` e são do tipo: cadeia, cadeia e Decimal, respetivamente.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A tabela seguinte descreve as propriedades que pode utilizar o **disponibilidade** secção:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para produção de setor de conjunto de dados.<br/><br/><b>Suportado frequência</b>: minuto, hora, dia, semana, mês |Sim |ND |
| intervalo |Especifica um multiplicador para a frequência<br/><br/>"Intervalo de frequência x" determina com que frequência o setor é produzido.<br/><br/>Se precisar do conjunto de dados segmentados numa base horária, defina <b>frequência</b> para <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/><b>Tenha em atenção</b>: Se especificar a frequência como minuto, recomendamos que defina o intervalo para não inferior a 15 |Sim |ND |
| Estilo |Especifica se deve ser produzido do setor no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência é definida para o mês e estilo está definido como EndOfInterval, o setor é produzido no último dia do mês. Se o estilo for definido como StartOfInterval, o setor é produzido no primeiro dia do mês.<br/><br/>Se a frequência é definida para o dia e estilo está definido como EndOfInterval, o setor é produzido na última hora do dia.<br/><br/>Se a frequência é definida para a hora e estilo está definido como EndOfInterval, o setor é produzido no fim da hora. Por exemplo, para um setor para 1 PM – 2 PM período, o setor é produzido em 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição na hora utilizado pelo programador para limites de setor de conjunto de dados de computação absoluta. <br/><br/><b>Tenha em atenção</b>: se o AnchorDateTime tem partes de data que são mais granulares do que a frequência, em seguida, as partes mais granulares são ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> é <b>hora a hora</b> (frequência: horas e intervalo: 1) e o <b>AnchorDateTime</b> contém <b>minutos e segundos</b>a <b>minutos e segundos</b> partes do AnchorDateTime são ignoradas. |Não |01/01/0001 |
| deslocamento |TimeSpan através do qual são desviados o início e de fim de todos os setores de conjunto de dados. <br/><br/><b>Tenha em atenção</b>: se forem especificadas anchorDateTime e desvio, o resultado é o shift combinada. |Não |ND |

A secção de disponibilidade seguinte especifica que o conjunto de dados de saída produzidos hora a hora (ou) entrada conjunto de dados está disponível por hora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

O **política** secção na definição do conjunto de dados define os critérios ou a condição que os setores de conjunto de dados tem de cumprir.

| Nome da política | Descrição | Aplicar a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que os dados de um **BLOBs do Azure** cumpre os requisitos de tamanho mínimo (em megabytes). |Blob do Azure |Não |ND |
| minimumRows |Valida que os dados de um **SQL database do Azure** ou um **tabela do Azure** contém o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

**Exemplo:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

A menos que um conjunto de dados está a ser produzido pelo Azure Data Factory, deverá ser marcado como **externo**. Esta definição geralmente aplica-se para as entradas de atividade primeiro num pipeline, a menos que está a ser utilizada a atividade ou encadeamento do pipeline.

| Nome | Descrição | Necessário | Valor predefinido |
| --- | --- | --- | --- |
| dataDelay |Tempo de atraso de verificação na disponibilidade dos dados externos para o setor especificado. Por exemplo, se os dados estão disponíveis numa base horária, a verificação para ver os dados externos estão disponíveis e o setor correspondente estiver pronta pode sofrer um atraso utilizando dataDelay.<br/><br/>Aplica-se apenas à hora presente.  Por exemplo, se for 1:00 PM agora e este valor é 10 minutos, a validação é iniciada à 1: as 22: 00.<br/><br/>Esta definição não afeta setores no passado (setores com a hora de fim do setor + dataDelay < agora) processados sem qualquer atrasos.<br/><br/>Tempo maior do que o 23:59, precisam de horas especificado utilizando o `day.hours:minutes:seconds` formato. Por exemplo, para especificar a 24 horas, não utilize 24:00:00; em alternativa, utilize 1.00:00:00. Se utilizar 24:00:00, esta é tratada como 24 dias (24.00:00:00). Para 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| RetryInterval |O tempo de espera entre uma falha e a próxima repetir a tentativa. Se tentar falhar, repita seguinte é após retryInterval. <br/><br/>Se for 1:00 PM agora, vamos começar a primeira tentativa. Se a duração para concluir a primeira verificação de validação é de 1 minuto e a operação falhou, a tentativa seguinte ocorrerá é 1:00 + 1 min (duração) + 1 min (intervalo de repetição) = 1:02 PM. <br/><br/>Setores no passado, não há nenhum atraso. Nova tentativa ocorre imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se esta propriedade está definida para 10 minutos, a validação necessita de ser concluída dentro de 10 minutos. Se demora mais de 10 minutos para efetuar a validação, a repetir o tempo limite.<br/><br/>Se a todas as tentativas para a validação do tempo limite excedido, o setor está marcado como ServiceHost. |Não |00:10:00 (10 minutos) |
| maximumRetry |Número de vezes para procurar a disponibilidade dos dados externos. O valor máximo permitido é 10. |Não |3 |


## <a name="data-stores"></a>ARQUIVOS DE DADOS
O [serviço ligado](#linked-service) descrições de secção fornecida para os elementos JSON que são comuns a todos os tipos de serviços ligados. Esta secção fornece detalhes sobre os elementos JSON que são específicos para cada arquivo de dados.

O [Dataset](#dataset) descrições de secção fornecida para os elementos JSON que são comuns a todos os tipos de conjuntos de dados. Esta secção fornece detalhes sobre os elementos JSON que são específicos para cada arquivo de dados.

O [atividade](#activity) descrições de secção fornecida para os elementos JSON que são comuns a todos os tipos de atividades. Esta secção fornece detalhes sobre os elementos JSON que são específicos para cada arquivo de dados quando é utilizado como uma origem sink numa atividade de cópia.  

Clique na ligação para o arquivo que está interessado para ver os esquemas JSON para o serviço ligado, conjunto de dados e o origem sink para a atividade de cópia.

| Categoria | Arquivo de dados 
|:--- |:--- |
| **Azure** |[Armazenamento de Blobs do Azure](#azure-blob-storage) |
| &nbsp; |[Azure Data Lake Store](#azure-datalake-store) |
| &nbsp; |[BD do Cosmos para o Azure](#azure-cosmos-db) |
| &nbsp; |[Base de Dados SQL do Azure](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Search](#azure-search) |
| &nbsp; |[Armazenamento de tabelas do Azure](#azure-table-storage) |
| **Bases de dados** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Ficheiro** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Sistema de Ficheiros](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Outros** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |[Tabela de Web](#web-table) |

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="linked-service"></a>Serviço ligado
Existem dois tipos de serviços ligados: serviço ligado do Storage do Azure e o serviço ligado do Storage do Azure SAS.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para ligar a sua conta do storage do Azure a uma fábrica de dados utilizando o **chave da conta**, criar um serviço ligado do Storage do Azure. Para definir um Storage do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureStorage**. Em seguida, pode especificar os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| connectionString |Especificar as informações necessárias para ligar ao armazenamento do Azure para a propriedade connectionString. |Sim |

##### <a name="example"></a>Exemplo  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Serviço ligado do Storage do Azure SAS
O serviço SAS de armazenamento do Azure ligado permite-lhe ligar uma conta de armazenamento do Azure a um Azure data factory com uma assinatura de acesso partilhado (SAS). Fornece a fábrica de dados com acesso restrito/vínculo de tempo para recursos de todos os/específico (/ contentor do blob) no armazenamento. Para associar a sua conta de armazenamento do Azure para uma fábrica de dados utilizando a assinatura de acesso partilhado, crie um serviço SAS de armazenamento do Azure ligado. Para definir um SAS de armazenamento do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureStorageSas**. Em seguida, pode especificar os seguintes propriedades no **typeProperties** secção:   

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| sasUri |Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento do Azure como BLOBs, contentor ou tabela. |Sim |

##### <a name="example"></a>Exemplo

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Para mais informações sobre estes serviços ligados, consulte [conector do Blob Storage do Azure](data-factory-azure-blob-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de Blobs do Azure, defina o **tipo** do conjunto de dados para **AzureBlob**. Em seguida, especifique as seguintes propriedades específicas Blob do Azure no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para o contentor e a pasta no armazenamento de Blobs. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |Nome do blob. nome de ficheiro é opcional e maiúsculas e minúsculas.<br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no Blob específico.<br/><br/>Quando o nome de ficheiro não for especificado, a cópia inclui todos os Blobs no folderPath para conjunto de dados de entrada.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: Data. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy propriedade é opcional. Pode utilizá-lo para especificar um folderPath dinâmica e o nome de ficheiro de dados de séries de tempo. Por exemplo, folderPath pode ser parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example"></a>Exemplo

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


Para obter mais informações, consulte [conector de Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties) artigo.

### <a name="blobsource-in-copy-activity"></a>BlobSource na atividade de cópia
Se estiver a copiar dados de um Blob Storage do Azure, defina o **tipo de origem** da atividade de cópia para **BlobSource**e especifique os seguintes propriedades no * * origem * * secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada. |TRUE (valor predefinido), False |Não |

#### <a name="example-blobsource"></a>Exemplo: BlobSource * *
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink na atividade de cópia
Se estiver a copiar dados para um armazenamento de Blobs do Azure, defina o **sink do tipo** da atividade de cópia para **BlobSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou sistema de ficheiros. |<b>PreserveHierarchy</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível da pasta de destino. Os ficheiros de destino tem o nome de automaticamente gerado. <br/><br/><b>MergeFiles (predefinição):</b> une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro/Blob, o nome de ficheiro intercalado seria o nome especificado; caso contrário, seria nome de ficheiro gerada automaticamente. |Não |

#### <a name="example-blobsink"></a>Exemplo: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector de Blob do Azure](data-factory-azure-blob-connector.md#copy-activity-properties) artigo. 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Serviço ligado
Para definir um Azure Data Lake Store serviço ligado, defina o tipo de serviço ligado para **AzureDataLakeStore**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifique informações sobre a conta do Azure Data Lake Store. É o seguinte formato: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | Id de subscrição do Azure à qual pertence Data Lake Store. | Obrigatório para sink |
| resourceGroupName | Nome do grupo de recursos do Azure à qual pertence Data Lake Store. | Obrigatório para sink |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim (para autenticação principal do serviço) |
| servicePrincipalKey | Especifique a chave da aplicação. | Sim (para autenticação principal do serviço) |
| Inquilino | Especifique as informações de inquilino (nome ou o inquilino ID de domínio) em que reside a aplicação. Pode obtê-lo por posicionado o rato no canto superior direito do portal do Azure. | Sim (para autenticação principal do serviço) |
| Autorização | Clique em **autorizar** clique no botão no **Editor do Data Factory** e introduza as credenciais que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim (para autenticação de credenciais de utilizador)|
| ID de sessão | Id de sessão OAuth da sessão de autorização do OAuth. Cada id de sessão é exclusivo e só pode ser utilizado uma vez. Esta definição é gerada automaticamente quando utiliza o Editor do Data Factory. | Sim (para autenticação de credenciais de utilizador) |

#### <a name="example-using-service-principal-authentication"></a>Exemplo: utilizar a autenticação principal de serviço
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Exemplo: utilizar a autenticação de credenciais de utilizador
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Para obter mais informações, consulte [conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure Data Lake Store, defina o **tipo** do conjunto de dados para **AzureDataLakeStore**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| folderPath |Armazenar o caminho para o contentor e a pasta no Data Lake do Azure. |Sim |
| fileName |Nome do ficheiro no arquivo Azure Data Lake. nome de ficheiro é opcional e maiúsculas e minúsculas. <br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no ficheiro específico.<br/><br/>Quando o nome de ficheiro não for especificado, cópia inclui todos os ficheiros em folderPath para conjunto de dados de entrada.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: Data. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy propriedade é opcional. Pode utilizá-lo para especificar um folderPath dinâmica e o nome de ficheiro de dados de séries de tempo. Por exemplo, folderPath pode ser parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
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

Para obter mais informações, consulte [conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties) artigo. 

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Origem do Azure Data Lake Store na atividade de cópia
Se estiver a copiar dados de um Azure Data Lake Store, defina o **tipo de origem** da atividade de cópia para **AzureDataLakeStoreSource**e especifique os seguintes propriedades no **origem**secção:

**AzureDataLakeStoreSource** suporta as seguintes propriedades **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada. |TRUE (valor predefinido), False |Não |

#### <a name="example-azuredatalakestoresource"></a>Exemplo: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) artigo.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>O Azure Data Lake Store dependente na atividade de cópia
Se estiver a copiar dados para um Azure Data Lake Store, defina o **sink do tipo** da atividade de cópia para **AzureDataLakeStoreSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Especifica o comportamento de cópia. |<b>PreserveHierarchy</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem são criados no primeiro nível da pasta de destino. Os ficheiros de destino são criados com o nome de automaticamente gerado.<br/><br/><b>MergeFiles</b>: une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de ficheiro/Blob, o nome de ficheiro intercalado seria o nome especificado; caso contrário, seria nome de ficheiro gerada automaticamente. |Não |

#### <a name="example-azuredatalakestoresink"></a>Exemplo: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
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
        }]
    }
}
```

Para obter mais informações, consulte [conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) artigo. 

## <a name="azure-cosmos-db"></a>Azure Cosmos DB  

### <a name="linked-service"></a>Serviço ligado
Para definir uma base de dados do Azure Cosmos serviço ligado, defina o **tipo** do serviço ligado para **DocumentDb**e especifique os seguintes propriedades no **typeProperties** secção:  

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para estabelecer ligação à base de dados do Azure Cosmos DB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Para obter mais informações, consulte [conector de base de dados do Azure Cosmos](data-factory-azure-documentdb-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure Cosmos DB, defina o **tipo** do conjunto de dados para **DocumentDbCollection**e especifique as seguintes propriedades no **typeProperties** secção: 

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| CollectionName |Nome da coleção de BD do Cosmos do Azure. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Para obter mais informações, consulte [conector de base de dados do Azure Cosmos](data-factory-azure-documentdb-connector.md#dataset-properties) artigo.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Origem da coleção do Azure Cosmos DB na atividade de cópia
Se estiver a copiar dados de uma base de dados do Cosmos do Azure, defina o **tipo de origem** da atividade de cópia para **DocumentDbCollectionSource**e especifique os seguintes propriedades no **origem**secção:


| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler os dados. |Suportado pelo Azure Cosmos DB de cadeia de consulta. <br/><br/>Exemplo:`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a instrução de SQL que é executada:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Caráter especial para indicar que o documento está aninhado |Qualquer caráter. <br/><br/>BD do Cosmos do Azure é um arquivo de NoSQL para documentos JSON, onde são permitidas estruturas aninhadas. O Azure Data Factory permite que o utilizador denota hierarquia através de nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia irá gerar o objeto "Name" com elementos três subordinados em primeiro lugar, média e o último, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Receptor de coleção de BD Cosmos do Azure na atividade de cópia
Se estiver a copiar dados para a base de dados do Azure Cosmos, defina o **sink do tipo** da atividade de cópia para **DocumentDbCollectionSink**e especifique os seguintes propriedades no **sink** secção :

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |É necessário um caráter especial no nome de coluna de origem para indicar que documentos aninhados. <br/><br/>Por exemplo acima: `Name.First` na saída tabela produz a seguinte estrutura JSON no documento de BD do Cosmos:<br/><br/>"Nome": {<br/>    "First": "João"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>Valor predefinido é `.` (ponto final). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>Valor predefinido é `.` (ponto final). |
| WriteBatchSize |Número de pedidos paralelos ao serviço de base de dados do Azure Cosmos para criar documentos.<br/><br/>Pode otimizar o desempenho ao copiar dados do Azure Cosmos DB utilizando esta propriedade. Pode esperar um desempenho melhor quando aumenta writeBatchSize porque mais pedidos paralelos à base de dados do Azure Cosmos são enviados. No entanto, terá de evitar limitação que pode acionar a mensagem de erro: "taxa é grande pedido".<br/><br/>Limitação é decidida por um número de fatores, incluindo o tamanho de documentos, número de termos de documentos, a indexação de política de coleção de destino, etc. Para operações de cópia, pode utilizar uma coleção melhor (por exemplo, S3) para ter o maior débito disponível (2,500 pedidos unidades por segundo). |Número inteiro |Não (predefinição: 5) |
| writeBatchTimeout |De tempo de espera para a operação seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Para obter mais informações, consulte [conector de base de dados do Azure Cosmos](data-factory-azure-documentdb-connector.md#copy-activity-properties) artigo.

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

### <a name="linked-service"></a>Serviço ligado
Para definir uma base de dados do SQL do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureSqlDatabase**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especificar as informações necessárias para ligar à instância do SQL Database do Azure para a propriedade connectionString. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte [conector do SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SQL Database do Azure, defina o **tipo** do conjunto de dados para **AzureSqlTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância SQL Database do Azure que o serviço ligado refere-se. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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
Para obter mais informações, consulte [conector do SQL do Azure](data-factory-azure-sql-connector.md#dataset-properties) artigo. 

### <a name="sql-source-in-copy-activity"></a>Origem de SQL na atividade de cópia
Se estiver a copiar dados de uma base de dados do SQL do Azure, defina o **tipo de origem** da atividade de cópia para **SqlSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados a partir da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```
Para obter mais informações, consulte [conector do SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties) artigo. 

### <a name="sql-sink-in-copy-activity"></a>Sink do SQL Server na atividade de cópia
Se estiver a copiar dados para a SQL Database do Azure, defina o **sink do tipo** da atividade de cópia para **SqlSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia para executar de forma a que os dados de um setor específico é limpa. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a atividade de cópia preencher com o identificador de setor automaticamente gerado, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado dados upserts (inserções/atualizações) para a tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia faz com que os dados a ser movidos disponível numa tabela temporária com este tipo de tabela. Código do procedimento armazenado, em seguida, pode intercalar os dados que está a ser copiados com dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
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
        }]
    }
}
```

Para obter mais informações, consulte [conector do SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties) artigo. 

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Serviço ligado
Para definir um Azure SQL Data Warehouse serviço ligado, defina o **tipo** do serviço ligado para **AzureSqlDW**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especificar as informações necessárias para ligar à instância do armazém de dados SQL do Azure para a propriedade connectionString. |Sim |



#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte [conector Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure SQL Data Warehouse, defina o **tipo** do conjunto de dados para **AzureSqlDWTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na base de dados do Azure SQL Data Warehouse que referencia o serviço ligado. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
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

Para obter mais informações, consulte [conector Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) artigo. 

### <a name="sql-dw-source-in-copy-activity"></a>Origem de armazém de dados do SQL Server na atividade de cópia
Se estiver a copiar dados do Azure SQL Data Warehouse, defina o **tipo de origem** da atividade de cópia para **SqlDWSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados a partir da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

Para obter mais informações, consulte [conector Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artigo. 

### <a name="sql-dw-sink-in-copy-activity"></a>Receptor de armazém de dados do SQL Server na atividade de cópia
Se estiver a copiar dados para o Azure SQL Data Warehouse, defina o **sink do tipo** da atividade de cópia para **SqlDWSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia para executar de forma a que os dados de um setor específico é limpa. |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se deve utilizar o PolyBase (quando aplicável) em vez de mecanismo BULKINSERT. <br/><br/> **Utilizando o PolyBase é a forma recomendada para carregar dados para o SQL Data Warehouse.** |Verdadeiro <br/>FALSE (predefinição) |Não |
| polyBaseSettings |Um grupo de propriedades que podem ser especificados quando o **allowPolybase** propriedade está definida como **verdadeiro**. |&nbsp; |Não |
| rejectValue |Especifica o número ou a percentagem de linhas que pode ser rejeitada antes da consulta falha. <br/><br/>Saiba mais sobre as opções de rejeitar o PolyBase no **argumentos** secção [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tópico. |0 (predefinição), 1, 2,... |Não |
| rejectType |Especifica se a opção de rejectValue é especificada como um valor literal ou uma percentagem. |Valor de percentagem (predefinição) |Não |
| rejectSampleValue |Determina o número de linhas a obter antes do PolyBase recalcula a percentagem de linhas rejeitadas. |1, 2, … |Sim, se **rejectType** é **percentagem** |
| useTypeDefault |Especifica como processar os valores em falta nos ficheiros de texto delimitado quando PolyBase obtém dados a partir do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade da secção de argumentos no [criar formato de ficheiro externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |TRUE, False (predefinição) |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
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
        }]
    }
}
```

Para obter mais informações, consulte [conector Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artigo. 

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>Serviço ligado
Para definir uma pesquisa do Azure de serviço ligado, defina o **tipo** do serviço ligado para **azuresearch, uma vez**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| URL | URL para o serviço de pesquisa do Azure. | Sim |
| key | Chave de administrador para o serviço de pesquisa do Azure. | Sim |

#### <a name="example"></a>Exemplo

```json
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

Para obter mais informações, consulte [conector da Azure Search](data-factory-azure-search-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de pesquisa do Azure, defina o **tipo** do conjunto de dados para **AzureSearchIndex**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade de tipo tem de ser definida **AzureSearchIndex**.| Sim |
| indexName | Nome do índice da Azure Search. Fábrica de dados não crie o índice. O índice tem de existir na Azure Search. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Para obter mais informações, consulte [conector da Azure Search](data-factory-azure-search-connector.md#dataset-properties) artigo.

### <a name="azure-search-index-sink-in-copy-activity"></a>Receptor de índice de pesquisa do Azure na atividade de cópia
Se estiver a copiar dados para um índice da Azure Search, defina o **sink do tipo** da atividade de cópia para **AzureSearchIndexSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve intercalar ou substituir quando já existe um documento no índice. | Intercalar (predefinição)<br/>Carregar| Não |
| WriteBatchSize | Carrega dados para o índice da Azure Search, quando o tamanho da memória intermédia atinge writeBatchSize. | 1 a 1000. Valor predefinido é 1000. | Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
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
        }]
    }
}
```

Para obter mais informações, consulte [conector da Azure Search](data-factory-azure-search-connector.md#copy-activity-properties) artigo.

## <a name="azure-table-storage"></a>Table Storage do Azure

### <a name="linked-service"></a>Serviço ligado
Existem dois tipos de serviços ligados: serviço ligado do Storage do Azure e o serviço ligado do Storage do Azure SAS.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para ligar a sua conta do storage do Azure a uma fábrica de dados utilizando o **chave da conta**, criar um serviço ligado do Storage do Azure. Para definir um Storage do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureStorage**. Em seguida, pode especificar os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida: **AzureStorage** |Sim |
| connectionString |Especificar as informações necessárias para ligar ao armazenamento do Azure para a propriedade connectionString. |Sim |

**Exemplo:**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

#### <a name="azure-storage-sas-linked-service"></a>Serviço ligado do Storage do Azure SAS
O serviço SAS de armazenamento do Azure ligado permite-lhe ligar uma conta de armazenamento do Azure a um Azure data factory com uma assinatura de acesso partilhado (SAS). Fornece a fábrica de dados com acesso restrito/vínculo de tempo para recursos de todos os/específico (/ contentor do blob) no armazenamento. Para associar a sua conta de armazenamento do Azure para uma fábrica de dados utilizando a assinatura de acesso partilhado, crie um serviço SAS de armazenamento do Azure ligado. Para definir um SAS de armazenamento do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureStorageSas**. Em seguida, pode especificar os seguintes propriedades no **typeProperties** secção:   

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida: **AzureStorageSas** |Sim |
| sasUri |Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento do Azure como BLOBs, contentor ou tabela. |Sim |

**Exemplo:**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Para mais informações sobre estes serviços ligados, consulte [conector Table Storage do Azure](data-factory-azure-table-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de tabelas do Azure, defina o **tipo** do conjunto de dados para **AzureTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados do Azure tabela pelo serviço ligado refere-se. |Sim. Quando um tableName for especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se um azureTableSourceQuery também está especificado, os registos da tabela que satisfaça a consulta são copiados para o destino. |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
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

Para mais informações sobre estes serviços ligados, consulte [conector Table Storage do Azure](data-factory-azure-table-connector.md#dataset-properties) artigo. 

### <a name="azure-table-source-in-copy-activity"></a>Origem de tabela do Azure na atividade de cópia
Se estiver a copiar dados do Table Storage do Azure, defina o **tipo de origem** da atividade de cópia para **AzureTableSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta de tabela do Azure. Veja exemplos na secção seguinte: |Não. Quando um tableName for especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se um azureTableSourceQuery também está especificado, os registos da tabela que satisfaça a consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indica se swallow a exceção da tabela não existe. |VERDADEIRO<br/>FALSO |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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
        }]
    }
}
```

Para mais informações sobre estes serviços ligados, consulte [conector Table Storage do Azure](data-factory-azure-table-connector.md#copy-activity-properties) artigo. 

### <a name="azure-table-sink-in-copy-activity"></a>Receptor de tabela do Azure na atividade de cópia
Se estiver a copiar dados para o Table Storage do Azure, defina o **sink do tipo** da atividade de cópia para **AzureTableSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Partição chave valor predefinido que pode ser utilizado pelo sink. |Um valor de cadeia. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como chaves de partição. Se não for especificado, AzureTableDefaultPartitionKeyValue é utilizado como a chave de partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores da coluna são utilizados como chave de linha. Se não for especificado, utilize um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo de inserir os dados na tabela do Azure.<br/><br/>Esta propriedade controla se as linhas existentes na tabela de saída com correspondentes chaves de partição e a linha tem os respetivos valores substituído ou intercaladas. <br/><br/>Para saber mais sobre como funcionam estas definições (intercalação e substitua), consulte [inserção ou entidade de intercalação](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) tópicos. <br/><br> Esta definição aplica-se ao nível da linha, não o nível de tabela e nenhuma opção elimina as linhas na tabela de saída que não existem na entrada. |Intercalar (predefinição)<br/>Substituir |Não |
| WriteBatchSize |Insere dados na tabela do Azure quando é atingido o writeBatchSize ou writeBatchTimeout. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure, quando é atingido o writeBatchSize ou writeBatchTimeout |TimeSpan<br/><br/>Exemplo: "00: 20:00" (20 minutos) |Não (seg 90 de valor predefinido para o tempo limite de predefinição do cliente armazenamento) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
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
        }]
    }
}
```
Para mais informações sobre estes serviços ligados, consulte [conector Table Storage do Azure](data-factory-azure-table-connector.md#copy-activity-properties) artigo. 

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Serviço ligado
Para definir um Redshift Amazon serviço ligado, defina o **tipo** do serviço ligado para **AmazonRedshift**e especifique os seguintes propriedades no **typeProperties** secção :  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome anfitrião ou endereço IP do servidor Amazon Redshift. |Sim |
| porta |O número da porta TCP que o servidor do Amazon Redshift utiliza para escutar ligações de cliente. |Valor predefinido não,: 5439 |
| base de dados |Nome da base de dados Amazon Redshift. |Sim |
| o nome de utilizador |Nome de utilizador que tenha acesso à base de dados. |Sim |
| palavra-passe |Palavra-passe da conta de utilizador. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Para obter mais informações, consulte [Amazon Redshift conector](#data-factory-amazon-redshift-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Amazon Redshift, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na base de dados do Amazon Redshift pelo serviço ligado refere-se. |Não (se **consulta** de **RelationalSource** especificado) |


#### <a name="example"></a>Exemplo

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte [Amazon Redshift conector](#data-factory-amazon-redshift-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia 
Se estiver a copiar dados do Amazon Redshift, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** de **dataset** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Para obter mais informações, consulte [Amazon Redshift conector](#data-factory-amazon-redshift-connector.md#copy-activity-properties) artigo.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Serviço ligado
Para definir um IBM DB2 serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesDB2**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor DB2. |Sim |
| base de dados |Nome da base de dados DB2. |Sim |
| Esquema |Nome do esquema na base de dados. O nome de esquema é maiúsculas e minúsculas. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados DB2. Os valores possíveis são: anónimo, básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados de DB2 no local. |Sim |

#### <a name="example"></a>Exemplo
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
Para obter mais informações, consulte [conector IBM DB2](#data-factory-onprem-db2-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados DB2, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados DB2 pelo serviço ligado refere-se. TableName é maiúsculas e minúsculas. |Não (se **consulta** de **RelationalSource** especificado) 

#### <a name="example"></a>Exemplo
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

Para obter mais informações, consulte [conector IBM DB2](#data-factory-onprem-db2-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados do IBM DB2, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. |Não (se **tableName** de **dataset** especificado) |

#### <a name="example"></a>Exemplo
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
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
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Para obter mais informações, consulte [conector IBM DB2](#data-factory-onprem-db2-connector.md#copy-activity-properties) artigo.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Serviço ligado
Para definir um MySQL serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesMySql**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor MySQL. |Sim |
| base de dados |Nome da base de dados MySQL. |Sim |
| Esquema |Nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados MySQL. Os valores possíveis são: `Basic`. |Sim |
| o nome de utilizador |Especifique o nome de utilizador para ligar à base de dados MySQL. |Sim |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou. |Sim |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar para a base de dados MySQL no local. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Para obter mais informações, consulte [MySQL conector](data-factory-onprem-mysql-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados MySQL, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados MySQL que o serviço ligado refere-se. |Não (se **consulta** de **RelationalSource** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
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
Para obter mais informações, consulte [MySQL conector](data-factory-onprem-mysql-connector.md#dataset-properties) artigo. 

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de uma base de dados MySQL, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** de **dataset** especificado) |


#### <a name="example"></a>Exemplo
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [MySQL conector](data-factory-onprem-mysql-connector.md#copy-activity-properties) artigo. 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>Serviço ligado
Para definir um Oracle serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesOracle**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| driverType | Especificar o controlador para utilizar para copiar dados de/para a base de dados Oracle. Valores permitidos são **Microsoft** ou **ODP** (predefinição). Consulte [suportada a instalação e versão](#supported-versions-and-installation) secção nos detalhes do controlador. | Não |
| connectionString | Especifique as informações necessárias para estabelecer ligação à instância de base de dados Oracle para a propriedade connectionString. | Sim |
| gatewayName | Nome do gateway que é utilizada para ligar ao servidor Oracle no local |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte [conector Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Oracle, defina o **tipo** do conjunto de dados para **OracleTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na base de dados Oracle que referencia o serviço ligado. |Não (se **oracleReaderQuery** de **OracleSource** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
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
Para obter mais informações, consulte [conector Oracle](data-factory-onprem-oracle-connector.md#dataset-properties) artigo.

### <a name="oracle-source-in-copy-activity"></a>Origem de Oracle na atividade de cópia
Se estiver a copiar dados de uma base de dados Oracle, defina o **tipo de origem** da atividade de cópia para **OracleSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| oracleReaderQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable` <br/><br/>Se não for especificado, a instrução de SQL que é executada:`select * from MyTable` |Não (se **tableName** de **dataset** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

Para obter mais informações, consulte [conector Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) artigo.

### <a name="oracle-sink-in-copy-activity"></a>Oracle Sink na atividade de cópia
Se estiver a copiar dados para am base de dados Oracle, defina o **sink do tipo** da atividade de cópia para **OracleSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: 00:30:00 (30 minutos). |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 100) |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia para executar de forma a que os dados de um setor específico é limpa. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia preencher com o identificador de setor automaticamente gerado, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |

#### <a name="example"></a>Exemplo
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte [conector Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) artigo.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Serviço ligado
Para definir um PostgreSQL serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesPostgreSql**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor PostgreSQL. |Sim |
| base de dados |Nome da base de dados PostgreSQL. |Sim |
| Esquema |Nome do esquema na base de dados. O nome de esquema é maiúsculas e minúsculas. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados PostgreSQL. Os valores possíveis são: anónimo, básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados de PostgreSQL no local. |Sim |

#### <a name="example"></a>Exemplo

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
Para obter mais informações, consulte [PostgreSQL conector](data-factory-onprem-postgresql-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados PostgreSQL, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados PostgreSQL pelo serviço ligado refere-se. TableName é maiúsculas e minúsculas. |Não (se **consulta** de **RelationalSource** especificado) |

#### <a name="example"></a>Exemplo
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
Para obter mais informações, consulte [PostgreSQL conector](data-factory-onprem-postgresql-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de uma base de dados PostgreSQL, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: "consulta": "selecionar * de \"MySchema\".\" MyTable\"". |Não (se **tableName** de **dataset** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
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
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [PostgreSQL conector](data-factory-onprem-postgresql-connector.md#copy-activity-properties) artigo.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Serviço ligado
Para definir um armazém de negócio de SAP (BW) serviço ligado, defina o **tipo** do serviço ligado para **SapBw**e especifique os seguintes propriedades no **typeProperties** secção :  

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância do SAP BW. | Cadeia | Sim
systemNumber | Número de sistema do sistema SAP BW. | Número de decimal dois dígitos representado como uma cadeia. | Sim
ID de cliente | ID de cliente do cliente no sistema SAP W. | Número decimal de três dígitos representado como uma cadeia. | Sim
o nome de utilizador | Nome do utilizador que tem acesso ao servidor do SAP | Cadeia | Sim
palavra-passe | Palavra-passe para o utilizador. | Cadeia | Sim
gatewayName | Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à instância de SAP BW no local. | Cadeia | Sim
encryptedCredential | A cadeia de credencial encriptada. | Cadeia | Não

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte [conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SAP BW, defina o **tipo** do conjunto de dados para **RelationalTable**. Não estão propriedades específicos do tipo suportadas para o conjunto de dados SAP BW do tipo **RelationalTable**.  

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte [conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties) artigo. 

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados do SAP Business Warehouse, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta MDX para ler os dados na instância do SAP BW. | Consulta MDX. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) artigo. 

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Serviço ligado
Para definir um SAP HANA serviço ligado, defina o **tipo** do serviço ligado para **SapHana**e especifique os seguintes propriedades no **typeProperties** secção:  

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância de SAP HANA. Se o servidor estiver a utilizar uma porta personalizada, especifique `server:port`. | Cadeia | Sim
authenticationType | Tipo de autenticação. | Cadeia. "Básico" ou "Windows" | Sim 
o nome de utilizador | Nome do utilizador que tem acesso ao servidor do SAP | Cadeia | Sim
palavra-passe | Palavra-passe para o utilizador. | Cadeia | Sim
gatewayName | Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à instância de SAP HANA no local. | Cadeia | Sim
encryptedCredential | A cadeia de credencial encriptada. | Cadeia | Não

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Para obter mais informações, consulte [conector de SAP HANA](data-factory-sap-hana-connector.md#linked-service-properties) artigo.
 
### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SAP HANA, defina o **tipo** do conjunto de dados para **RelationalTable**. Não estão propriedades específicos do tipo suportadas para o conjunto de dados SAP HANA do tipo **RelationalTable**. 

#### <a name="example"></a>Exemplo

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
Para obter mais informações, consulte [conector de SAP HANA](data-factory-sap-hana-connector.md#dataset-properties) artigo. 

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de um arquivo de dados SAP HANA, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta SQL para ler os dados da instância de SAP HANA. | Consulta SQL. | Sim |


#### <a name="example"></a>Exemplo


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
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
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector de SAP HANA](data-factory-sap-hana-connector.md#copy-activity-properties) artigo.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Serviço ligado
Criar um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server no local a uma fábrica de dados. A tabela seguinte fornece uma descrição para os elementos JSON específicas do serviço do SQL Server ligado no local.

A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço ligado do SQL Server.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especificar as informações de connectionString necessárias para ligar à base de dados de SQL Server no local, utilizando a autenticação SQL ou autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar a autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |

Pode encriptar as credenciais utilizando o **New-AzureRmDataFactoryEncryptValue** cmdlet e utilizá-los na cadeia de ligação, conforme mostrado no exemplo seguinte (**EncryptedCredential** propriedade):  

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para utilizar a autenticação do SQL Server

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para utilizar a autenticação do Windows

Se o nome de utilizador e palavra-passe são especificadas, o gateway utiliza-los para representar a conta de utilizador especificada para ligar à base de dados do SQL Server no local. Caso contrário, o gateway se ligar ao SQL Server diretamente com o contexto de segurança do Gateway (a conta de arranque).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SQL Server, defina o **tipo** do conjunto de dados para **SqlServerTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância da base de dados do SQL Server que o serviço ligado refere-se. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "SqlServerInput",
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

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#dataset-properties) artigo. 

### <a name="sql-source-in-copy-activity"></a>Origem de SQL na atividade de cópia
Se estiver a copiar dados de uma base de dados do SQL Server, defina o **tipo de origem** da atividade de cópia para **SqlSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. Pode referenciar várias tabelas da base de dados referenciada pelo conjunto de dados de entrada. Se não for especificado, a instrução de SQL que é executada: selecione a partir de MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados a partir da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

Se o **sqlReaderQuery** está especificado para o SqlSource, a atividade de cópia executa esta consulta contra a origem de base de dados do SQL Server para obter os dados.

Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura serão utilizadas para criar uma consulta Selecione para executar a base de dados do SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

> [!NOTE]
> Quando utiliza **sqlReaderStoredProcedureName**, terá de especificar um valor para o **tableName** propriedade no conjunto de dados JSON. Não existem nenhum validações executadas apesar desta tabela.


#### <a name="example"></a>Exemplo
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

Neste exemplo, **sqlReaderQuery** especificado para o SqlSource. A atividade de cópia executa esta consulta na origem de base de dados do SQL Server para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros). O sqlReaderQuery pode fazer referência a várias tabelas na base de dados referenciada pelo conjunto de dados de entrada. Não é limitado para a tabela só definir como typeProperty tableName do conjunto de dados.

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura serão utilizadas para criar uma consulta Selecione para executar a base de dados do SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) artigo. 

### <a name="sql-sink-in-copy-activity"></a>Sink do SQL Server na atividade de cópia
Se estiver a copiar dados para uma base de dados do SQL Server, defina o **sink do tipo** da atividade de cópia para **SqlSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpa. Para obter mais informações, consulte [repetibilidade](#repeatability-during-copy) secção. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia preencher com o identificador de setor automaticamente gerado, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. Para obter mais informações, consulte [repetibilidade](#repeatability-during-copy) secção. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado dados upserts (inserções/atualizações) para a tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |
| sqlWriterTableType |Especifique o nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia faz com que os dados a ser movidos disponível numa tabela temporária com este tipo de tabela. Código do procedimento armazenado, em seguida, pode intercalar os dados que está a ser copiados com dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="example"></a>Exemplo
O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
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
        }]
    }
}
```

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) artigo. 

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Serviço ligado
Para definir um Sybase serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesSybase**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor Sybase. |Sim |
| base de dados |Nome da base de dados Sybase. |Sim |
| Esquema |Nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados Sybase. Os valores possíveis são: anónimo, básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados de Sybase no local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
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

Para obter mais informações, consulte [Sybase conector](data-factory-onprem-sybase-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Sybase, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados Sybase pelo serviço ligado refere-se. |Não (se **consulta** de **RelationalSource** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
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

Para obter mais informações, consulte [Sybase conector](data-factory-onprem-sybase-connector.md#dataset-properties) artigo. 

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de uma base de dados Sybase, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** de **dataset** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [Sybase conector](data-factory-onprem-sybase-connector.md#copy-activity-properties) artigo.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Serviço ligado
Para definir um Teradata serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesTeradata**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor Teradata. |Sim |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados Teradata. Os valores possíveis são: anónimo, básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados Teradata no local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Para obter mais informações, consulte [Teradata conector](data-factory-onprem-teradata-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Teradata Blob, defina o **tipo** do conjunto de dados para **RelationalTable**. Atualmente, não existem propriedades de tipo não suportadas para o conjunto de dados Teradata. 

#### <a name="example"></a>Exemplo
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
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

Para obter mais informações, consulte [Teradata conector](data-factory-onprem-teradata-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de uma base de dados Teradata, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte [Teradata conector](data-factory-onprem-teradata-connector.md#copy-activity-properties) artigo.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço Cassandra ligado, defina o **tipo** do serviço ligado para **OnPremisesCassandra**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| anfitrião |Uma ou mais endereços IP ou nomes de anfitrião dos servidores de Cassandra.<br/><br/>Especifica uma lista separada por vírgulas dos endereços IP ou nomes de anfitrião para ligar a todos os servidores em simultâneo. |Sim |
| porta |A porta TCP que o servidor de Cassandra utiliza para escutar ligações de cliente. |Valor predefinido não,: 9042 |
| authenticationType |Basic ou Anonymous |Sim |
| o nome de utilizador |Especifique o nome de utilizador da conta de utilizador. |Sim, se authenticationType está definido para básico. |
| palavra-passe |Especifique a palavra-passe da conta de utilizador. |Sim, se authenticationType está definido para básico. |
| gatewayName |O nome do gateway que é utilizado para ligar à base de dados de Cassandra no local. |Sim |
| encryptedCredential |Credencial encriptada pelo gateway. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [conector Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Cassandra, defina o **tipo** do conjunto de dados para **CassandraTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| keyspace |Nome do esquema na base de dados Cassandra ou keyspace. |Sim (se **consulta** para **CassandraSource** não está definido). |
| tableName |Nome da tabela na base de dados Cassandra. |Sim (se **consulta** para **CassandraSource** não está definido). |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
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

Para obter mais informações, consulte [conector Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties) artigo. 

### <a name="cassandra-source-in-copy-activity"></a>Origem de Cassandra na atividade de cópia
Se estiver a copiar dados de Cassandra, defina o **tipo de origem** da atividade de cópia para **CassandraSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Consulta de SQL 92 ou consulta CQL. Consulte [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando utilizar a consulta SQL, especifique **keyspace name.table nome** para representar a tabela que pretende consultar. |Não (se for tableName e keyspace no conjunto de dados estão definidos). |
| consistencyLevel |O nível de consistência Especifica quantos réplicas tem de responder a um pedido de leitura antes da devolução de dados para a aplicação de cliente. Cassandra verifica o número especificado de réplicas de dados satisfazer o pedido de leitura. |UM, DOIS, TRÊS, QUÓRUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Consulte [consistência dos dados de configuração](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter mais detalhes. |Não. Valor predefinido é um. |

#### <a name="example"></a>Exemplo
  
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
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
        }]
    }
}
```

Para obter mais informações, consulte [conector Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties) artigo.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Serviço ligado
Para definir um MongoDB serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesMongoDB**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome anfitrião ou endereço IP do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor do MongoDB utiliza para escutar ligações de cliente. |Opcional, valor predefinido: 27017 |
| authenticationType |Basic ou Anonymous. |Sim |
| o nome de utilizador |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |Palavra-passe para o utilizador. |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Opcional (se for utilizada a autenticação básica). predefinição: utiliza a conta de administrador e a base de dados especificada utilizando a propriedade databaseName. |
| DatabaseName |Nome da base de dados MongoDB que pretende aceder. |Sim |
| gatewayName |Nome do gateway que acede ao arquivo de dados. |Sim |
| encryptedCredential |Credencial encriptada pelo gateway. |Opcional |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [artigo de conector do MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de MongoDB, defina o **tipo** do conjunto de dados para **MongoDbCollection**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| CollectionName |Nome da coleção na base de dados de MongoDB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Para obter mais informações, consulte [artigo de conector do MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Origem do MongoDB na atividade de cópia
Se estiver a copiar dados de MongoDB, defina o **tipo de origem** da atividade de cópia para **MongoDbSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta de SQL 92. Por exemplo: `select * from MyTable`. |Não (se **collectionName** de **dataset** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [artigo de conector do MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Serviço ligado
Para definir um Amazon S3 serviço ligado, defina o **tipo** do serviço ligado para **AwsAccessKey**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| accessKeyID |ID da chave de acesso secreta. |Cadeia |Sim |
| secretAccessKey |A chave de acesso secreta próprio. |Cadeia secreta encriptada |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Para obter mais informações, consulte [artigo de conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Amazon S3, defina o **tipo** do conjunto de dados para **AmazonS3**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| bucketName |O nome do registo de S3. |Cadeia |Sim |
| key |A chave do objeto de S3. |Cadeia |Não |
| prefixo |Prefixo para a chave do objeto de S3. Objetos cujas chaves começar a utilizar este prefixo estão selecionados. Aplica-se apenas quando o chave está vazia. |Cadeia |Não |
| Versão |A versão do objeto de S3 se o controlo de versões de S3 estiver ativado. |Cadeia |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não | |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não | |


> [!NOTE]
> bucketName + chave especifica a localização do objeto S3 onde o registo é o contentor de raiz para objetos de S3 e a chave é o caminho completo para o objeto de S3.

#### <a name="example-sample-dataset-with-prefix"></a>Exemplo: Conjunto de dados de exemplo com prefixo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Exemplo: Conjunto de dados de exemplo (com a versão)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Exemplo: Caminhos dinâmicos para S3
No exemplo, utilizamos fixos valores das propriedades de chave e bucketName no conjunto de dados do Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Pode ter o Data Factory calcular a chave e bucketName dinamicamente durante a execução através da utilização de variáveis do sistema, tais como SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Pode efetuar as mesmas para a propriedade de prefixo de um conjunto de dados do Amazon S3. Consulte [funções de Data Factory e variáveis do sistema](data-factory-functions-variables.md) para uma lista de variáveis e funções suportadas.

Para obter mais informações, consulte [artigo de conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem de sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do Amazon S3, defina o **tipo de origem** da atividade de cópia para **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Especifica se recursivamente lista S3 objetos sob o diretório. |Verdadeiro/Falso |Não |


#### <a name="example"></a>Exemplo


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Para obter mais informações, consulte [artigo de conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Sistema de Ficheiros


### <a name="linked-service"></a>Serviço ligado
Pode associar um sistema de ficheiros no local para um Azure data factory com o **local no servidor de ficheiros** serviço ligado. A tabela seguinte fornece descrições para os elementos JSON que são específicas para o serviço de servidor de ficheiros no local ligada.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |Certifique-se de que a propriedade type é definida como **OnPremisesFileServer**. |Sim |
| anfitrião |Especifica o caminho da raiz da pasta que pretende copiar. Utilizar o caráter de escape ' \ ' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos. |Sim |
| ID de utilizador |Especifique o ID do utilizador que tem acesso ao servidor. |Não (se optar por encryptedCredential) |
| palavra-passe |Especifique a palavra-passe para o utilizador (ID de utilizador). |Não (se optar por encryptedCredential |
| encryptedCredential |Especifique as credenciais encriptadas que pode obter executando o cmdlet New-AzureRmDataFactoryEncryptValue. |Não (se optar por especificar ID de utilizador e palavra-passe em texto simples) |
| gatewayName |Especifica o nome do gateway que fábrica de dados deve utilizar para ligar ao servidor de ficheiros no local. |Sim |

#### <a name="sample-folder-path-definitions"></a>Definições de caminho de pasta de exemplo 
| Cenário | Na definição de serviço ligado do anfitrião | folderPath na definição do conjunto de dados |
| --- | --- | --- |
| Pasta local no computador do Data Management Gateway: <br/><br/>Exemplos: D:\\ \* ou D:\folder\subfolder\\* |D:\\ \\ (para o Data Management Gateway 2.0 e versões posteriores) <br/><br/> localhost (para versões anteriores a 2.0 do Data Management Gateway) |. \\ \\ ou pasta\\\\subpasta (para o Data Management Gateway 2.0 e versões posteriores) <br/><br/>D:\\ \\ ou d:\\\\pasta\\\\subpasta (para a versão do gateway abaixo 2.0) |
| Pasta partilhada remota: <br/><br/>Exemplos: \\ \\myserver\\partilhar\\ \* ou \\ \\myserver\\partilhar\\pasta\\subpasta\\* |\\\\\\\\MyServer\\\\partilhar |. \\ \\ ou pasta\\\\subpasta |


#### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: Utilizar o nome de utilizador e palavra-passe em texto simples

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Exemplo: Utilizar encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [artigo de conector sistema de ficheiros](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do sistema de ficheiros, defina o **tipo** do conjunto de dados para **FileShare**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Especifica o Subcaminho para a pasta. Utilizar o caráter de escape ' \' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter pasta caminhos com base no setor início/fim tempos de data. |Sim |
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, é o nome do ficheiro gerado no seguinte formato: <br/><br/>`Data.<Guid>.txt`(Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro para ser utilizado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros. <br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (único caráter).<br/><br/>Exemplo 1: "fileFilter": "*. log"<br/>Exemplo 2: "fileFilter": 2016 - 1-?. txt"<br/><br/>Tenha em atenção que fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. |Não |
| partitionedBy |Pode utilizar partitionedBy para especificar um folderPath/fileName dinâmico para dados de séries de tempo. Um exemplo é folderPath parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**; e níveis suportados são: **Optimal** e **mais rápido**. consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Não é possível utilizar fileName e fileFilter em simultâneo.

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
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

Para obter mais informações, consulte [artigo de conector sistema de ficheiros](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem de sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do sistema de ficheiros, defina o **tipo de origem** da atividade de cópia para **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é leitura recursivamente as subpastas ou apenas a pasta especificada. |TRUE, False (predefinição) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
        }]
    }
}
```
Para obter mais informações, consulte [artigo de conector sistema de ficheiros](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Sink do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados para o sistema de ficheiros, defina o **sink do tipo** da atividade de cópia para **FileSystemSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou sistema de ficheiros. |**PreserveHierarchy:** preserva a hierarquia de ficheiros na pasta de destino. Ou seja, o caminho relativo do ficheiro de origem para a pasta de origem é o mesmo que o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** todos os ficheiros da pasta de origem são criados no primeiro nível da pasta de destino. Os ficheiros de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles:** une todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro name/blob for especificado, o nome de ficheiro intercalado é o nome especificado. Caso contrário, é um nome de ficheiro gerada automaticamente. |Não |
auto-

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [artigo de conector sistema de ficheiros](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Serviço ligado
Para definir um FTP serviço ligado, defina o **tipo** do serviço ligado para **FtpServer**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| anfitrião |Nome ou endereço IP do servidor de FTP |Sim |&nbsp; |
| authenticationType |Especifique o tipo de autenticação |Sim |Anónimo, básico |
| o nome de utilizador |Utilizador com acesso ao servidor de FTP |Não |&nbsp; |
| palavra-passe |Palavra-passe para o utilizador (nome de utilizador) |Não |&nbsp; |
| encryptedCredential |Credencial encriptada para aceder ao servidor FTP |Não |&nbsp; |
| gatewayName |Nome do gateway Data Management Gateway para ligar a um servidor FTP no local |Não |&nbsp; |
| porta |Porta em que o servidor FTP está à escuta |Não |21 |
| enableSsl |Especifique se pretende utilizar FTP através do canal SSL/TLS |Não |VERDADEIRO |
| enableServerCertificateValidation |Especifique se pretende ativar a validação do certificado SSL de servidor quando a utilizar FTP através do canal SSL/TLS |Não |VERDADEIRO |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilizar a autenticação anónima

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exemplo: Utilizar o nome de utilizador e palavra-passe em texto simples para a autenticação básica

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exemplo: Utilizar a porta, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exemplo: Utilizar encryptedCredential para autenticação e de gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Para obter mais informações, consulte [conector FTP](data-factory-ftp-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do FTP, defina o **tipo** do conjunto de dados para **FileShare**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho de subchaves para a pasta. Utilize o caráter de escape ' \ ' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter pasta caminhos com base no setor início/fim tempos de data. |Sim 
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| fileFilter |Especifique um filtro para ser utilizado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (único caráter).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro de dados de séries de tempo. Por exemplo, folderPath parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**; e níveis suportados são: **Optimal** e **mais rápido**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência de binários. Verdadeiro para o modo binário e ASCII FALSO. Valor predefinido: True. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizados em simultâneo.

#### <a name="example"></a>Exemplo

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte [conector FTP](data-factory-ftp-connector.md#dataset-properties) artigo.

### <a name="file-system-source-in-copy-activity"></a>Origem de sistema de ficheiros na atividade de cópia
Se estiver a copiar dados de um servidor FTP, defina o **tipo de origem** da atividade de cópia para **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector FTP](data-factory-ftp-connector.md#copy-activity-properties) artigo.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Serviço ligado
Para definir um HDFS serviço ligado, defina o **tipo** do serviço ligado para **Hdfs**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **Hdfs** |Sim |
| Url |URL para o HDFS |Sim |
| authenticationType |Anónimo, ou o Windows. <br><br> Para utilizar **a autenticação Kerberos** para o conector do HDFS, consulte [nesta secção](#use-kerberos-authentication-for-hdfs-connector) para configurar o seu ambiente no local em conformidade. |Sim |
| Nome de utilizador |Autenticação de nome de utilizador do Windows. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para autenticação do Windows. |Sim (para autenticação do Windows) |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar para o HDFS. |Sim |
| encryptedCredential |[Novo AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) saída da credencial de acesso. |Não |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilizar a autenticação anónima

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Exemplo: Através da autenticação Windows

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [conector HDFS](#data-factory-hdfs-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do HDFS, defina o **tipo** do conjunto de dados para **FileShare**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo:`myfolder`<br/><br/>Utilize o caráter de escape ' \ ' para carateres especiais na cadeia. Por exemplo: para folder\subfolder, especifique a pasta\\\\subpasta e para d:\samplefolder, especifique d:\\\\samplefolder.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter pasta caminhos com base no setor início/fim tempos de data. |Sim |
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro de dados de séries de tempo. Exemplo: folderPath parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizados em simultâneo.

#### <a name="example"></a>Exemplo

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte [conector HDFS](#data-factory-hdfs-connector.md#dataset-properties) artigo. 

### <a name="file-system-source-in-copy-activity"></a>Origem de sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do HDFS, defina o **tipo de origem** da atividade de cópia para **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector HDFS](#data-factory-hdfs-connector.md#copy-activity-properties) artigo.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Serviço ligado
Para definir um SFTP de serviço ligado, defina o **tipo** do serviço ligado para **Sftp**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta em que o servidor SFTP faz a escuta. O valor predefinido é: 21 |Não |
| authenticationType |Especifique o tipo de autenticação. Valores permitidos: **básico**, **parâmetros SshPublicKey**. <br><br> Consulte [utilizando a autenticação básica](#using-basic-authentication) e [utilizando SSH autenticação de chave pública](#using-ssh-public-key-authentication) secções mais propriedades e exemplos JSON, respetivamente. |Sim |
| skipHostKeyValidation | Especifique se pretende ignorar a validação de chave de anfitrião. | Não. O valor predefinido: false |
| hostKeyFingerprint | Especifique a impressão de cima da situação da chave do anfitrião. | Se Sim o `skipHostKeyValidation` está definido como falso.  |
| gatewayName |Nome do Data Management Gateway para ligar a um servidor SFTP no local. | Sim, se copiar dados a partir de um servidor SFTP no local. |
| encryptedCredential | Credencial encriptada para aceder ao servidor SFTP. Gerado automaticamente quando especifica a autenticação básica (nome de utilizador + palavra-passe) ou autenticação de parâmetros SshPublicKey (nome de utilizador + caminho da chave privado ou conteúdo) no Assistente para copiar ou a caixa de diálogo de pop-up de ClickOnce. | Não. Aplicam-se apenas ao copiar dados a partir de um servidor SFTP no local. |

#### <a name="example-using-basic-authentication"></a>Exemplo: Utilizar a autenticação básica

Para utilizar autenticação básica, defina `authenticationType` como `Basic`e especifique as seguintes propriedades além do conector SFTP aqueles genéricos introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| o nome de utilizador | Utilizador que tenha acesso ao servidor SFTP. |Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: A autenticação básica com credencial encriptada * *

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>Utilizar a autenticação de chave pública SSH: * *

Para utilizar autenticação básica, defina `authenticationType` como `SshPublicKey`e especifique as seguintes propriedades além do conector SFTP aqueles genéricos introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| o nome de utilizador |Utilizador com acesso ao servidor de SFTP |Sim |
| privateKeyPath | Especifique um caminho absoluto para o ficheiro de chave privada pode aceder a este gateway. | Especifique o `privateKeyPath` ou `privateKeyContent`. <br><br> Aplicam-se apenas ao copiar dados a partir de um servidor SFTP no local. |
| privateKeyContent | Uma cadeia serializada do conteúdo da chave privada. O Assistente para copiar pode ler o ficheiro de chave privado e extrair automaticamente o conteúdo da chave privado. Se estiver a utilizar qualquer outra ferramenta/SDK, utilize a propriedade de privateKeyPath em vez disso. | Especifique o `privateKeyPath` ou `privateKeyContent`. |
| frase de acesso | Especifique a passagem frase/palavra-passe para desencriptar a chave privada, se o ficheiro de chave estiver protegido por uma frase de acesso. | Sim, se o ficheiro de chave privada está protegido por uma frase de acesso. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: Autenticação de parâmetros SshPublicKey utilizando privada chave conteúdo * *

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Para obter mais informações, consulte [conector SFTP](data-factory-sftp-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados SFTP, defina o **tipo** do conjunto de dados para **FileShare**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho de subchaves para a pasta. Utilize o caráter de escape ' \ ' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter pasta caminhos com base no setor início/fim tempos de data. |Sim |
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| fileFilter |Especifique um filtro para ser utilizado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (único caráter).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro de dados de séries de tempo. Por exemplo, folderPath parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência de binários. Verdadeiro para o modo binário e ASCII FALSO. Valor predefinido: True. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizados em simultâneo.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte [conector SFTP](data-factory-sftp-connector.md#dataset-properties) artigo. 

### <a name="file-system-source-in-copy-activity"></a>Origem de sistema de ficheiros na atividade de cópia
Se estiver a copiar dados de uma origem SFTP, defina o **tipo de origem** da atividade de cópia para **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| Recursiva |Indica se os dados é lida a recursivamente partir das pastas sub ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |



#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector SFTP](data-factory-sftp-connector.md#copy-activity-properties) artigo.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Serviço ligado
Para definir um HTTP de serviço ligado, defina o **tipo** do serviço ligado para **Http**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| URL | URL de base ao servidor Web | Sim |
| authenticationType | Especifica o tipo de autenticação. Valores permitidos são: **anónimo**, **básico**, **resumida**, **Windows**, **ClientCertificate**. <br><br> Consulte a secções abaixo desta tabela mais propriedades e exemplos JSON para os tipos de autenticação, respetivamente. | Sim |
| enableServerCertificateValidation | Especifique se pretende ativar a validação do certificado SSL de servidor, se a origem é o servidor de Web de HTTPS | Não, a predefinição é verdadeiro |
| gatewayName | Nome do Data Management Gateway para ligar a uma origem HTTP no local. | Sim, se copiar dados a partir de uma origem HTTP no local. |
| encryptedCredential | Credencial encriptada para aceder ao ponto final HTTP. Gerado automaticamente quando configura as informações de autenticação no Assistente para copiar ou a caixa de diálogo de pop-up de ClickOnce. | Não. Aplicam-se apenas ao copiar dados a partir de um servidor HTTP no local. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemplo: Utilizar a autenticação básica, resumida ou do Windows
Definir `authenticationType` como `Basic`, `Digest`, ou `Windows`e especifique as seguintes propriedades além do conector HTTP aqueles genéricos introduzidas acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Nome de utilizador para aceder ao ponto final HTTP. | Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Exemplo: Utilizar a autenticação de ClientCertificate

Para utilizar autenticação básica, defina `authenticationType` como `ClientCertificate`e especifique as seguintes propriedades além do conector HTTP aqueles genéricos introduzidas acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| embeddedCertData | O conteúdo dos dados binários do ficheiro Personal Information (Exchange PFX) com codificação Base64. | Especifique o `embeddedCertData` ou `certThumbprint`. |
| CertThumbprint | O thumbprint do certificado que foi instalado no arquivo de certificados do computador do gateway. Aplicam-se apenas ao copiar dados a partir de uma origem HTTP no local. | Especifique o `embeddedCertData` ou `certThumbprint`. |
| palavra-passe | Palavra-passe associado ao certificado. | Não |

Se utilizar `certThumbprint` para autenticação e o certificado está instalado no arquivo pessoal do computador local, tem de conceder a permissão de leitura para o serviço de gateway:

1. Inicie a consola de gestão da Microsoft (MMC). Adicionar o **certificados** snap-in que tenha como destino o **computador Local**.
2. Expanda **certificados**, **pessoais**e clique em **certificados**.
3. O certificado do arquivo pessoal com o botão direito e selecione **todas as tarefas**->**gerir chaves privadas...**
3. No **segurança** separador, adicione a conta de utilizador na qual o serviço de anfitrião do Data Management Gateway está em execução com o acesso de leitura para o certificado.  

**Exemplo: utilizar o certificado de cliente:** este serviço ligado liga a fábrica de dados para um servidor de web HTTP no local. Utiliza um certificado de cliente é instalado na máquina com o Data Management Gateway instalado.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exemplo: utilizar o certificado de cliente num ficheiro
Este serviço ligado liga a fábrica de dados para um servidor de web HTTP no local. Utiliza um ficheiro de certificado de cliente na máquina com o Data Management Gateway instalado.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Para obter mais informações, consulte [conetor HTTP](data-factory-http-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados HTTP, defina o **tipo** do conjunto de dados para **Http**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| relativeUrl | Um URL relativo para o recurso que contém os dados. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição de serviço ligado. <br><br> Para construir o URL dinâmico, pode utilizar [funções de Data Factory e variáveis do sistema](data-factory-functions-variables.md), exemplo: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Não |
| requestMethod | Método de HTTP. Valores permitidos são **obter** ou **POST**. | Não. Predefinição é `GET`. |
| additionalHeaders | Cabeçalhos de pedido HTTP adicionais. | Não |
| RequestBody | Corpo do pedido de HTTP. | Não |
| formato | Se pretender simplesmente **obter os dados do ponto final de HTTP como-é** sem análise-lo, ignore este definições do formato. <br><br> Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formato](data-factory-supported-file-and-compression-formats.md#orc-format), e [Parquet formato](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e compressão no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example-using-the-get-default-method"></a>Exemplo: utilizando o método GET (predefinição)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Exemplo: utilizando o método POST

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Para obter mais informações, consulte [conetor HTTP](data-factory-http-connector.md#dataset-properties) artigo.

### <a name="http-source-in-copy-activity"></a>Origem de HTTP na atividade de cópia
Se estiver a copiar dados de uma origem HTTP, defina o **tipo de origem** da atividade de cópia para **HttpSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (TimeSpan) para o pedido HTTP obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite de ler os dados de resposta. | Não. Valor predefinido: 00:01:40 |


#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
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
        }]
    }
}
```

Para obter mais informações, consulte [conetor HTTP](data-factory-http-connector.md#copy-activity-properties) artigo.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Serviço ligado
Para definir um OData serviço ligado, defina o **tipo** do serviço ligado para **OData**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| URL |URL do serviço OData. |Sim |
| authenticationType |Tipo de autenticação utilizado para ligar à origem de OData. <br/><br/> Para a nuvem OData, os valores possíveis são anónimo, básico e OAuth (tenha em atenção o suporte do Azure Data Factory atualmente, apenas do Azure Active Directory com base em OAuth). <br/><br/> De OData no local, os valores possíveis são anónimo, básico e Windows. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica. |Sim (apenas se estiver a utilizar autenticação básica) |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Sim (apenas se estiver a utilizar autenticação básica) |
| authorizedCredential |Se estiver a utilizar o OAuth, clique em **autorizar** botão no Assistente de cópia do Data Factory ou Editor e introduza as suas credenciais, o valor desta propriedade será gerado automaticamente. |Sim (apenas se estiver a utilizar autenticação OAuth) |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar ao serviço de OData no local. Especifique apenas se estiver a copiar dados a partir da origem de OData no local. |Não |

#### <a name="example---using-basic-authentication"></a>Exemplo - utilizar a autenticação básica
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Exemplo - utilizando a autenticação anónima

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exemplo - aceder do Windows utilizando a autenticação no local origem OData

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exemplo - através da autenticação do OAuth aceder à nuvem origem OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Para obter mais informações, consulte [OData conector](data-factory-odata-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de OData, defina o **tipo** do conjunto de dados para **ODataResource**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Caminho |Caminho para o recurso de OData |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
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

Para obter mais informações, consulte [OData conector](data-factory-odata-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de uma origem de OData, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Exemplo | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |"? $select = o nome, descrição & $top = 5" |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Para obter mais informações, consulte [OData conector](data-factory-odata-connector.md#copy-activity-properties) artigo.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Serviço ligado
Para definir um ODBC serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesOdbc**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |A parte de uma credencial de acesso não da cadeia de ligação e uma credencial encriptada opcional. Veja exemplos nas seguintes secções: |Sim |
| credencial |A parte de credencial de acesso da cadeia de ligação especificada no formato do valor da propriedade do controlador específico. Exemplo: "Uid =<user ID>; Pwd =<password>; RefreshToken =<secret refresh token>; ". |Não |
| authenticationType |Tipo de autenticação utilizado para ligar ao arquivo de dados ODBC. Os valores possíveis são: básico e anónimos. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar ao arquivo de dados ODBC. |Sim |

#### <a name="example---using-basic-authentication"></a>Exemplo - utilizar a autenticação básica

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Exemplo - através da autenticação básica com credenciais encriptado
Pode encriptar as credenciais utilizando o [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) cmdlet (1.0 versão do Azure PowerShell) ou [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 ou anterior a versão do Azure PowerShell).  

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilizar a autenticação anónima

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [conector ODBC](data-factory-odbc-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados ODBC, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no arquivo de dados ODBC. |Sim |


#### <a name="example"></a>Exemplo

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
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

Para obter mais informações, consulte [conector ODBC](data-factory-odbc-connector.md#dataset-properties) artigo. 

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de um arquivo de dados ODBC, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
``` 

Para obter mais informações, consulte [conector ODBC](data-factory-odbc-connector.md#copy-activity-properties) artigo.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Serviço ligado
Para definir um Salesforce serviço ligado, defina o **tipo** do serviço ligado para **Salesforce**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| environmentUrl | Especifique a instância do URL do Salesforce. <br><br> -Predefinição é "https://login.salesforce.com". <br> -Para copiar dados a partir de sandbox, especifique "https://test.salesforce.com". <br> -Para copiar dados de domínio personalizado, especificar, por exemplo, "https://[domain].my.salesforce.com". |Não |
| o nome de utilizador |Especifique um nome de utilizador para a conta de utilizador. |Sim |
| palavra-passe |Especifique uma palavra-passe da conta de utilizador. |Sim |
| securityToken |Especifique um token de segurança para a conta de utilizador. Consulte [obtenha o token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como repor/obter um token de segurança. Para saber mais sobre os tokens de segurança em geral, consulte [segurança e a API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Para obter mais informações, consulte [conector do Salesforce](data-factory-salesforce-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Salesforce, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no Salesforce. |Não (se um **consulta** de **RelationalSource** especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
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

Para obter mais informações, consulte [conector do Salesforce](data-factory-salesforce-connector.md#dataset-properties) artigo. 

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados do Salesforce, defina o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Uma consulta de SQL 92 ou [idioma de consulta de objeto do Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) consulta. Por exemplo: `select * from MyTable__c`. |Não (se o **tableName** do **dataset** especificado) |

#### <a name="example"></a>Exemplo  



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
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
        }]
    }
}
```

> [!IMPORTANT]
> A parte "__c" o nome da API é necessário para qualquer objeto personalizado.

Para obter mais informações, consulte [conector do Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) artigo. 

## <a name="web-data"></a>Dados da Web 

### <a name="linked-service"></a>Serviço ligado
Para definir uma Web do serviço ligado, defina o **tipo** do serviço ligado para **Web**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Url |URL para a origem da Web |Sim |
| authenticationType |Anónimo. |Sim |
 

#### <a name="example"></a>Exemplo


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Para obter mais informações, consulte [conector Web tabela](data-factory-web-table-connector.md#linked-service-properties) artigo. 

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Web, defina o **tipo** do conjunto de dados para **WebTable**e especifique as seguintes propriedades no **typeProperties** secção: 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |tipo do conjunto de dados. tem de ser definido como **WebTable** |Sim |
| Caminho |Um URL relativo para o recurso que contém a tabela. |Não. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição de serviço ligado. |
| Índice |O índice da tabela no recurso. Consulte [Get índice de uma tabela numa página HTML](#get-index-of-a-table-in-an-html-page) secção para obter passos para obter o índice de uma tabela numa página HTML. |Sim |

#### <a name="example"></a>Exemplo

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
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte [conector Web tabela](data-factory-web-table-connector.md#dataset-properties) artigo. 

### <a name="web-source-in-copy-activity"></a>Origem de Web de atividade de cópia
Se estiver a copiar dados de uma tabela de web, defina o **tipo de origem** da atividade de cópia para **WebSource**. Atualmente, quando a origem na atividade de cópia é do tipo **WebSource**, sem propriedades adicionais são suportadas.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }]
    }
}
```

Para obter mais informações, consulte [conector Web tabela](data-factory-web-table-connector.md#copy-activity-properties) artigo. 

## <a name="compute-environments"></a>AMBIENTES DE COMPUTAÇÃO
A tabela seguinte lista os ambientes de computação suportados pela fábrica de dados e as atividades de transformação que podem ser executadas nos mesmos. Clique na ligação para a capacidade de cálculo que está interessado para ver os esquemas JSON para o serviço ligado ao ligá-la a uma fábrica de dados. 

| Ambiente de computação | Atividades |
| --- | --- |
| [Cluster do HDInsight a pedido](#on-demand-azure-hdinsight-cluster) ou [seu próprio cluster do HDInsight](#existing-azure-hdinsight-cluster) |[Atividade personalizada do .NET](#net-custom-activity), [atividade do Hive](#hdinsight-hive-activity), [Pig atividade] (#-pig-atividade do hdinsight, [atividade de MapReduce](#hdinsight-mapreduce-activity), [atividade de transmissão em fluxo do Hadoop](#hdinsight-streaming-activityd), [ Atividade do Spark](#hdinsight-spark-activity) |
| [O Azure Batch](#azure-batch) |[Atividade personalizada do .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Machine Learning atividade de execução de lote](#machine-learning-batch-execution-activity), [Machine Learning atividade do recurso da atualização](#machine-learning-update-resource-activity) |
| [Análise do Azure Data Lake](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Base de dados SQL do Azure](#azure-sql-database-1), [do armazém de dados SQL do Azure](#azure-sql-data-warehouse-1), [do SQL Server](#sql-server-1) |[Procedimento Armazenado](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster de Azure HDInsight a pedido
O serviço do Azure Data Factory automaticamente pode criar um cluster do HDInsight baseado em Windows/Linux a pedido para processamento de dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName no JSON) associada com o cluster. Pode efetuar as seguintes atividades de transformação este serviço ligado: [atividade personalizada do .NET](#net-custom-activity), [atividade do Hive](#hdinsight-hive-activity), [Pig atividade] (#-pig-atividade do hdinsight, [atividade de MapReduce ](#hdinsight-mapreduce-activity), [Atividade de transmissão em fluxo do Hadoop](#hdinsight-streaming-activityd), [Spark atividade](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Serviço ligado 
A tabela seguinte fornece descrições para as propriedades utilizadas na definição JSON do Azure de um serviço de ligado de HDInsight a pedido.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como **HDInsightOnDemand**. |Sim |
| ClusterSize |Número de nós de trabalho/dados do cluster. O cluster do HDInsight é criado com 2 nós principais, juntamente com o número de nós de trabalho que especificar para esta propriedade. Os nós são de tamanho Standard_D3 tem 4 núcleos, pelo que um cluster de nós de 4 trabalho demora 24 núcleos (4\*4 = 16 núcleos para nós de trabalho plus 2\*4 = 8 núcleos de nós principais). Consulte [clusters do Hadoop baseados em criar Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes sobre a camada de Standard_D3. |Sim |
| TimeToLive |O tempo de inatividade permitido para o cluster do HDInsight a pedido. Especifica o tempo durante o qual o cluster do HDInsight a pedido permanece alive após a conclusão de uma atividade executar se existirem outras tarefas ativas no cluster.<br/><br/>Por exemplo, se executar uma atividade demora 6 minutos e timetolive está definido para 5 minutos, o cluster permanece ativo durante 5 minutos após a execução de 6 minutos de atividade de processamento. Se executar de outra atividade é executada com a janela de 6 minutos, é processada pelo mesmo cluster.<br/><br/>Criar um cluster do HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo), por isso, utilize esta definição como necessários para melhorar o desempenho de uma fábrica de dados através da reutilização de um cluster do HDInsight a pedido.<br/><br/>Se definido pelo valor timetolive como 0, o cluster é eliminado logo que a atividade ser executada num processados. Por outro lado, se definir um valor elevado, o cluster pode permanecer inativo, desnecessariamente, resultando em custos elevados. Por conseguinte, é importante que defina o valor adequado com base nas suas necessidades.<br/><br/>Vários pipelines podem partilhar a mesma instância de cluster do HDInsight a pedido se o valor da propriedade timetolive está corretamente definido |Sim |
| Versão |Versão do cluster do HDInsight. Para obter mais informações, consulte [suportado as versões do HDInsight no Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Não |
| linkedServiceName |Serviço ligado do Storage do Azure a ser utilizado pelo cluster a pedido para armazenar e processar dados. <p>Atualmente, não é possível criar um cluster do HDInsight a pedido que utiliza um Azure Data Lake Store, como o armazenamento. Se pretende armazenar os dados de resultado do HDInsight em processamento no Azure Data Lake Store, utilize uma atividade de cópia para copiar os dados de armazenamento de Blobs do Azure para o Azure Data Lake Store.</p>  | Sim |
| additionalLinkedServiceNames |Especifica a contas de armazenamento adicional para o HDInsight serviço ligado para que o serviço fábrica de dados pode registá-los em seu nome. |Não |
| osType |Tipo de sistema operativo. Valores permitidos são: (predefinição) do Windows e Linux |Não |
| hcatalogLinkedServiceName |O nome do SQL do Azure ligado serviço que apontam para a base de dados do HCatalog. O cluster do HDInsight a pedido é criado utilizando a base de dados SQL do Azure como o metastore. |Não |

### <a name="json-example"></a>Exemplo JSON
O JSON seguinte define um serviço ligado do HDInsight baseado em Linux da pedido. O serviço Data Factory cria automaticamente um **baseado em Linux** cluster do HDInsight ao processar um setor de dados. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Para obter mais informações, consulte [serviços ligados de computação](data-factory-compute-linked-services.md) artigo. 

## <a name="existing-azure-hdinsight-cluster"></a>Cluster do Azure HDInsight existente
Pode criar um serviço ligado do Azure HDInsight para registar o seu próprio cluster do HDInsight com o Data Factory. Pode executar as seguintes atividades de transformação de dados neste serviço ligado: [atividade personalizada do .NET](#net-custom-activity), [atividade do Hive](#hdinsight-hive-activity), [Pig atividade] (#-pig-atividade do hdinsight, [MapReduce atividade](#hdinsight-mapreduce-activity), [atividade de transmissão em fluxo do Hadoop](#hdinsight-streaming-activityd), [Spark atividade](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição JSON do Azure de um serviço ligado do Azure HDInsight.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como **HDInsight**. |Sim |
| clusterUri |O URI de cluster do HDInsight. |Sim |
| o nome de utilizador |Especifique o nome do utilizador a ser utilizado para ligar a um cluster do HDInsight existente. |Sim |
| palavra-passe |Especifique a palavra-passe da conta de utilizador. |Sim |
| linkedServiceName | Nome do serviço ligado do Storage do Azure que se refere ao blob storage do Azure utilizado pelo HDInsight cluster. <p>Atualmente, não é possível especificar o que serviço para esta propriedade de ligado um Azure Data Lake Store. Pode aceder a dados no Azure Data Lake Store de scripts Hive/Pig se o cluster do HDInsight tem acesso ao Data Lake Store. </p>  |Sim |

Para versões de clusters do HDInsight suportados, consulte [HDInsight versões](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). 

#### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Pode criar um serviço ligado do Azure Batch para registar um conjunto do Batch de máquinas virtuais (VMs) com uma fábrica de dados. Pode executar atividades personalizadas do .NET utilizando o Azure Batch ou para o Azure HDInsight. Pode executar um [atividade personalizada do .NET](#net-custom-activity) neste serviço ligado. 

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição JSON do Azure de um serviço ligado do Azure Batch.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como **AzureBatch**. |Sim |
| accountName |Nome da conta do Azure Batch. |Sim |
| accessKey |Chave de acesso para a conta do Azure Batch. |Sim |
| poolName |Nome do conjunto de máquinas virtuais. |Sim |
| linkedServiceName |Nome do Storage do Azure ligado serviço associado este serviço ligado do Azure Batch. Este serviço ligado é utilizado para os ficheiros de testes necessários para executar a atividade e armazenar os registos de execução da atividade. |Sim |


#### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Criar um serviço ligado do Azure Machine Learning para registar um ponto final com uma fábrica de dados de classificação de lote de Machine Learning. Atividades de transformação de dados de dois que podem ser executados no serviço ligado: [atividade de execução de lote do Machine Learning](#machine-learning-batch-execution-activity), [atividade do recurso de atualização do Machine Learning](#machine-learning-update-resource-activity). 

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição JSON do Azure de um serviço ligado do Azure Machine Learning.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Tipo |A propriedade de tipo deve ser definida como: **AzureML**. |Sim |
| mlEndpoint |O URL de classificação de lote. |Sim |
| ApiKey |API o modelo de área de trabalho publicado. |Sim |

#### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Criar um **Azure Data Lake Analytics** um Azure data factory do serviço de computação de serviço ligado para ligar um Azure Data Lake Analytics antes de utilizar o [atividade U-SQL do Data Lake Analytics](data-factory-usql-activity.md) num pipeline.

### <a name="linked-service"></a>Serviço ligado

A tabela seguinte fornece descrições para as propriedades utilizadas na definição do JSON de um serviço ligado do Azure Data Lake Analytics. 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Tipo |A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. |Sim |
| accountName |Nome de conta do Azure Data Lake Analytics. |Sim |
| dataLakeAnalyticsUri |URI do Azure Data Lake Analytics. |Não |
| Autorização |Código de autorização é obtido automaticamente depois de clicar em **autorizar** botão no Editor de fábrica de dados e concluir o início de sessão OAuth. |Sim |
| subscriptionId |Id de subscrição do Azure |Não (se não for especificado, a subscrição do data factory é utilizada). |
| resourceGroupName |Nome do grupo de recursos do Azure |Não (se não for especificado, grupo de recursos do data factory é utilizado). |
| ID de sessão |id de sessão da sessão de autorização do OAuth. Cada id de sessão é exclusivo e só pode ser utilizado uma vez. Quando utiliza o Editor do Data Factory, este ID é gerado automaticamente. |Sim |


#### <a name="json-example"></a>Exemplo JSON
O exemplo seguinte fornece definição JSON para um serviço ligado do Azure Data Lake Analytics.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Criar um serviço ligado SQL do Azure e utilizá-la com o [atividade de procedimento armazenado](#stored-procedure-activity) para invocar um procedimento armazenado de um pipeline do Data Factory. 

### <a name="linked-service"></a>Serviço ligado
Para definir uma base de dados do SQL do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureSqlDatabase**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especificar as informações necessárias para ligar à instância do SQL Database do Azure para a propriedade connectionString. |Sim |

#### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Consulte [conector do SQL Azure](data-factory-azure-sql-connector.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Criar um serviço ligado do Azure SQL Data Warehouse e utilizá-la com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. 

### <a name="linked-service"></a>Serviço ligado
Para definir um Azure SQL Data Warehouse serviço ligado, defina o **tipo** do serviço ligado para **AzureSqlDW**e especifique os seguintes propriedades no **typeProperties** secção:  

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especificar as informações necessárias para ligar à instância do armazém de dados SQL do Azure para a propriedade connectionString. |Sim |

#### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte [conector Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artigo. 

## <a name="sql-server"></a>SQL Server 
Criar um serviço ligado do SQL Server e utilizá-la com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. 

### <a name="linked-service"></a>Serviço ligado
Criar um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server no local a uma fábrica de dados. A tabela seguinte fornece uma descrição para os elementos JSON específicas do serviço do SQL Server ligado no local.

A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço ligado do SQL Server.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especificar as informações de connectionString necessárias para ligar à base de dados de SQL Server no local, utilizando a autenticação SQL ou autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar a autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |

Pode encriptar as credenciais utilizando o **New-AzureRmDataFactoryEncryptValue** cmdlet e utilizá-los na cadeia de ligação, conforme mostrado no exemplo seguinte (**EncryptedCredential** propriedade):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para utilizar a autenticação do SQL Server

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para utilizar a autenticação do Windows

Se o nome de utilizador e palavra-passe são especificadas, o gateway utiliza-los para representar a conta de utilizador especificada para ligar à base de dados do SQL Server no local. Caso contrário, o gateway se ligar ao SQL Server diretamente com o contexto de segurança do Gateway (a conta de arranque).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artigo.

## <a name="data-transformation-activities"></a>ATIVIDADES DE TRANSFORMAÇÃO DE DADOS

Atividade | Descrição
-------- | -----------
[Atividade do ramo de registo do HDInsight](#hdinsight-hive-activity) | A atividade do ramo de registo do HDInsight num pipeline Data Factory executa consultas do Hive no seu próprio ou cluster de HDInsight baseado em Windows/Linux a pedido. 
[Atividade do HDInsight Pig](#hdinsight-pig-activity) | A atividade do HDInsight Pig num pipeline Data Factory executa as consultas de Pig por si ou cluster de HDInsight baseado em Windows/Linux a pedido.
[Atividade MapReduce do HDInsight](#hdinsight-mapreduce-activity) | A atividade de HDInsight MapReduce no pipeline fábrica de dados executa programas de MapReduce na sua própria ou cluster de HDInsight baseado em Windows/Linux a pedido.
[Atividade Streaming do HDInsight](#hdinsight-streaming-activity) | A atividade de transmissão em fluxo do HDInsight num pipeline Data Factory executa programas de transmissão em fluxo do Hadoop na sua própria ou cluster de HDInsight baseado em Windows/Linux a pedido.
[Atividade do HDInsight Spark](#hdinsight-spark-activity) | A atividade do HDInsight Spark num pipeline Data Factory executa programas do Spark no seu próprio cluster do HDInsight. 
[Atividade de Execução em Lote do Machine Learning](#machine-learning-batch-execution-activity) | O Azure Data Factory permite-lhe facilmente criar pipelines que utilizam um serviço web do Azure Machine Learning publicado para Análise Preditiva. Utilizar a atividade de execução de lote um pipeline do Azure Data Factory, pode invocar um serviço web do Machine Learning para tornar as predições nos dados no batch. 
[Atividade de Recursos de Atualização de Machine Learning](#machine-learning-update-resource-activity) | Ao longo do tempo, os modelos preditivos no Machine Learning experimentações da classificação tem de ser retrained utilizando conjuntos de dados de entrada novo. Depois de terminar com reparametrização, que pretende atualizar o serviço web de classificação com o modelo de Machine Learning retrained. Pode utilizar a atividade de recursos de atualização para atualizar o serviço web com o modelo treinado recentemente.
[Atividade de Procedimento Armazenado](#stored-procedure-activity) | Pode utilizar a atividade de procedimento armazenado num pipeline Data Factory para invocar um procedimento armazenado dos arquivos de dados seguintes: SQL Database do Azure, Azure SQL Data Warehouse, base de dados do SQL Server na sua empresa ou uma VM do Azure. 
[Atividade do Data Lake U-SQL de análise](#data-lake-analytics-u-sql-activity) | Atividade de U-SQL de análise do Data Lake executa um script de U-SQL num cluster do Azure Data Lake Analytics.  
[Atividade personalizada do .NET](#net-custom-activity) | Se precisar de transformar dados de uma forma que não é suportada pela fábrica de dados, pode criar uma atividade personalizada com a suas próprias lógica de processamento de dados e utilize a atividade no pipeline. Pode configurar a atividade personalizada do .NET a serem executados utilizando um serviço Azure Batch ou um cluster do Azure HDInsight. 

     
## <a name="hdinsight-hive-activity"></a>Atividade Hive do HDInsight
Pode especificar as seguintes propriedades numa definição JSON de atividade do Hive. A propriedade de tipo para a atividade tem de ser: **HDInsightHive**. Tem de criar primeiro um serviço ligado do HDInsight e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade HDInsightHive:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Script |Especifique o inline de script de ramo de registo |Não |
| caminho do script |Guarde o script de ramo de registo num armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'scriptPath'. Não podem ser utilizados em conjunto. O nome de ficheiro é maiúsculas e minúsculas. |Não |
| Define |Especifique parâmetros como pares chave-valor para referenciar dentro do script de ramo de registo utilizando 'hiveconf' |Não |

Estas propriedades de tipo são específicas da atividade do ramo de registo. Outras propriedades (fora da secção de typeProperties) são suportadas para todas as atividades.   

### <a name="json-example"></a>Exemplo JSON
O JSON seguinte define uma atividade do ramo de registo do HDInsight num pipeline.  

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Para obter mais informações, consulte [atividade do ramo de registo](data-factory-hive-activity.md) artigo. 

## <a name="hdinsight-pig-activity"></a>Atividade Pig do HDInsight
Pode especificar as seguintes propriedades numa definição de Pig JSON de atividade. A propriedade de tipo para a atividade tem de ser: **HDInsightPig**. Tem de criar primeiro um serviço ligado do HDInsight e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para HDInsightPig: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Script |Especifique o inline de scripts Pig |Não |
| caminho do script |Armazene no script Pig no armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'scriptPath'. Não podem ser utilizados em conjunto. O nome de ficheiro é maiúsculas e minúsculas. |Não |
| Define |Especifique parâmetros como pares chave-valor para referenciar no Pig script |Não |

Estas propriedades de tipo são específicas da atividade de Pig. Outras propriedades (fora da secção de typeProperties) são suportadas para todas as atividades.   

### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Para obter mais informações, consulte [Pig atividade](#data-factory-pig-activity.md) artigo. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade MapReduce do HDInsight
Pode especificar as seguintes propriedades numa definição JSON de atividade de MapReduce. A propriedade de tipo para a atividade tem de ser: **HDInsightMapReduce**. Tem de criar primeiro um serviço ligado do HDInsight e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para HDInsightMapReduce: 

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| jarLinkedService | Nome do serviço ligado para o armazenamento do Azure que contém o ficheiro JAR. | Sim |
| jarFilePath | Caminho para o ficheiro JAR no armazenamento do Azure. | Sim | 
| className | Nome da classe principal no ficheiro JAR. | Sim | 
| Argumentos | Uma lista de argumentos separados por vírgulas para o programa de MapReduce. Em runtime, pode ver alguns argumentos adicionais (por exemplo: mapreduce.job.tags) do framework de MapReduce. Para diferenciar os argumentos com os argumentos de MapReduce, considere utilizar a opção e o valor como argumentos, conforme mostrado no exemplo seguinte (- s, - entrada, - saída etc., são opções seguidas imediatamente pelos respetivos valores) | Não | 

### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Para obter mais informações, consulte [atividade de MapReduce](data-factory-map-reduce.md) artigo. 

## <a name="hdinsight-streaming-activity"></a>Atividade Streaming do HDInsight
Pode especificar as seguintes propriedades numa definição JSON de atividade de transmissão em fluxo Hadoop. A propriedade de tipo para a atividade tem de ser: **HDInsightStreaming**. Tem de criar primeiro um serviço ligado do HDInsight e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para HDInsightStreaming: 

| Propriedade | Descrição | 
| --- | --- |
| Mapeador de | Nome do mapeador executável. No exemplo, cat.exe é o mapeador de executável.| 
| reducer | Nome do reducer executável. No exemplo, wc.exe é reducer executável. | 
| Entrada | Ficheiro de entrada (incluindo a localização) para o mapeador de pontos. No exemplo: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample é o contentor de blob, dados/exemplo/Gutenberg é a pasta, não sendo davinci.txt o blob. |
| Saída | Ficheiro de saída (incluindo a localização) para o reducer. O resultado da tarefa de transmissão em fluxo do Hadoop é escrito para a localização especificada para esta propriedade. |
| filePaths | Caminhos para o mapeador de pontos e reducer executáveis. No exemplo: "adfsample/example/apps/wc.exe" adfsample é o contentor de blob, / aplicações de exemplo é a pasta e wc.exe for o executável. | 
| fileLinkedService | Serviço ligado do Storage do Azure que representa o armazenamento do Azure que contém os ficheiros especificados na secção filePaths. | 
| Argumentos | Uma lista de argumentos separados por vírgulas para o programa de MapReduce. Em runtime, pode ver alguns argumentos adicionais (por exemplo: mapreduce.job.tags) do framework de MapReduce. Para diferenciar os argumentos com os argumentos de MapReduce, considere utilizar a opção e o valor como argumentos, conforme mostrado no exemplo seguinte (- s, - entrada, - saída etc., são opções seguidas imediatamente pelos respetivos valores) | 
| getDebugInfo | Um elemento opcional. Quando for definida para falha, os registos são transferidos só em caso de falha. Quando estiver definido como todos, os registos serão sempre transferidos independentemente do Estado de execução. | 

> [!NOTE]
> Tem de especificar um conjunto de dados de saída para a atividade de transmissão em fluxo do Hadoop para o **produz** propriedade. Este conjunto de dados pode ser apenas um dataset fictício que é necessário para a unidade a agenda de pipeline (hora a hora, diária, etc..). Se a atividade não tem uma entrada, pode ignorar a especificação de um conjunto de dados de entrada para a atividade para o **entradas** propriedade.  

## <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Para obter mais informações, consulte [atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md) artigo. 

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
Pode especificar as seguintes propriedades numa definição JSON de atividade do Spark. A propriedade de tipo para a atividade tem de ser: **HDInsightSpark**. Tem de criar primeiro um serviço ligado do HDInsight e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para HDInsightSpark: 

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| rootPath | O contentor de Blob do Azure e a pasta que contém o ficheiro de Spark. O nome de ficheiro é maiúsculas e minúsculas. | Sim |
| entryFilePath | Caminho relativo para a pasta raiz do Spark/pacote do código. | Sim |
| className | Classe principal de Java/Spark da aplicação | Não | 
| Argumentos | Uma lista de argumentos da linha de comandos para o programa de Spark. | Não | 
| proxyUser | A conta de utilizador para representar a execução do programa de Spark | Não | 
| sparkConfig | Propriedades de configuração de Spark. | Não | 
| getDebugInfo | Especifica se os ficheiros de registo do Spark são copiados para o armazenamento do Azure utilizado pelo cluster do HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: None, sempre ou falha. Valor predefinido: nenhuma. | Não | 
| sparkJobLinkedService | Storage do Azure ligado serviço que detém o Spark registos, dependências e ficheiro de tarefa.  Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster do HDInsight é utilizado. | Não |

### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Tenha em atenção os seguintes pontos: 

- O **tipo** propriedade está definida como **HDInsightSpark**.
- O **rootPath** está definido como **adfspark\\pyFiles** onde adfspark é o contentor de Blob do Azure e pyFiles é bem pasta no contentor. Neste exemplo, armazenamento de Blobs do Azure é o que está associado um cluster do Spark. Pode carregar o ficheiro para um armazenamento do Azure diferente. Se o fizer, crie um serviço ligado do Storage do Azure para ligar essa conta de armazenamento à fábrica de dados. Em seguida, especifique o nome do serviço ligado como um valor para o **sparkJobLinkedService** propriedade. Consulte [propriedades da atividade de Spark](#spark-activity-properties) para obter detalhes sobre esta propriedade e outras propriedades suportadas pela atividade de Spark.
- O **entryFilePath** está definido como o **test.py**, que é o ficheiro de python. 
- O **getDebugInfo** propriedade está definida como **sempre**, que significa que os ficheiros de registo são sempre gerada (êxito ou falha).  

    > [!IMPORTANT]
    > Recomendamos que não defina esta propriedade para sempre num ambiente de produção, a menos que esteja a resolver um problema. 
- O **produz** secção tem um conjunto de dados de saída. Tem de especificar um conjunto de dados de saída, mesmo que o programa de spark não produzir qualquer saída. O conjunto de dados de saída pauta a agenda para o pipeline (hora a hora, diária, etc..).

Para obter mais informações sobre a atividade, consulte [Spark atividade](data-factory-spark.md) artigo.  

## <a name="machine-learning-batch-execution-activity"></a>Atividade de Execução em Lote do Machine Learning
Pode especificar as seguintes propriedades numa definição JSON de atividade do execução de lote do Azure ML. A propriedade de tipo para a atividade tem de ser: **AzureMLBatchExecution**. Tem de criar do Azure Machine Learning o serviço ligado pela primeira vez e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para AzureMLBatchExecution:

Propriedade | Descrição | Necessário 
-------- | ----------- | --------
webServiceInput | O conjunto de dados a ser transmitido como entrada para o serviço web do Azure ML. Este conjunto de dados também deve ser incluído nas entradas para a atividade. |Utilize webServiceInput ou webServiceInputs. | 
webServiceInputs | Especifique a ser transmitidos como entradas para o serviço web do Azure ML conjuntos de dados. Se o serviço web tem várias entradas, utilize a propriedade de webServiceInputs em vez de utilizar a propriedade webServiceInput. Conjuntos de dados que são referenciados pelo **webServiceInputs** também têm de ser incluídos na atividade **entradas**. | Utilize webServiceInput ou webServiceInputs. | 
webServiceOutputs | Os conjuntos de dados que são atribuídos como saídas do serviço web do Azure ML. O serviço web devolve os dados de saída neste DataSet. | Sim | 
globalParameters | Especifique valores para os parâmetros de serviço web nesta secção. | Não | 

### <a name="json-example"></a>Exemplo JSON
Neste exemplo, a atividade tem o conjunto de dados **MLSqlInput** como entrada e **MLSqlOutput** como resultado. O **MLSqlInput** é transmitida como uma entrada para o serviço web utilizando o **webServiceInput** propriedade JSON. O **MLSqlOutput** é transmitida como um resultado para o serviço Web utilizando o **webServiceOutputs** propriedade JSON. 

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }              
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

No exemplo JSON, o serviço Web do Azure Machine Learning implementado utiliza um leitor e de um módulo de escritor de dados de/para uma base de dados do SQL do Azure de leitura/escrita. Este serviço Web expõe os seguintes quatro parâmetros: nome do servidor, nome de base de dados, nome de conta de utilizador do servidor e palavra-passe de conta de utilizador servidor de base de dados.

> [!NOTE]
> Apenas entradas e saídas da atividade AzureMLBatchExecution podem ser transmitidas como parâmetros para o serviço Web. Por exemplo, no fragmento JSON acima, MLSqlInput for uma entrada para a atividade AzureMLBatchExecution, que é transmitida como uma entrada para o serviço Web através do parâmetro webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Atividade de Recursos de Atualização de Machine Learning
Pode especificar as seguintes propriedades numa definição JSON de atividade de recursos do Azure ML atualização. A propriedade de tipo para a atividade tem de ser: **AzureMLUpdateResource**. Tem de criar do Azure Machine Learning o serviço ligado pela primeira vez e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para AzureMLUpdateResource:

Propriedade | Descrição | Necessário 
-------- | ----------- | --------
Trainedmodeldatasetname | Nome do modelo de retrained. | Sim |  
trainedModelDatasetName | O conjunto de dados que apontam para o ficheiro iLearner devolvido pela operação de reparametrização. | Sim | 

### <a name="json-example"></a>Exemplo JSON
O pipeline com duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução de lote do Azure ML aceita como entrada os dados de preparação e produz um ficheiro iLearner como resultado. A atividade invoca o serviço web de formação (experimentação de preparação exposta como um serviço web) com os dados de entrada de formação e recebe o ficheiro ilearner a partir do webservice. O placeholderBlob é apenas um saída fictício conjunto de dados que é necessário para o serviço do Azure Data Factory para executar o pipeline.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
Pode especificar as seguintes propriedades numa definição JSON de atividade U-SQL. A propriedade de tipo para a atividade tem de ser: **DataLakeAnalyticsU SQL**. Tem de criar um serviço ligado do Azure Data Lake Analytics e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para DataLakeAnalyticsU-SQL: 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| ScriptPath |Caminho para a pasta que contém o script U-SQL. Nome do ficheiro é maiúsculas e minúsculas. |Não (se for utilizar o script) |
| scriptLinkedService |Serviço ligado que liga o armazenamento que contém o script para a fábrica de dados |Não (se for utilizar o script) |
| Script |Especifique o script inline de em vez de especificar scriptPath e scriptLinkedService. Por exemplo: ". o script": "Test criar base de dados". |Não (se for utilizar scriptPath e scriptLinkedService) |
| degreeOfParallelism |O número máximo de nós em simultâneo utilizada para executar a tarefa. |Não |
| prioridade |Determina quais os que são colocados em fila trabalhos devem ser selecionados para ser executado primeiro. Menor número, maior prioridade. |Não |
| parâmetros |Parâmetros para o script U-SQL |Não |

### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte [atividade de U-SQL de análise do Data Lake](data-factory-usql-activity.md). 

## <a name="stored-procedure-activity"></a>Atividade de Procedimento Armazenado
Pode especificar as seguintes propriedades numa definição JSON de atividade de procedimento armazenado. A propriedade de tipo para a atividade tem de ser: **SqlServerStoredProcedure**. Tem de criar um dos seguintes serviços ligados e especificar o nome do serviço ligado como um valor para o **linkedServiceName** propriedade:

- SQL Server 
- Base de Dados SQL do Azure
- Azure SQL Data Warehouse

São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para SqlServerStoredProcedure:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| storedProcedureName |Especifique o nome do procedimento armazenado na base de dados SQL do Azure ou do Azure SQL Data Warehouse que é representada pelo serviço ligado que utilize a tabela de saída. |Sim |
| storedProcedureParameters |Especifique os valores dos parâmetros de procedimento armazenado. Se tiver de passar nulo para um parâmetro, utilize a sintaxe: "param1": um valor nulo (todas as minúsculas). Consulte o exemplo seguinte para saber mais sobre como utilizar esta propriedade. |Não |

Se especificar um conjunto de dados de entrada, tem de estar disponível (no estado "Pronto") para a atividade de procedimento armazenado ser executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Só é utilizada para verificar a dependência antes de iniciar a atividade de procedimento armazenado. Tem de especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. 

Especifica o conjunto de dados de saída a **agenda** para a atividade de procedimento armazenado (hora a hora, semanais, mensais, etc.). O conjunto de dados de saída tem de utilizar um **serviço ligado** que se refere a uma base de dados do SQL do Azure ou um Azure SQL Data Warehouse ou uma base de dados SQL Server na qual pretende que o procedimento armazenado para ser executada. O conjunto de dados de saída pode servir como uma forma de passar o resultado do procedimento armazenado para processamento por outra atividade subsequentes ([encadeamento atividades](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) no pipeline. No entanto, a fábrica de dados não automaticamente escrever a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve a uma tabela SQL que o conjunto de dados de saída aponta para. Em alguns casos, o conjunto de dados de saída pode ser um **dataset fictício**, que é utilizada apenas para especificar a agenda de execução da atividade de procedimento armazenado.  

### <a name="json-example"></a>Exemplo JSON

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) artigo. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Pode especificar as seguintes propriedades numa atividade personalizada do .NET definição JSON. A propriedade de tipo para a atividade tem de ser: **DotNetActivity**. Tem de criar um serviço ligado do Azure HDInsight ou um Batch do Azure ligada de serviço e especifique o nome do serviço ligado como um valor para o **linkedServiceName** propriedade. São suportadas as seguintes propriedades no **typeProperties** secção quando definir o tipo de atividade para DotNetActivity:
 
| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| AssemblyName | Nome da assemblagem. No exemplo, é: **MyDotnetActivity.dll**. | Sim |
| Ponto de entrada |Nome da classe que implementa a interface IDotNetActivity. No exemplo, é: **MyDotNetActivityNS.MyDotNetActivity** onde MyDotNetActivityNS é o espaço de nomes e MyDotNetActivity é a classe.  | Sim | 
| PackageLinkedService | Nome do serviço ligado do Storage do Azure que aponta para o armazenamento de blob que contém o ficheiro zip de atividade personalizado. No exemplo, é: **AzureStorageLinkedService**.| Sim |
| PackageFile | Nome do ficheiro zip. No exemplo, é: **customactivitycontainer/MyDotNetActivity.zip**. | Sim |
| ExtendedProperties | Propriedades expandidas, que pode definir e transmitir o código de .NET. Neste exemplo, o **SliceStart** variável é definida para um valor com base na variável de sistema SliceStart. | Não | 

### <a name="json-example"></a>Exemplo JSON

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Para obter informações detalhadas, consulte [utilizar atividades personalizadas no Data Factory](data-factory-use-custom-activities.md) artigo. 

## <a name="next-steps"></a>Passos Seguintes
Veja os tutoriais seguintes: 

- [Tutorial: criar um pipeline com uma atividade de cópia](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Tutorial: criar um pipeline com uma atividade do ramo de registo](data-factory-build-your-first-pipeline-using-editor.md)
