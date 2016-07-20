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
    ms.date="02/04/2016"
    ms.author="awills"/>


# Adicionar o Application Insights SDK para monitorizar a aplicação ASP.NET

*O Application Insights está em pré-visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


O Visual Studio Application Insights monitoriza a aplicação em direto para o ajudar a [detetar e diagnosticar problemas de desempenho e exceções][detect] e [detetar como a aplicação é utilizada][knowUsers]. Pode ser utilizado com uma ampla variedade de tipos de aplicação. Funciona para as aplicações alojadas nos seus próprios servidores IIS no local ou em VMs do Azure, bem como em aplicações Web do Azure.



![Gráficos de exemplo da monitorização do desempenho](./media/app-insights-start-monitoring-app-health-usage/10-perf.png)

*Veja também:*

* [ASP.NET 5](app-insights-asp-net-five.md)
* [Aplicações de dispositivos e servidores Java][platforms]

#### Antes de começar

Para muitos tipos de aplicação, o [Visual Studio pode adicionar o Application Insights à sua aplicação](#ide), quase sem dar por isso. Mas, uma vez que está a ler este artigo para obter uma melhor compreensão, vamos apresentar-lhe os passos manualmente.


É necessário:

* Uma subscrição do [Microsoft Azure](http://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo utilizando a [Conta Microsoft](http://live.com).
* Visual Studio 2013 ou posterior.

## <a name="add"></a> 1. Criar um recurso do Application Insights

Inicie sessão no [Portal do Azure][portal] e crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicação.

![Clicar em Novo, Application Insights](./media/app-insights-start-monitoring-app-health-usage/01-new-asp.png)

Um [recurso][roles] no Azure é uma instância de um serviço. Este recurso é onde a telemetria da aplicação será analisada e apresentada.

A escolha do tipo de aplicação define o conteúdo predefinido dos painéis de recursos e as propriedades visíveis no [Explorador de Métricas][metrics].

#### Copiar a Chave de Instrumentação

A chave identifica o recurso. Deverá instalá-lo logo no SDK para direcionar os dados para o recurso.

![Clicar em Propriedades, selecionar a chave e premir Ctrl+C](./media/app-insights-start-monitoring-app-health-usage/02-props-asp.png)

Os passos que acabou de fazer para criar um novo recurso são uma boa forma de iniciar a monitorização de qualquer aplicação. Agora pode começar a enviar dados.

## <a name="sdk"></a> 2. Instalar o SDK na aplicação

A instalação e configuração do Application Insights SDK variam consoante a plataforma em que está a trabalhar. Para aplicações ASP.NET, é fácil.

1. No Visual Studio, edite os pacotes NuGet do seu projeto de aplicação Web.

    ![Clicar com o botão direito do rato no projeto e selecionar Gerir Pacotes Nuget](./media/app-insights-start-monitoring-app-health-usage/03-nuget.png)

2. Instale o Application Insights SDK para as Web Apps.

    ![Procurar “Application Insights”](./media/app-insights-start-monitoring-app-health-usage/04-ai-nuget.png)

3. Edite ApplicationInsights.config (que foi adicionado à instalação do NuGet). Insira-o imediatamente antes da tag de fecho:

    `<InstrumentationKey>` *a chave de instrumentação que copiou* `</InstrumentationKey>`

    (Em alternativa, pode [definir a chave escrevendo algum código][apikey] na aplicação.)

#### Para atualizar para versões futuras do SDK

Lançamos novas versões do SDK, ocasionalmente.

Para atualizar para uma [nova versão do SDK](app-insights-release-notes-dotnet.md), abra o gestor de pacotes NuGet novamente e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se tiver efetuado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar e, posteriormente, intercale as alterações na nova versão.


## <a name="run"></a> 3. Executar o projeto

Utilize **F5** para executar e experimentar a aplicação: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que foram enviados.

![](./media/app-insights-start-monitoring-app-health-usage/appinsights-09eventcount.png)

## <a name="monitor"></a> 4. Ver a telemetria

Volte ao [Portal do Azure][portal] e navegue para o recurso do Application Insights.


Procure dados nos gráficos Descrição Geral. Inicialmente, verá apenas um ou dois pontos. Por exemplo:

![Clicar para mais dados](./media/app-insights-start-monitoring-app-health-usage/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre as métricas.][perf]

#### Não existem dados?

* Utilize a aplicação, abrindo páginas diferentes, de modo a gerar alguma telemetria.
* Abra o mosaico [Pesquisa][diagnostic] para ver eventos individuais. Por vezes, os eventos demoram um pouco mais de tempo a chegar ao pipeline de métricas.
* Aguarde alguns segundos e clique em **Atualizar**. Os gráficos atualizam-se periodicamente, mas pode atualizá-los manualmente se estiver à espera que apareçam alguns dados.
* Veja [Resolução de Problemas][qna].

## Publicar a aplicação

Agora, implemente a aplicação no IIS ou no Azure e veja os dados a acumularem.

![Utilizar o Visual Studio para publicar a aplicação](./media/app-insights-start-monitoring-app-health-usage/15-publish.png)

Quando é executada em modo de depuração, a telemetria é emitida através do pipeline, pelo que deve ver os dados que aparecem em segundos. Ao implementar a aplicação na configuração da Versão, os dados acumulam-se mais lentamente.

#### Não existem dados depois de publicar no servidor?

Abra estas portas para o tráfego de saída na firewall do servidor:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Problemas no servidor de compilação?

Veja [este item de Resolução de Problemas](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Se a sua aplicação gerar uma grande quantidade de telemetria (e estiver a utilizar o ASP.NET SDK versão 2.0.0-beta3 ou posterior), o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. No entanto, os eventos relacionados com o mesmo pedido serão selecionados ou desmarcados como um grupo, para que possa navegar entre os eventos relacionados. 
> [Saiba mais sobre a amostragem](app-insights-sampling.md).


## 5. Adicionar controlo de dependência (e contadores de desempenho do IIS)

O SDK precisa de ajuda para obter acesso a alguns dados. Em particular, irá precisar deste passo adicional para medir automaticamente chamadas da sua aplicação para bases de dados, APIs REST e outros componentes externos. Estas métricas de dependência podem ser valiosas para o ajudar a diagnosticar problemas de desempenho.

Se estiver a executar no seu próprio servidor de IIS, este passo também permitirá que os contadores de desempenho do sistema apareçam no [Explorador de Métricas](app-insights-metrics-explorer.md).

#### Se a aplicação for executada no servidor de IIS

Inicie sessão no servidor com direitos de administrador e instale o [Monitor de Estado do Application Insights](http://go.microsoft.com/fwlink/?LinkId=506648).

Poderá ser necessário [abrir portas de saída adicionais na firewall](app-insights-monitor-performance-live-website-now.md#troubleshooting).

Este passo ativa também os [relatórios dos contadores de desempenho](app-insights-web-monitor-performance.md#system-performance-counters) como CPU, memória, ocupação da rede.

#### Se a aplicação for uma aplicação Web do Azure

No painel de controlo da aplicação Web do Azure, adicione a extensão do Application Insights.

![Na aplicação Web, Definições, Extensões, Adicionar, Application Insights](./media/app-insights-start-monitoring-app-health-usage/05-extend.png)


#### Se for um projeto dos serviços em nuvem do Azure

[Adicione scripts às funções Web e de trabalho](app-insights-cloudservices.md).



## 6. Adicionar a monitorização do lado do cliente

Instalou o SDK que envia dados de telemetria do servidor (back-end) da aplicação. Agora pode adicionar a monitorização do lado do cliente. Esta monitorização fornece-lhe dados de utilizadores, sessões, vistas de página e quaisquer exceções ou falhas que ocorram no browser. Também conseguirá escrever o seu próprio código para controlar a forma como os seus utilizadores trabalham com a aplicação, até ao nível detalhado de cliques e batimentos de teclas.


Adicione um fragmento JavaScript a todas as páginas. Obtenha o código do recurso do Application Insights:

![Na aplicação Web, abrir o Início Rápido e clicar em “Obter o código para monitorizar as minhas páginas Web”](./media/app-insights-start-monitoring-app-health-usage/02-monitor-web-page.png)

Repare que o código contém a chave de instrumentação que identifica o seu recurso de aplicação.

[Saiba mais sobre o controlo de páginas Web.](app-insights-web-track-usage.md)


## Versão da Aplicação de Controlo

Verifique se `buildinfo.config` é gerado pelo processo do MSBuild. No ficheiro .csproj, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Quando possui informações de compilação, o módulo Web do Application Insights adiciona automaticamente a **Versão da aplicação** como uma propriedade a todos os itens de telemetria. Tal permite-lhe filtrar por versão ao realizar [pesquisas de diagnóstico][diagnostic] ou ao [explorar métricas][metrics]. 

No entanto, tenha em atenção que o número de versão da compilação é gerado apenas pelo MS Build, não pela compilação do programador no Visual Studio.

## 7. Concluir a instalação

Para obter a vista de 360 graus completa da aplicação, existem mais algumas coisas que pode fazer:

* [Configurar testes Web][availability] para confirmar que a aplicação permanece em direto e reativa.
* [Capturar rastreios de registo][netlogs] na sua arquitetura de registo favorita
* [Controlar métricas e eventos personalizados][api] no cliente ou servidor, ou em ambos, para saber mais sobre a utilização da aplicação.

## <a name="ide"></a> A forma automática

No início deste artigo, mencionamos que iríamos mostrar a forma manual para criar um recurso do Application Insights e, em seguida, instalar o SDK. Achamos que é boa ideia compreender as duas partes desse procedimento. No entanto, para aplicações ASP.NET (e muitas outras), existe uma forma automática ainda mais rápida.

Precisará do [Visual Studio](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (2013 atualização 3 ou posterior) e uma conta do [Microsoft Azure](http://azure.com).

#### Se for um projeto novo...

Ao criar um projeto novo no Visual Studio, verifique se **Adicionar o Application Insights** está selecionado.


![Criar um projeto ASP.NET](./media/app-insights-start-monitoring-app-health-usage/appinsights-01-vsnewp1.png)

O Visual Studio cria um recurso no Application Insights, adiciona o SDK ao projeto e coloca a chave no ficheiro `.config`.

Se o projeto tiver páginas Web, será também adicionado o [JavaScript SDK][client] à página Web mestra.

#### ... ou se for um projeto existente

Clique com o botão direito do rato no Explorador de Soluções e escolha **Adicionar o Application Insights**.

![Escolher Adicionar o Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-03-addExisting.png)

O Visual Studio cria um recurso no Application Insights, adiciona o SDK ao projeto e coloca a chave no ficheiro `.config`.

Neste caso, não adiciona o [JavaScript SDK][client] às páginas Web. Recomendamos que siga o passo seguinte.

#### Opções de configuração

Se esta for a primeira vez, ser-lhe-á pedido para iniciar sessão ou inscrever-se na Pré-Visualização do Microsoft Azure. 

Se esta aplicação fizer parte de uma aplicação maior, poderá utilizar **Definições de configuração ** para colocá-la no mesmo grupo de recursos dos outros componentes.

*Nenhuma opção do Application Insights? Verifique se está a utilizar o Visual Studio 2013 Atualização 3 ou posterior e se as Ferramentas do Application Insights estão ativadas em Extensões e Atualizações.*

#### Abrir o Application Insights a partir do projeto

![Clique com o botão direito do rato no projeto e abra o Portal do Azure](./media/app-insights-start-monitoring-app-health-usage/appinsights-04-openPortal.png)




## <a name="video"></a>Vídeo

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md



<!--HONumber=Jun16_HO2-->


