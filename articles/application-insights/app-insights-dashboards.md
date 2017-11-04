---
title: "Dashboards e navegação no Azure Application Insights | Microsoft Docs"
description: "Crie vistas da sua chaves gráficos APM e consultas."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 9a4a839e7b5cc772fb9d4c57ed70484d90a87fdd
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navegação e Dashboards no portal do Application Insights
Depois de ter [configurar o Application Insights no seu projeto](app-insights-overview.md), dados de telemetria sobre o desempenho e a utilização da sua aplicação aparecerá no recurso do Application Insights do seu projeto no [portal do Azure](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Localizar a telemetria
Iniciar sessão para o [portal do Azure](https://portal.azure.com) e navegue para o recurso do Application Insights que criou para a sua aplicação.

![Clique em Procurar, selecione o Application Insights, em seguida, a aplicação.](./media/app-insights-dashboards/00-start.png)

O painel de descrição geral (página) para a sua aplicação mostra um resumo das métricas principais diagnóstico da sua aplicação e é um gateway para outras funcionalidades do portal.

![Principais rotas para ver a telemetria](./media/app-insights-dashboards/010-oview.png)

Pode personalizar qualquer um dos gráficos e grelhas e afixar ao dashboard. Dessa forma, pode colocar em conjunto a telemetria de chave a partir de diferentes aplicações num central dashboard.

## <a name="dashboards"></a>Dashboards
A primeira coisa que vê depois de iniciar sessão para o [portal do Microsoft Azure](https://portal.azure.com) é um dashboard. Aqui, pode reunir os gráficos que são mais importantes para si através de todos os recursos do Azure, incluindo a telemetria de [Azure Application Insights](app-insights-overview.md).

![Um dashboard personalizado.](./media/app-insights-dashboards/31.png)

1. **Navegue para recursos específicos** , tais como a sua aplicação no Application Insights: utilizar a barra da esquerda.
2. **Regressar ao dashboard atual**, ou mudar a outras vistas recentes: Utilize o menu de lista pendente na parte superior esquerda.
3. **Comutador dashboards**: Utilize o menu de lista pendente no título de dashboard
4. **Criar, editar e partilhar os dashboards** na barra de ferramentas do dashboard.
5. **Editar o dashboard**: paira o rato sobre um mosaico e, em seguida, utilizar a barra superior para mover, personalizar ou removê-lo.

## <a name="add-to-a-dashboard"></a>Adicionar a um dashboard
Quando está a observar um painel ou um conjunto de gráficos que é particularmente interessante, pode afixar uma cópia do mesmo ao dashboard. Este é apresentado próxima vez que não existe regressar.

![Para afixar um gráfico, coloque o cursor sobre- e, em seguida, clique em "…" no cabeçalho.](./media/app-insights-dashboards/33.png)

1. Gráfico de PIN ao dashboard. Uma cópia do gráfico é apresentado no dashboard.
2. Afixar todo o painel ao dashboard - aparece no dashboard como um mosaico que pode clicar sucessivamente.
3. Clique em canto superior esquerdo para regressar ao dashboard atual. Em seguida, pode utilizar o menu pendente para regressar à vista atual.

Tenha em atenção que os gráficos estão agrupados em mosaicos: um mosaico pode conter mais de um gráfico. Afixe o mosaico todo ao dashboard.

O gráfico é atualizado automaticamente com uma frequência que depende do intervalo de tempo do gráfico:

* Intervalo de tempo até 1 hora: atualizar cada 5 minutos
* Intervalo de tempo 1-24 horas: atualizar a cada 15 minutos
* Tempo de intervalo superior a 24 horas: (intervalo de tempo) / 60.

### <a name="pin-any-query-in-analytics"></a>Afixar qualquer consulta em análise
Também pode [afixar análise](app-insights-analytics-using.md#pin-to-dashboard) gráficos para um [partilhado](#share-dashboards-with-your-team) dashboard. Isto permite-lhe adicionar gráficos de qualquer consulta arbitrário juntamente com as métricas padrão. 

Os resultados são automaticamente recalculados a cada hora. Clique no ícone de atualização no gráfico para recalcular imediatamente. (Atualizar do browser não recalcula.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Ajustar um mosaico no dashboard
Depois de um mosaico no dashboard, pode ajustá-lo.

![Coloque o cursor sobre um gráfico para editá-lo.](./media/app-insights-dashboards/36.png)

1. Adicione um gráfico para o mosaico.
2. Defina a métrica, a dimensão de grupo e o estilo (tabela, gráfico) de um gráfico.
3. Arraste através do diagrama como ampliar; Clique no botão de anulação para repor o período de tempo; definir propriedades de filtro para os gráficos no mosaico.
4. Definir o título do mosaico.

Os mosaicos afixados a partir de painéis de métrica explorer têm mais opções de edição de mosaicos afixados a partir de um painel de descrição geral.

O mosaico original que afixou não é afetado as suas edições.

## <a name="switch-between-dashboards"></a>Alternar entre dashboards
Pode guardar mais do que um dashboard e alternar entre elas. Quando afixar um gráfico ou um painel, que está a adicionados ao dashboard atual.

![Para alternar entre os dashboards, clique em Dashboard e selecione um dashboard guardado. Para criar e guardar um novo dashboard, clique em novo. Para reorganizar, clique em Editar.](./media/app-insights-dashboards/32.png)

Por exemplo, pode ter um dashboard para apresentar o ecrã inteiro na sala de equipa e outra para o desenvolvimento geral.

No dashboard, é apresentado um painel como um mosaico: clique nele para ir para o painel. Um gráfico replica do gráfico na localização original.

![Clique num mosaico para abrir o painel representa](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Partilhar dashboards
Quando tiver criado um dashboard, pode partilhá-las com outros utilizadores.

![No cabeçalho do dashboard, clique em partilha](./media/app-insights-dashboards/41.png)

Saiba mais sobre [funções e controlo de acesso](app-insights-resources-roles-access-control.md).

## <a name="app-navigation"></a>Navegação de aplicação
O painel de descrição geral é o gateway para obter mais informações sobre a sua aplicação.

* **Qualquer mosaico ou gráfico** - clique em qualquer mosaico ou gráfico para ver mais detalhes sobre o que é apresentado.

### <a name="overview-blade-buttons"></a>Botões do painel de descrição geral
![Barra de navegação superior do painel de descrição geral](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Explorador de métricas** ](app-insights-metrics-explorer.md) -criar os seus próprios gráficos de desempenho e utilização.
* [**Pesquisa** ](app-insights-diagnostic-search.md) - investigar instâncias específicas de eventos, tais como pedidos, exceções, ou os rastreios de registo.
* [**Análise de** ](app-insights-analytics.md) -poderosas consultas ao longo da sua telemetria.
* **Intervalo de tempo** -ajustar o intervalo apresentado por todos os gráficos no painel.
* **Eliminar** -eliminar o recurso do Application Insights para esta aplicação. Deve também remova os pacotes do Application Insights do seu código de aplicação, ou editar o [chave de instrumentação](app-insights-create-new-resource.md#copy-the-instrumentation-key) na sua aplicação para direcionar a telemetria para um recurso do Application Insights diferentes.

### <a name="essentials-tab"></a>Separador Essentials
* [Chave de instrumentação](app-insights-create-new-resource.md#copy-the-instrumentation-key) -identifica este recurso de aplicação.
* Preços - tornar as funcionalidades caps de volume disponível e definida.

### <a name="app-navigation-bar"></a>Barra de navegação de aplicações
![Barra de navegação esquerdo](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Descrição geral** -regressar ao painel de descrição geral de aplicação.
* **Registo de atividade** -alertas e eventos administrativos do Azure.
* [**Controlo de acesso** ](app-insights-resources-roles-access-control.md) -fornecem acesso para os membros da equipa e outros.
* [**Etiquetas** ](../azure-resource-manager/resource-group-using-tags.md) -utilizar etiquetas para agrupar a sua aplicação com outras pessoas.

INVESTIGAR

* [**O mapeamento de aplicações** ](app-insights-app-map.md) -Active Directory mapa que mostra os componentes da aplicação, derivado as informações de dependência.
* [**Deteção de smart** ](app-insights-proactive-diagnostics.md) -rever os alertas de desempenho recentes.
* [**Transmissão de velocidade** ](app-insights-live-stream.md) - A fixa conjunto de métricas de perto instantâneas, úteis quando implementar uma nova compilação ou depuração.
* [**Disponibilidade / Web testes** ](app-insights-monitor-web-app-availability.md) -enviar pedidos regulares à sua aplicação web de volta a world.*
* [**Falhas de desempenho** ](app-insights-web-monitor-performance.md) -exceções, taxas de falha e tempos de resposta para pedidos para a sua aplicação e para pedidos a partir da sua aplicação para [dependências](app-insights-asp-net-dependencies.md).
* [**Desempenho** ](app-insights-web-monitor-performance.md) -tempo de resposta, os tempos de resposta de dependência.
* [Servidores](app-insights-web-monitor-performance.md) -contadores de desempenho. Se disponíveis, [instalar Monitor de estado](app-insights-monitor-performance-live-website-now.md).
* **Browser** -página vista e o desempenho de AJAX. Se disponíveis, [instrumentar suas páginas web](app-insights-javascript.md).
* **Utilização** -contagens de sessões, utilizador e vista de página. Se disponíveis, [instrumentar suas páginas web](app-insights-javascript.md).

CONFIGURAR

* **Introdução** -inline tutorial.
* **Propriedades** -chave de instrumentação, a subscrição e o id do recurso.
* [Alertas](app-insights-alerts.md) -configuração da métrica de alerta.
* [A exportação contínua](app-insights-export-telemetry.md) -configurar a exportação de telemetria para o armazenamento do Azure.
* [Teste de desempenho](app-insights-monitor-web-app-availability.md#performance-tests) -configurar uma carga sintética no seu Web site.
* [Quota e preços](app-insights-pricing.md) e [amostragem de ingestão](app-insights-sampling.md).
* **Acesso à API** -criar [versão anotações](app-insights-annotations.md) e para a API de acesso de dados.
* [**Itens de trabalho** ](app-insights-diagnostic-search.md#create-work-item) -ligar a um sistema de controlo, para que possa criar erros ao inspecionar a telemetria de trabalho.

DEFINIÇÕES

* [**Bloqueia** ](../azure-resource-manager/resource-group-lock-resources.md) -bloquear recursos do Azure
* [**Script de automatização** ](app-insights-powershell.md) -exportar uma definição do recurso do Azure para que possa utilizar como um modelo para criar novos recursos.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes

|  |  |
| --- | --- |
| [Explorador de métricas](app-insights-metrics-explorer.md)<br/>Métricas de filtro e segmento |![Exemplo de pesquisa](./media/app-insights-dashboards/64.png) |
| [Pesquisa de diagnóstico](app-insights-diagnostic-search.md)<br/>Localizar e Inspecione os eventos, eventos relacionados e criar erros |![Exemplo de pesquisa](./media/app-insights-dashboards/61.png) |
| [Análise](app-insights-analytics.md)<br/>Linguagem de consulta poderosa |![Exemplo de pesquisa](./media/app-insights-dashboards/63.png) |
