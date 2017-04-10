---
title: "Azure Application Insights para Windows Server e funções de trabalho | Microsoft Docs"
description: "Adicione manualmente o SDK do Application Insights à sua aplicação do ASP.NET para analisar a utilização, a disponibilidade e o desempenho."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 97d750035e79d4780738e660d05e1a41085d51f1
ms.lasthandoff: 03/31/2017


---
# <a name="manually-configure-application-insights-for-aspnet-applications"></a>Configurar manualmente o Application Insights para aplicações ASP.NET
O [Application Insights](app-insights-overview.md) é uma ferramenta extensível para os programadores Web monitorizarem o desempenho e a utilização da aplicação em direto. Pode configurá-lo manualmente para monitorizar o Windows Server, funções de trabalho e outras aplicações ASP.NET. Para aplicações Web, a configuração manual é uma alternativa à [configuração automática](app-insights-asp-net.md) oferecida pelo Visual Studio.

![Gráficos de exemplo da monitorização do desempenho](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Antes de começar
É necessário:

* Uma subscrição do [Microsoft Azure](http://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo à mesma, utilizando a sua [conta Microsoft](http://live.com).
* Visual Studio 2013 ou posterior.

## <a name="add"></a>1. Criar um recurso do Application Insights
Inicie sessão no [Portal do Azure](https://portal.azure.com/) e crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicação.

![Clicar em Novo, Application Insights](./media/app-insights-windows-services/01-new-asp.png)

Um [recurso](app-insights-resources-roles-access-control.md) no Azure é uma instância de um serviço. Este recurso é onde a telemetria da aplicação será analisada e apresentada.

A escolha do tipo de aplicação define o conteúdo predefinido dos painéis de recursos e as propriedades visíveis no [Explorador de Métricas](app-insights-metrics-explorer.md).

#### <a name="copy-the-instrumentation-key"></a>Copiar a Chave de Instrumentação
A chave identifica o recurso. Deverá instalá-lo logo no SDK para direcionar os dados para o recurso.

![Clicar em Propriedades, selecionar a chave e premir Ctrl+C](./media/app-insights-windows-services/02-props-asp.png)

Os passos que acabou de fazer para criar um novo recurso são uma boa forma de iniciar a monitorização de qualquer aplicação. Agora pode começar a enviar dados.

## <a name="sdk"></a>2. Instalar o pacote do Application Insights na sua aplicação
A instalação e configuração do pacote do Application Insights varia consoante a plataforma em que está a trabalhar. Para aplicações ASP.NET, é fácil.

1. No Visual Studio, edite os pacotes NuGet do seu projeto de aplicação Web.
   
    ![Clicar com o botão direito do rato no projeto e selecionar Gerir Pacotes Nuget](./media/app-insights-windows-services/03-nuget.png)
2. Instale o pacote do Application Insights para aplicações do Windows Server.
   
    ![Procurar “Application Insights”](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *Posso utilizar outros pacotes?*
   
    Sim. Escolha a API Core (Microsoft.ApplicationInsights) se pretender utilizar a API para enviar a sua própria telemetria. O pacote do Windows Server inclui automaticamente a API Core, assim como outros pacotes, como a recolha de contadores de desempenho e a monitorização de dependência. 

#### <a name="to-upgrade-to-future-package-versions"></a>Para atualizar para versões futuras do pacote
Lançamos novas versões do SDK, ocasionalmente.

Para atualizar para uma [nova versão do pacote](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), abra o gestor de pacotes NuGet novamente e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.WindowsServer** e escolha **Atualizar**.

Se tiver efetuado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar e, posteriormente, intercale as alterações na nova versão.

## <a name="3-send-telemetry"></a>3. Enviar telemetria
**Se instalou apenas o pacote de API Core:**

* Defina a chave de instrumentação no código, por exemplo `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *a sua chave* `";` 
* [Escreva a sua própria telemetria utilizando a API](app-insights-api-custom-events-metrics.md#ikey).

**Se tiver instalado outros pacotes do Application Insights,** pode, se preferir, utilizar o ficheiro .config para definir a chave de instrumentação:

* Edite ApplicationInsights.config (que foi adicionado à instalação do NuGet). Insira-o imediatamente antes da tag de fecho:
  
    `<InstrumentationKey>` *a chave de instrumentação que copiou* `</InstrumentationKey>`
* Certifique-se de que as propriedades de ApplicationInsights.config no Explorador de Soluções estão definidas para **Ação de Compilação = Conteúdo, Copiar para o Diretório de Saída = Copiar**.

É útil definir a chave de instrumentação no código, se quiser [mudar a chave de diferentes configurações de compilações](app-insights-separate-resources.md). Se definir a chave no código, não tem de defini-la no ficheiro `.config`.

## <a name="run"></a> Executar o projeto
Utilize **F5** para executar e experimentar a aplicação: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que foram enviados.

![Contagem de eventos no Visual Studio](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Ver a telemetria
Volte ao [Portal do Azure](https://portal.azure.com/) e navegue para o recurso do Application Insights.

Procure dados nos gráficos Descrição Geral. Inicialmente, verá apenas um ou dois pontos. Por exemplo:

![Clicar para mais dados](./media/app-insights-windows-services/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre métricas.](app-insights-web-monitor-performance.md)

#### <a name="no-data"></a>Não existem dados?
* Utilize a aplicação, abrindo páginas diferentes, de modo a gerar alguma telemetria.
* Abra o mosaico [Pesquisa](app-insights-diagnostic-search.md) para ver eventos individuais. Por vezes, os eventos demoram um pouco mais de tempo a chegar ao pipeline de métricas.
* Aguarde alguns segundos e clique em **Atualizar**. Os gráficos atualizam-se periodicamente, mas pode atualizá-los manualmente se estiver à espera que apareçam alguns dados.
* Veja [Resolução de Problemas](app-insights-troubleshoot-faq.md).

## <a name="publish-your-app"></a>Publicar a aplicação
Em seguida, implemente a aplicação no servidor ou no Azure e veja os dados a acumularem.

![Utilizar o Visual Studio para publicar a aplicação](./media/app-insights-windows-services/15-publish.png)

Quando é executada em modo de depuração, a telemetria é emitida através do pipeline, pelo que deve ver os dados que aparecem em segundos. Ao implementar a aplicação na configuração da Versão, os dados acumulam-se mais lentamente.

#### <a name="no-data-after-you-publish-to-your-server"></a>Não existem dados depois de publicar no servidor?
Abra estas portas para o tráfego de saída na firewall do servidor:

* `dc.services.visualstudio.com:443`
* `f5.services.visualstudio.com:443`

#### <a name="trouble-on-your-build-server"></a>Problemas no servidor de compilação?
Veja [este item de Resolução de Problemas](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Se a sua aplicação gerar uma grande quantidade de telemetria (e estiver a utilizar o ASP.NET SDK versão 2.0.0-beta3 ou posterior), o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. No entanto, os eventos relacionados com o mesmo pedido serão selecionados ou desmarcados como um grupo, para que possa navegar entre os eventos relacionados. 
> [Saiba mais sobre a amostragem](app-insights-sampling.md).
> 
> 

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes
* [Adicione mais telemetria](app-insights-asp-net-more.md) para obter a vista completa em 360 graus da sua aplicação.


