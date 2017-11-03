---
title: Azure Application Insights para ASP.NET Core | Microsoft Docs
description: "Monitorizar aplicações web de disponibilidade, desempenho e utilização."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 74f99dd6f31ecff7c838d8f710a7fe4279ce0ea9
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para Núcleo do ASP.NET
[Application Insights](app-insights-overview.md) permite-lhe monitorizar a sua aplicação web de disponibilidade, desempenho e utilização. Com o feedback que recebe relativamente ao desempenho e à eficácia da sua aplicação no terreno, pode fazer escolhas informadas sobre o rumo do design em cada ciclo de vida do desenvolvimento.

![Exemplo](./media/app-insights-asp-net-core/sample.png)

Irá precisar de uma subscrição com [Microsoft Azure](http://azure.com). Inicie sessão com uma conta Microsoft, que poderá ter para o Windows, o XBox Live ou outros serviços cloud do Microsoft. A equipa pode ter uma subscrição organizacional do Azure: peça ao proprietário para adicioná-lo utilizando a sua conta Microsoft.

## <a name="getting-started"></a>Introdução

* No Explorador de soluções do Visual Studio, clique no seu projeto e selecione **configurar o Application Insights**, ou **adicionar > Application Insights**. [Saiba mais](app-insights-asp-net.md).
* Se não vir os comandos de menu, siga o [manual guia de introdução ao obter](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started). Terá de fazê-lo se o projeto foi criado com uma versão do Visual Studio antes de 2017.

## <a name="using-application-insights"></a>A utilizar o Application Insights
Inicie sessão no [portal do Microsoft Azure](https://portal.azure.com), selecione **todos os recursos** ou **Application Insights**e, em seguida, selecione o recurso que criou para monitorizar a aplicação.

Na janela de browser separados, utilize a aplicação de tempo. Irá ver os dados que aparecem nos gráficos Application Insights. (Poderá ter de clicar em Atualizar.) Haverá apenas uma pequena quantidade de dados enquanto estiver a desenvolver, mas estes gráficos realmente entrem alive quando publicar a aplicação e têm vários utilizadores. 

A página de descrição geral apresenta os gráficos de chave de desempenho: tempo de resposta do servidor, o tempo de carregamento de página e contagens de pedidos falhados. Clique em qualquer gráfico para ver mais dados e gráficos.

Vistas no portal do enquadram-se em três categorias principais:

* [Explorador de métricas](app-insights-metrics-explorer.md) mostra gráficos e tabelas de métricas e contagens, tais como tempos de resposta, taxas de falha ou métricas tem de criar manualmente com o [API](app-insights-api-custom-events-metrics.md). Filtrar e segmentar os dados por valores de propriedade para obter uma melhor compreensão da sua aplicação e os seus utilizadores.
* [Explorador de pesquisa](app-insights-diagnostic-search.md) apresenta uma lista de eventos individuais, tais como pedidos específicos, exceções, rastreios de registo ou eventos criado por si com o [API](app-insights-api-custom-events-metrics.md). Filtrar e procurar nos eventos e navegar entre os eventos relacionados para investigar problemas.
* [Análise de](app-insights-analytics.md) permite-lhe executar consultas de como o SQL Server ao longo da sua telemetria e é uma ferramenta poderosa de analítica e de diagnóstico.

## <a name="alerts"></a>Alertas
* Obter automaticamente [proativa alertas diagnóstico](app-insights-proactive-diagnostics.md) que indicam sobre alterações anómalas taxas de falha e outras métricas.
* Configurar [testes de disponibilidade](app-insights-monitor-web-app-availability.md) para testar o seu site continuamente a partir de localizações em todo o mundo e receber e-mails, assim que qualquer falha de teste.
* Configurar [alertas métricas](app-insights-monitor-web-app-availability.md) saber se as métricas como tempos de resposta ou taxas de exceção aceda exteriores limites aceitáveis.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>Código fonte aberto
[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>Passos seguintes
* [Adicione a telemetria às suas páginas web](app-insights-javascript.md) para monitorizar a utilização de página e o desempenho.
* [Monitorizar dependências](app-insights-asp-net-dependencies.md) para ver se REST, SQL Server ou outros recursos externos são abrandamento.
* [Utilize a API](app-insights-api-custom-events-metrics.md) para enviar os seus próprios eventos e as métricas para uma vista mais detalhada do desempenho e a utilização da sua aplicação.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) verificar a sua aplicação constantemente a partir em todo o mundo. 

