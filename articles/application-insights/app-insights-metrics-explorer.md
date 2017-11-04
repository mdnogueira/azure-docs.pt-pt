---
title: "Explorar métricas no Azure Application Insights | Microsoft Docs"
description: "Como interpretar os gráficos no Explorador de métrica e como personalizar painéis de métrica explorer."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: mbullwin
ms.openlocfilehash: 01b45323b74b54da157f4e9f1af783759c121be1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="exploring-metrics-in-application-insights"></a>Explorar métricas no Application Insights
As métricas no [Application Insights] [ start] são medidos valores e contagens de eventos que são enviados na telemetria da sua aplicação. Ajudam a detetar problemas de desempenho e veja as tendências na forma como a aplicação está a ser utilizada. Há uma vasta gama de métricas padrão e também pode criar os seus próprios métricas e eventos personalizados.

Contagens de métricas e eventos são apresentadas nos gráficos de valores agregados, tais como somas, médias ou contagens.

Eis um conjunto de exemplo de gráficos:

![](./media/app-insights-metrics-explorer/01-overview.png)

Localizar em qualquer lugar os gráficos de métricas no portal do Application Insights. Na maioria dos casos, podem ser personalizadas e pode adicionar mais gráficos para o painel. No painel de descrição geral, clique em através de gráficos mais detalhados (que tem títulos, tais como "Servidores") ou clique em **Explorador de métricas** para abrir um novo painel onde pode criar gráficos personalizados.

## <a name="time-range"></a>Intervalo de tempo
Pode alterar o intervalo de tempo abrangido pelo gráficos ou grelhas em qualquer painel.

![Abra o painel de descrição geral da sua aplicação no portal do Azure](./media/app-insights-metrics-explorer/03-range.png)

Se está à espera de alguns dados que não apareceu ainda, clique em Atualizar. Os gráficos atualizam-se em intervalos, mas os intervalos são mais para intervalos de tempo maior. -Pode demorar algum tempo para dados fique através do pipeline de análise para um gráfico.

Para aplicar zoom no parte de um gráfico, arraste sobre:

![Arraste por parte de um gráfico.](./media/app-insights-metrics-explorer/12-drag.png)

Clique no botão Anular Zoom para o restaurar.

## <a name="granularity-and-point-values"></a>Valores de granularidade e o ponto de
Paire o rato sobre o gráfico para apresentar os valores das métricas nesse momento.

![Paire o rato sobre um gráfico](./media/app-insights-metrics-explorer/02-focus.png)

O valor da métrica num determinado ponto é agregado ao longo do intervalo de amostra anterior.

O intervalo de amostragem ou "granularidade" é apresentada na parte superior do painel.

![O cabeçalho de um painel.](./media/app-insights-metrics-explorer/11-grain.png)

Pode ajustar a granularidade no painel de intervalo de tempo:

![O cabeçalho de um painel.](./media/app-insights-metrics-explorer/grain.png)

Granularidades disponíveis dependem do intervalo de tempo selecionado. As granularidades explícitas são alternativas para a granularidade "automática" para o intervalo de tempo.


## <a name="editing-charts-and-grids"></a>Editar gráficos e grelhas
Para adicionar um novo gráfico para o painel:

![No Explorador de métricas, escolha adicionar gráfico](./media/app-insights-metrics-explorer/04-add.png)

Selecione **editar** num gráfico novo ou existente para editar o que é mostrado:

![Selecione uma ou mais métricas](./media/app-insights-metrics-explorer/08-select.png)

Pode apresentar mais do que uma métrica num gráfico, apesar de existirem restrições sobre as combinações que podem ser apresentadas em conjunto. Assim que escolher uma métrica, alguns dos outros estão desativadas.

Se lhe coded [métricas personalizadas] [ track] na sua aplicação (chamadas para TrackMetric e TrackEvent) estes serão apresentados aqui.

## <a name="segment-your-data"></a>Segmentar os seus dados
Pode dividir uma métrica pela propriedade - por exemplo, para comparar as vistas de página nos clientes com sistemas operativos diferentes.

Selecione um gráfico ou grelha, mude no agrupamento e escolha uma propriedade pela qual agrupar:

