---
title: "Transmissão de velocidade de métricas com métricas personalizadas e diagnósticos no Azure Application Insights | Microsoft Docs"
description: "Monitorizar a aplicação web em tempo real com métricas personalizadas e diagnosticar problemas com um feed de falhas, rastreios e eventos em direto."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mbullwin
ms.openlocfilehash: 866fc729b3167863c2d423d0e6ac0d7640e3425e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Métricas em fluxo em direto: Monitor de & Diagnostique com uma latência de 1 segundo 

Pesquisa o heart beating da aplicação web em direto, de produção, utilizando o fluxo de métricas em direto de [Application Insights](app-insights-overview.md). Selecionar e filtrar os contadores de desempenho e métricas para ver em tempo real, sem qualquer disturbance ao seu serviço. Inspecione os rastreios de pilha do exemplo falhado pedidos e exceções. Em conjunto com [gerador de perfis](app-insights-profiler.md), [depurador de instantâneo](app-insights-snapshot-debugger.md), e [teste de desempenho](app-insights-monitor-web-app-availability.md#performance-tests), métricas em fluxo em direto fornece uma ferramenta de diagnóstico não é INVASIVO e poderosa para o web site em direto.

Com o fluxo de métricas em direto, pode:

* Validar uma correção enquanto é lançada, ao observar as contagens de desempenho e falha.
* Observar o efeito de cargas de teste e diagnosticar problemas em direto. 
* Concentre-se em sessões de teste específica ou filtrar problemas conhecidos, ao selecionar e filtrar as métricas que pretende ver.
* Obter rastreios de exceção que possam ocorrem.
* Experimente os filtros para localizar os KPIs mais relevantes.
* Monitor de quaisquer janelas de desempenho contador em direto.
* Identifica facilmente um servidor que está a ter problemas e o filtro de todos os o KPI/live feed apenas nesse servidor.

[![Vídeo de transmissão de métricas em direto](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Transmissão de métricas em direto se encontra atualmente disponível nas aplicações do ASP.NET em execução no local ou na nuvem. 

## <a name="get-started"></a>Introdução

1. Se ainda [instalou o Application Insights](app-insights-asp-net.md) na aplicação web ASP.NET ou [aplicação do Windows server](app-insights-windows-services.md), faça-o agora. 
2. **Atualize para a versão mais recente** do pacote do Application Insights. No Visual Studio, clique no seu projeto e escolha **pacotes Nuget gerir**. Abra o **atualizações** separador, verificação **incluir pré-lançamento**e selecione todos os pacotes de Microsoft.ApplicationInsights.*.

    Volte a implementar a aplicação.

3. No [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para a sua aplicação e, em seguida, abrir o fluxo em direto.

4. [Proteja o canal de controlo](#secure-the-control-channel) se pode utilizar os dados confidenciais, tais como nomes de cliente nos filtros.


![No painel de descrição geral, clique em fluxo em direto](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>Não existem dados? Verifique a firewall do servidor

Verifique o [portas de saída para a transmissão de métricas em direto](app-insights-ip-addresses.md#outgoing-ports) estão abertas na firewall dos seus servidores. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Como diferem métricas em fluxo em direto a partir do Explorador de métricas e análise?

| |Live Stream | Explorador de métricas e análise |
|---|---|---|
|Latência|Dados apresentados dentro de um segundo|Agregado ao longo de minutos|
|Nenhuma retenção|Dados persistir enquanto está no gráfico e, em seguida, são rejeitado|[Dados mantidos durante 90 dias](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|A pedido|Dados é transmitida em fluxo enquanto abrir métricas em direto|Os dados são enviados sempre que o SDK está instalado e ativado|
|Gratuito|Há sem qualquer encargo para dados de fluxo em direto|Sujeitos a [preços](app-insights-pricing.md)
|Amostragem|Todas as métricas e contadores são transmitidos. Servem como amostra falhas e de rastreios de pilha. TelemetryProcessors não são aplicadas.|Eventos podem ter [amostragem](app-insights-api-filtering-sampling.md)|
|Canal de controlo|Sinais de controlo de filtro são enviadas para o SDK. Recomendamos que [este canal](#secure-channel).|Comunicação é unidirecional, para o portal|


## <a name="select-and-filter-your-metrics"></a>Selecionar e filtrar as métricas

(Disponível nas aplicações ASP.NET clássicas com o SDK mais recente.)

Pode monitorizar o KPI personalizado em direto aplicando filtros arbitrários qualquer telemetria do Application Insights no portal. Clique no controlo de filtro que mostra quando rato a ativação pós-falha qualquer um dos gráficos. O gráfico seguinte está a representar uma contagem de pedido de KPI personalizada com filtros nos atributos de URL e duração. Valide os filtros com a secção de pré-visualização de fluxo que mostra um feed em direto de telemetria que corresponde aos critérios de que especificou em qualquer ponto no tempo. 

![Pedido personalizado KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Pode monitorizar um valor diferente da contagem. As opções dependem do tipo de fluxo, o que pode ser qualquer telemetria do Application Insights: pedidos, dependências, exceções, rastreios, eventos ou métricas. Pode ser a suas próprias [medida personalizada](app-insights-api-custom-events-metrics.md#properties):

![Opções de valor](./media/app-insights-live-stream/live-stream-valueoptions.png)

Para além de telemetria do Application Insights, também é possível monitorizar qualquer contador de desempenho do Windows selecionando que as opções de fluxo e fornecer o nome do contador de desempenho.

Métricas em direto são agregadas em dois pontos: localmente em cada servidor e, em seguida, em todos os servidores. Pode alterar a predefinição em qualquer um, selecionando outras opções no respetivos listas pendentes.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria de exemplo: Eventos de diagnóstico em direto personalizados
Por predefinição, o feed de eventos em direto mostra exemplos de pedidos falhados e chamadas de dependência, exceções, eventos e rastreios. Clique no ícone de filtro para ver os critérios aplicados em qualquer ponto no tempo. 

![Feed em direto predefinido](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Como com métricas, pode especificar qualquer critérios arbitrários para qualquer um dos tipos de telemetria do Application Insights. Neste exemplo, vamos estão a selecionar eventos, rastreios e falhas de pedidos específicos. Podemos também são selecionar todas as exceções e falhas de dependência.

![Personalizado feed em direto](./media/app-insights-live-stream/live-stream-events.png)

Nota: Atualmente, para os critérios de com base na mensagem de exceção, utilize a mensagem de exceção mais exterior. No exemplo anterior, para filtrar a exceção benignas com a mensagem de excepção interna (segue-se a "< –" delimitador) "o cliente desligado." Utilize uma mensagem não-contém os critérios de "Erro ao ler o conteúdo do pedido".

Consulte os detalhes de um item no feed em direto clicando nela. Pode interromper o feed clicando **colocar em pausa** ou simplesmente deslocar o ecrã para baixo, ou clicar um item. Feed em direto será retomada depois de se deslocar para a voltar à parte superior, ou clicando o contador de itens recolhidos enquanto estava em pausa.

![Amostras de falhas em direto](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância de servidor

Se pretender monitorizar uma instância de função de servidor em particular, pode filtrar por servidor.

![Amostras de falhas em direto](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Requisitos de SDK
Transmissão de métricas em direto personalizado está disponível com a versão 2.4.0-beta2 ou mais recente do [Application Insights SDK para o web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Lembre-se selecionar a opção "Incluir pré-lançamento" do Gestor de pacotes NuGet.

## <a name="secure-the-control-channel"></a>Proteja o canal de controlo
Os critérios de filtros personalizados que especificar são enviados para o componente de métricas em direto no Application Insights SDK. Os filtros, potencialmente, podem conter informações confidenciais, tais como customerIDs. Pode efetuar o canal seguro com uma chave de API secreta para além da chave de instrumentação.
### <a name="create-an-api-key"></a>Criar uma chave de API

![Criar chave de api](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave de API à configuração
No ficheiro applicationinsights, adicione o AuthenticationApiKey para o QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
Ou no código, defina-o QuickPulseTelemetryModule:

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

No entanto, se reconhecer e confiar todos os servidores ligados, pode tentar os filtros personalizados sem o canal autenticado. Esta opção está disponível durante seis meses. Esta substituição não é necessária uma vez cada nova sessão ou quando um novo servidor fica online.

![Opções de autenticação de métricas em direto](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Recomendamos vivamente que configurou o canal autenticado antes de introduzir informações potencialmente confidenciais, como CustomerID nos critérios de filtro.
>

## <a name="generating-a-performance-test-load"></a>Gerar uma carga de teste de desempenho

Se pretender que a observar o efeito de um aumento de carga, utilize o painel de teste de desempenho. Simula-pedidos de um número de utilizadores em simultâneo. Pode executar qualquer um dos testes"manuais" (ping testes) de um URL único, ou pode ser executado um [teste de desempenho da web com vários passos](app-insights-monitor-web-app-availability.md#multi-step-web-tests) que carregar (da mesma forma como um teste de disponibilidade).

> [!TIP]
> Depois de criar o teste de desempenho, abra o painel de fluxo em direto e o teste no windows separados. Pode ver quando inicia o teste de desempenho em fila e em fluxo em direto veja ao mesmo tempo.
>


## <a name="troubleshooting"></a>Resolução de problemas

Não existem dados? Se a aplicação estiver numa rede protegida: transmissão de métricas em direto utiliza um diferentes endereços IP que outros telemetria do Application Insights. Certifique-se [esses endereços IP](app-insights-ip-addresses.md) estão abertas na firewall.



## <a name="next-steps"></a>Passos seguintes
* [Utilização de monitorização com o Application Insights](app-insights-web-track-usage.md)
* [Utilizando a pesquisa de diagnóstico](app-insights-diagnostic-search.md)
* [Gerador de perfis](app-insights-profiler.md)
* [Depurador de instantâneo](app-insights-snapshot-debugger.md)
