<properties 
    pageTitle="Configurar a análise da aplicação Web do ASP.NET com o Application Insights" 
    description="Configure a análise de desempenho, disponibilidade e utilização do seu site ASP.NET, alojado no local ou no Azure." 
    services="application-insights" 
    documentationCenter=".net"
    authors="NumberByColors" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="06/21/2016" 
    ms.author="daviste"/>


# Configurar o Application Insights para o ASP.NET

O [Visual Studio Application Insights](app-insights-overview.md) monitoriza a aplicação em direto para o ajudar a [detetar e diagnosticar problemas de desempenho e exceções](app-insights-detect-triage-diagnose.md) e [detetar como a aplicação é utilizada](app-insights-overview-usage.md).  Funciona para as aplicações que estão alojadas nos seus próprios servidores IIS no local ou em VMs na nuvem, bem como em aplicações Web do Azure.


## Antes de começar

É necessário:

* Visual Studio 2013 atualização 3 ou posterior. É preferível a versão posterior.
* Uma subscrição do [Microsoft Azure](http://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo à mesma, utilizando a sua [conta Microsoft](http://live.com). 

Existem artigos alternativos para ver se estiver interessado:

* [Instrumentar uma aplicação Web em tempo de execução](app-insights-monitor-performance-live-website-now.md)
* [Serviços em Nuvem do Azure](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Adicionar o Application Insights SDK


### Se for um novo projeto...

Certifique-se de que a opção Application Insights está selecionada quando criar um novo projeto no Visual Studio. 


![Criar um projeto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### ... ou se for um projeto existente

Clique com o botão direito do rato no Explorador de Soluções e escolha **Adicionar Telemetria do Application Insights** ou **Configurar o Application Insights**.

![Escolher Adicionar o Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* Projeto em ASP.NET Core? - [Siga estas instruções para corrigir algumas linhas de código](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 



## <a name="run"></a> 2. Executar a aplicação

Execute a aplicação ao premir F5 e experimente-a: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que foram registados. 

![No Visual Studio, o botão do Application Insights é apresentado durante a depuração.](./media/app-insights-asp-net/54.png)

## 3. Ver a telemetria...

### ... no Visual Studio

Abra a janela do Application Insights no Visual Studio: clique no botão Application Insights ou clique com o botão direito do rato no projeto no Explorador de Soluções:

![No Visual Studio, o botão do Application Insights é apresentado durante a depuração.](./media/app-insights-asp-net/55.png)

Esta vista mostra a telemetria gerada do lado do servidor da sua aplicação. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a> 
### ... no portal

A não ser que tenha escolhido *Instalar apenas SDK*, também pode ver a telemetria no portal Web do Application Insights. 

O portal tem mais gráficos, ferramentas de análise e dashboards que o Visual Studio. 


Abra o recurso do Application Insights no [portal do Azure](https://portal.azure.com/).

![Clique com o botão direito do rato no projeto e abra o portal do Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

O portal é aberto numa vista de telemetria a partir da sua aplicação:
![](./media/app-insights-asp-net/66.png)

* Os eventos individuais são apresentados em **Pesquisar** (1). Os dados são apresentados aqui primeiro (e na [Transmissão de Métricas em Direto](app-insights-metrics-explorer.md#live-metrics-stream)). Clique em qualquer evento para ver as respetivas propriedades. 
* As métricas agregadas são apresentadas nos gráficos (2). Poderá demorar um ou dois minutos para os dados serem apresentados aqui. Clique em qualquer gráfico para abrir um painel com mais detalhes.

[Saiba mais sobre como utilizar o Application Insights no portal do Azure](app-insights-dashboards.md).

##<a name="land"></a> O que fez ’’Adicionar o Application Insights’’?

O Application Insights envia a telemetria da sua aplicação para o portal do Application Insights (que está alojado no Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Por isso, o comando faz três ações:

1. Adiciona o pacote Application Insights Web SDK NuGet ao seu projeto. Para vê-lo no Visual Studio, clique com o botão direito do rato no projeto e selecione Gerir Pacotes NuGet.
2. Cria um recurso do Application Insights no [portal do Azure](https://portal.azure.com/). É aqui que verá os seus dados. Obtém a *chave de instrumentação*, a qual identifica o recurso.
3. Insere a chave de instrumentação em `ApplicationInsights.config`, para que o SDK possa enviar a telemetria para o portal.

Se quiser, pode executar estes passos manualmente para [ASP.NET 4](app-insights-asp-net-manual.md) ou [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

## Para atualizar para versões futuras do SDK

Para atualizar para uma [nova versão do SDK](app-insights-release-notes-dotnet.md), abra o gestor de pacotes NuGet novamente e filtre os pacotes instalados. Selecione Microsoft.ApplicationInsights.Web e escolha Atualizar.

Se tiver efetuado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar e, posteriormente, intercale as alterações na nova versão.



## Passos seguintes?

| | 
|---|---
|**[Trabalhar com o Application Insights no Visual Studio](app-insights-visual-studio.md)**<br/>Depuração com telemetria, pesquisa de diagnóstico e exploração do código.|![Visual Studio](./media/app-insights-asp-net/61.png)
|**[Trabalhar com o portal do Application Insights](app-insights-dashboards.md)**<br/>Dashboards, ferramentas de diagnóstico e análise poderosas, alertas, mapa de dependência em direto da aplicação e exportação de telemetria. |![Visual Studio](./media/app-insights-asp-net/62.png)
|**[Adicionar mais dados](app-insights-asp-net-more.md)**<br/>Monitorize a utilização, a disponibilidade, as dependências e as exceções. Integre rastreios a partir de arquiteturas de registo. Grave a telemetria personalizada. | ![Visual Studio](./media/app-insights-asp-net/64.png)









<!--HONumber=Aug16_HO1-->


