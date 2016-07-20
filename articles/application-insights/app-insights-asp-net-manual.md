<properties
    pageTitle="Adicionar o Application Insights SDK para monitorizar a aplicação ASP.NET | Microsoft Azure"
    description="Analise a utilização, a disponibilidade e o desempenho da aplicação Web no local ou do Microsoft Azure com o Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/19/2016"
    ms.author="awills"/>


# Adicionar o Application Insights SDK para monitorizar a aplicação ASP.NET

*O Application Insights está em pré-visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


O Visual Studio Application Insights monitoriza a aplicação em direto para o ajudar a [detetar e diagnosticar problemas de desempenho e exceções][detetar] e [detetar como a aplicação é utilizada][knowUsers]. Pode ser utilizado com uma ampla variedade de tipos de aplicação. Funciona para as aplicações que estão alojadas nos seus próprios servidores IIS no local ou em VMs do Azure, bem como em aplicações Web do Azure.



![Gráficos de exemplo da monitorização do desempenho](./media/app-insights-asp-net-manual/10-perf.png)

*Veja também:*

* [ASP.NET 5](app-insights-asp-net-five.md)
* [Aplicações de dispositivos e servidores Java][plataformas]

#### Antes de começar

Para muitos tipos de aplicação, o [Visual Studio pode adicionar o Application Insights à sua aplicação](#ide), quase sem dar por isso. Mas, uma vez que está a ler isto para obter uma melhor compreensão da que se passa, iremos guiá-lo pelos passos manualmente.


É necessário:

* Uma subscrição do [Microsoft Azure](http://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo à mesma, utilizando a sua [conta Microsoft](http://live.com).
* Visual Studio 2013 ou posterior.

## <a name="add"></a>Criar um recurso do Application Insights

Inicie sessão no [portal do Azure][portal], e crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicação.

![Clique em Novo, Application Insights](./media/app-insights-asp-net-manual/01-new-asp.png)

Um [recurso][funções] no Azure é uma instância de um serviço. Este recurso é onde a telemetria da sua aplicação irá ser analisada e apresentada.

A escolha do tipo de aplicação define o conteúdo predefinido dos painéis de recursos e as propriedades visíveis no [Explorador de Métricas][métricas].

#### Copiar a Chave de Instrumentação

A chave identifica o recurso e irá instalá-lo em breve no SDK para direcionar os dados para o recurso.

![Clique em propriedades, selecione a chave e prima ctrl+C](./media/app-insights-asp-net-manual/02-props-asp.png)

Os passos que acabou de fazer para criar um novo recurso são uma boa forma de iniciar a monitorização de qualquer aplicação. Agora pode enviar dados para a mesma.

## <a name="sdk"></a> Instalar o SDK na sua aplicação

A instalação e configuração do Application Insights SDK variam consoante a plataforma em que está a trabalhar. Para aplicações ASP.NET, é fácil.

1. No Visual Studio, edite os pacotes NuGet do seu projeto de aplicação Web.

    ![Clique com o botão direito do rato no projeto e selecione Gerir Pacotes Nuget](./media/app-insights-asp-net-manual/03-nuget.png)

2. Instale o Application Insights SDK para Aplicações Web.

    ![Pesquise "Application Insights"](./media/app-insights-asp-net-manual/04-ai-nuget.png)

3. Edite ApplicationInsights.config (que foi adicionado à instalação do NuGet). Insira-o imediatamente antes da etiqueta de fecho:

    `<InstrumentationKey>` *a chave de instrumentação que copiou* `</InstrumentationKey>`

    (Em alternativa, pode [definir a chave, escrevendo algum código][apikey] na sua aplicação.)

#### Para atualizar para versões futuras do SDK

Ocasionalmente, lançamos uma nova versão do SDK.

Para atualizar para uma [nova versão do SDK](app-insights-release-notes-dotnet.md), abra o Gestor de Pacotes NuGet novamente e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se tiver efetuado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar e, posteriormente, intercale as alterações na nova versão.


## <a name="run"></a> Executar o seu projeto

Utilize **F5** para executar a aplicação e experimentá-la: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que foram enviados.

![](./media/app-insights-asp-net-manual/appinsights-09eventcount.png)

## <a name="monitor"></a> Ver a telemetria

Volte ao [portal do Azure][portal], e navegue para o recurso do Application Insights.


Procure dados nos gráficos da Descrição Geral. Inicialmente, verá apenas um ou dois pontos. Por exemplo:

![Clique para mais dados](./media/app-insights-asp-net-manual/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre as métricas.][desempenho]

#### Não existem dados?

* Utilize a aplicação, abrindo páginas diferentes, de modo a gerar alguma telemetria.
* Abra o mosaico [Pesquisa][diagnóstico] para ver eventos individuais. Por vezes, os eventos demoram um pouco mais de tempo a chegar ao pipeline de métricas.
* Aguarde alguns segundos e clique em **Atualizar**. Os gráficos atualizam-se periodicamente, mas pode atualizá-los manualmente se estiver à espera que apareçam alguns dados.
* Veja [Resolução de Problemas][FAQ].

## Publicar a aplicação

Agora, implemente a aplicação para o IIS ou o Azure e veja os dados acumularem.

![Utilizar o Visual Studio para publicar a aplicação](./media/app-insights-asp-net-manual/15-publish.png)

Quando é executada em modo de depuração, a telemetria é emitida através do pipeline, pelo que deve ver os dados que aparecem em segundos. Ao implementar a aplicação na configuração da Versão, os dados acumulam-se mais lentamente.

#### Não existem dados depois de publicar no seu servidor?

Abra estas portas para o tráfego de saída na firewall do servidor:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Problemas no seu servidor de compilação?

Veja [este item de Resolução de Problemas](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Se a sua aplicação gerar uma grande quantidade de telemetria (e estiver a utilizar o ASP.NET SDK versão 2.0.0-beta3 ou posterior), o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. No entanto, os eventos relacionados com o mesmo pedido serão selecionados ou desmarcados como um grupo, para que possa navegar entre os eventos relacionados. 
> [Saiba mais sobre amostragem](app-insights-sampling.md).


## Adicionar controlo de dependência (e contadores de desempenho do IIS)

O SDK precisa de ajuda para obter acesso a alguns dados. Em particular, irá precisar deste passo adicional para medir automaticamente chamadas da sua aplicação para bases de dados, APIs REST e outros componentes externos. Estas métricas de dependência podem ser inestimáveis para o ajudar a diagnosticar problemas de desempenho.

Se estiver a executar no seu próprio servidor IIS, este passo também permitirá que os contadores de desempenho do sistema apareçam no [explorador de métricas](app-insights-metrics-explorer.md).

#### Se a aplicação for executada no seu servidor de IIS

Inicie sessão no servidor com direitos de administrador e instale [Monitor de Estado do Application Insights](http://go.microsoft.com/fwlink/?LinkId=506648).

Poderá ser necessário [abrir portas de saída adicionais na sua firewall](app-insights-monitor-performance-live-website-now.md#troubleshooting).

Este passo ativa também os [relatórios de contadores de desempenho](app-insights-web-monitor-performance.md#system-performance-counters) como CPU, memória, ocupação da rede.

#### Se a aplicação for uma aplicação Web do Azure

No painel de controlo da sua aplicação Web do Azure, adicione a extensão do Application Insights.

![Na sua aplicação Web, Definições, Extensões, Adicionar, Application Insights](./media/app-insights-asp-net-manual/05-extend.png)


#### Se for um projeto do serviço em nuvem do Azure

[Adicione scripts às funções Web e de trabalho](app-insights-cloudservices.md).



## Adicionar a monitorização do lado do cliente

Instalou o SDK que envia dados de telemetria do servidor (back-end) da sua aplicação. Agora pode adicionar a monitorização do lado do cliente. Isto fornece-lhe dados de utilizadores, sessões, vistas de página e quaisquer exceções ou falhas que ocorram no browser. Também conseguirá escrever o seu próprio código para controlar a forma como os seus utilizadores trabalham com a sua aplicação, até ao nível detalhado de cliques e batimentos de teclas.


Adicione um fragmento JavaScript a todas as páginas. Obtenha o código do seu recurso do Application Insights:

![Na sua aplicação Web, abra Início Rápido e clique em “Obter o código para monitorizar as minhas páginas Web”](./media/app-insights-asp-net-manual/02-monitor-web-page.png)

Repare que o código contém a chave de instrumentação que identifica o seu recurso de aplicação.

[Saiba mais sobre o controlo de páginas Web.](app-insights-web-track-usage.md)


## Controlar a versão da Aplicação

Certifique-se de que `buildinfo.config` é gerado pelo seu processo de MSBuild. No ficheiro .csproj, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Quando possui informações de compilação,o módulo Web do Application Insights adiciona automaticamente **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Tal permite-lhe filtrar por versão ao efetuar [pesquisas de diagnóstico][diagnóstico] ou ao [explorar métricas][métricas]. 

No entanto, tenha em atenção que o número de versão da compilação é gerado apenas por Build MS, não pela compilação do programador no Visual Studio.

## Concluir a instalação

Para obter a vista completa em 360 graus da sua aplicação, existem mais algumas coisas que pode fazer:

* [Configure testes Web][disponibilidade] para certificar-se de que a aplicação permanece em direto e reativa.
* [Capture rastreios de registo][netlogs] a partir da sua arquitetura de registo favorita
* [Controle eventos personalizados e métricas][api] no cliente ou servidor, ou ambos, saiba mais sobre a utilização da aplicação.

## <a name="ide"></a> A forma automática

No início deste artigo, dissemos-lhe que iríamos mostrar a forma manual para criar um recurso do Application Insights e, em seguida, instalar o SDK. Acreditamos que é boa ideia compreender as duas partes desse procedimento. No entanto, para aplicações ASP.NET (e muitas outras), existe uma forma automática ainda mais rápida.

Precisará do [Visual Studio](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (2013 atualização 3 ou posterior) e uma conta do [Microsoft Azure](http://azure.com).

#### Se for um novo projeto...

Quando cria um novo projeto no Visual Studio, certifique-se de que **Adicionar o Application Insights** está selecionado.


![Criar um projeto ASP.NET](./media/app-insights-asp-net-manual/appinsights-01-vsnewp1.png)

O Visual Studio cria um recurso no Application Insights, adiciona o SDK ao seu projeto e coloca a chave no ficheiro `.config`.

Se o projeto tiver páginas Web, é também adicionado o [JavaScript SDK][cliente] à página Web mestra.

#### ... ou se for um projeto existente

Clique com o botão direito do rato no Explorador de Soluções e escolha **Adicionar o Application Insights**.

![Escolha Adicionar o Application Insights](./media/app-insights-asp-net-manual/appinsights-03-addExisting.png)

O Visual Studio cria um recurso no Application Insights, adiciona o SDK ao seu projeto e coloca a chave no ficheiro `.config`.

Neste caso, não adiciona o [JavaScript SDK][cliente] às suas páginas Web - recomendamos que faça como no passo seguinte.

#### Opções de configuração

Se esta for a primeira vez, ser-lhe-á pedido para iniciar sessão ou inscrever-se na Pré-Visualização do Microsoft Azure. 

Se esta aplicação fizer parte de uma aplicação maior, poderá utilizar **Definições de configuração ** para colocá-la no mesmo grupo de recursos dos outros componentes.

*Nenhuma opção do Application Insights? Certifique-se de que está a utilizar o Visual Studio 2013 Atualização 3 ou posterior e que as Ferramentas do Application Insights estão ativadas em Extensões e Atualizações.*

#### Abrir o Application Insights a partir do seu projeto

![Clique com o botão direito do rato no projeto e abra o portal do Azure](./media/app-insights-asp-net-manual/appinsights-04-openPortal.png)




## <a name="video"></a>Vídeo

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[disponibilidade]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[cliente]: app-insights-javascript.md
[detetar]: app-insights-detect-triage-diagnose.md
[diagnóstico]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[métricas]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[desempenho]: app-insights-web-monitor-performance.md
[plataformas]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[FAQ]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[funções]: app-insights-resources-roles-access-control.md
[iniciar]: app-insights-overview.md



<!--HONumber=Jun16_HO2-->


