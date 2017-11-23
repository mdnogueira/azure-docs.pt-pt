---
title: Partilhar vistas personalizadas do Azure Time Series Insights com URLs parametrizados | Microsoft Docs
description: Este artigo descreve como desenvolver URLs parametrizados no Azure Time Series Insights, para partilhar facilmente a vista de um cliente.
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: MarkMcGeeAtAquent
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: rest-api
ms.topic: get-started-article
ms.workload: big-data
ms.date: 11/21/2017
ms.openlocfilehash: ac48969a9166080384dccf606f0401a82016a60a
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Partilhar vistas personalizadas com um URL parametrizado

Para partilhar uma vista personalizada no explorador do Time Series Insights, pode criar programaticamente um URL parametrizado dessa vista.

O explorador do Time Series Insights suporta parâmetros de consulta de URL para especificar vistas na experiência diretamente a partir de um URL.  Por exemplo, ao utilizar apenas o URL, pode especificar um ambiente de destino, um predicado de pesquisa e um intervalo de tempo pretendido. Quando um utilizador clica no URL personalizado, a interface fornece uma ligação diretamente para esse recurso no portal do Time Series Insights.  Aplicam-se as políticas de acesso a dados. 

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica o ID do ambiente de destino.  Este é um componente do FQDN de acesso aos dados e pode encontrá-lo no canto superior direito da descrição geral do ambiente no portal do Azure.  É tudo o que vem antes de `env.timeseries.azure.com`. Um exemplo de parâmetro de ID de ambiente é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Hora

Pode especificar valores de hora absolutos ou relativos com URLs parametrizados.

### <a name="absolute-time-values"></a>Valores de hora absolutos

Para valores de hora absolutos, utilize os parâmetros `from=<integer>` e `to=<integer>`. 

`from=<integer>` é um valor em milissegundos de JavaScript da hora de início do intervalo de pesquisa.

`to=<integer>` é um valor em milissegundos de JavaScript da hora de fim do intervalo de pesquisa. 

Para identificar os milissegundos de JavaScript de uma data, veja [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html) (Conversor de Carimbo de Data/Hora Epoch e Unix).

### <a name="relative-time-values"></a>Valores de hora relativos

Para valores de hora relativos, utilize `relativeMillis=<value>`, em que o *valor* está em milissegundos de JavaScript da data mais recente no back-end.

Por exemplo, `&relativeMillis=3600000` apresenta os 60 segundos de dados mais recentes.

Os valores aceites correspondem ao menu **hora rápida** do explorador do Time Series Insights e incluem o seguinte:

- 1800000 (últimos 30 min)
- 3600000 (últimos 60 min)
- 10800000 (últimas 3 horas)
- 21600000 (últimas 6 horas)
- 43200000 (últimas 12 horas)
- 86400000 (últimas 24 horas)
- 604800000 (últimos 7 dias)
- 2592000000 (últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O parâmetro `timeSeriesDefinitions=<collection of term objects>` especifica os termos de uma vista do Time Series Insights, em que:

- `name=<string>`
  - É o nome do *termo*.
- `splitBy=<string>`
  - É o nome da coluna pela qual *dividir*.
- `measureName=<string>`
  - É o nome da coluna da *medida*.
- `predicate=<string>`
  - É a cláusula *where* para a filtragem do lado do servidor.

 
### <a name="examples"></a>Exemplos

Por exemplo, para adicionar definições de séries temporal como parâmetro de URL, pode utilizar o seguinte:

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Ao utilizar estas definições de série temporal de exemplo para 

- o ID do ambiente
- os últimos 60 minutos de dados
- os termos (F1PressureID, F2TempStation e F3VibrationPL) que compõem os parâmetros opcionais
 
pode construir o URL parametrizado seguinte para uma vista:

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Se tivesse utilizado o explorador do Time Series Insights para criar a vista descrita no URL anterior, teria o aspeto seguinte:

![Termos do explorador do Time Series Insights](media/parameterized-url/url1.png)

A vista completa (incluindo o gráfico) apareceria assim:

![Vista de gráfico](media/parameterized-url/url2.png)

## <a name="next-steps"></a>Passos seguintes
[Consultar dados com C#](time-series-insights-query-data-csharp.md)
