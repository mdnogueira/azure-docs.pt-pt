---
title: "Filtragem e pré-processamentos no Application Insights SDK do Azure | Microsoft Docs"
description: Escreva processadores de telemetria e inicializadores de telemetria para o SDK para filtrar ou adicionar propriedades para os dados antes do envio de telemetria ao portal do Application Insights.
services: application-insights
documentationcenter: 
author: beckylino
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: borooji;mbullwin
ms.openlocfilehash: 9261f44a0c0400a0a8d908b0ff72318c637771de
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtragem e pré-processamentos telemetria no Application Insights SDK


Pode escrever e configurar o plug-ins para o Application Insights SDK personalizar a forma como a telemetria é capturada e processada antes de ser enviada para o serviço do Application Insights.

* [Amostragem](app-insights-sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Em conjunto mantém relacionadas com pontos de dados, de modo a que possa navegar entre os mesmos quando diagnosticar um problema. No portal, o número total é multiplicado para compensar a amostragem.
* Filtragem com processadores de telemetria [para ASP.NET](#filtering) ou [Java](app-insights-java-filter-telemetry.md) permite-lhe selecionar ou modificar a telemetria no SDK antes de ser enviada para o servidor. Por exemplo, poderia reduzir o volume de telemetria excluindo pedidos da robots. Mas filtragem é uma abordagem mais básica para reduzir o tráfego de amostragem. Permite-lhe mais controlo sobre o que é transmitido, mas tem de ter conhecimento de que afetará as estatísticas - por exemplo, se filtrar todos os pedidos com êxito.
* [Os inicializadores de telemetria adicionar propriedades](#add-properties) a qualquer telemetria enviada a partir da sua aplicação, incluindo a telemetria dos módulos padrão. Por exemplo, pode adicionar valores calculados; ou números de versão através do qual filtrar os dados no portal.
* [A API do SDK](app-insights-api-custom-events-metrics.md) é utilizada para enviar métricas e eventos personalizados.

Antes de começar:

* Instale o Application Insights [SDK para ASP.NET](app-insights-asp-net.md) ou [SDK para Java](app-insights-java-get-started.md) na sua aplicação.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtragem: ITelemetryProcessor
Esta técnica dá-lhe controlo direto mais sobre o que é incluído ou excluído do fluxo de telemetria. Pode utilizá-la em conjunto com a amostragem, ou em separado.

Para filtrar telemetria, escrever um processador de telemetria e registá-lo com o SDK. Toda a telemetria atravessa o processador e pode optar por removê-la a partir do fluxo ou adicionar propriedades. Isto inclui a telemetria dos módulos padrão, tais como o recoletor de pedido HTTP e o recoletor de dependência, bem como telemetria que ter escrito por si. Por exemplo, pode filtrar telemetria sobre pedidos de robots ou chamadas de dependência com êxito.

> [!WARNING]
> Filtragem a telemetria enviada do SDK com processadores pode prejudicar que vir no portal as estatísticas e dificultar a seguir itens relacionados.
>
> Em vez disso, considere utilizar [amostragem](app-insights-sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Criar um processador de telemetria (c#)
1. Certifique-se de que o Application Insights SDK no projeto versão 2.0.0 ou posterior. O projeto no Explorador de soluções do Visual Studio com o botão direito e selecione gerir pacotes NuGet. No Gestor de pacotes NuGet, verifique applicationinsights.
2. Para criar um filtro, implementa ITelemetryProcessor. Este é outro ponto de extensibilidade, como o módulo de telemetria, o inicializador de telemetria e o canal de telemetria.

    Tenha em atenção que os processadores de telemetria construir uma cadeia de processamento. Quando instanciar um processador de telemetria, passa uma ligação para o processador seguinte da cadeia. Quando um ponto de dados de telemetria é passado para o método de processo, não o trabalho e, em seguida, chama o processador de telemetria seguinte da cadeia.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
1. Insira-o no Applicationinsights:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Esta é a mesma secção onde a inicializar um filtro de amostragem.)

Pode passar valores de cadeia do ficheiro. config, fornecendo propriedades com nome públicas na sua classe.

> [!WARNING]
> Asseguramos para corresponder a quaisquer nomes de propriedade no ficheiro. config para os nomes de classe e a propriedade com o código e o nome do tipo. Se o ficheiro. config referencia um tipo de inexistente ou uma propriedade, o SDK silenciosamente pode falhar ao enviar a qualquer telemetria.
>
>

**Em alternativa,** pode inicializar o filtro no código. Classe de inicialização adequado - por exemplo AppStart no Global.asax.cs - inserir o processador de cadeia:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients criadas após este ponto irá utilizar o fabricante.

### <a name="example-filters"></a>Filtros de exemplo
#### <a name="synthetic-requests"></a>Pedidos sintéticos
Filtre testes bots e web. Embora o Explorador de métricas dá-lhe a opção para filtrar sintéticas origens, esta opção reduz o tráfego filtrando-los no SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Falha na autenticação
Filtre pedidos com uma resposta "401".

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar chamadas de dependência rápido remoto
Se pretender apenas diagnosticar chamadas lentas, filtre as fast.

> [!NOTE]
> Isto irá desfasamento as estatísticas que vir no portal. O gráfico de dependência procurará como se as chamadas de dependência são todas as falhas.
>
>

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependência
[Este blogue](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência enviando automaticamente pings regulares para dependências.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Adicionar propriedades: ITelemetryInitializer
Inicializadores de telemetria de utilização para definir propriedades globais que são enviadas com toda a telemetria; e substituir selecionado comportamento dos módulos de telemetria padrão.

Por exemplo, o Application Insights para o pacote do Web recolhe telemetria sobre pedidos de HTTP. Por predefinição, sinalizadores como falhada qualquer pedido com um código de resposta > = 400. Mas se pretender processar 400 como concluído com êxito, pode fornecer um inicializador de telemetria que define a propriedade de êxito.

Se fornecer um inicializador de telemetria, é chamado sempre que qualquer um dos métodos Track*() é chamado. Isto inclui métodos chamados por módulos de telemetria padrão. Por convenção, estes módulos não defina qualquer propriedade que já foi definida por um inicializador de elementos.

**Definir o inicializador**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK
       * behavior of treating response codes >= 400 as failed requests
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**O inicializador de carga**

No Applicationinsights:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Em alternativa,* pode instanciar o inicializador no código, por exemplo no Global.aspx.cs:

```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Ver mais deste exemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetria de JavaScript
*JavaScript*

Inserir um inicializador de telemetria imediatamente após o código de inicialização que recebeu do portal:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Para obter um resumo das propriedades personalizadas não disponíveis no telemetryItem, consulte [modelo de dados exportar do Application Insights](app-insights-export-data-model.md).

Pode adicionar tantos inicializadores conforme pretender.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer
O que é a diferença entre os processadores de telemetria e inicializadores de telemetria?

* Existem algumas sobreposições no que pode fazer com as mesmas: podem ser utilizados para adicionar propriedades a telemetria.
* TelemetryInitializers sempre executar antes de TelemetryProcessors.
* TelemetryProcessors permitem-lhe completamente substituir ou eliminar um item de telemetria.
* TelemetryProcessors não processar a telemetria de contador de desempenho.


## <a name="reference-docs"></a>Documentos de referência
* [Descrição Geral da API](app-insights-api-custom-events-metrics.md)
* [Referência ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Código do SDK
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK DO ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Passos seguintes
* [Eventos de pesquisa e registos](app-insights-diagnostic-search.md)
* [Amostragem](app-insights-sampling.md)
* [Resolução de problemas](app-insights-troubleshoot-faq.md)
