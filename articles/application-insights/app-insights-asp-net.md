---
title: "Configurar a análise de aplicações Web para ASP.NET com o Azure Application Insights | Microsoft Docs"
description: "Configure a análise de desempenho, disponibilidade e utilização do seu site ASP.NET, alojado no local ou no Azure."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: bwren
ms.openlocfilehash: cb247ee68da88265f7c51258644064463d44f8b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurar o Application Insights para o seu site ASP.NET

Este procedimento configura a sua aplicação Web do ASP.NET para enviar telemetria para o serviço [Azure Application Insights](app-insights-overview.md). Funciona para aplicações ASP.NET alojadas no seu próprio servidor IIS ou na cloud. Obtém gráficos e um idioma de consulta poderoso que o ajudam a compreender o desempenho da sua aplicação e como as pessoas estão a utilizá-la, bem como alertas automáticos sobre falhas ou problemas de desempenho. Muitos programadores acham que estas funcionalidades são excelentes tal como estão, mas também pode expandir e personalizar a telemetria se precisar.

A configuração demora apenas alguns cliques no Visual Studio. Tem a opção de evitar encargos ao limitar o volume de telemetria. Isto permite-lhe experimentar e depurar, ou monitorizar um site com poucos utilizadores. Quando decidir que pretende continuar e monitorizar o seu site de produção, pode elevar o limite facilmente mais tarde.

## <a name="before-you-start"></a>Antes de começar
É necessário:

