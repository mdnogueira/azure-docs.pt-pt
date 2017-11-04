---
title: "Agendamento e execução com o Data Factory | Microsoft Docs"
description: "Saiba aspetos de agendamento e execução do modelo de aplicação do Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: d1cd846418b0e65b978971526af7b918bd03a7a1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="data-factory-scheduling-and-execution"></a>Agendamento da fábrica de dados e execução
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [acionadores e da execução de pipeline](../concepts-pipeline-execution-triggers.md) artigo.

Este artigo explica os aspetos de agendamento e execução do modelo de aplicação do Azure Data Factory. Este artigo parte do princípio de que compreende Noções básicas dos conceitos de modelo de aplicação de fábrica de dados, incluindo atividades, pipelines, serviços ligados e conjuntos de dados. Para obter conceitos básicos do Azure Data Factory, consulte os artigos seguintes:

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Conjuntos de dados](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Horas de início e de fim do pipeline
Um pipeline está ativo apenas entre o **iniciar** tempo e **final** tempo. Não foi executada antes da data de início ou após a hora de fim. Se o pipeline está em pausa, não é executado independentemente da respetiva hora de início e de fim. Para um pipeline executar, que deve não ser pausado. Pode encontrar estas definições (início, fim, em pausa) na definição do pipeline: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Para obter mais informações consulte estas propriedades, [Criar pipelines](data-factory-create-pipelines.md) artigo. 


## <a name="specify-schedule-for-an-activity"></a>Especifique o agendamento para uma atividade
Não é o pipeline de que é executado. É as atividades no pipeline, que são executadas no contexto global do pipeline. Pode especificar uma agenda periódica para uma atividade utilizando a **programador** secção JSON da atividade. Por exemplo, pode agendar uma atividade seja executada hora a hora da seguinte forma:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Como é mostrado no diagrama seguinte, especificando que um agendamento para uma atividade cria uma série de em cascata windows com no início do pipeline e de fim. Em cascata windows são uma série de intervalos de tempo de contíguas, sem se sobreporem de tamanho fixo. Estas janelas em cascata lógica para uma atividade são denominadas **windows atividade**.

![Exemplo de agendador da atividade](media/data-factory-scheduling-and-execution/scheduler-example.png)

O **programador** propriedade de uma atividade é opcional. Se especificar esta propriedade, tem de corresponder a cadência que especificou na definição de conjunto de dados de saída para a atividade. Atualmente, é o conjunto de dados de saída que controla a agenda. Por conseguinte, tem de criar um conjunto de dados de saída, mesmo se a atividade não produzir qualquer saída. 

## <a name="specify-schedule-for-a-dataset"></a>Especifique o agendamento para um conjunto de dados
Uma atividade no pipeline fábrica de dados poderá demorar a entrada de zero ou mais **conjuntos de dados** e produzir uma ou mais conjuntos de dados de saída. Para uma atividade, pode especificar a cadência à qual os dados de entrada estão disponíveis ou os dados de saída são produzidos utilizando o **disponibilidade** secção nas definições de conjunto de dados. 

**Frequência** no **disponibilidade** secção especifica a unidade de tempo. Os valores permitidos para a frequência são: minuto, hora, dia, semana e mês. O **intervalo** propriedade na secção de disponibilidade Especifica um multiplicador para a frequência. Por exemplo: se a frequência é definida ao dia e o intervalo está definido para 1 para um conjunto de dados de saída, os dados de saída são produzidos diariamente. Se especificar a frequência como minuto, recomendamos que defina o intervalo para não inferior a 15. 

No exemplo seguinte, os dados de entrada estão disponíveis por hora e os dados de saída são produzidos por hora (`"frequency": "Hour", "interval": 1`). 

**Conjunto de dados de entrada:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Conjunto de dados de saída**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Atualmente, **conjunto de dados de saída pauta a agenda**. Por outras palavras, a agenda especificada para o conjunto de dados de saída é utilizada para executar uma atividade no tempo de execução. Por conseguinte, tem de criar um conjunto de dados de saída, mesmo se a atividade não produzir qualquer saída. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. 

Na definição do pipeline seguinte, o **programador** propriedade é utilizada para especificar a agenda para a atividade. Esta propriedade é opcional. Atualmente, a agenda para a atividade tem de corresponder à agenda especificada para o conjunto de dados de saída.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

Neste exemplo, a atividade é executada hora a hora entre os tempos de início e de fim do pipeline. Os dados de saída são produzidos hora a hora para o windows de três horas (8 AM - 09 AM, 09: 00 - 10: 00 e 10 AM - 11 AM). 

Cada unidade de dados consumidos ou produzidos por uma atividade executar é chamada um **setor de dados**. O diagrama seguinte mostra um exemplo de uma atividade com um conjunto de dados de entrada e um conjunto de dados de saída: 

![Programador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

O diagrama mostra os setores de dados por hora para o conjunto de dados de entrada e de saída. O diagrama mostra os três setores de entrada que estão prontos para processamento. A atividade de 10 11 AM está em curso, que produz o setor de saída AM 10 11. 

Pode aceder ao intervalo de tempo associado com o setor atual no conjunto de dados JSON utilizando variáveis: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Da mesma forma, pode aceder a associados uma janela de atividade ao utilizar o WindowStart e WindowEnd o intervalo de tempo. O agendamento de uma atividade tem de corresponder à agenda do conjunto de dados de saída para a atividade. Por conseguinte, os valores do SliceStart e SliceEnd são os mesmos que os valores WindowStart e WindowEnd respetivamente. Para obter mais informações sobre estas variáveis, consulte [funções de Data Factory e variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables) artigos.  

Pode utilizar estas variáveis para diferentes fins o JSON da atividade. Por exemplo, pode utilizá-los para selecionar dados de entrada e de saída conjuntos de dados que representam dados de séries de tempo (por exemplo: 8 AM para 09: 00). Este exemplo utiliza também **WindowStart** e **WindowEnd** para selecionar relevantes para uma atividade execução e copie-a para um blob com o adequado **folderPath**. O **folderPath** parametrizadas ter uma pasta separada para cada hora.  

No exemplo anterior, a agenda especificada para a entrada e conjuntos de dados de saída é a mesma (hora a hora). Se o conjunto de dados de entrada para a atividade estiver disponível uma frequência diferente, diga a cada 15 minutos, a atividade que produz este conjunto de dados de saída ainda é executado uma vez a uma hora como o conjunto de dados de saída é o que pauta a agenda de atividade. Para obter mais informações, consulte [conjuntos de dados com diferentes frequências de modelo](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Disponibilidade do conjunto de dados e políticas
Constatou a utilização de frequência e propriedades de intervalo na secção de disponibilidade da definição do conjunto de dados. Existem alguns outras propriedades que afetam o agendamento e execução de uma atividade. 

### <a name="dataset-availability"></a>Disponibilidade do conjunto de dados 
A tabela seguinte descreve as propriedades que pode utilizar o **disponibilidade** secção:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para produção de setor de conjunto de dados.<br/><br/><b>Suportado frequência</b>: minuto, hora, dia, semana, mês |Sim |ND |
| intervalo |Especifica um multiplicador para a frequência<br/><br/>"Intervalo de frequência x" determina com que frequência o setor é produzido.<br/><br/>Se precisar do conjunto de dados segmentados numa base horária, defina <b>frequência</b> para <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/><b>Tenha em atenção</b>: Se especificar a frequência como minuto, recomendamos que defina o intervalo para não inferior a 15 |Sim |ND |
| Estilo |Especifica se deve ser produzido do setor no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência é definida para o mês e estilo está definido como EndOfInterval, o setor é produzido no último dia do mês. Se o estilo for definido como StartOfInterval, o setor é produzido no primeiro dia do mês.<br/><br/>Se a frequência é definida para o dia e estilo está definido como EndOfInterval, o setor é produzido na última hora do dia.<br/><br/>Se a frequência é definida para a hora e estilo está definido como EndOfInterval, o setor é produzido no fim da hora. Por exemplo, para um setor para 1 PM – 2 PM período, o setor é produzido em 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição na hora utilizado pelo programador para limites de setor de conjunto de dados de computação absoluta. <br/><br/><b>Tenha em atenção</b>: se o AnchorDateTime tem partes de data que são mais granulares do que a frequência, em seguida, as partes mais granulares são ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> é <b>hora a hora</b> (frequência: horas e intervalo: 1) e o <b>AnchorDateTime</b> contém <b>minutos e segundos</b>, em seguida, a <b>minutos e segundos</b> partes do AnchorDateTime são ignoradas. |Não |01/01/0001 |
| deslocamento |TimeSpan através do qual são desviados o início e de fim de todos os setores de conjunto de dados. <br/><br/><b>Tenha em atenção</b>: se forem especificadas anchorDateTime e desvio, o resultado é o shift combinada. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por predefinição, diariamente (`"frequency": "Day", "interval": 1`) setores iniciar no momento da UTC 12: 00 (meia-noite). Se pretender que a hora de início, a hora UTC de 6: 00 em vez disso, defina o deslocamento conforme mostrado no seguinte fragmento: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>exemplo de anchorDateTime
No exemplo seguinte, o conjunto de dados é produzido cada 23 horas. Primeiro setor é iniciada no momento especificado pelo anchorDateTime, que está definido para `2017-04-19T08:00:00` (hora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>o desvio/estilo de exemplo
O seguinte conjunto de dados é um conjunto de dados mensal e é produzido no 3rd de cada mês às 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Política de conjunto de dados
Um conjunto de dados pode ter uma política de validação definida que especifica a forma como os dados gerados por uma execução de setor podem ser validados antes de está pronto para consumo. Nestes casos, depois do setor concluiu a execução, o estado do setor de saída é alterado para **a aguardar** com um subestado de **validação**. Depois dos setores serem validados, o estado do setor é alterado para **pronto**. Se um setor de dados tem foram produzido, mas não foi aprovado na validação do, atividade executa a jusante setores que dependem deste setor não é processadas. [Monitorizar e gerir pipelines](data-factory-monitor-manage-pipelines.md) abrange vários Estados de setores de dados no Data Factory.

O **política** secção na definição do conjunto de dados define os critérios ou a condição que os setores de conjunto de dados tem de cumprir. A tabela seguinte descreve as propriedades que pode utilizar o **política** secção:

| Nome da política | Descrição | Aplicar a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Valida que os dados de um **BLOBs do Azure** cumpre os requisitos de tamanho mínimo (em megabytes). |Blob do Azure |Não |ND |
| minimumRows | Valida que os dados de um **SQL database do Azure** ou um **tabela do Azure** contém o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

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

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Para obter mais informações sobre estas propriedades e exemplos, consulte [criar conjuntos de dados](data-factory-create-datasets.md) artigo. 

## <a name="activity-policies"></a>Políticas de atividade
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

Para obter mais informações, consulte [Pipelines](data-factory-create-pipelines.md) artigo. 

## <a name="parallel-processing-of-data-slices"></a>Processamento paralelo dos setores de dados
Pode definir a data de início para o pipeline no passado. Quando o fizer, fábrica de dados é automaticamente calcula (preenche back) todos os setores de dados no passado e inicia o processamento-los. Por exemplo: Se criar um pipeline com a data de início de 2017-04-01 e a data atual for 2017-04-10. Se a cadência do conjunto de dados de saída é diariamente, em seguida, inicia Data Factory processar todos os setores de 2017-04-01 para 2017-04-09 imediatamente porque a data de início é no passado. O setor de 2017-04-10 não foi processado ainda porque o valor da propriedade de estilo na secção de disponibilidade é EndOfInterval por predefinição. O mais antigo setor é processado pela primeira vez como predefinição, o valor de executionPriorityOrder é OldestFirst. Para obter uma descrição da propriedade de estilo, consulte [disponibilidade do conjunto de dados](#dataset-availability) secção. Para obter uma descrição da secção executionPriorityOrder, consulte o [as políticas de atividade](#activity-policies) secção. 

Pode configurar setores de dados de back-preenchida a serem processados em paralelo, definindo o **simultaneidade** propriedade no **política** secção do JSON de atividade. Esta propriedade determina o número de execuções de actividade paralela que pode acontecer em diferentes setores. O valor predefinido para a propriedade de concorrência é 1. Por conseguinte, um setor é processado simultaneamente por predefinição. O valor máximo é 10. Quando um pipeline tem de passar por um grande conjunto de dados disponíveis, que tenham um valor de concorrência maior acelera o processamento de dados. 

## <a name="rerun-a-failed-data-slice"></a>Voltar a executar um setor de dados com falhas
Quando ocorre um erro ao processar um setor de dados, pode saber por que motivo o processamento de um setor falhou ao utilizar os painéis do portal do Azure ou monitorizar e gerir aplicações. Consulte [monitorização e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md) ou [aplicação de monitorização e gestão](data-factory-monitor-manage-app.md) para obter mais detalhes.

Considere o exemplo seguinte, que mostra duas atividades. Activity1 e a atividade 2. Activity1 consome um setor de Dataset1 e produz um setor de Dataset2, que é utilizada como uma entrada por Activity2 para produzir um setor do último conjunto de dados.

![Falha de setor](./media/data-factory-scheduling-and-execution/failed-slice.png)

O diagrama mostra que fora três ciclos recentes, Ocorreu uma falha que produz o setor de 9 10 AM para Dataset2. Fábrica de dados controla automaticamente a dependência para o conjunto de dados de séries de tempo. Como resultado, não iniciar a execução para o setor a jusante 09-10: 00 da atividade.

Ferramentas de gestão e monitorização de fábrica de dados permitem-lhe explorar os registos de diagnóstico para o setor falhou facilmente descobrir a causa de raiz para o problema e corrigi-la. Depois de corrigir o problema, pode facilmente começar a atividade executar para produzir o setor falhou. Para obter mais informações sobre como executar novamente e compreender transições de Estados para setores de dados, consulte [monitorização e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md) ou [aplicação de monitorização e gestão](data-factory-monitor-manage-app.md).

Depois de voltar a executar o setor de 9 10 AM para **Dataset2**, Data Factory inicia a executar o setor de dependentes 9 10 AM no conjunto de dados final.

![Volte a executar setor de falha](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Múltiplas atividades num pipeline
Pode ter mais de uma atividade num pipeline. Se tiver várias atividades num pipeline e o resultado de uma atividade não é uma entrada de outra atividade, as atividades poderão ser executadas em paralelo se setores de dados de entrada para as atividades estão prontos.

Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. As atividades podem ser na mesmo pipeline ou em diferentes pipelines. A segunda atividade executa apenas quando primeiro for concluído com êxito.

Por exemplo, considere o seguinte cenário onde um pipeline com duas atividades:

1. A1 atividade que requer o conjunto de entrada externo dados D1 e produz o conjunto de dados D2 de saída.
2. A2 atividade que requer intervenção do conjunto de dados D2 e produz o conjunto de dados D3 de saída.

Neste cenário, as atividades A1 e A2 estão no mesmo pipeline. A atividade é executada A1 quando os dados externos estão disponíveis e a frequência de disponibilidade agendada for atingida. A atividade é executada A2 quando os setores agendados de D2 fiquem disponíveis e a frequência de disponibilidade agendada for atingida. Se existir um erro dos setores no conjunto de dados D2, A2 não é executado para esse setor até ficarem disponível.

A vista de diagrama com ambas as atividades no pipeline mesmo teria aspeto o diagrama a seguir:

![Encadeamento de atividades no pipeline mesmo](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Conforme mencionado anteriormente, as atividades dever-se em diferentes pipelines. Neste cenário, a vista de diagrama teria aspeto o diagrama a seguir:

![Encadeamento de atividades em duas pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Consulte o [copiar sequencialmente](#copy-sequentially) secção no anexo para obter um exemplo.

## <a name="model-datasets-with-different-frequencies"></a>Conjuntos de dados de modelo com frequências diferentes
Nos exemplos, as frequências para conjuntos de dados de entrada e de saída e a janela de agendamento de atividade foram o mesmo. Alguns cenários necessitam da capacidade para produzir resultados de uma frequência diferente as frequências de uma ou mais entradas. Fábrica de dados suporta modelação destes cenários.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exemplo 1: Produzir um relatório de saída diária para dados de entrada que está disponíveis a cada hora
Considere um cenário no qual ter introduzido dados de medição de sensores disponíveis a cada hora no Blob storage do Azure. Pretende para produzir um relatório agregado diário com estatísticas como a média, máximo e mínimo para o dia com [atividade do ramo de registo de fábrica de dados](data-factory-hive-activity.md).

Eis como pode modelo neste cenário com o Data Factory:

**Conjunto de dados de entrada**

Os ficheiros de entrada por hora são ignorados na pasta para o dia especificado. Disponibilidade para a entrada é definida no **hora** (frequência: hora, intervalo: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Conjunto de dados de saída**

Um ficheiro de saída é criado diariamente na pasta do dia. Disponibilidade de saída está definida no **dia** (frequência: dia e intervalo: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Atividade: atividade do ramo de registo num pipeline**

O script de ramo de registo recebe apropriados *DateTime* informações como parâmetros que utilizam o **WindowStart** variável, conforme mostrado no seguinte fragmento. O script de ramo de registo utiliza esta variável para carregar os dados a partir da pasta correta para o dia e executar a agregação para gerar a saída.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
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
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

O diagrama seguinte mostra o cenário de um ponto de vista da dependência de dados.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

O setor de saída para todos os dias depende 24 setores de hora a hora de um conjunto de dados de entrada. Fábrica de dados calcula automaticamente estas dependências por perceber os dados de entrada setores que se encontra no mesmo período de tempo como o setor de saída produzidos. Se qualquer um dos 24 setores de entrada não estiver disponível, o Data Factory aguarda o setor de entrada estar pronto antes de iniciar a execução da atividade diária.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exemplo 2: Especificar dependência com expressões e funções de Data Factory
Vejamos outro cenário. Suponha que tem uma atividade do ramo de registo que processa os dois conjuntos de dados de entrada. Um dos atributos tem novos dados diariamente, mas uma delas obtém dados novos todas as semanas. Suponha que quer fazer uma associação entre as duas entradas e produz uma saída todos os dias.

A abordagem simple que fábrica de dados automaticamente figuras terminar o direito de entrada do setores processar alinhando à saída de tempo de segmentação de dados período não funciona.

Tem de especificar o que cada atividade executada, a fábrica de dados deve utilizar segmentação de dados da última semana para o conjunto de dados de entrada semanal. Utilize as funções do Azure Data Factory conforme mostrado no seguinte fragmento para implementar este comportamento.

**Input1: Blob do Azure**

Primeira entrada é o blob do Azure que está a ser atualizado diariamente.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: Blob do Azure**

Input2 é o blob do Azure que está a ser atualizado semanalmente.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Saída: Blob do Azure**

Um ficheiro de saída é criado diariamente na pasta para o dia. Disponibilidade de saída está definida como **dia** (frequência: dia, intervalo: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Atividade: atividade do ramo de registo num pipeline**

A atividade do ramo de registo utiliza as duas entradas e produz um setor de saída todos os dias. Pode especificar o setor de saída de todos os dias para depender de setor de entrada a semana anterior para a entrada semanal da seguinte forma.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Consulte [funções de Data Factory e variáveis do sistema](data-factory-functions-variables.md) para uma lista de funções e variáveis do sistema que suporte a fábrica de dados.

## <a name="appendix"></a>Apêndice

### <a name="example-copy-sequentially"></a>Exemplo: copiar sequencialmente
É possível executar várias operações de cópia umas a seguir de forma sequenciais/ordenadas. Por exemplo, pode ter duas atividades de cópia no pipeline (CopyActivity1 e CopyActivity2) com os seguintes conjuntos de dados de saída de dados de entrada:   

CopyActivity1

Entrada: conjunto de dados. Saída: Dataset2.

CopyActivity2

Entrada: Dataset2.  Saída: Dataset3.

CopyActivity2 iria ser executado apenas se o CopyActivity1 foi executada com êxito e Dataset2 está disponível.

Eis o exemplo de pipeline JSON:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Tenha em atenção que, no exemplo, o conjunto de dados de saída da atividade de cópia primeiro (Dataset2) está especificado como entrada para a segunda atividade. Por conseguinte, a segunda atividade é executado apenas quando o conjunto de dados de saída da atividade primeiro está pronto.  

No exemplo, CopyActivity2 pode ter uma entrada diferentes, tais como Dataset3, mas especificou Dataset2 como uma entrada para CopyActivity2, pelo que a atividade não é executado até CopyActivity1 terminar. Por exemplo:

CopyActivity1

Entrada: Dataset1. Saída: Dataset2.

CopyActivity2

Entradas: Dataset3, Dataset2. Saída: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Tenha em atenção que, no exemplo, dois conjuntos de dados de entrada são especificados para a segunda atividade de cópia. Quando são especificadas várias entradas, apenas o primeiro entrado conjunto de dados é utilizado para copiar dados, mas outros conjuntos de dados são utilizados como dependências. CopyActivity2 seria começar apenas depois das seguintes condições são cumpridas:

* CopyActivity1 foi concluída com êxito e Dataset2 está disponível. Este conjunto de dados não é utilizado ao copiar dados para Dataset4. Só funciona como uma dependência de agendamento para CopyActivity2.   
* Dataset3 está disponível. Este conjunto de dados representa os dados que são copiados para o destino. 