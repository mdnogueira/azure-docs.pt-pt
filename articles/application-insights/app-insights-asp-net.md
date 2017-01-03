---
title: "Configurar a análise da aplicação Web do ASP.NET com o Application Insights | Microsoft Docs"
description: "Configure a análise de desempenho, disponibilidade e utilização do seu site ASP.NET, alojado no local ou no Azure."
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dc95c922b71d18cf791ea98f4ab1a02d2bac2c3b
ms.openlocfilehash: 5103c28047e6d5e7be5f4f3b7933196de7045eeb


---
# <a name="set-up-application-insights-for-aspnet"></a>Configurar o Application Insights para o ASP.NET
O [Azure Application Insights](app-insights-overview.md) monitoriza sua a aplicação em direto para o ajudar a [detetar e diagnosticar problemas de desempenho e exceções](app-insights-detect-triage-diagnose.md), bem como a [detetar como a aplicação é utilizada](app-insights-overview-usage.md).  Funciona para as aplicações que estão alojadas nos seus próprios servidores IIS no local ou em VMs na nuvem, bem como em aplicações Web do Azure.

## <a name="before-you-start"></a>Antes de começar
É necessário:

* Visual Studio 2013 atualização 3 ou posterior. É preferível a versão posterior.
* Uma subscrição do [Microsoft Azure](http://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo à mesma, utilizando a sua [conta Microsoft](http://live.com). 

Existem artigos alternativos para ver se estiver interessado:

* [Instrumentar aplicações Web no tempo de execução](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud services (Serviços em Nuvem do Azure)](app-insights-cloudservices.md)

## <a name="a-nameidea-1-add-application-insights-sdk"></a><a name="ide"></a> 1. Adicionar o Application Insights SDK
### <a name="if-its-a-new-project"></a>Se for um novo projeto...
Certifique-se de que a opção Application Insights está selecionada quando criar um novo projeto no Visual Studio. 

![Criar um projeto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### <a name="-or-if-its-an-existing-project"></a>... ou se for um projeto existente
Clique com o botão direito do rato no Explorador de Soluções e escolha **Adicionar Telemetria do Application Insights** ou **Configurar o Application Insights**.

![Escolher Adicionar o Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* Projeto em ASP.NET Core? - [Siga estas instruções para corrigir algumas linhas de código](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 

## <a name="a-nameruna-2-run-your-app"></a><a name="run"></a> 2. Executar a aplicação
Execute a aplicação com F5 e experimente-a: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, vê uma contagem dos eventos que foram registados. 

![No Visual Studio, o botão do Application Insights é apresentado durante a depuração.](./media/app-insights-asp-net/54.png)

## <a name="3-see-your-telemetry"></a>3. Ver a telemetria...
### <a name="-in-visual-studio"></a>... no Visual Studio
Abra a janela do Application Insights no Visual Studio: clique no botão Application Insights ou clique com o botão direito do rato no projeto no Explorador de Soluções:

![No Visual Studio, o botão do Application Insights é apresentado durante a depuração.](./media/app-insights-asp-net/55.png)

Esta vista ("Dados da sessão de depuração") mostra a telemetria gerada do lado do servidor da sua aplicação. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

* *Não existem dados? Certifique-se do que intervalo de tempo está correto e clique no ícone de pesquisa.*

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a> 

### <a name="-in-the-portal"></a>... no portal
A não ser que tenha escolhido *Instalar apenas SDK*, também pode ver a telemetria no portal Web do Application Insights. 

O portal tem mais gráficos, ferramentas de análise e dashboards que o Visual Studio. 

Abra o recurso do Application Insights - inicie sessão no [portal do Azure](https://portal.azure.com/) e encontre-o aí ou clique com o botão direito no projeto no Visual Studio e permita que o mesmo o redirecione.

![Clique com o botão direito do rato no projeto e abra o portal do Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

* *Erro de acesso? Se tiver mais do que um conjunto de credenciais da Microsoft, a sessão poderá ser iniciada com o conjunto de errado. No portal, termine a sessão e inicie sessão novamente.*

O portal é aberto numa vista de telemetria na sua aplicação: ![página de Descrição Geral do Application Insights](./media/app-insights-asp-net/66.png)

Clique em qualquer gráfico ou mosaico para ver mais detalhes.

### <a name="more-detail-in-the-portal"></a>Mais detalhe no portal

* O [**Fluxo de métricas do Live** ](app-insights-metrics-explorer.md#live-metrics-stream) apresenta telemetria quase instantaneamente.

    ![No painel de Descrição geral, clique em Live Stream](./media/app-insights-asp-net/livestream.png)

    Abrir o Live Stream ao mesmo tempo que a aplicação está em execução, para permitir que os mesmos se liguem.

    O Live Stream só mostra telemetria durante um minuto após ter sido enviada. Para mais investigações históricas, utilize a Pesquisa, o Explorador de Métricas e o Analytics. Podem ser necessários alguns minutos para que os dados sejam apresentados nestes locais.

* A [**Pesquisa**](app-insights-diagnostic-search.md) mostra eventos individuais, tais como pedidos, exceções e vistas de página. Pode filtrar por tipo de evento, correspondência de termos e valores de propriedade. Clique em qualquer evento para ver as respetivas propriedades e eventos relacionados. 

    ![No painel de Descrição geral, clique em Pesquisa](./media/app-insights-asp-net/search.png)

 * No modo de desenvolvimento, poderá ver uma grande quantidade de eventos de dependência (AJAX). Estas são as sincronizações entre o browser e o emulador de servidor. Para ocultá-las, clique no filtro Dependência.
* [**Métricas agregadas** ](app-insights-metrics-explorer.md) como taxas de pedido e de falha são apresentadas nos gráficos. Clique em qualquer gráfico para abrir um painel com mais detalhes. Clique na etiqueta **Editar** de qualquer gráfico para definir filtros, tamanho e assim sucessivamente.
    
    ![No painel Descrição geral, clique em qualquer gráfico](./media/app-insights-asp-net/metrics.png)

[Saiba mais sobre como utilizar o Application Insights no portal do Azure](app-insights-dashboards.md).

## <a name="4-publish-your-app"></a>4. Publicar a aplicação
Publique a sua aplicação no seu servidor IIS ou no Azure. Veja o [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) para garantir que não existem problemas.

Irá ver a telemetria a acumular-se no portal Application Insights, onde pode monitorizar métricas, procurar a sua telemetria e configurar [dashboards](app-insights-dashboards.md). Também pode utilizar o [Idioma de consulta Analytics](app-insights-analytics.md) avançado para analisar a utilização e o desempenho ou procurar eventos específicos. 

Também pode continuar a analisar a sua telemetria no [Visual Studio](app-insights-visual-studio.md) com ferramentas como a pesquisa de diagnóstico e as [Tendências](app-insights-visual-studio-trends.md).

> [!NOTE]
> Se a sua aplicação enviar telemetria suficiente para se aproximar dos [limites de monitorização](app-insights-pricing.md#limits-summary), é ativada a [amostragem](app-insights-sampling.md) automática. A amostragem reduz a quantidade de telemetria enviada pela sua aplicação, conservando os dados correlacionados para efeitos de diagnóstico.
> 
> 

## <a name="a-namelanda-what-did-add-application-insights-do"></a><a name="land"></a> O que fez “Adicionar o Application Insights”?
O Application Insights envia a telemetria da sua aplicação para o portal do Application Insights (que está alojado no Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Por isso, o comando faz três ações:

1. Adiciona o pacote Application Insights Web SDK NuGet ao seu projeto. Para vê-lo no Visual Studio, clique com o botão direito do rato no projeto e selecione Gerir Pacotes NuGet.
2. Cria um recurso do Application Insights no [portal do Azure](https://portal.azure.com/). É aqui que verá os seus dados. Obtém a *chave de instrumentação*, a qual identifica o recurso.
3. Insere a chave de instrumentação em `ApplicationInsights.config`, para que o SDK possa enviar a telemetria para o portal.

Se quiser, pode executar estes passos manualmente para [ASP.NET 4](app-insights-windows-services.md) ou [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### <a name="to-upgrade-to-future-sdk-versions"></a>Para atualizar para versões futuras do SDK
Para atualizar para uma [nova versão do SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), abra o gestor de pacotes NuGet novamente e filtre os pacotes instalados. Selecione Microsoft.ApplicationInsights.Web e escolha Atualizar.

Se tiver efetuado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar e, posteriormente, intercale as alterações na nova versão.

## <a name="add-more-telemetry"></a>Adicionar mais telemetria
### <a name="web-pages-and-single-page-apps"></a>Páginas Web e aplicações de página única
1. [Adicione o fragmento do JavaScript](app-insights-javascript.md) às suas páginas Web para animar os painéis Browser e Utilização com dados sobre vistas de página, tempos de carregamento, exceções do browser, desempenho de chamadas do AJAX, contagens de utilizadores e de sessões.
2. [Eventos personalizados de código](app-insights-api-custom-events-metrics.md) para contagem, hora ou ações de utilizador de medidas.

### <a name="dependencies-exceptions-and-performance-counters"></a>Dependências, exceções e contadores de desempenho
[Instale o Monitor de Estado](app-insights-monitor-performance-live-website-now.md) em cada um dos computadores do servidor para obter telemetria adicional sobre a sua aplicação. Eis o que obtém:

* [Contadores de desempenho](app-insights-performance-counters.md) - 
  ontadores da CPU, de memória, de disco e outros contadores de desempenho relacionados com a sua aplicação. 
* [Exceções](app-insights-asp-net-exceptions.md) – telemetria mais detalhada para algumas exceções.
* [Dependências](app-insights-asp-net-dependencies.md) – chamadas para serviços do SQL ou da API REST. Descubra se as respostas lentas por componentes externos estão a causar problemas de desempenho na sua aplicação. (Se a aplicação for executada no .NET 4.6, não precisará do Monitor de Estado para obter esta telemetria.)

### <a name="diagnostic-code"></a>Código de diagnóstico
Tem algum problema? Se pretender inserir código na sua aplicação para o ajudar a diagnosticá-lo, tem várias opções:

* [Capturar rastreios de registos](app-insights-asp-net-trace-logs.md): se já utilizar o Log4N, NLog ou System.Diagnostics.Trace para registar eventos de rastreio, o resultado poderá ser enviado para o Application Insights para que possa correlacioná-lo com pedidos, pesquisá-lo e analisá-lo. 
* [Métricas e eventos personalizados](app-insights-api-custom-events-metrics.md): utilize TrackEvent() e TrackMetric() no servidor ou no código de página Web.
* [Etiquetar telemetria com propriedades adicionais](app-insights-api-filtering-sampling.md#add-properties)

Utilize [Pesquisar](app-insights-diagnostic-search.md) para localizar e correlacionar eventos específicos e o [Analytics](app-insights-analytics.md) para fazer consultas mais potentes.

## <a name="alerts"></a>Alertas
Seja o primeiro a saber se a sua aplicação tem problemas. (Não espere até que os utilizadores lhe digam!) 

* [Crie testes Web](app-insights-monitor-web-app-availability.md) para garantir que o site está visível na Web.
* Os [diagnósticos proativos](app-insights-proactive-diagnostics.md) são automaticamente executados (se a sua aplicação tiver um determinada quantia mínima de tráfego). Não precisa de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Defina alertas métricos](app-insights-alerts.md) para o avisar se uma métrica ultrapassa um limiar. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.

Por predefinição, as notificações de alerta são enviadas para o proprietário da subscrição do Azure. 

![exemplo de e-mail de alerta](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>Versão e controlo de versão
### <a name="track-application-version"></a>Versão da aplicação de controlo
Verifique se `buildinfo.config` é gerado pelo processo do MSBuild. No ficheiro .csproj, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Quando possui informações de compilação, o módulo Web do Application Insights adiciona automaticamente a **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Desta forma, poderá filtrar por versão ao executar [pesquisas de diagnóstico](app-insights-diagnostic-search.md) ou quando [explorar métricas](app-insights-metrics-explorer.md). 

No entanto, tenha em atenção que o número de versão da compilação é gerado apenas pelo MS Build, não pela compilação do programador no Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se utilizar o Visual Studio Team Services, poderá [obter um marcador de anotação](app-insights-annotations.md) adicionado aos seus gráficos sempre que lançar uma nova versão.

![exemplo de anotação de versão](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>Passos seguintes
|  |
| --- | --- |
| **[Trabalhar com o Application Insights no Visual Studio](app-insights-visual-studio.md)**<br/>Depuração com telemetria, pesquisa de diagnóstico e exploração do código. |
| **[Trabalhar com o portal do Application Insights](app-insights-dashboards.md)**<br/>Dashboards, ferramentas de diagnóstico e análise poderosas, alertas, mapa de dependência em direto da aplicação e exportação de telemetria. |
| **[Adicionar mais dados](app-insights-asp-net-more.md)**<br/>Monitorize a utilização, a disponibilidade, as dependências e as exceções. Integre rastreios a partir de arquiteturas de registo. Grave a telemetria personalizada. |




<!--HONumber=Dec16_HO5-->


