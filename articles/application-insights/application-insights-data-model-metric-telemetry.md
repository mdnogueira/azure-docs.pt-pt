---
title: "Modelo de dados de telemetria de Insights de aplicação do Azure - métrica de telemetria | Microsoft Docs"
description: "Modelo de dados do Application Insights para telemetria métrica"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: bd09e2a21c25097fa4b378cb2dbe2787edbb1967
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetria de métrica: modelo de dados do Application Insights

Existem dois tipos de telemetria métrica suportado pelo [Application Insights](app-insights-overview.md): único e medições e uma métrica previamente agregada. Única medida é apenas um nome e valor. Métrica previamente agregada Especifica o valor mínimo e máximo da métrica no intervalo de agregação e o desvio-padrão do mesmo.

Telemetria de métrica previamente agregada assume esse período de agregação foi um minuto.

Existem vários nomes de métricos conhecidos suportados pelo Application Insights. Estas métricas juntar performanceCounters tabela.

Métrica que representa os contadores de sistema e do processo:

| **Nome de .NET**             | **Nome de agnóstico relativamente de plataforma** | **Nome da API de REST** | **Descrição**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Trabalho em curso... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | CPU de máquinas totais
| `\Memory\Available Bytes`                 | Trabalho em curso... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | memória disponível no disco
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Trabalho em curso... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU do processo que aloja a aplicação
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Trabalho em curso... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memória utilizada pelo processo que aloja a aplicação
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Trabalho em curso... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | velocidade das operações de e/s de executa pelo processo que aloja a aplicação
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Trabalho em curso... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | taxa de pedidos processados por aplicação 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Trabalho em curso... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | taxa de exceções iniciadas pela aplicação
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Trabalho em curso... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tempo de execução média de pedidos
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Trabalho em curso... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | número de pedidos a aguardar que o processamento de uma fila

## <a name="name"></a>Nome

Nome da métrica que gostaria de ver no portal do Application Insights e IU. 

## <a name="value"></a>Valor

Valor único para a medida. Soma de medidas individuais para a agregação.

## <a name="count"></a>Contagem

Métrica ponderação da métrica agregada. Não deve ser definida para uma medida.

## <a name="min"></a>Mín.

Valor mínimo da métrica agregada. Não deve ser definida para uma medida.

## <a name="max"></a>Máx

Valor máximo da métrica agregada. Não deve ser definida para uma medida.

## <a name="standard-deviation"></a>Desvio-padrão

Desvio-padrão da métrica agregada. Não deve ser definida para uma medida.

## <a name="custom-properties"></a>Propriedades personalizadas

Métrica com a propriedade personalizada `CustomPerfCounter` definido como `true` indicar que a métrica representa o contador de desempenho do windows. Estas métricas colocados na tabela de performanceCounters. Não se encontra na customMetrics. Também o nome desta métrica é analisado para extrair categoria, o contador e os nomes de instâncias.

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba como utilizar [API do Application Insights para as métricas e eventos personalizados](app-insights-api-custom-events-metrics.md#trackmetric).
- Consulte [modelo de dados](application-insights-data-model.md) para o modelo de tipos e os dados do Application Insights.
- Veja [plataformas](app-insights-platforms.md) suportado pelo Application Insights.
