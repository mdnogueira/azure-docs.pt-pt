---
title: "Monitorizar o estado de funcionamento da sua aplicação e a utilização com o Application Insights"
description: "Introdução ao Application Insights. Analise a utilização, disponibilidade e desempenho dos seus no local ou aplicações do Microsoft Azure."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: mbullwin
ms.openlocfilehash: 32000f5a85c84913aa820df00f1bb7f877bf037f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-performance-in-web-applications"></a>Monitorizar o desempenho nas aplicações Web


Certifique-se de que a aplicação está a executar corretamente e descobrir rapidamente sobre eventuais falhas. [Application Insights] [ start] conte quaisquer problemas de desempenho e exceções e ajudar a localizar e diagnosticar as causas raiz.

Application Insights podem monitorizar aplicações web de Java e ASP.NET e serviços, serviços do WCF. Podem ser alojado no local, em máquinas virtuais, ou como Web sites do Microsoft Azure. 

No lado do cliente, o Application Insights pode demorar a telemetria de páginas web e uma ampla variedade de dispositivos, incluindo iOS, Android e aplicações da loja Windows.

>[!Note]
> Efetuamos uma nova experiência disponível para localizar lenta efetuar páginas na sua aplicação web. Se não tiver acesso ao mesmo, ativá-la ao configurar as opções de pré-visualização com o [painel de pré-visualização](app-insights-previews.md). Leia sobre esta nova experiência em [localizar e corrigir os congestionamentos de desempenho com a investigação de desempenho interativa](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation).

## <a name="setup"></a>Configurar a monitorização de desempenho
Se ainda não adicionou ainda Application Insights ao seu projeto (ou seja, se não tem Applicationinsights), escolha uma das seguintes formas para começar a utilizar:

* [Aplicações web do ASP.NET](app-insights-asp-net.md)
  * [Adicionar a monitorização de exceções](app-insights-asp-net-exceptions.md)
  * [Adicionar a monitorização de dependência](app-insights-monitor-performance-live-website-now.md)
* [Aplicações web J2EE](app-insights-java-get-started.md)
  * [Adicionar a monitorização de dependência](app-insights-java-agent.md)

