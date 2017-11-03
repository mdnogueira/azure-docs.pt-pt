---
title: "Referência de Applicationinsights - Azure | Microsoft Docs"
description: "Ativar ou desativar os módulos de recolha de dados e adicionar os contadores de desempenho e outros parâmetros."
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
editor: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: e59df358f25663c742b0da09cf27b974787536dc
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar o SDK do Application Insights com ApplicationInsights.config ou .xml
O SDK .NET do Application Insights é composta por um número de pacotes NuGet. O [pacote core](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para enviar telemetria para o Application Insights. [Pacotes adicionais](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecer telemetria *módulos* e *inicializadores* para controlo automaticamente telemetria da sua aplicação e o respetivo contexto. Ao ajustar o ficheiro de configuração, pode ativar ou desativar os módulos de telemetria e inicializadores e definir os parâmetros de algumas delas.

O ficheiro de configuração é denominado `ApplicationInsights.config` ou `ApplicationInsights.xml`, consoante o tipo da sua aplicação. É automaticamente adicionada ao seu projeto quando que [instalar a maioria das versões do SDK][start]. Também é adicionada a uma aplicação web [Monitor de estado num servidor IIS][redfield], ou quando seleciona o Insights Appplication [extensão para um Web site Azure ou VM](app-insights-azure-web-apps.md).

Não é um ficheiro equivalente para controlar o [SDK numa página web][client].

Este documento descreve as secções que vê na configuração do ficheiro, como controlarem se os componentes do SDK, e quais os pacotes NuGet carregar desses componentes.

## <a name="telemetry-modules-aspnet"></a>Módulos de telemetria (ASP.NET)
Cada módulo telemetria recolhe um tipo específico de dados e utiliza as principais API para enviar os dados. Os módulos são instalados por diferentes pacotes de NuGet, que também adicione as linhas necessárias para o ficheiro. config.

Não há um nó no ficheiro de configuração para cada módulo. Para desativar um módulo, elimine o nó ou comente-lo.

### <a name="dependency-tracking"></a>Controlo de dependência
[Controlo de dependência](app-insights-asp-net-dependencies.md) recolhe telemetria sobre as chamadas a aplicação faz com que as bases de dados e serviços externos e as bases de dados. Para permitir que este módulo funcionar no servidor IIS, terá de [instalar Monitor de estado][redfield]. Utilizá-la de aplicações web do Azure ou VMs, [selecione a extensão do Application Insights](app-insights-azure-web-apps.md).

Também pode escrever o seu próprio controlo utilizando o código de dependência a [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) pacote NuGet.

### <a name="performance-collector"></a>Recoletor de desempenho
[Recolhe os contadores de desempenho do sistema](app-insights-performance-counters.md) tais como CPU, memória e rede carregar das instalações do IIS. Pode especificar que contadores para recolher, incluindo os contadores de desempenho que configurou por si.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) pacote NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Telemetria de diagnóstico do Application Insights
O `DiagnosticsTelemetryModule` relatórios de erros no código de instrumentação do Application Insights próprio. Por exemplo, se o código não é possível aceder aos contadores de desempenho ou se um `ITelemetryInitializer` emite uma exceção. Telemetria de rastreio registada por este módulo é apresentado no [pesquisa de diagnóstico][diagnostic]. Envia dados de diagnóstico para dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) pacote NuGet. Se apenas a instalar este pacote, o ficheiro Applicationinsights não é criado automaticamente.

### <a name="developer-mode"></a>Modo de programador
`DeveloperModeWithDebuggerAttachedTelemetryModule`força o Application Insights `TelemetryChannel` para enviar dados imediatamente, itens de um telemetria cada vez, quando é ligado um depurador para o processo de aplicação. Isto reduz a quantidade de tempo entre o momento em que, quando a aplicação controla telemetria e quando é apresentado no portal do Application Insights. Causa overhead significativo na largura de banda de CPU e da rede.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Windows Server do Application Insights](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) pacote NuGet

### <a name="web-request-tracking"></a>Pedido de Web de controlo
Relatórios de [código de tempo e o resultado da resposta](app-insights-asp-net.md) de pedidos HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Applicationinsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) pacote NuGet

