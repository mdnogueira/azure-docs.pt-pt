---
title: "Modelo de dados de telemetria de informações de aplicação do Azure - telemetria de rastreio | Microsoft Docs"
description: Modelo de dados do Application Insights para telemetria de rastreio
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
ms.openlocfilehash: 0398774e21d89fd084e6929bc5e410697d2aafaa
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de rastreio: modelo de dados do Application Insights

Telemetria de rastreio (no [Application Insights](app-insights-overview.md)) representa `printf` declarações de rastreio que são pesquisados por texto de estilo. `Log4Net`, `NLog`, e outras entradas de ficheiro de registo baseados em texto são convertidas em instâncias deste tipo. O rastreio não tem valores como uma extensibilidade.

## <a name="message"></a>Mensagem

Mensagem de rastreio.

Comprimento máximo: 32768 carateres

## <a name="severity-level"></a>Nível de gravidade

Nível de gravidade de rastreio. O valor pode ser `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- [Explorar os registos de rastreio do .NET no Application Insights](app-insights-asp-net-trace-logs.md).
- [Explorar Java registos de rastreio no Application Insights](app-insights-java-trace-logs.md).
- Consulte [modelo de dados](application-insights-data-model.md) para o modelo de tipos e os dados do Application Insights.
- [Escrever telemetria de rastreio personalizada](app-insights-api-custom-events-metrics.md#tracktrace)
- Veja [plataformas](app-insights-platforms.md) suportado pelo Application Insights.