## <a name="view"></a>Explorar métricas de desempenho
No [portal do Azure](https://portal.azure.com), navegue para o recurso do Application Insights que configurou para a sua aplicação. O painel de descrição geral apresenta dados de desempenho básico:

Clique em qualquer gráfico para ver mais detalhes e para ver os resultados para um período mais longo. Por exemplo, clique no mosaico de pedidos e, em seguida, selecione um intervalo de tempo:

![Clicar para obter mais dados e selecione um intervalo de tempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Clique num gráfico para escolher as métricas apresenta, ou adicione um novo gráfico e selecionar a métricas:

![Clique num gráfico para escolher as métricas](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desmarque todas as métricas** para ver a seleção completa que está disponível. As métricas enquadram-se grupos; Quando qualquer membro de um grupo é selecionado, são apresentados apenas os outros membros desse grupo.

## <a name="metrics"></a>O que faz-média de todos os? Os mosaicos de desempenho e relatórios
Existem várias métricas de desempenho, que pode obter. Vamos começar com as que são apresentados por predefinição no painel de aplicação.

### <a name="requests"></a>Pedidos
O número de pedidos HTTP recebidos no período de tempo especificado. Compare-as com os resultados em outros relatórios para ver como a aplicação se comporta como a carga varia.

Pedidos de HTTP incluem todos os pedidos GET ou POST de páginas, dados e imagens.

Clique no mosaico para obter contagens para os URLs específicos.

### <a name="average-response-time"></a>Tempo de resposta médio
Mede o tempo entre a um pedido web introduzir a sua aplicação e a resposta a ser devolvido.

Os pontos de mostram uma mudança médio. Se existirem muitos pedidos, poderão existir alguns desvio da média sem um horário de pico óbvios ou dip no gráfico.

Procure picos de atividade invulgares. Em geral, esperar que um tempo de resposta a aumentar com um aumento súbito do pedidos. Se o aumento súbito desproporcionado, pode atingir um limite de recursos, tais como CPU ou a capacidade de um serviço que utiliza a sua aplicação.

Clique no mosaico para obter vezes para URLs específicos.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Pedidos mais lentos
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Mostra quais os pedidos poderão ter de otimização de desempenho.

### <a name="failed-requests"></a>Pedidos falhados
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Uma contagem de pedidos que emitiu exceções não identificadas.

Clique no mosaico para ver os detalhes de falhas específicas e selecione um pedido individual para ver os detalhes. 

Apenas uma amostra representativa de falhas é mantida para inspecção individuais.

### <a name="other-metrics"></a>Outras métricas
Para ver o que definir outras métricas pode apresentar, clique num gráfico e, em seguida, desmarque todas as métricas para ver o completo disponível. Clique (i) para ver a definição de cada métrica.

![Anular seleção de todas as métricas para ver o conjunto completo](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Selecionar qualquer métrica desativa a outras pessoas que não pode aparecer no mesmo gráfico.

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por correio eletrónico dos valores invulgares de qualquer métrica, adicione um alerta. Pode escolher a enviar o e-mail para os administradores de conta ou para endereços de e-mail específico.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Defina o recurso antes das outras propriedades. Não escolha os recursos de teste Web, se pretender definir alertas nas métricas de desempenho ou utilização.

Tenha o cuidado de tenha em atenção as unidades nas quais está a pedido para introduzir o valor de limiar.

*Não consigo ver o botão Adicionar alerta.* -Este é um grupo de conta para o qual tem acesso só de leitura? Contacte o administrador da conta.

## <a name="diagnosis"></a>Diagnosticar problemas
Eis algumas sugestões para localizar e diagnosticar problemas de desempenho:

* Configurar [testes web] [ availability] para ser alertado se o web site ficar inativo ou responder lentamente ou incorretamente. 
* Compare a contagem de pedidos com outras métricas para ver se falhas ou resposta lenta relacionadas com a carregar.
* [Inserir, procure as instruções de rastreio] [ diagnostic] no código para ajudar a identificar problemas.
* Monitorizar a aplicação Web na operação com [métricas em fluxo em direto][livestream].
* Capturar o estado da aplicação .net com [instantâneo depurador][snapshot].

>[!Note]
> Estamos no processo de transição investigação de desempenho do Application Insights para uma experiência interativa do ecrã inteiro. A seguinte documentação abrange a nova experiência de primeiro e, em seguida, revê a experiência anterior, caso ainda precisa de aceder ao mesmo, enquanto permanece disponível em toda a transição.

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>Localizar e corrigir os congestionamentos de desempenho com uma investigação de desempenho de ecrã inteiro interativa

Pode utilizar a novo investigação de desempenho interativa do Application Insights para rever as operações de desempenho lentas na sua aplicação Web. Pode selecionar uma operação lenta específica e utilizar rapidamente [gerador de perfis](app-insights-profiler.md) raiz fazer com que as operações lentas para baixo até o código. Utilizar a distribuição da duração novo apresentada para a operação selecionada, pode rapidamente rapidamente apenas como incorreta é a experiência para os seus clientes. Na verdade, para cada operação lenta, pode ver o número das suas interações do utilizador foram afetado. No exemplo seguinte, foi decidido demorar um olhar mais a experiência de operação de clientes/detalhes obter. A distribuição da duração é possível ver que existem três picos. Pico de pedidos mais à esquerda é aproximadamente 400ms e representa excelente experiência reativa. Pico de pedidos média é aproximadamente 1.2s e representa uma experiência mediocre. Por fim, o 3.6s temos outro pico pequeno pedidos que representa a experiência de percentil 99th, que é provavelmente, causar os nossos clientes deixe dissatisfied. Essa experiência é dez vezes mais lenta do que o excelente experiência para a mesma operação. 

![Picos de duração três clientes/detalhes GET](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Para obter um melhor sentido das experiências de utilizador para esta operação, mas pode selecionar um intervalo de tempo maior. Iremos pode, em seguida, também reduzir no tempo numa janela de tempo específico em que a operação foi particularmente lenta. No exemplo seguinte tiver Mudámos da predefinição 24 horas, intervalo de tempo para 7 dias intervalo de tempo e, em seguida, ampliado para o intervalo de tempo 9:47 para 12:47 entre Tue a 12 e qua a 13. Tenha em atenção que a distribuição da duração e o número de rastreios de gerador de perfis de exemplo e foram atualizadas no lado direito.

![Obtenha os clientes/detalhes intervalo três picos de duração dentro de 7 dias com uma janela de tempo](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Para restringir nas experiências lentas, iremos junto aplicar zoom no durações que se inserem entre 95th e o percentil 99th. Estes representam a % de 4 de interações do utilizador que foram particularmente lentas.

![Obtenha os clientes/detalhes intervalo três picos de duração dentro de 7 dias com uma janela de tempo](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Podemos agora a vista de olhos amostras representativos, clicando no botão exemplos ou os rastreios de gerador de perfis representativa, clicando no botão de rastreios de gerador de perfis. Neste exemplo, existem quatro rastreios que tenham sido recolhidos para obter os clientes/detalhes na duração de janela e o intervalo de tempo de interesse.

Por vezes, o problema não será possível no seu código, mas em vez numa dependência chamadas por código. Pode mudar para o separador de dependências na vista de triagem de desempenho para investigar essas dependências lentas. Tenha em atenção que por predefinição a vista de desempenho é médias tendências, mas que realmente pretende observar o percentil 95th (ou 99th, caso esteja a monitorizar um serviço muito madura). No exemplo seguinte Concentramo-na dependência lenta de BLOB do Azure, onde chamamos PUT fabrikamaccount. O cluster de experiências boa em torno 40ms, enquanto as chamadas lentas para a dependência mesma três vezes inferior, 120ms cerca de clustering. Não tem muitas destas chamadas para adicionar a cópia de segurança para fazer com que a respetiva operação abrandar intensas em termos. Pode explorar representativos exemplos e rastreios de gerador de perfis, tal como pode fazer com o separador de operações.

![Obtenha os clientes/detalhes intervalo três picos de duração dentro de 7 dias com uma janela de tempo](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

Outra funcionalidade realmente poderosa que há de nova para a investigação de desempenho de ecrã inteiro interativo é a integração com o insights. Application Insights podem detetar e descobrir como regressões de capacidade de resposta de insights, bem como ajuda a identificar propriedades comuns no conjunto de exemplo que escolheu para se focarem em. É a melhor forma de ver todas as informações disponíveis para mudar para um intervalo de tempo de 30 dias e, em seguida, selecione geral para ver informações em todas as operações para o último mês.

![Obtenha os clientes/detalhes intervalo três picos de duração dentro de 7 dias com uma janela de tempo](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

Application Insights no nova vista de triagem de desempenho literalmente podem ajudar a determinar os needles na haystack que resultar num fracas experiências para os utilizadores de aplicação Web.

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>Preteridas: Localizar e corrigir os congestionamentos de desempenho com uma investigação de desempenho de legado bladed estreito

Pode utilizar a investigação de desempenho bladed legada do Application Insights para localizar as áreas da sua aplicação Web que são abrandamento desempenho global. Pode encontrar páginas específicas que são abrandamento e utilizam o [gerador de perfis](app-insights-profiler.md) para analisar a causa raiz, estes problemas para baixo até o código. 

### <a name="create-a-list-of-slow-performing-pages"></a>Criar uma lista de páginas lentas de desempenho 

O primeiro passo para encontrar problemas de desempenho é para obter uma lista das páginas lentas está a responder. O ecrã captura abaixo demonstra utilizando o painel desempenho para obter uma lista de páginas potenciais para investigação mais aprofundada. Pode ver rapidamente nesta página que ocorreu uma slow-down o tempo de resposta da aplicação aproximadamente 6:00 PM e novamente em aproximadamente as 22: 00. Também pode ver que a operação de cliente/detalhes GET tinha algumas operações de execução longa com um tempo de resposta mediano de 507.05 milissegundos. 

![Desempenho interativa do Application Insights](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>Desagregar nas páginas específicas

Assim que tiver um instantâneo do desempenho da aplicação, pode obter mais detalhes nas operações de desempenho lento específicas. Clique em qualquer operação na lista para ver os detalhes conforme mostrado abaixo. Do gráfico de pode ver se o desempenho foi com base numa dependência. Também pode ver o número de utilizadores que teve os vários tempos de resposta. 

![Painel de operações do Application Insights](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>Desagregar no período de tempo específico

Depois de identificar um ponto no tempo para investigar, desagregar mesmo further to ver as operações específicas que poderá ter causado o slow-down de desempenho. Quando clica num ponto específico no tempo a obter os detalhes da página conforme mostrado abaixo. No exemplo abaixo, pode ver as operações listadas para um determinado período de tempo, juntamente com os códigos de resposta do servidor e a duração da operação. Também tem o url para abrir um item de trabalho do TFS, se tiver de enviar essas informações a sua equipa de desenvolvimento.

![Intervalo de tempo do Application Insights](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>Desagregar uma operação específica

Depois de identificar um ponto no tempo para investigar, desagregar mesmo further to ver as operações específicas que poderá ter causado o slow-down de desempenho. Clique numa operação a partir da lista para ver os detalhes da operação, conforme mostrado abaixo. Neste exemplo, pode ver que a operação falhou e o Application Insights forneceu os detalhes da exceção que emitiu a aplicação. Novamente, pode facilmente criar um item de trabalho do TFS a partir deste painel.

![Painel de operação do Application Insights](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>Passos seguintes
[Testes Web] [ availability] -tenham pedidos web enviados para a aplicação em intervalos regulares de todo o mundo.

[Capturar e procurar rastreios de diagnóstico] [ diagnostic] - inserir chamadas de rastreio e pouco úteis percorrer os resultados para identificar problemas.

[Controlo de utilização] [ usage] -saber como as pessoas utilizam a sua aplicação.

[Resolução de problemas] [ qna] - e as perguntas e respostas



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



