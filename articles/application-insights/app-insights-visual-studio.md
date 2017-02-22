---
title: Trabalhar com o Application Insights no Visual Studio
description: "Análise de desempenho e diagnóstico durante a depuração e produção."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 2334a85402049eb8654039e98cf2c3131a43de62


---
# <a name="working-with-application-insights-in-visual-studio"></a>Trabalhar com o Application Insights no Visual Studio
No Visual Studio (versão 2015 e posterior), pode analisar o desempenho e diagnosticar problemas de depuração e produção, com a telemetria do [Azure Application Insights](app-insights-overview.md).

Se ainda não [instalou o Application Insights na aplicação](app-insights-asp-net.md), faça-o agora.

## <a name="a-nameruna-debug-your-project"></a><a name="run"></a> Depurar o projeto
Execute a aplicação com F5 e experimente-a: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que foram registados.

![No Visual Studio, o botão do Application Insights é apresentado durante a depuração.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

Clique neste botão para abrir a pesquisa de diagnóstico. 

## <a name="diagnostic-search"></a>Pesquisa de diagnóstico
A janela Pesquisa mostra os eventos que foram registados. (Se iniciou sessão no Azure aquando da configuração do Application Insights, poderá pesquisar os mesmos eventos no Portal.)

![Clique com o botão direito do rato no projeto e escolha Application Insights, Pesquisa](./media/app-insights-visual-studio/34.png)

A pesquisa de texto livre funciona em todos campos dos eventos. Por exemplo, procurar parte do URL de uma página; ou o valor de uma propriedade como a cidade do cliente; ou palavras específicas no registo de rastreio.

Clique em qualquer evento para ver as respetivas propriedades detalhadas.

Pode ainda abrir o separador Itens Relacionados para ajudar a diagnosticar os pedidos falhados ou as exceções.

![](./media/app-insights-visual-studio/41.png)

## <a name="diagnostics-hub"></a>Hub de diagnóstico
O Hub de Diagnóstico (no Visual Studio 2015 ou posterior) mostra como é gerada a telemetria do servidor do Application Insights. Esta ação funciona mesmo se tiver apenas optado por instalar o SDK sem o ligar a um recurso no Portal do Azure.

![Abra a janela Ferramentas de Diagnóstico e inspecione os eventos do Application Insights.](./media/app-insights-visual-studio/31.png)

## <a name="exceptions"></a>Exceções
Se tiver [configurado a monitorização de exceção](app-insights-asp-net-exceptions.md), serão apresentados relatórios de exceções na janela Pesquisa. 

Clique numa exceção para obter um rastreio de pilha. Se abrir o código da aplicação no Visual Studio, pode clicar no rastreio de pilha para a linha relevante do código.

![Rastreio de pilha de exceção](./media/app-insights-visual-studio/17.png)

Além disso, na linha Código lente acima de cada método, verá uma contagem das exceções registadas pelo Application Insights nas últimas 24 horas.

![Rastreio de pilha de exceção](./media/app-insights-visual-studio/21.png)

## <a name="local-monitoring"></a>Monitorização local
(A partir do Visual Studio 2015 Update 2) Se não tiver configurado o SDK para enviar telemetria ao portal do Application Insights (de modo a não haver nenhuma chave de instrumentação no ApplicationInsights.config), a janela de diagnóstico mostrará a telemetria da sua sessão de depuração mais recente. 

Esta ação é desejável se já tiver publicado uma versão anterior da aplicação. De certeza que não quer que a telemetria das suas sessões de depuração seja misturada com a telemetria no portal do Application Insights da aplicação publicada.

Também poderá ser útil se tiver alguma [telemetria personalizada](app-insights-api-custom-events-metrics.md) que pretende depurar antes de enviar a telemetria ao portal.

* *Inicialmente, configurei totalmente o Application Insights para enviar telemetria para o portal. No entanto, agora, apenas quero a telemetria no Visual Studio.*
  
  * Nas Definições da janela Pesquisa, encontrará uma opção para procurar o diagnóstico local, mesmo se a sua aplicação enviar telemetria para o portal.
  * Para parar o envio de telemetria para o portal, comente a linha `<instrumentationkey>...` no ApplicationInsights.com. Quando estiver pronto para enviar novamente telemetria para o portal, anule o comentário.

## <a name="trends"></a>Tendências
Tendências é uma ferramenta para visualizar a forma como a aplicação se comporta ao longo do tempo. 

Selecione **Explorar Tendências de Telemetria** no botão da barra de ferramentas do Application Insights ou na janela Pesquisa do Application Insights Escolha uma das cinco consultas comuns para começar. Pode analisar os diferentes conjuntos de dados com base em tipos de telemetria, intervalos de tempo e outras propriedades. 

Para localizar anomalias nos seus dados, escolha uma das opções anómalas na lista pendente "Tipo de Vista". As opções de filtragem na parte inferior da janela facilitam a maximização de subconjuntos específicos da sua telemetria.

![Tendências](./media/app-insights-visual-studio/51.png)

[Mais informações sobre as Tendências](app-insights-visual-studio-trends.md).

## <a name="whats-next"></a>Passos seguintes?
|  |  |
| --- | --- |
| **[Adicionar mais dados](app-insights-asp-net-more.md)**<br/>Monitorize a utilização, a disponibilidade, as dependências e as exceções. Integre rastreios a partir de arquiteturas de registo. Grave a telemetria personalizada. |![Visual Studio](./media/app-insights-visual-studio/64.png) |
| **[Trabalhar com o portal do Application Insights](app-insights-dashboards.md)**<br/>Dashboards, ferramentas de diagnóstico e análise poderosas, alertas, mapa de dependência em direto da aplicação e exportação de telemetria. |![Visual Studio](./media/app-insights-visual-studio/62.png) |




<!--HONumber=Nov16_HO3-->


