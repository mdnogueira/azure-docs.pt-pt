---
title: "Smart deteção no Azure Application Insights | Microsoft Docs"
description: "Application Insights faz uma análise detalhada automática da sua telemetria de aplicação e avisa-o de potenciais problemas."
services: application-insights
documentationcenter: windows
author: rakefetj
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: c15db8451cc1c5380bcf4a29c5e722d09c0a603b
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="smart-detection-in-application-insights"></a>Deteção inteligente no Application Insights
 Deteção inteligente automaticamente avisa-o de potenciais problemas de desempenho na sua aplicação web. Efetua a análise proativa de telemetria que a aplicação envia a [Application Insights](app-insights-overview.md). Se existir um aumento súbito repentino no taxas de falha ou padrões anormais no desempenho do cliente ou servidor, receberá um alerta. Esta funcionalidade não necessita de nenhuma configuração. Funciona se a aplicação enviar telemetria suficiente.

Pode aceder a alertas de deteção inteligente de receber e-mails tanto no painel de deteção inteligente.

## <a name="review-your-smart-detections"></a>Reveja as deteções inteligentes
Pode detetar deteções de duas formas:

* **Receberá um e-mail** do Application Insights. Eis um exemplo típico:
  
    ![Alerta de e-mail](./media/app-insights-proactive-diagnostics/03.png)
  
    Clique no botão grande para abrir mais detalhadamente no portal.
* **O mosaico de deteção inteligente** na descrição geral da sua aplicação painel mostra uma contagem de alertas recentes. Clique no mosaico para ver uma lista dos alertas recentes.

![Ver as deteções recentes](./media/app-insights-proactive-diagnostics/04.png)

Selecione um alerta para ver os respetivos detalhes.

## <a name="what-problems-are-detected"></a>Que problemas são detetados?
Existem três tipos de Deteção:

* [Smart - deteção de anomalias falha](app-insights-proactive-failure-diagnostics.md). Utilizamos o machine learning para definir a taxa de pedidos falhados para a sua aplicação, esperada correlacionando com carga e ainda outros fatores. Se a taxa de falhas ficar fora do envelope esperado, podemos enviar um alerta.
* [Smart - deteção de anomalias de desempenho](app-insights-proactive-performance-diagnostics.md). Receber notificações se o tempo de resposta de uma duração de operação ou dependência é abrandamento em comparação com a linha de base histórica ou se um padrão anómalo, identificar no tempo de resposta ou tempo de carregamento de página.   
* [Smart deteção - problemas de serviço de nuvem do Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Obtenha alertas se a aplicação está alojada na Cloud Services do Azure e uma instância de função tem de falhas de arranque, a Reciclagem frequentes ou falhas de tempo de execução.

(As ligações de ajuda em cada notificação encaminha para os artigos relevantes.)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
Estas ferramentas de diagnóstico de ajudam a inspecionar a telemetria da sua aplicação:

* [Explorador de métrica](app-insights-metrics-explorer.md)
* [Explorador de pesquisa](app-insights-diagnostic-search.md)
* [Análise - linguagem de consulta poderosa](app-insights-analytics-tour.md)

Deteção inteligente é completamente automática. Mas talvez pretenda configurar alguns alertas mais?

* [Alertas de métricos configurados manualmente](app-insights-alerts.md)
* [Testes web de disponibilidade](app-insights-monitor-web-app-availability.md) 