* Visual Studio 2013 atualização 3 ou posterior. É preferível a versão posterior.
* Uma subscrição do [Microsoft Azure](http://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo à mesma através da sua [conta Microsoft](http://live.com).

Existem tópicos alternativos que pode ver se estiver interessado em:

* [Instrumentar aplicações Web no runtime](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> Passo 1: Adicionar o Application Insights SDK

Clique com o botão direito do rato no projeto da aplicação Web no Explorador de Soluções e escolha **Adicionar** > **Telemetria do Application Insights...** ou **Configurar o Application Insights**.

![Captura de ecrã do Explorador de Soluções, com a opção Adicionar Telemetria do Application Insights realçada](./media/app-insights-asp-net/appinsights-03-addExisting.png)

(No Visual Studio 2015, existe também uma opção para adicionar o Application Insights na caixa de diálogo Novo Projeto.)

Avance para a página de configuração do Application Insights:

![Captura de ecrã da página Registar a sua aplicação no Application Insights](./media/app-insights-asp-net/visual-studio-register-dialog.png)

**a.** Selecione a conta e subscrição que utiliza para aceder ao Azure.

**b.** Selecione o recurso no Azure onde pretende ver os dados da sua aplicação. Normalmente:

* Utilize um [único recurso para diferentes componentes](app-insights-monitor-multi-role-apps.md) de uma única aplicação. 
* Crie recursos separados para aplicações não relacionadas.
 
Se pretender definir o grupo de recursos ou a localização onde os seus dados são armazenado, clique em **Configurar definições**. Os grupos de recursos são utilizados para controlar o acesso aos dados. Por exemplo, se tiver várias aplicações que fazem parte do mesmo sistema, poderá colocar os seus dados do Application Insights no mesmo grupo de recursos.

**c.** Defina um valor no limite do volume de dados gratuito, para evitar encargos. O Application Insights é gratuito até um determinado volume de telemetria. Depois de o recurso ser criado, pode alterar a seleção no portal, abrindo **Funcionalidades + preços** > **Gestão de volumes de dados** > **Limite de volume diário**.

**d.** Clique em **Registar** para avançar e configurar o Application Insights para a sua aplicação Web. A telemetria será enviada para o [portal do Azure](https://portal.azure.com), durante a depuração e após ter publicado a aplicação.

**e.** Se não pretender enviar telemetria para o portal enquanto estiver a depurar, basta adicionar o SDK do Application Insights à sua aplicação, mas não configure um recurso no portal. Poderá ver a telemetria no Visual Studio enquanto faz a depuração. Mais tarde, pode voltar a esta página de configuração ou pode esperar até depois de implementar a aplicação e [ativar a telemetria em tempo de execução](app-insights-monitor-performance-live-website-now.md).


## <a name="run"></a> Passo 2: Executar a aplicação
Execute a aplicação com F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, vê uma contagem dos eventos que foram registados.

![Captura de ecrã do Visual Studio. O botão do Application Insights é apresentado durante a depuração.](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry"></a>Passo 3: Ver a telemetria
Pode ver a sua telemetria no Visual Studio ou no portal Web do Application Insights. Procure telemetria no Visual Studio para o ajudar a depurar a sua aplicação. Monitorize o desempenho e a utilização no portal Web quando o sistema estiver em direto. 

### <a name="see-your-telemetry-in-visual-studio"></a>Consulte a sua telemetria no Visual Studio

No Visual Studio, abra a janela do Application Insights. Clique no botão **Application Insights** ou clique com o botão direito do rato no seu projeto no Explorador de Soluções, selecione **Application Insights** e clique em **Procurar Telemetria em Direto**.

Na janela de Pesquisa do Visual Studio Application Insights, consulte a vista **Dados da sessão de Depuração** para ver a telemetria gerada no lado do servidor da aplicação. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

![Captura de ecrã da vista Dados da sessão de Depuração na janela do Application Insights.](./media/app-insights-asp-net/55.png)

> [!NOTE]
> Se não existirem quaisquer dados, certifique-se de que o intervalo de tempo está correto e clique no ícone de Pesquisa.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Consulte a telemetria no portal Web

Também pode ver a telemetria no portal Web do Application Insights, (a não ser que tenha optado por instalar apenas o SDK). O portal tem mais gráficos, ferramentas de análise e vistas de vários componentes que o Visual Studio. O portal também fornece alertas.

Abra o recurso do Application Insights. Inicie sessão no [portal do Azure](https://portal.azure.com/) e encontre-o aí ou clique com o botão direito do rato no projeto no Visual Studio e permita que o mesmo o redirecione.

![Captura de ecrã do Visual Studio que mostra como abrir o portal do Application Insights](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> Se receber o erro de acesso “Tem mais de um conjunto de credenciais da Microsoft e tem sessão iniciada com o conjunto errado?” No portal, termine a sessão e inicie sessão novamente.

O portal é aberto na vista da telemetria da sua aplicação.

![Captura de ecrã da página de descrição geral do Application Insights](./media/app-insights-asp-net/66.png)

No portal, clique em qualquer gráfico ou mosaico para ver mais detalhes.

[Saiba mais sobre como utilizar o Application Insights no portal do Azure](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Passo 4: Publicar a aplicação
Publique a sua aplicação no seu servidor IIS ou no Azure. Veja o [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) para garantir que não existem problemas.

A telemetria acumula-se no portal do Application Insights, onde pode monitorizar métricas, pesquisar a sua telemetria e configurar [dashboards](app-insights-dashboards.md). Também pode utilizar a avançada [Linguagem de consulta do Log Analytics](https://docs.loganalytics.io/) para analisar a utilização e o desempenho ou para encontrar eventos específicos.

Também pode continuar a analisar a sua telemetria no [Visual Studio](app-insights-visual-studio.md) com ferramentas como a pesquisa de diagnóstico e as [tendências](app-insights-visual-studio-trends.md).

> [!NOTE]
> Se a sua aplicação enviar telemetria suficiente para se aproximar dos [limites de monitorização](app-insights-pricing.md#limits-summary), é ativada a [amostragem](app-insights-sampling.md) automática. A amostragem reduz a quantidade de telemetria enviada pela sua aplicação, conservando os dados correlacionados para efeitos de diagnóstico.
>
>

## <a name="land"></a>Está tudo pronto

Parabéns! Instalou o pacote do Application Insights na sua aplicação e configurou o mesmo para enviar telemetria para o serviço do Application Insights no Azure.

![Diagrama do movimento da telemetria](./media/app-insights-asp-net/01-scheme.png)

O recurso do Azure que recebe a telemetria da sua aplicação é identificado por uma *chave de instrumentação*. Encontrará esta chave no ficheiro ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Atualizar para versões futuras do SDK
Para atualizar para uma [nova versão do SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), abra o **gestor de pacotes NuGet** novamente e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se tiver realizado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar. Em seguida, intercale as suas alterações na nova versão.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes

### <a name="more-telemetry"></a>Mais telemetria

* **[Dados de carregamento da página e do browser](app-insights-javascript.md)** - insira um fragmento de código nas suas páginas Web.
* **[Obtenha monitorização de dependência e exceção mais detalhada](app-insights-monitor-performance-live-website-now.md)** - instale o Monitor de Estado no seu servidor.
* **[Eventos personalizados de código](app-insights-api-custom-events-metrics.md)** para ações do utilizador de contagem, hora ou medida.
* **[Obtenha dados de registo](app-insights-asp-net-trace-logs.md)** - correlacione registos de dados com a sua telemetria.

### <a name="analysis"></a>Análise

* **[Trabalhar com o Application Insights no Visual Studio](app-insights-visual-studio.md)**<br/>Inclui informações sobre depuração com telemetria, pesquisa de diagnóstico e exploração do código.
* **[Trabalhar com o portal do Application Insights](app-insights-dashboards.md)**<br/> Inclui informações sobre dashboards, ferramentas avançadas de diagnóstico e análise, alertas, um mapa de dependência em direto da aplicação e exportação da telemetria.
* **[Análise](app-insights-analytics-tour.md)** - a linguagem de consulta poderosa.

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md): Crie testes para garantir que o seu site está visível na Web.
* [Diagnóstico inteligente](app-insights-proactive-diagnostics.md): Estes testes são executados automaticamente, pelo que não precisa de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Alertas de métricas](app-insights-alerts.md): Defina estes alertas para o avisar se uma métrica ultrapassar um limiar. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.

### <a name="automation"></a>Automatização

* [Automatizar a criação de um recurso do Application Insights](app-insights-powershell.md)
