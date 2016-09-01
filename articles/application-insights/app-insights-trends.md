<properties
    pageTitle="Analisar Tendências no Visual Studio | Microsoft Azure"
    description="Analise, visualize e explore tendências na sua telemetria do Application Insights no Visual Studio."
    services="application-insights"
    documentationCenter=".net"
    authors="numberbycolors"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="daviste"/>

# Analisar tendências no Visual Studio

A ferramenta Application Insights Trends visualiza como eventos de telemetria importantes da sua aplicação são alterados ao longo do tempo, ajudando-o a identificar rapidamente problemas e anomalias. Ao ligá-lo a informações mais detalhadas de diagnóstico, o Trends pode ajudá-lo a melhorar o desempenho da aplicação, identificar as causas de exceções e descobrir conhecimentos aprofundados dos seus eventos personalizados.

![Janela de exemplo do Trends](./media/app-insights-trends/app-insights-trends-hero-750.png)

> [AZURE.NOTE] O Application Insights Trends está disponível no Visual Studio 2015 Update 3 e posterior, ou com a [extensão do Developer Analytics Tools](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) versão 5.209 e posterior.

## Abrir o Application Insights Trends

Pode abrir a janela Application Insights Trends das seguintes formas:

* No botão da barra de ferramentas do Application Insights, escolha **Explorar Tendências de Telemetria**.
* No menu de contexto do projeto, escolha **Application Insights > Explorar Tendências de Telemetria**.
* A partir da barra de menus do Visual Studio, escolha **Ver > Outras Janelas > Application Insights Trends**.

Poderá ver uma mensagem para selecionar um recurso. Se for o caso, clique em **Selecione um recurso**, inicie sessão com uma subscrição do Azure e, em seguida, escolha um recurso do Application Insights para o qual gostaria de analisar tendências de telemetria.

## Escolher uma análise de tendências

![Menu de tipos comuns de análise de tendências](./media/app-insights-trends/app-insights-trends-1-750.png)

Comece por escolher uma das cinco análises de tendência comuns, em que cada analisa os dados das últimas 24 horas:

* **Investigar problemas de desempenho com os seus pedidos de servidor**: pedidos efetuados ao seu serviço, agrupados por tempos de resposta
* **Analisar erros nos seus pedidos de servidor**: pedidos efetuados ao seu serviço, agrupados por códigos de resposta HTTP
* **Examinar as exceções na sua aplicação**: exceções do seu serviço, agrupadas por tipo de exceção
* **Verificar o desempenho das dependências da aplicação**: serviços chamados pelo seu serviço, agrupados por tempos de resposta
* **Inspecionar os eventos personalizados**: eventos personalizados que configurou para o seu serviço, agrupados por tipo de evento

Estas análises criadas previamente estão disponíveis mais tarde a partir do botão **Ver tipos comuns de análise de telemetria** no canto superior esquerdo da janela do Trends.

## Visualizar tendências na sua aplicação

O Application Insights Trends cria uma visualização de séries de tempo a partir da telemetria da sua aplicação. Cada visualização de séries de tempo apresenta um tipo de telemetria, agrupado por uma propriedade dessa telemetria, através de um intervalo de tempo específico.

Por exemplo, pode querer ver os pedidos do servidor, agrupados pelo país de origem, durante as últimas 24 horas. Neste exemplo, cada bolha na visualização representa uma contagem dos pedidos de servidor para um país específico durante uma hora.

Utilize os controlos na parte superior da janela para ajustar que tipos de telemetria pretende ver. Em primeiro lugar, escolha os tipos de telemetria nos quais está interessado:

* **Tipo de Telemetria**: pedidos de servidor, exceções, dependências ou eventos personalizados
* **Intervalo de Tempo**: em qualquer lugar nos últimos 30 minutos para os últimos 3 dias
* **Agrupar Por**: tipo de exceção, ID do problema, país/região e muito mais

Em seguida, clique em **Analisar Telemetria** para executar a consulta.

Para navegar entre bolhas na visualização:

