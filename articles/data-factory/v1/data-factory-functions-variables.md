---
title: "Funções de fábrica de dados e variáveis do sistema | Microsoft Docs"
description: "Fornece uma lista de funções do Azure Data Factory e variáveis do sistema"
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3c506ee95281e1250a721a9c150bd839b4c1fcdb
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Fábrica de dados do Azure - as funções e variáveis do sistema
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [variáveis do sistema na versão 2 do Data Factory](../control-flow-system-variables.md).

Este artigo fornece informações sobre as funções e variáveis suportadas pelo Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variáveis de sistema de fábrica de dados
| Nome da variável | Descrição | Âmbito de objeto | Casos de utilização e o âmbito JSON |
| --- | --- | --- | --- |
| WindowStart |Início do intervalo de tempo para a janela de execução da atividade atual |Atividade |<ol><li>Especifique as consultas de seleção de dados. Consulte os artigos de conector referenciados no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo.</li> |
| WindowEnd |Fim do intervalo de tempo para a janela de execução da atividade atual |Atividade |mesmo que WindowStart. |
| SliceStart |Início do intervalo de tempo de segmentação de dados que está a ser produzido |Atividade<br/>Conjunto de dados |<ol><li>Especifique os caminhos de pasta dinâmica e nomes de ficheiros ao trabalhar com [Blob do Azure](data-factory-azure-blob-connector.md) e [conjuntos de dados do sistema de ficheiros](data-factory-onprem-file-system-connector.md).</li><li>Especificar dependências de entrada com as funções de fábrica de dados na coleção de entradas de atividade.</li></ol> |
| SliceEnd |Fim do intervalo de tempo de segmentação de dados atual. |Atividade<br/>Conjunto de dados |mesmo que SliceStart. |

> [!NOTE]
> A fábrica de dados requer que a agenda especificada na atividade exatamente corresponde à agenda especificada na disponibilidade do conjunto de dados de saída. Por conseguinte, WindowStart, WindowEnd e SliceStart e SliceEnd sempre mapeiam para o mesmo período de tempo e um setor de resultado única.
> 

### <a name="example-for-using-a-system-variable"></a>Exemplo para utilizar uma variável de sistema
No exemplo seguinte, ano, mês, dia e hora de **SliceStart** são extraídos em separado variáveis que são utilizadas por **folderPath** e **fileName** propriedades.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funções de fábrica de dados
Pode utilizar funções na fábrica de dados, juntamente com variáveis do sistema para os seguintes fins:

1. Especificar as consultas de seleção de dados (consulte os artigos de conector referenciados pelo [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo.
   
   A sintaxe para invocar uma função de fábrica de dados é:  **$$ <function>**  para consultas de seleção de dados e outras propriedades na atividade e conjuntos de dados.  
2. Especificar dependências de entrada com as funções de fábrica de dados na coleção de entradas de atividade.
   
    $$ Não é necessária para a especificação de expressões de dependência de entrada.     

No exemplo seguinte, **sqlReaderQuery** propriedade de um ficheiro JSON está atribuída a um valor devolvido pelo `Text.Format` função. Este exemplo utiliza também a variável sistema designada **WindowStart**, que representa a hora de início da janela de execução da atividade.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Consulte [data personalizada e cadeias de formato de hora](https://msdn.microsoft.com/library/8kb3ddd4.aspx) tópico descreve as diferentes opções de formatação que pode utilizar (por exemplo: ay vs aaaa). 

### <a name="functions"></a>Funções
As tabelas seguintes listam todas as funções no Azure Data Factory:

| Categoria | Função | Parâmetros | Descrição |
| --- | --- | --- | --- |
| Hora |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y horas para X do tempo indicado. <br/><br/>Exemplo:`9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Hora |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y minutos x.<br/><br/>Exemplo:`9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Hora |StartOfHour(X) |X: Datetime |Obtém o tempo de partida para a hora representado pelo componente de hora de X. <br/><br/>Exemplo:`StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Data |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |Adiciona Y dias x. <br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 2 dias = 9/17/2013 12:00:00 PM.<br/><br/>Pode subtrair dias demasiado especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Data |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |Adiciona os meses Y x.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Pode subtrair meses demasiado especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Data |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y * 3 meses x.<br/><br/>Exemplo:`9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Data |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |Adiciona Y * x de 7 dias<br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 1 semana = 9/22/2013 12:00:00 PM<br/><br/>Pode subtrair semanas demasiado especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Data |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |Adiciona os anos Y x.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Pode subtrair anos demasiado especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Data |Day(X) |X: DateTime |Obtém o componente de dia de X.<br/><br/>Exemplo: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Data |DayOfWeek(X) |X: DateTime |Obtém o dia do componente de semana de X.<br/><br/>Exemplo: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Data |DayOfYear(X) |X: DateTime |Obtém o dia do ano representado pelo componente de ano de X.<br/><br/>Exemplos:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Data |DaysInMonth(X) |X: DateTime |Obtém os dias no mês representado pelo componente de mês do parâmetro X.<br/><br/>Exemplo: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Data |EndOfDay(X) |X: DateTime |Obtém a data / hora que representa o fim do dia (componente de dia) de X.<br/><br/>Exemplo: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Data |EndOfMonth(X) |X: DateTime |Obtém o fim do mês representado pelo componente de mês do parâmetro X. <br/><br/>Exemplo: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (hora que representa o fim do mês de Setembro de data) |
| Data |StartOfDay(X) |X: DateTime |Obtém o início do dia representado pelo componente de dia do parâmetro X.<br/><br/>Exemplo: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |FROM(X) |X: cadeia |Analisar cadeia X para o tempo de data. |
| DateTime |Ticks(X) |X: DateTime |Obtém os batimentos serão propriedade do parâmetro X. Uma escala é igual a 100 nanoseconds. O valor desta propriedade representa o número de ticks decorridos desde as 12:00:00 meia-noite, 1 de Janeiro, 0001 e. |
| Texto |Format(X) |X: variável da cadeia |Formatos de texto (utilizar `\\'` combinação para o escape `'` carateres).|

> [!IMPORTANT]
> Quando utilizar uma função dentro de outra função, não terá de utilizar  **$$**  prefixo para a função interna. Por exemplo: $$Text.Format ('PartitionKey eq \\' my_pkey_filter_value\\' e RowKey ge \\' {0: aaaa-MM-dd hh: mm:}\\', Time.AddHours (SliceStart, -6)). Neste exemplo, repare que  **$$**  prefixo não é utilizado para o **Time.AddHours** função. 

#### <a name="example"></a>Exemplo
No exemplo seguinte, os parâmetros de entrada e de saída para a atividade do ramo de registo são determinados utilizando o `Text.Format` função e a variável de sistema do SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Exemplo 2

No exemplo seguinte, o parâmetro DateTime para a atividade de procedimento armazenado é determinado através da utilização de texto. Função de formato e a variável do SliceStart. 

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
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Exemplo 3
Ler dados a partir do dia anterior em vez de dia representado pelo SliceStart, utilize a função de AddDays conforme mostrado no exemplo seguinte: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Consulte [data personalizada e cadeias de formato de hora](https://msdn.microsoft.com/library/8kb3ddd4.aspx) tópico descreve as diferentes opções de formatação que pode utilizar (por exemplo: aa vs aaaa). 