![Selecione agrupamento no conjunto de selecionar uma propriedade no Group By](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> Quando utilizar o agrupamento, os tipos de área e gráfico de barras fornecem uma apresentação empilhada. Esta é adequada em que o método de agregação Sum. Mas, em que o tipo de agregação médio, escolha os tipos de visualização de linha ou grelha.
>
>

Se lhe coded [métricas personalizadas] [ track] na sua aplicação e incluem os valores de propriedade, poderá selecionar a propriedade na lista.

O gráfico é demasiado pequeno para dados segmentados? Ajuste a altura:

![Ajustar o controlo de deslize](./media/app-insights-metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Tipos de agregação
A legenda do lado por predefinição, normalmente, mostra o valor agregado durante o período do gráfico. Se paira o rato sobre o gráfico, mostra o valor nesse momento.

Cada ponto de dados no gráfico é um agregado dos valores de dados recebidos no intervalo de amostragem anterior ou "granularidade". A granularidade é apresentada na parte superior do painel e varia de acordo com a escala temporal geral do gráfico.

Podem ser agregadas métricas formas diferentes:

* **Contagem** é uma contagem dos eventos recebidos no intervalo de amostragem. É utilizado para eventos, tais como pedidos. Variações na altura do gráfico indica variações na frequência com que os eventos. Mas tenha em atenção que o valor numérico é alterado quando alterar o intervalo de amostragem.
* **Soma** adiciona segurança os valores de todos os pontos de dados recebidos ao longo do intervalo de amostragem, ou o período do gráfico.
* **Médio** divide a soma pelo número de pontos de dados recebidos ao longo do intervalo.
* **Exclusivo** contagens são utilizadas para contagens de utilizadores e contas. Ao longo do intervalo de amostragem, ou durante o período do gráfico, a ilustração mostra a contagem de utilizadores diferentes, é apresentada no tempo.
* **%**-versões de percentagem de cada agregação são utilizadas apenas com gráficos segmentados. O total sempre adiciona até 100%, e o gráfico mostra a contribuição relativa de diferentes componentes de um total.

    ![Agregação de percentagem](./media/app-insights-metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Alterar o tipo de agregação

![Edite o gráfico e, em seguida, selecione agregação](./media/app-insights-metrics-explorer/05-aggregation.png)

O método predefinido para cada métrica é apresentado ao criar um novo gráfico ou quando as métricas são desmarcadas:

![Anular seleção de todas as métricas para ver as predefinições](./media/app-insights-metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Eixo y de PIN 
Por predefinição, um gráfico mostra valores do eixo Y a partir de zero até os valores máximos no intervalo de dados, para lhe dar uma representação visual de quantum dos valores. Mas, em alguns casos mais do que o quantum poderá ser interessante visualmente inspecionar pequenas alterações nos valores. Para personalizações como esta utilização no eixo y intervalo funcionalidade edição para afixar o valor de mínimo ou máximo do eixo y no local pretendido.
Clique na caixa de verificação "Definições avançadas" para fazer aparecer o intervalo do eixo y definições

![Clique em definições avançadas, selecione o intervalo personalizado e especifique os valores máximo de mín.](./media/app-insights-metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtre os dados
Para ver apenas as métricas de um conjunto de valores de propriedade selecionado:

![Clicar em filtro, expanda uma propriedade e alguns valores de verificação](./media/app-insights-metrics-explorer/19-filter.png)

Se não selecionar quaisquer valores para uma propriedade específica, é o mesmo que selecionar todos eles: não há nenhum filtro nessa propriedade.

Tenha em atenção o número de eventos em conjunto com a cada valor de propriedade. Quando seleciona os valores de uma propriedade, o número de juntamente com outros valores de propriedade é ajustado.

Os filtros aplicam a todos os gráficos num painel. Se quiser diferentes filtros aplicados a vários gráficos, crie e guarde painéis de métricas diferentes. Se quiser, pode afixar gráficos de painéis diferentes para o dashboard, para que possa vê-los em conjunto com entre si.

### <a name="remove-bot-and-web-test-traffic"></a>Remova o tráfego de teste web e bot
Utilize o filtro **tráfego Real ou sintético** e verifique **Real**.

Também pode filtrar por **origem do tráfego sintético**.

### <a name="to-add-properties-to-the-filter-list"></a>Para adicionar propriedades para a lista de filtros
Gostaria de telemetria sobre uma categoria à sua escolha de filtro? Por exemplo, talvez dividir os seus utilizadores em diferentes categorias de cópia de segurança e gostaria de segmentar os seus dados por estas categorias.

[Criar a sua própria propriedade](app-insights-api-custom-events-metrics.md#properties). Defini-lo num [inicializador de telemetria](app-insights-api-custom-events-metrics.md#defaults) que são apresentados em toda a telemetria - incluindo padrão telemetria enviada pelo diferentes módulos SDK.

## <a name="edit-the-chart-type"></a>Editar o tipo de gráfico
Tenha em atenção que pode alternar entre grelhas e gráficos:

![Selecione uma grelha ou gráfico, em seguida, escolha um tipo de gráfico](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Guardar o painel de métricas
Quando criou algumas gráficos, guardá-los como um favorito. Pode escolher se pretende partilhar com outros membros do agrupamento, se utilizar uma conta institucional.

![Escolha o favorito](./media/app-insights-metrics-explorer/21-favorite-save.png)

Para ver o painel novamente, **aceda ao painel de descrição geral** e abra favoritos:

![No painel de descrição geral, escolha Favoritos](./media/app-insights-metrics-explorer/22-favorite-get.png)

Se tiver escolhido o intervalo de tempo relativo quando é guardado, o painel será atualizado com as métricas mais recentes. Se tiver escolhido o intervalo de tempo absoluto, esta operação irá mostrar os mesmos dados sempre.

## <a name="reset-the-blade"></a>Repor o painel
Se editar um painel, mas, em seguida, gostaria de voltar a guardar o conjunto original, basta clicar reposição.

![Nos botões no topo do Explorador de métrica](./media/app-insights-metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Transmissão de métricas em direto

Para obter uma vista muito mais imediata da sua telemetria, abra [em fluxo em direto](app-insights-live-stream.md). A maioria das métricas demorar alguns minutos a aparecer devido ao processo de agregação. Por outro lado, as métricas em direto estão otimizadas para latência baixa. 

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por correio eletrónico dos valores invulgares de qualquer métrica, adicione um alerta. Pode escolher a enviar o e-mail para os administradores de conta ou para endereços de e-mail específico.

![No Explorador de métricas, escolher regras de alertas, adicionar alerta](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[Saiba mais sobre alertas][alerts].


## <a name="continuous-export"></a>Exportação Contínua
Se pretender que os dados exportados continuamente para que possa processar externamente, considere utilizar [a exportação contínua](app-insights-export-telemetry.md).

### <a name="power-bi"></a>Power BI
Se quiser vistas ricas dos seus dados, pode [exportar para o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Análise
[Análise de](app-insights-analytics.md) é uma forma mais versátil para analisar a telemetria utilizando uma linguagem de consulta poderosa. Utilizá-lo se pretender combinar os resultados de métricas de computação ou para efetuar uma exploração aprofundada de desempenho recente da aplicação. 

A partir de um gráfico de métrico, pode clicar no ícone de análise para obter diretamente para a consulta de análise equivalente.

## <a name="troubleshooting"></a>Resolução de problemas
*Não vejo todos os dados no meu gráfico.*

* Os filtros aplicam a todos os gráficos no painel. Certifique-se de que, enquanto estiver a concentrar-se no uma gráfico, não foi definido um filtro que exclua todos os dados de outra.

    Se pretender definir filtros diferentes no gráficos diferentes, crie-a num outros painéis, guardá-las como separados Favoritos. Se quiser, pode afixá-los para o dashboard para que possa vê-los em conjunto com entre si.
* Se o grupo um gráfico por uma propriedade que não está definida na métrica, em seguida, será nada no gráfico. Tente limpar "group by", ou escolha uma propriedade de diferentes de agrupamento.
* Dados de desempenho (CPU, velocidade de e/s e assim sucessivamente) está disponível para os serviços web de Java, aplicações de ambiente de trabalho do Windows, [web IIS aplicações e serviços se instalar o monitor de estado](app-insights-monitor-performance-live-website-now.md), e [Cloud Services do Azure](app-insights-azure.md). Não se encontra disponível para Web sites do Azure.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
* [Utilização de monitorização com o Application Insights](app-insights-web-track-usage.md)
* [Utilizando a pesquisa de diagnóstico](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
