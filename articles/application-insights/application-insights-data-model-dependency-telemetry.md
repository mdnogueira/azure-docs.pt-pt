---
title: "Modelo de dados de telemetria de informações de aplicação do Azure - telemetria de dependência | Microsoft Docs"
description: "Modelo de dados do Application Insights para telemetria de dependência"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: mbullwin
ms.openlocfilehash: aa305c30dc358997420be6802d43fa69e45f4a5f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria de dependência: modelo de dados do Application Insights

Telemetria de dependência (no [Application Insights](app-insights-overview.md)) representa uma interação do componente monitorizado com um componente remoto, tais como o SQL Server ou um ponto final de HTTP.

## <a name="name"></a>Nome

Nome do comando iniciado com chamada de dependência. Valor de cardinalidade baixa. Os exemplos são o nome de procedimento armazenado e modelo do caminho de URL.

## <a name="id"></a>ID

Identificador de uma instância de chamada de dependência. Utilizada para correlação com o item de telemetria de pedido correspondente a esta chamada de dependência. Para obter mais informações, consulte [correlação](application-insights-correlation.md) página.

## <a name="data"></a>Dados

Comandos iniciados por esta chamada de dependência. Os exemplos são instrução SQL e o URL de HTTP com todos os parâmetros de consulta.

## <a name="type"></a>Tipo

Nome do tipo de dependência. Valor de cardinalidade baixa para agrupamento lógico das dependências e interpretação dos outros campos como commandName e resultCode. Os exemplos são SQL, tabela do Azure e HTTP.

## <a name="target"></a>destino

Site de destino de uma chamada de dependência. Os exemplos são o nome de servidor, o endereço do anfitrião. Para obter mais informações, consulte [correlação](application-insights-correlation.md) página.

## <a name="duration"></a>Duração

Duração em formato de pedido: `DD.HH:MM:SS.MMMMMM`. Tem de ser inferior a `1000` dias.

## <a name="result-code"></a>Código de resultado

Código de resultado de uma chamada de dependência. Os exemplos são o código de erro do SQL Server e o código de estado HTTP.

## <a name="success"></a>Êxito

Indicação de chamada com êxito ou sem êxito.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Passos seguintes

- Configurar a dependência de controlo para [.NET](app-insights-asp-net-dependencies.md).
- Configurar a dependência de controlo para [Java](app-insights-java-agent.md).
- [Escrever telemetria de dependência personalizado](app-insights-api-custom-events-metrics.md#trackdependency)
- Consulte [modelo de dados](application-insights-data-model.md) para o modelo de tipos e os dados do Application Insights.
- Veja [plataformas](app-insights-platforms.md) suportado pelo Application Insights.