* Clique para selecionar uma bolha, que atualiza os filtros na parte inferior da janela, resumindo os eventos que ocorreram durante um período de tempo específico.
* Faça duplo clique numa bolha para navegar para a ferramenta Pesquisa e ver todos os eventos de telemetria individuais que ocorreram durante esse período de tempo.
* Prima a tecla **Ctrl** e, em seguida, clique numa bolha para desmarcá-la na visualização.

> [AZURE.TIP] As ferramentas Tendências e Pesquisa ajudam-no a identificar os eventos de telemetria que estão a causar problemas no seu serviço. Por exemplo, se os seus clientes repararem que a aplicação está a ser menos reativa numa tarde, pode iniciar o processo de análise do problema com as Tendências. Analise os pedidos efetuados pelo seu serviço durante as últimas horas, agrupados por tempo de resposta. Ver se existe um cluster de excecionalmente grande de pedidos de lentos. Em seguida, faça duplo clique nessa bolha para ir para a ferramenta Pesquisa, que é filtrada para esses eventos de pedido. A partir da Pesquisa, pode explorar o conteúdo desses pedidos e localizar o código envolvido, para resolver o problema.

## Filtrar por tendências específicas

Detete tendências mais específicas com os controlos de filtro na parte inferior da janela. Para aplicar um filtro, clique no respetivo nome. Pode alternar rapidamente entre diferentes filtros para detetar as tendências que possam estar ocultas numa dimensão específica da sua telemetria. Se aplicar um filtro a uma dimensão, como a dimensão Tipo de Exceção, os filtros noutras dimensões permanecem clicáveis, mesmo que pareçam estar desativados. Para anular a aplicação de um filtro, clique nele novamente. Prima a tecla **Ctrl** e, em seguida, clique para selecionar vários filtros na mesma dimensão.

![Filtros de tendência](./media/app-insights-trends/TrendsFiltering-750.png)

E se pretende aplicar vários filtros?

1. Aplique o primeiro filtro.
2. Clique no botão **Aplicar filtros selecionados e consultar novamente** pelo nome da dimensão do primeiro filtro. Isto irá efetuar uma nova consulta de telemetria especificamente para os eventos que correspondem ao primeiro filtro.
3. Aplica um segundo filtro.
4. Repita o processo para localizar tendências em subconjuntos específicos da sua telemetria. Por exemplo, pode consultar os pedidos de servidor com o nome “GET Home/Index” que são provenientes da Alemanha e que receberam o código de estado 500.

Para anular a aplicação de um destes filtros, clique no botão **Remover filtros selecionados e consultar novamente** da dimensão.

![Vários filtros](./media/app-insights-trends/TrendsFiltering2-750.png)

## Localizar anomalias

A ferramenta Trends pode realçar bolhas de eventos que são anómalos em comparação com outras bolhas na mesma série de tempo. No menu pendente **Tipo de Vista**, selecione **Contagens no registo de tempo (realçar anomalias)** ou **Percentagens no registo de tempo (realçar anomalias)**. As bolhas vermelhas são anómalas.

As anomalias são definidas como bolhas com contagens/percentagens que excedem 2,1 vezes o desvio padrão das contagens/percentagens ocorridas nos dois últimos períodos de tempo (por exemplo, 48 horas se estiver a ver as últimas 24 horas).

![Os pontos coloridos indicam anomalias](./media/app-insights-trends/TrendsAnomalies-750.png)

> [AZURE.TIP] O processo de realce de anomalias pode ser especialmente útil para encontrar valores atípicos na série de tempo de bolhas pequenas que, de outro modo, poderiam ter um tamanho semelhante.  

## <a name="next"></a>Passos seguintes


[Trabalhar com o Application Insights no Visual Studio](app-insights-visual-studio.md): pesquise telemetria, consulte dados em CodeLens e configure o Application Insights, tudo dentro do Visual Studio.

[Adicionar mais dados](app-insights-asp-net-more.md): monitorize a utilização, a disponibilidade, as dependências e as exceções. Integre rastreios a partir de arquiteturas de registo. Grave a telemetria personalizada.

[Trabalhar com o portal do Application Insights](app-insights-dashboards.md): dashboards, ferramentas de diagnóstico e análise poderosas, alertas, mapa de dependência em direto da aplicação e exportação de telemetria.



<!--HONumber=ago16_HO4-->