### <a name="exception-tracking"></a>Controlo de exceções
`ExceptionTrackingTelemetryModule`controla exceções não processadas na aplicação web. Consulte [falhas e exceções][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Applicationinsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) pacote NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-controla [não observada exceções tarefas](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-controla exceções não processadas para as funções de trabalho, serviços do windows e aplicações de consola.
* [Windows Server do Application Insights](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) pacote NuGet.

### <a name="eventsource-tracking"></a>Controlo de EventSource
`EventSourceTelemetryModule`permite-lhe configurar eventos EventSource sejam enviados para o Application Insights como rastreios. Para obter informações sobre o registo de eventos de EventSource, consulte [através de eventos de EventSource](app-insights-asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Registo de eventos do ETW
`EtwCollectorTelemetryModule`permite-lhe configurar eventos a partir de fornecedores ETW sejam enviados para o Application Insights como rastreios. Para obter informações sobre o registo de eventos ETW, consulte [através de eventos do ETW](app-insights-asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
O pacote de Microsoft.ApplicationInsights fornece o [principal API](https://msdn.microsoft.com/library/mt420197.aspx) do SDK. Utilizam esta opção os outros módulos de telemetria e também pode [utilizá-la para definir a sua própria telemetria](app-insights-api-custom-events-metrics.md).

* Nenhuma entrada no Applicationinsights.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) pacote NuGet. Se instalar apenas esta NuGet, não é gerado nenhum ficheiro. config.

## <a name="telemetry-channel"></a>Canal de telemetria
O canal de telemetria gere a colocação em memória intermédia e transmissão de telemetria para o serviço do Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`é o canal predefinido para os serviços. Este serve de memória intermédia dados na memória.
* `Microsoft.ApplicationInsights.PersistenceChannel`é uma alternativa para aplicações de consola. -Pode guardar quaisquer dados unflushed armazenamento persistente quando a aplicação fecha-se para baixo e envia-lo quando a aplicação for iniciada novamente.

## <a name="telemetry-initializers-aspnet"></a>Inicializadores de telemetria (ASP.NET)
Os inicializadores de telemetria definir propriedades de contexto, que são enviadas juntamente com todos os itens de telemetria.

Pode [escrever os seus próprios inicializadores](app-insights-api-filtering-sampling.md#add-properties) para definir propriedades de contexto.

Os inicializadores padrão estão todos definidos pelos pacotes de Web ou WindowsServer NuGet:

* `AccountIdTelemetryInitializer`Define a propriedade AccountId.
* `AuthenticatedUserIdTelemetryInitializer`Define a propriedade AuthenticatedUserId conforme definido pelo SDK de JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer`atualizações a `RoleName` e `RoleInstance` propriedades do `Device` contexto de todos os itens de telemetria com informações extraídas do ambiente de tempo de execução do Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`atualizações a `Version` propriedade o `Component` contexto de todos os itens de telemetria com o valor extraídos do `BuildInfo.config` ficheiro produzido pelo MS Build.
* `ClientIpHeaderTelemetryInitializer`atualizações `Ip` propriedade o `Location` contexto de todos os itens de telemetria com base no `X-Forwarded-For` cabeçalho HTTP do pedido.
* `DeviceTelemetryInitializer`Atualiza as seguintes propriedades do `Device` contexto de todos os itens de telemetria.
  * `Type`está definido como "PC"
  * `Id`está definido para o nome de domínio do computador onde a aplicação web está em execução.
  * `OemName`está definido para o valor extraído do `Win32_ComputerSystem.Manufacturer` campo através do WMI.
  * `Model`está definido para o valor extraído do `Win32_ComputerSystem.Model` campo através do WMI.
  * `NetworkType`está definido para o valor extraído do `NetworkInterface`.
  * `Language`está definido como o nome do `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`atualizações a `RoleInstance` propriedade o `Device` contexto de todos os itens de telemetria com o nome de domínio do computador onde a aplicação web está em execução.
* `OperationNameTelemetryInitializer`atualizações a `Name` propriedade do `RequestTelemetry` e o `Name` propriedade o `Operation` contexto de todos os itens de telemetria com base no método HTTP, bem como os nomes de controlador MVC do ASP.NET e a ação invocada para processar o pedido.
* `OperationIdTelemetryInitializer`ou `OperationCorrelationTelemetryInitializer` atualizações a `Operation.Id` propriedade de contexto de todos os itens de telemetria controlados durante o processamento de um pedido com gerado automaticamente `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`atualizações a `Id` propriedade o `Session` contexto de todos os itens de telemetria com valor extraídos do `ai_session` cookie gerado pelo código da instrumentação de ApplicationInsights JavaScript em execução no browser do utilizador.
* `SyntheticTelemetryInitializer`ou `SyntheticUserAgentTelemetryInitializer` atualizações a `User`, `Session` e `Operation` controlado de propriedades de contextos de todos os itens de telemetria ao processar um pedido de uma origem sintética, tal como um disponibilidade testar ou bot do motor de pesquisa. Por predefinição, [Explorador de métricas](app-insights-metrics-explorer.md) não apresenta a telemetria sintética.

    O `<Filters>` definir identificar as propriedades dos pedidos.
* `UserAgentTelemetryInitializer`atualizações a `UserAgent` propriedade o `User` contexto de todos os itens de telemetria com base no `User-Agent` cabeçalho HTTP do pedido.
* `UserTelemetryInitializer`atualizações a `Id` e `AcquisitionDate` propriedades de `User` contexto de todos os itens de telemetria com valores extraídos do `ai_user` cookie gerado pelo código de instrumentação do Application Insights JavaScript em execução no browser do utilizador.
* `WebTestTelemetryInitializer`Define o id de utilizador, id de sessão e propriedades da origem sintético para pedidos de HTTP que provenientes do [testes de disponibilidade](app-insights-monitor-web-app-availability.md).
  O `<Filters>` definir identificar as propriedades dos pedidos.

Para aplicações de .NET em execução no Service Fabric, pode incluir o `Microsoft.ApplicationInsights.ServiceFabric` pacote NuGet. Este pacote inclui um `FabricTelemetryInitializer`, que adiciona propriedades de Service Fabric para itens de telemetria. Para obter mais informações, consulte o [página do GitHub](https://go.microsoft.com/fwlink/?linkid=848457) sobre as propriedades adicionadas por este pacote NuGet.

## <a name="telemetry-processors-aspnet"></a>Processadores de telemetria (ASP.NET)
Processadores de telemetria podem filtrar e modificar cada item de telemetria apenas antes de ser enviada do SDK para o portal.

Pode [escrever os seus próprios processadores de telemetria](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processador de telemetria de amostragem adaptável (a partir de 2.0.0-Beta3)
Opção ativada por predefinição. Se a aplicação envia uma grande quantidade de telemetria, este processador remove algumas do mesmo.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

O parâmetro fornece o destino em que o algoritmo tenta alcançar. Cada instância do SDK funciona de forma independente, pelo que o se o servidor for um cluster de vários computadores, o volume real dos telemetria irá ser multiplicado em conformidade.

[Saiba mais sobre amostragem](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>A amostragem-taxa telemetria processador (2.0.0-beta1)
Também é uma norma [amostragem processador telemetria](app-insights-api-filtering-sampling.md) (a partir de 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parâmetros do canal (Java)
Estes parâmetros afetam a forma como o SDK de Java deve armazenar e esvaziar os dados de telemetria que recolhe.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
O número de itens de telemetria que podem ser armazenados no armazenamento de dentro da memória do SDK. Quando este número for atingido, a memória intermédia de telemetria é descarregada - ou seja, os itens de telemetria são enviados para o servidor do Application Insights.

* Mínimo: 1
* Máx.: 1000
* Predefinição: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Determina com que frequência os dados armazenados no armazenamento na memória devem ser removida da cache (enviado para o Application Insights).

* Mínimo: 1
* Máx.: 300
* Predefinição: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Determina o tamanho máximo em MB, que é atribuído ao armazenamento persistente no disco local. Este tipo de armazenamento é utilizado para itens de telemetria persistentes que falharam ao ser transmitidos para o ponto final do Application Insights. Quando o tamanho de armazenamento tiver sido cumprido, novos itens de telemetria serão eliminados.

* Mínimo: 1
* Máxima: 100
* Predefinição: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey
Isto determina o recurso do Application Insights na qual os dados são apresentados. Normalmente, crie um recurso de separado, com uma chave separada, para cada uma das suas aplicações.

Se pretender definir a chave dinamicamente - por exemplo, se pretender enviar os resultados da sua aplicação para diferentes recursos - pode omitir a chave no ficheiro de configuração e configurá-lo no código.

Para definir a chave para todas as instâncias TelemetryClient, incluindo módulos de telemetria padrão, defina a chave no TelemetryConfiguration.Active. Fazê-lo um método de inicialização, tais como global.aspx.cs num serviço ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Se pretender enviar um conjunto específico de eventos para um recurso diferente, pode definir a chave para um TelemetryClient específico:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Para obter uma nova chave, [criar um novo recurso no portal do Application Insights][new].

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre a API][api].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
