---
title: "Filtrar a telemetria do Azure Application Insights na aplicação web Java | Microsoft Docs"
description: "Reduza o tráfego de telemetria ao filtrar os eventos que não precisa de monitorizar."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: f9e061c010667bc18ac54e6546cc25339e9c0e3e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrar a telemetria na aplicação web Java

Filtros de fornecem uma forma para selecionar a telemetria que o [aplicação web Java envia para o Application Insights](app-insights-java-get-started.md). Existem alguns filtros out of box, que pode utilizar e, também pode escrever o seus próprio filtros personalizados.

Os filtros de out of box incluem:

* Nível de gravidade de rastreio
* URLs específicos, as palavras-chave ou códigos de resposta
* Respostas rápidas - ou seja, os pedidos para o qual a aplicação respondeu ao rapidamente
* Nomes de evento específico

> [!NOTE]
> Filtros de desfasamento as métricas da sua aplicação. Por exemplo, poderá decidir que, para diagnosticar lentas, defina um filtro para eliminar os tempos de resposta rápidos. Mas deve ter em atenção que os tempos de resposta médio comunicados pelo Application Insights, em seguida, será mais lentos do que a velocidade true e a contagem de pedidos irá ser menor que a contagem real.
> Se esta for uma preocupação, utilize [amostragem](app-insights-sampling.md) em vez disso.

## <a name="setting-filters"></a>Definir filtros

No ApplicationInsights.xml, adicione um `TelemetryProcessors` secção com este exemplo:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Inspecione o conjunto completo de processadores incorporadas](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Filtros incorporados

### <a name="metric-telemetry-filter"></a>Filtro de telemetria métrico

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`-Lista separados por vírgulas de nomes de métricas personalizadas.


### <a name="page-view-telemetry-filter"></a>Filtro de telemetria de visualizações de página

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`-Duração refere-se para o tempo que demora a carregar a página. Se isto estiver definido, as páginas que carregar mais rapidamente do que este período de tempo não são reportadas.
* `NotNeededNames`-Lista separados por vírgulas de nomes de página.
* `NotNeededUrls`-Os fragmentos de lista separados por vírgulas de URL. Por exemplo, `"home"` filtra todas as páginas que tenham "página principal" no URL.


### <a name="request-telemetry-filter"></a>Filtro de telemetria de pedido


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtro de origem sintético

Filtra a toda a telemetria que têm valores na propriedade SyntheticSource. Estes incluem pedidos de bots, spiders e testes de disponibilidade.

Filtre a telemetria para todos os pedidos sintéticos:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtre a telemetria origens sintéticas específicas:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`-Lista de nomes de origem sintético separados por vírgulas.

### <a name="telemetry-event-filter"></a>Filtro de eventos de telemetria

Filtra eventos personalizados (registado utilizando [trackevent ()](app-insights-api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`-Lista de nomes do evento separados por vírgulas.


### <a name="trace-telemetry-filter"></a>Filtro de telemetria de rastreio

Filtros de rastreios de registo (registado utilizando [tracktrace ()](app-insights-api-custom-events-metrics.md#tracktrace) ou um [recoletor de arquitetura de registo](app-insights-java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel`os valores válidos são:
 *  DESATIVAR - filtrar todos os rastreios
 *  RASTREIO - nenhuma filtragem. igual a nível de rastreio
 *  INFORMAÇÃO - filtro o nível de rastreio
 *  AVISO - filtro de rastreio e de informações
 *  ERRO - o filtro de saída de rastreio de aviso, informações,
 *  CRÍTICO - filtro enviados todos os léxicos crítico


## <a name="custom-filters"></a>Filtros personalizados

### <a name="1-code-your-filter"></a>1. O filtro de código

No seu código, crie uma classe que implementa `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Invocar o filtro no ficheiro de configuração

No ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

## <a name="troubleshooting"></a>Resolução de problemas

*A minha filtro não está a funcionar.*

* Certifique-se de que forneceu valores de parâmetro válido. Por exemplo, durações devem ser números inteiros. Valores inválidos fará com que o filtro a ser ignorados. Se o seu filtro personalizado emite uma exceção de um construtor ou método definido, será ignorado.

## <a name="next-steps"></a>Passos seguintes

* [Amostragem](app-insights-sampling.md) -considere amostragem como alternativa que não desfasamento as métricas.
