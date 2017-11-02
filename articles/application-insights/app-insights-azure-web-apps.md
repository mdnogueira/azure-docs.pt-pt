---
title: "Monitorizar o desempenho das aplicações Web do Azure | Microsoft Docs"
description: "Monitorização do desempenho de aplicações para aplicações Web do Azure. Crie gráficos de tempos de carregamento e resposta, informações de dependências e defina alertas relativos ao desempenho."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: mbullwin
ms.openlocfilehash: e3615280ec902833248d9acc1c9348d68e5c5e82
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-azure-web-app-performance"></a>Monitorizar o desempenho das aplicações Web do Azure
No [Portal do Azure](https://portal.azure.com), pode configurar a monitorização de desempenho de aplicações para as suas [aplicações Web do Azure](../app-service/app-service-web-overview.md). O [Azure Application Insights](app-insights-overview.md) instrui a sua aplicação a enviar telemetria sobre as atividades para o serviço Application Insights, onde são armazenadas e analisadas. Aí, podem ser utilizados gráficos de métricas e ferramentas de pesquisa para ajudar a diagnosticar problemas, melhorar o desempenho e avaliar a utilização.

## <a name="run-time-or-build-time"></a>Hora de execução ou hora de compilação
Para configurar a monitorização, pode instrumentar a aplicação de uma de duas formas:

* **Tempo de execução** - pode selecionar uma extensão de monitorização se a sua aplicação Web já estiver publicada. Não é necessário recompilar ou reinstalar a aplicação. Obtém um conjunto padrão de pacotes que monitorizam os tempos de resposta, as taxas de êxito, as exceções, as dependências e assim sucessivamente. 
* **Tempo de compilação** - pode instalar um pacote na sua aplicação durante a programação. Esta opção é mais versátil. Para além dos mesmos pacotes padrão, pode escrever código para personalizar a telemetria ou enviar a sua própria telemetria. Pode registar atividades específicas ou eventos, de acordo com a semântica do domínio da aplicação. 

## <a name="run-time-instrumentation-with-application-insights"></a>Instrumentação do tempo de execução com o Application Insights
Se já estiver a executar uma aplicação Web no Azure, já beneficia de alguma monitorização: taxas de pedidos e de erros. Adicione o Application Insights para beneficiar de mais monitorização, como tempos de resposta, monitorização de chamadas para dependências, deteção inteligente e a poderosa linguagem de consultas do Log Analytics. 

1. **Selecione Application Insights** no painel de controlo do Azure da sua aplicação Web.
   
    ![Em Monitorização, escolha Application Insights](./media/app-insights-azure-web-apps/05-extend.png)
   
   * Opte por criar um recurso novo, a menos que já tenha configurado um recurso do Application Insights para esta aplicação por outro meio.
2. **Instrumente a sua aplicação Web** depois de o Application Insights ser instalado. 
   
    ![Instrumente a sua aplicação Web](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)

   **Ative a monitorização do lado do cliente** para visualização de páginas e telemetria do utilizador.

   * Selecione Definições > Definições da Aplicação
   * Em Definições da Aplicação, adicione um par de chaves-valores novo: 
   
    Chave: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Valor:`true`
   * **Guarde** as definições e **reinicie** a aplicação.
3. **Monitorizar a sua aplicação**  [Explorar os dados](#explore-the-data).

Mais tarde, pode criar a aplicação com o Application Insights, se pretender.

*Como posso remover o Application Insights ou mudar para enviar para outro recurso?*

* No Azure, abra o painel de controlo da aplicação Web e, em Ferramentas de Programação, abra **Extensões**. Elimine a extensão do Application Insights. Depois, em monitorização, escolha Application Insights e crie ou selecione o recurso que pretende.

## <a name="build-the-app-with-application-insights"></a>Compilar a aplicação com o Application Insights
O Application Insights pode proporcionar telemetria mais detalhada, mediante a instalação de um SDK na sua aplicação. Em particular, pode recolher registos de rastreio, [escrever telemetria personalizada](app-insights-api-custom-events-metrics.md)e obter relatórios de exceções mais detalhados.

1. **No Visual Studio** (2013 update 2 ou posterior), configure o Application Insights para o seu projeto.

    Clique com o botão direito do rato no projeto Web e selecione **Adicionar > Application Insights** ou **Configurar o Application Insights**.
   
    ![Clique com o botão direito do rato no projeto e escolha Adicionar ou Configurar o Application Insights](./media/app-insights-azure-web-apps/03-add.png)
   
    Se lhe for pedido para iniciar sessão, utilize as credenciais da sua conta do Azure.
   
    A operação tem dois efeitos:
   
   1. Cria um recurso do Application Insights no Azure, onde a telemetria é armazenada, analisada e apresentada.
   2. Adiciona o pacote NuGet do Application Insights ao seu código (se ainda não estiver lá) e configura-o para enviar a telemetria para o recurso do Azure.
2. Execute a aplicação no seu computador de programação (F5) para **testar a telemetria**.
3. **Publique a aplicação** no Azure, da forma habitual. 

*Como posso mudar para enviar para outro recurso do Application Insights?*

* No Visual Studio, clique com o botão direito do rato no projeto, escolha **Configurar o Application Insights** e selecione o recurso que pretende. Terá a opção de criar um recurso novo. Recompile e reimplemente.

## <a name="explore-the-data"></a>Explorar os dados
1. No painel Application Insights do painel de controlo da sua aplicação Web, vai ver Métricas em Direto, que mostra os pedidos e as falhas ao fim de um ou dois segundos de terem ocorrido. É um dado muito importante quando está a publicar de novo a sua aplicação, pois pode ver eventuais problemas imediatamente.
2. Clique para aceder ao recurso completo do Application Insights.

    ![Clicar](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    Também pode aceder aí diretamente a partir da navegação do Azure.

1. Clique em qualquer gráfico para obter mais detalhes:
   
    ![No painel de descrição geral do Application Insights, clique num gráfico](./media/app-insights-azure-web-apps/07-dependency.png)
   
    Pode [personalizar os painéis das métricas](app-insights-metrics-explorer.md).
2. Clique mais para ver eventos individuais e respetivas propriedades:
   
    ![Clique num tipo de evento para abrir uma pesquisa filtrada para esse tipo](./media/app-insights-azure-web-apps/08-requests.png)
   
    Repare na ligação “...” para abrir todas as propriedades.
   
    Pode [personalizar as pesquisas](app-insights-diagnostic-search.md).

Para fazer pesquisas mais poderosas na sua telemetria, utilize a [linguagem de consultas do Log Analytics](app-insights-analytics-tour.md).

## <a name="more-telemetry"></a>Mais telemetria

* [Web page load data](app-insights-javascript.md) (Dados de carregamento de páginas Web)
* [Custom telemetry](app-insights-api-custom-events-metrics.md) (Telemetria personalizada)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes
* [Run the profiler on your live app](app-insights-profiler.md) (Executar o gerador de perfis na sua aplicação publicada).
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample) - monitorize Funções do Azure com o Application Insights
* [Ativar os diagnósticos do Azure](app-insights-azure-diagnostics.md) para serem enviados para o Application Insights.
* [Monitorizar as métricas de estado de funcionamento de serviço](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e a responder.
* [Receber notificações de alertas](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) sempre que ocorrem eventos operacionais ou quando as métricas ultrapassam um determinado limiar.
* Utilizar o [Application Insights para aplicações JavaScript e páginas Web](app-insights-javascript.md) para obter telemetria de clientes a partir dos browsers que visitam as páginas Web.
* [Configurar os Testes Web de disponibilidade](app-insights-monitor-web-app-availability.md) para ser alertado se o seu site estiver em baixo.

