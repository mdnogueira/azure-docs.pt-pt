---
title: "Obter mais informações sobre o Azure Application Insights | Microsoft Docs"
description: "Depois de introdução ao Application Insights, eis um resumo das funcionalidades que pode explorar."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 167f0175b2c5de804a4251307a7b16e5e40a516a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="more-telemetry-from-application-insights"></a>Mais de telemetria do Application Insights
Depois de ter [adicionado Application Insights ao seu código ASP.NET](app-insights-asp-net.md), existem algumas coisas que pode fazer para obter telemetria ainda mais. 

| Ação | O que obtém|
|---|---|
|(Os servidores do IIS) [Instalar Monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648) em cada computador do servidor.<br/>(As aplicações da web do azure) No painel de controlo do Azure para a aplicação web, abra o painel do Application Insights.| [**Contadores de desempenho**](app-insights-performance-counters.md)<br/>[**Exceções** ](app-insights-asp-net-exceptions.md) - detalhadas rastreios de pilha<br/>[**Dependências**](app-insights-asp-net-dependencies.md)|
|[Adicione o fragmento do JavaScript para páginas web](app-insights-javascript.md)|[Página desempenho](app-insights-web-track-usage.md), exceções de browser de desempenho de AJAX. Telemetria personalizada do lado do cliente.|
|[Criar testes web de disponibilidade](app-insights-monitor-web-app-availability.md)|Obtenha alertas se o seu site ficar indisponível|
|[Certifique-se buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) é gerado pelo MSBuild|[Criar anotações nos gráficos métricas](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Escrever métricas e eventos personalizados](app-insights-api-custom-events-metrics.md)|Contagem de métricas e eventos de negócio, controlar a utilização detalhada e muito mais.|
|[Perfil do site em direto](https://aka.ms/AIProfilerPreview)|Temporizações de detalhado de função da sua aplicação web em direto|






