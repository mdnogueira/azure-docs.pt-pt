---
title: "Application Insights para as web apps Java que já estão em direto"
description: "Iniciar a monitorização de uma aplicação web que já está em execução no seu servidor"
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: 152e21bd9fc6db424c3caff4ce425e54ecab11e2
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights para as web apps Java que já estão em direto


Se tiver uma aplicação web que já está em execução no seu servidor J2EE, pode começar a monitorizar com [Application Insights](app-insights-overview.md) sem a necessidade de fazer alterações ao código ou recompilar o projeto. Com esta opção, obter informações sobre pedidos HTTP enviados para o servidor, exceções não processadas e os contadores de desempenho.

Precisará de uma subscrição do [Microsoft Azure](https://azure.com).

> [!NOTE]
> O procedimento nesta página adiciona o SDK à sua aplicação web no tempo de execução. Este instrumentação de runtime é útil se não pretender atualizar ou reconstrua o código de origem. Mas se possível, recomendamos que [adicionar o SDK ao código fonte](app-insights-java-get-started.md) em vez disso. Que dá-lhe mais opções como escrever código para controlar a atividade do utilizador.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obter uma chave de instrumentação do Application Insights
1. Iniciar sessão para o [portal do Microsoft Azure](https://portal.azure.com)
2. Criar um novo recurso do Application Insights e defina o tipo de aplicação para aplicações web de Java.
   
    ![Preencher um nome, escolher aplicação Web em Java e clicar em criar](./media/app-insights-java-live/02-create.png)

    O recurso é criado dentro de alguns segundos.

4. Abra o novo recurso e obter a chave de instrumentação. Terá de colar esta chave no seu projeto de código em breve.
   
    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Transferir o SDK
1. Transfira o [Application Insights SDK para Java](https://aka.ms/aijavasdk). 
2. No seu servidor, extraia os conteúdos do SDK para o diretório a partir da qual são carregados os binários de projeto. Se estiver a utilizar Tomcat, este diretório normalmente seriam em`webapps/<your_app_name>/WEB-INF/lib`

Tenha em atenção que terá de repetir este em cada instância de servidor e para cada aplicação.

## <a name="3-add-an-application-insights-xml-file"></a>3. Adicionar um ficheiro xml do Application Insights
Crie ApplicationInsights.xml na pasta na qual adicionou o SDK. Inserida-o seguinte XML.

Substitua a chave de instrumentação que recebeu do portal do Azure.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* A chave de instrumentação é enviada juntamente com todos os itens de telemetria e diz ao Application Insights para apresentá-la no seu recurso.
* O componente de Pedido HTTP é opcional. Envia automaticamente telemetria sobre pedidos e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de pedido HTTP. Atribui um identificador a cada pedido recebido pelo servidor e adiciona este identificador como uma propriedade a todos os itens de telemetria como a propriedade "Operation.Id". Permite-lhe correlacionar a telemetria associada a cada pedido, definindo um filtro em [pesquisa de diagnóstico](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Adicionar um filtro HTTP
Localize e abra o ficheiro web.xml no seu projeto e intercale o seguinte fragmento de código sob o nó da aplicação web, onde os filtros da aplicação são configurados.

Para obter resultados mais exatos, o filtro deve ser mapeado antes de todos os outros filtros.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5. Verifique as exceções de firewall
Poderá ter de [definir exceções para enviar dados de saída](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Reinicie a aplicação web
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Ver a telemetria no Application Insights
Regresse ao seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Telemetria sobre pedidos de HTTP é apresentada no painel de descrição geral. (Se não aparecerem, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![dados de exemplo](./media/app-insights-java-live/5-results.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. 

![](./media/app-insights-java-live/6-barchart.png)

E, ao visualizar as propriedades de um pedido, pode ver os eventos de telemetria associados, como os pedidos e exceções.

![](./media/app-insights-java-live/7-instance.png)

[Saiba mais sobre métricas.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Passos seguintes
* [Adicione a telemetria às suas páginas web](app-insights-javascript.md) para monitorizar vistas de página e métricas de utilizador.
* [Configurar testes web](app-insights-monitor-web-app-availability.md) para se certificar de que a aplicação permanece em direto e reativa.
* [Capturar rastreios de registo](app-insights-java-trace-logs.md)
* [Pesquise eventos e registos](app-insights-diagnostic-search.md) para ajudar a diagnosticar problemas.

