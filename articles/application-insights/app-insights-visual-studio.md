---
title: Trabalhar com o Application Insights do Azure no Visual Studio | Microsoft Docs
description: "Análise de desempenho da aplicação Web e diagnóstico durante a depuração e produção."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/17/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 7fe1b572104416fec62261c60228fa966d197dd9
ms.lasthandoff: 03/21/2017


---
# <a name="working-with-azure-application-insights-in-visual-studio"></a>Trabalhar com o Application Insights do Azure no Visual Studio
No Visual Studio (versão&2015; e posterior), pode analisar o desempenho e diagnosticar problemas de depuração e produção na sua aplicação Web ASP.NET, com a telemetria do [Application Insights do Azure](app-insights-overview.md).

Se criou a aplicação Web ASP.NET com o Visual Studio 2017 ou posterior, já tem o SDK do Application Insights. Caso contrário, se ainda não o tiver feito, [adicione o Application Insights à sua aplicação](app-insights-asp-net.md).

Para monitorizar a aplicação quando estiver a ser produzida, vê normalmente a telemetria do Application Insights no [portal do Azure](https://portal.azure.com), onde pode definir alertas e aplicar ferramentas de monitorização poderosas. Mas, para a depuração, também pode procurar e analisar a telemetria no Visual Studio. Pode utilizar o Visual Studio para analisar a telemetria do seu site de produção e as execuções das depurações no seu computador de desenvolvimento. Em último caso, pode analisar as execuções das depurações, mesmo que ainda não tenha configurado o SDK para enviar telemetria para o portal do Azure. 

## <a name="run"></a> Depurar o projeto
Execute a aplicação Web no modo de depuração local com o F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que tenham sido registados pelo módulo do Application Insights no seu projeto.

![No Visual Studio, o botão do Application Insights é apresentado durante a depuração.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

Clique neste botão para procurar a sua telemetria. 

## <a name="application-insights-search"></a>Pesquisa do Application Insights
A janela Pesquisa do Application Insights mostra os eventos que foram registados. (Se iniciou sessão no Azure aquando da configuração do Application Insights, poderá pesquisar os mesmos eventos no portal do Azure.)

![Clique com o botão direito do rato no projeto e escolha Application Insights, Pesquisa](./media/app-insights-visual-studio/34.png)

> [!NOTE] 
> Depois de selecionar ou desmarcar filtros, clique no botão Pesquisar no fim do campo de pesquisa de texto.
>

A pesquisa de texto livre funciona em todos campos dos eventos. Por exemplo, procurar parte do URL de uma página; ou o valor de uma propriedade como a cidade do cliente; ou palavras específicas no registo de rastreio.

Clique em qualquer evento para ver as respetivas propriedades detalhadas.

Para pedidos para a sua aplicação Web, pode clicar para o código.

![Em Detalhes do Pedido, clique no código](./media/app-insights-visual-studio/31.png)

Pode ainda abrir os itens relacionados para ajudar a diagnosticar os pedidos falhados ou as exceções.

![Em Detalhes do Pedido, desloque para baixo para itens relacionados](./media/app-insights-visual-studio/41.png)

## <a name="exceptions-and-failed-requests"></a>Exceções e pedidos falhados
Apresentação de relatórios de exceção na janela de Pesquisa. (Em alguns tipos mais antigos da aplicação ASP.NET, tem de [configurar a monitorização de exceção](app-insights-asp-net-exceptions.md) para ver as exceções que são processadas pelo framework.)

Clique numa exceção para obter um rastreio de pilha. Se abrir o código da aplicação no Visual Studio, pode clicar no rastreio de pilha para a linha relevante do código.

![Rastreio de pilha de exceção](./media/app-insights-visual-studio/17.png)

## <a name="request-and-exception-summaries-in-the-code"></a>Resumos de pedido e de exceção no código
Na linha Código Lente acima de cada método do processador, verá uma contagem das exceções e pedidos registados pelo Application Insights nas últimas 24 horas.

![Rastreio de pilha de exceção](./media/app-insights-visual-studio/21.png)

> [!NOTE] 
> O Código Lente mostra os dados do Application Insights apenas se tiver [configurado a sua aplicação para enviar telemetria para o portal do Application Insights](app-insights-asp-net.md).
>

[Saiba mais sobre o Application Insights no Código Lente](app-insights-visual-studio-codelens.md)

## <a name="trends"></a>Tendências
Tendências é uma ferramenta para visualizar a forma como a aplicação se comporta ao longo do tempo. 

Selecione **Explorar Tendências de Telemetria** no botão da barra de ferramentas do Application Insights ou na janela Pesquisa do Application Insights Escolha uma das cinco consultas comuns para começar. Pode analisar os diferentes conjuntos de dados com base em tipos de telemetria, intervalos de tempo e outras propriedades. 

Para localizar anomalias nos seus dados, escolha uma das opções anómalas na lista pendente "Tipo de Vista". As opções de filtragem na parte inferior da janela facilitam a maximização de subconjuntos específicos da sua telemetria.

![Tendências](./media/app-insights-visual-studio/51.png)

[Mais informações sobre as Tendências](app-insights-visual-studio-trends.md).

## <a name="local-monitoring"></a>Monitorização local
(A partir do Visual Studio 2015 Update 2) Se não tiver configurado o SDK para enviar telemetria ao portal do Application Insights (de modo a não haver nenhuma chave de instrumentação no ApplicationInsights.config), a janela de diagnóstico mostrará a telemetria da sua sessão de depuração mais recente. 

Esta ação é desejável se já tiver publicado uma versão anterior da aplicação. De certeza que não quer que a telemetria das suas sessões de depuração seja misturada com a telemetria no portal do Application Insights da aplicação publicada.

Também poderá ser útil se tiver alguma [telemetria personalizada](app-insights-api-custom-events-metrics.md) que pretende depurar antes de enviar a telemetria ao portal.

* *Inicialmente, configurei totalmente o Application Insights para enviar telemetria para o portal. No entanto, agora, apenas quero a telemetria no Visual Studio.*
  
  * Nas Definições da janela Pesquisa, encontrará uma opção para procurar o diagnóstico local, mesmo se a sua aplicação enviar telemetria para o portal.
  * Para parar o envio de telemetria para o portal, comente a linha `<instrumentationkey>...` no ApplicationInsights.com. Quando estiver pronto para enviar novamente telemetria para o portal, anule o comentário.


## <a name="whats-next"></a>Passos seguintes?
|  |  |
| --- | --- |
| **[Adicionar mais dados](app-insights-asp-net-more.md)**<br/>Monitorize a utilização, a disponibilidade, as dependências e as exceções. Integre rastreios a partir de arquiteturas de registo. Grave a telemetria personalizada. |![Visual Studio](./media/app-insights-visual-studio/64.png) |
| **[Trabalhar com o portal do Application Insights](app-insights-dashboards.md)**<br/>Dashboards, ferramentas de diagnóstico e análise poderosas, alertas, mapa de dependência em direto da aplicação e exportação de telemetria. |![Visual Studio](./media/app-insights-visual-studio/62.png) |


