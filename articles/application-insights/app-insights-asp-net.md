---
title: "Configurar a análise de aplicações Web para ASP.NET com o Azure Application Insights | Microsoft Docs"
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
ms.sourcegitcommit: 13ff55ff06d0afd5e29ce85e7cc8e1c7e0a12a6c
ms.openlocfilehash: b2e82cffa9c258d99dc26346851ebfc016d09839


---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurar o Application Insights para o seu site ASP.NET
O [Azure Application Insights](app-insights-overview.md) monitoriza sua a aplicação em direto para o ajudar a [detetar e diagnosticar problemas de desempenho e exceções](app-insights-detect-triage-diagnose.md). Também o ajuda a [detetar como a aplicação é utilizada](app-insights-overview-usage.md). Funciona para a funcionalidade Aplicações Web do Serviço de Aplicações do Azure, bem como para as aplicações que estão alojadas nos seus próprios servidores IIS no local ou em VMs na cloud.

## <a name="before-you-start"></a>Antes de começar
É necessário:

* Visual Studio 2013 atualização 3 ou posterior. É preferível a versão posterior.
* Uma subscrição do [Microsoft Azure](http://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo à mesma através da sua [conta Microsoft](http://live.com).

Existem tópicos alternativos que pode ver se estiver interessado em:

* [Instrumentar aplicações Web no runtime](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="a-nameidea-step-1-add-the-application-insights-sdk"></a><a name="ide"></a> Passo 1: Adicionar o Application Insights SDK
Se for um projeto novo, certifique-se de que a opção Application Insights está selecionada quando criar um novo projeto no Visual Studio.

![Captura de ecrã de um novo projeto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

Se for um projeto existente, clique com o botão direito do rato no Explorador de Soluções e selecione **Adicionar Telemetria do Application Insights** ou **Configurar o Application Insights**.

![Captura de ecrã do Explorador de Soluções, com a opção Adicionar Telemetria do Application Insights realçada](./media/app-insights-asp-net/appinsights-03-addExisting.png)

> [!NOTE]
> Se estiver a trabalhar com um projeto do ASP.NET Core, [siga estas instruções para corrigir algumas linhas de código](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs).

## <a name="a-nameruna-step-2-run-your-app"></a><a name="run"></a> Passo 2: Executar a aplicação
Execute a aplicação com F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, vê uma contagem dos eventos que foram registados.

![Captura de ecrã do Visual Studio. O botão do Application Insights é apresentado durante a depuração.](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry-in-visual-studio-or-application-insights"></a>Passo 3: Ver a telemetria no Visual Studio ou no Application Insights
Pode ver a sua telemetria no Visual Studio ou no portal Web do Application Insights.

**No Visual Studio**, abra a janela do Application Insights. Clique no botão **Application Insights** ou clique com o botão direito do rato no seu projeto no Explorador de Soluções, selecione **Application Insights** e clique em **Procurar Telemetria em Direto**.

Na janela de Pesquisa do Visual Studio Application Insights, consulte a vista **Dados da sessão de Depuração** para ver a telemetria gerada no lado do servidor da aplicação. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

![Captura de ecrã da vista Dados da sessão de Depuração na janela do Application Insights.](./media/app-insights-asp-net/55.png)

> [!NOTE]
> Se não existirem quaisquer dados, certifique-se de que o intervalo de tempo está correto e clique no ícone de Pesquisa.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="the-application-insights-web-portal"></a>Portal Web do Application Insights
Também pode ver a telemetria **no portal Web do Application Insights**, a não ser que tenha optado por instalar apenas o SDK. O portal tem mais gráficos, ferramentas de análise e dashboards do que o Visual Studio.

Abra o recurso do Application Insights. Inicie sessão no [portal do Azure](https://portal.azure.com/) e encontre-o aí ou clique com o botão direito do rato no projeto no Visual Studio e permita que o mesmo o redirecione.

![Captura de ecrã do Visual Studio que mostra como abrir o portal do Application Insights](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> Se receber um erro de acesso, é possível que tenha mais do que um conjunto de credenciais da Microsoft, pelo que a sessão poderá ser iniciada com o conjunto errado. No portal, termine a sessão e inicie sessão novamente.

O portal é aberto na vista da telemetria da sua aplicação.
![Captura de ecrã da página de descrição geral do Application Insights](./media/app-insights-asp-net/66.png)

Clique em qualquer gráfico ou mosaico para ver mais detalhes.

### <a name="more-details-in-the-application-insights-web-portal"></a>Mais detalhes no portal Web do Application Insights
Aqui estão alguns exemplos de como o portal lhe fornece mais detalhes.

* O [**Fluxo de métricas do Live** ](app-insights-live-stream.md) apresenta telemetria quase instantaneamente.

    ![Captura de ecrã do portal. No painel Descrição Geral, clique em Live Stream.](./media/app-insights-asp-net/livestream.png)

    Abra o Live Metrics Stream ao mesmo tempo enquanto a aplicação está em execução para permitir que os mesmos se liguem.

    O Live Metrics Stream só mostra telemetria durante um minuto após ter sido enviada. Para mais investigações históricas, utilize a Pesquisa, o Explorador de Métricas e o Analytics. Podem ser necessários alguns minutos para que os dados sejam apresentados nestes locais.

* A [**Pesquisa**](app-insights-diagnostic-search.md) mostra eventos individuais, tais como pedidos, exceções e vistas de página. Pode filtrar por tipo de evento, correspondência de termos e valores de propriedade. Clique em qualquer evento para ver as respetivas propriedades e eventos relacionados.

    ![Captura de ecrã do portal. No painel Descrição Geral, clique em Pesquisa.](./media/app-insights-asp-net/search.png)

 * No modo de desenvolvimento, poderá ver uma grande quantidade de eventos de dependência (AJAX). Estas são as sincronizações entre o browser e o emulador de servidor. Para ocultá-las, clique no filtro **Dependência**.
* [**Métricas agregadas** ](app-insights-metrics-explorer.md) como taxas de pedido e de falha são apresentadas nos gráficos. Clique em qualquer gráfico para abrir um painel com mais detalhes. Clique na etiqueta **Editar** de qualquer gráfico para definir filtros e o tamanho.

    ![Captura de ecrã do painel de métricas agregadas disponível no portal](./media/app-insights-asp-net/metrics.png)

[Saiba mais sobre como utilizar o Application Insights no portal do Azure](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Passo 4: Publicar a aplicação
Publique a sua aplicação no seu servidor IIS ou no Azure. Veja o [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) para garantir que não existem problemas.

A telemetria acumula-se no portal do Application Insights, onde pode monitorizar métricas, pesquisar a sua telemetria e configurar [dashboards](app-insights-dashboards.md). Também pode utilizar a avançada [Linguagem de consulta do Analytics](app-insights-analytics.md) para analisar a utilização e o desempenho ou para encontrar eventos específicos.

Também pode continuar a analisar a sua telemetria no [Visual Studio](app-insights-visual-studio.md) com ferramentas como a pesquisa de diagnóstico e as [tendências](app-insights-visual-studio-trends.md).

> [!NOTE]
> Se a sua aplicação enviar telemetria suficiente para se aproximar dos [limites de monitorização](app-insights-pricing.md#limits-summary), é ativada a [amostragem](app-insights-sampling.md) automática. A amostragem reduz a quantidade de telemetria enviada pela sua aplicação, conservando os dados correlacionados para efeitos de diagnóstico.
>
>

## <a name="a-namelanda-what-does-the-add-application-insights-command-do"></a><a name="land"></a> O que faz o comando Adicionar o Application Insights?
O Application Insights envia a telemetria da sua aplicação para o portal do Application Insights (que está alojado no Azure).

![Diagrama do movimento da telemetria](./media/app-insights-asp-net/01-scheme.png)

Desta forma, o comando faz três ações:

1. Adiciona o pacote Application Insights Web SDK NuGet ao seu projeto. Para vê-lo no Visual Studio, clique com o botão direito do rato no seu projeto e selecione **Gerir Pacotes NuGet**.
2. Cria um recurso do Application Insights no [portal do Azure](https://portal.azure.com/). É aqui que verá os seus dados. Obtém a *chave de instrumentação*, a qual identifica o recurso.
3. Insere a chave de instrumentação em `ApplicationInsights.config`, para que o SDK possa enviar a telemetria para o portal.

Se quiser, pode executar estes passos manualmente para [ASP.NET 4](app-insights-windows-services.md) ou [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### <a name="upgrade-to-future-sdk-versions"></a>Atualizar para versões futuras do SDK
Para atualizar para uma [nova versão do SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), abra o **gestor de pacotes NuGet** novamente e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se tiver realizado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar. Em seguida, intercale as suas alterações na nova versão.

## <a name="add-more-telemetry"></a>Adicionar mais telemetria
Seguem-se outros tipos de telemetria que pode adicionar.
### <a name="dependencies-exceptions-and-performance-counters"></a>Dependências, exceções e contadores de desempenho

[Instale o monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648) em cada máquina do servidor IIS para obter telemetria adicional sobre as suas aplicações Web. Se já estiver instalado, não precisa de fazer nada. (Já poderá ter utilizado o monitor de estado para iniciar a monitorização de uma aplicação no runtime).

Com a utilização do monitor de estado para além do SDK do tempo de compilação, obtém um conjunto mais completo de telemetria que inclui:

* [Contadores de desempenho](app-insights-performance-counters.md): Contadores da CPU, de memória, de disco e outros contadores de desempenho relacionados com a sua aplicação.
* [Exceções](app-insights-asp-net-exceptions.md): Telemetria mais detalhada para algumas exceções.
* [Dependências](app-insights-asp-net-dependencies.md): Incluindo valores de retorno.

### <a name="webpages-and-single-page-apps"></a>Páginas Web e aplicações de página única
1. [Adicione o fragmento do JavaScript](app-insights-javascript.md) às suas páginas Web para mostrar dados sobre vistas de página, tempos de carregamento, exceções do browser, desempenho de chamadas do AJAX e contagens de utilizadores e de sessões. Estas informações são apresentadas nos painéis Browser e Utilização.
2. [Eventos personalizados de código](app-insights-api-custom-events-metrics.md) para contagem, hora ou ações de utilizador de medidas.


### <a name="diagnostic-code"></a>Código de diagnóstico
Tem algum problema? Se pretender inserir código na sua aplicação para o ajudar a diagnosticá-lo, tem várias opções:

* [Capturar rastreios de registos](app-insights-asp-net-trace-logs.md): Se já utilizar o Log4N, NLog ou System.Diagnostics.Trace para registar eventos de rastreio, o resultado pode ser enviado para o Application Insights. Pode correlacionar este resultado com pedidos, pesquisá-lo e analisá-lo.
* [Métricas e eventos personalizados](app-insights-api-custom-events-metrics.md): Utilize TrackEvent() e TrackMetric() no código do servidor ou da página Web.
* [Etiquetar telemetria com propriedades adicionais](app-insights-api-filtering-sampling.md#add-properties).

Utilize [Pesquisar](app-insights-diagnostic-search.md) para localizar e correlacionar eventos específicos e o [Analytics](app-insights-analytics.md) para fazer consultas mais potentes.

## <a name="alerts"></a>Alertas
Seja o primeiro a saber se a sua aplicação tem problemas.

* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md): Crie testes para garantir que o seu site está visível na Web.
* [Diagnóstico inteligente](app-insights-proactive-diagnostics.md): Estes testes são executados automaticamente, pelo que não precisa de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Alertas de métricas](app-insights-alerts.md): Defina estes alertas para o avisar se uma métrica ultrapassar um limiar. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.

Por predefinição, as notificações de alerta são enviadas para o proprietário da subscrição do Azure.

![Captura de ecrã de um exemplo de e-mail de alerta](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>Versão e controlo de versão
Para controlar a versão da aplicação, certifique-se de que `buildinfo.config` é gerado pelo processo do Microsoft Build Engine. No ficheiro .csproj, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Quando possui informações de compilação, o módulo Web do Application Insights adiciona automaticamente a **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Desta forma, poderá filtrar por versão quando executar [pesquisas de diagnóstico](app-insights-diagnostic-search.md) ou [explorar métricas](app-insights-metrics-explorer.md).

No entanto, tenha em atenção que o número de versão da compilação é gerado apenas pelo Microsoft Build Engine, não pela compilação do programador no Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se utilizar o Visual Studio Team Services, poderá [obter um marcador de anotação](app-insights-annotations.md) adicionado aos seus gráficos sempre que lançar uma nova versão. A imagem seguinte mostra como este marcador é apresentado.

![Captura de ecrã de um exemplo de anotação de versão num gráfico](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>Passos seguintes
**[Trabalhar com o Application Insights no Visual Studio](app-insights-visual-studio.md)**<br/>Inclui informações sobre depuração com telemetria, pesquisa de diagnóstico e exploração do código.

**[Trabalhar com o portal do Application Insights](app-insights-dashboards.md)**<br/> Inclui informações sobre dashboards, ferramentas avançadas de diagnóstico e análise, alertas, um mapa de dependência em direto da aplicação e exportação da telemetria.



<!--HONumber=Feb17_HO3-->


