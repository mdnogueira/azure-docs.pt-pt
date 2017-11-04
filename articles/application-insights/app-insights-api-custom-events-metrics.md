---
title: "API de métricas e eventos personalizados do Application Insights | Microsoft Docs"
description: "Insira alguns linhas de código na sua aplicação de dispositivo ou o ambiente de trabalho, a página Web ou serviço, para controlar a utilização e diagnosticar problemas."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 1e7b5d4409b3e53db9313cf353894d5818837588
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API do Application Insights para as métricas e eventos personalizados

Insira alguns linhas de código na sua aplicação para saber que os utilizadores estão a fazer com o mesmo, ou para ajudar a diagnosticar problemas. Pode enviar telemetria a partir de aplicações de ambiente de trabalho e dispositivos, os clientes web e servidores web. Utilize o [Azure Application Insights](app-insights-overview.md) principal API de telemetria para enviar eventos personalizados, métricas e as suas próprias versões de telemetria padrão. Esta API está a API mesma que utilizam os padrão recoletores de dados do Application Insights.

## <a name="api-summary"></a>Resumo da API
A API é uniforme em todas as plataformas, para além dos algumas variações pequenas.

| Método | Utilizado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, ecrãs, painéis ou formulários. |
| [`TrackEvent`](#trackevent) |Ações de utilizador e outros eventos. Utilizado para controlar o comportamento do utilizador ou para monitorizar o desempenho. |
| [`TrackMetric`](#trackmetric) |Medidas de desempenho, tais como os comprimentos fila não relacionada com eventos específicos. |
| [`TrackException`](#trackexception) |Exceções de registo para diagnósticos adicionais. Rastreio onde ocorrer em relação a outros eventos e examine rastreios de pilha. |
| [`TrackRequest`](#trackrequest) |O registo a frequência e duração de pedidos de servidor para análise de desempenho. |
| [`TrackTrace`](#tracktrace) |Mensagens de registo de diagnóstico. Também pode capturar os registos de terceiros. |
| [`TrackDependency`](#trackdependency) |O registo a duração e a frequência de chamadas para componentes externos que depende da sua aplicação. |

Pode [anexar propriedades e métricas](#properties) para a maioria destas chamadas de telemetria.

## <a name="prep"></a>Antes de começar
Se ainda não tem uma referência no Application Insights SDK:

* Adicione o Application Insights SDK ao projeto:

  * [Projeto ASP.NET](app-insights-asp-net.md)
  * [Projeto de Java](app-insights-java-get-started.md)
  * [Projeto de node.js](app-insights-nodejs.md)
  * [JavaScript em cada página Web](app-insights-javascript.md) 
* No seu código de servidor web ou de dispositivo, incluem:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`
    
    *NODE.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Obter uma instância de TelemetryClient
Obter uma instância de `TelemetryClient` (exceto em JavaScript em páginas Web):

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();
    
*Node.js*

    var telemetry = applicationInsights.defaultClient;


TelemetryClient é seguro para thread.

Para ASP.NET e o Java projetos, recomendamos que crie uma instância de TelemetryClient para cada módulo da sua aplicação. Por exemplo, pode ter uma instância de TelemetryClient no seu serviço web para que reportem pedidos HTTP recebidos e outra numa classe middleware a eventos de relatório de lógica de negócio. Pode definir as propriedades, tais como `TelemetryClient.Context.User.Id` para controlar os utilizadores e sessões, ou `TelemetryClient.Context.Device.Id` identifiquem a máquina. Esta informação está ligada a todos os eventos que envia a instância.

Em projetos de Node.js, pode utilizar `new applicationInsights.TelemetryClient(instrumentationKey?)` para criar uma nova instância, mas isto é recomendada apenas para cenários que necessitam de configuração isolada a partir de singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent
No Application Insights, um *evento personalizado* é um ponto de dados que pode apresentar em [Explorador de métricas](app-insights-metrics-explorer.md) como uma conta a contagem agregada e na [pesquisa de diagnóstico](app-insights-diagnostic-search.md) como ocorrências individuais. (Não se encontra relacionado MVC ou outro framework ". eventos")

Inserir `TrackEvent` chama no seu código a contagem de vários eventos. Como muitas vezes, os utilizadores devem escolher uma funcionalidade específica, frequência alcançarem estes objetivos específicos ou talvez frequência que efetuam a tipos específicos de prende.

Por exemplo, uma aplicação de jogo, envie um evento sempre que um utilizador wins no jogo:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");
    
*Node.js*

    telemetry.trackEvent({name: "WinGame"});

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>Ver os eventos no portal do Microsoft Azure
Para ver uma contagem dos seus eventos, abra uma [Explorador de métricas](app-insights-metrics-explorer.md) painel, adicione um novo gráfico e selecione **eventos**.  

![Consulte uma contagem dos eventos personalizados](./media/app-insights-api-custom-events-metrics/01-custom.png)

Para comparar o número de eventos diferentes, defina o tipo de gráfico para **grelha**e grupo por nome de evento:

![Definir o tipo de gráfico e o agrupamento](./media/app-insights-api-custom-events-metrics/07-grid.png)

Na grelha, clique em através de um nome de evento para ver as ocorrências individuais esse evento. Para ver mais detalhes, clique qualquer ocorrência na lista.

![Os eventos de exploração](./media/app-insights-api-custom-events-metrics/03-instances.png)

Para se focarem em eventos específicos na pesquisa ou no Explorador de métricas, defina o filtro do painel para os nomes do evento que está interessado em:

![Abra os filtros, expanda o nome do evento e selecione um ou mais valores](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Eventos personalizados no Analytics

A telemetria está disponível no `customEvents` tabela no [Application Insights Analytics](app-insights-analytics.md). Cada linha representa uma chamada para `trackEvent(..)` na sua aplicação. 

Se [amostragem](app-insights-sampling.md) está numa operação, a propriedade itemCount mostra um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas para trackevent (), o processo de amostragem apenas transmitidos um deles. Para obter um número correto de eventos personalizados, deve utilizar, por conseguinte, código de utilização, tais como `customEvent | summarize sum(itemCount)`.


## <a name="trackmetric"></a>TrackMetric

Application Insights podem gráfico métricas que não estão anexadas a eventos específicos. Por exemplo, pode monitorizar um comprimento de fila em intervalos regulares. Com métricas, as medidas individuais são menos interesse que as variações e as tendências e estatísticos, por isso, gráficos são úteis.

Para poder enviar métricas para o Application Insights, pode utilizar o `TrackMetric(..)` API. Existem duas formas de enviar uma métrica de: 

* Valor único. Sempre que efetuar uma medida na sua aplicação, envie o valor correspondente para o Application Insights. Por exemplo, suponha que tem uma métrica que descreve o número de itens num contentor. Durante um período de tempo específico, primeiro coloque o três itens para o contentor e, em seguida, remover dois itens. Em conformidade, iria chamar `TrackMetric` duas vezes: primeiro transmitir o valor `3` e, em seguida, o valor `-2`. Application Insights armazena ambos os valores em seu nome. 

* Agregação. Ao trabalhar com as métricas, cada medida único é raramente de interesse. Em vez disso, um resumo do que aconteceu durante um período de tempo específico é importante. Denomina-se um resumo essa _agregação_. No exemplo acima, é a soma de métrica de agregação para esse período de tempo `1` e a contagem dos valores da métricas é `2`. Quando utilizar a abordagem de agregação, apenas invocar `TrackMetric` uma vez por período de tempo e enviar os valores de agregação. Esta é a abordagem recomendada, uma vez que este pode reduzir significativamente o custo e o desempenho dos custos gerais enviando menos pontos de dados para o Application Insights, ao ainda recolher todas as informações relevantes.

### <a name="examples"></a>Exemplos:

#### <a name="single-values"></a>Valores único

Para enviar um valor métrico único:

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#, Java*

```C#
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

*Node.js*

 ```Javascript
     telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

#### <a name="aggregating-metrics"></a>Agregar as métricas

Recomenda-se ligado à métrica agregada antes de lhes enviar da sua aplicação, para reduzir a largura de banda, custo e para melhorar o desempenho.
Eis um exemplo de código agregar:

*C#*

```C#
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>Métricas personalizadas no Explorador de métricas

Para ver os resultados, abra o Explorador de métricas e adicione um novo gráfico. Edite o gráfico para mostrar a métrica.

> [!NOTE]
> A métrica personalizada poderá demorar vários minutos a aparecer na lista de métricas disponíveis.
>

![Adicione um novo gráfico ou selecione um gráfico e, em personalizado, selecione a métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Métricas personalizadas no Analytics

A telemetria está disponível no `customMetrics` tabela no [Application Insights Analytics](app-insights-analytics.md). Cada linha representa uma chamada para `trackMetric(..)` na sua aplicação.
* `valueSum`-Esta é a soma dos valores. Para obter o valor médio, dividir por `valueCount`.
* `valueCount`-O número de valores que foram agregados neste `trackMetric(..)` chamada.

## <a name="page-views"></a>Vistas de página
Numa aplicação ou página Web de dispositivo, a telemetria de visualizações de página é enviada por predefinição quando cada ecrã ou a página é carregada. Mas pode alterar que para controlar as vistas de página em alturas diferentes ou adicionais. Por exemplo, numa aplicação que apresenta os separadores ou em painéis, pode querer controlar uma página sempre que o utilizador abre um novo painel.

![Lente de utilização no painel de descrição geral](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Dados de utilizador e de sessão são enviados como propriedades, juntamente com as vistas de página, para que os gráficos de utilizador e a sessão fique ativo quando não existe telemetria de visualizações de página.

### <a name="custom-page-views"></a>Vistas de página personalizada
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Se tiver vários separadores dentro páginas HTML diferentes, pode especificar o URL demasiado:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Vistas de página de temporização
Por predefinição, os tempos são reportados como **tempo de carregamento da vista de página** são medido desde que o browser envia o pedido, até que os eventos de carregamento de página no browser é chamado.

Em vez disso, pode:

* Defina uma duração de explícita [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) chamar: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Utilize a vista de página temporização chamadas `startTrackPage` e `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

O nome que utilizar como o primeiro parâmetro associa as chamadas de início e fim. Predefinições para o nome de página atual.

Durações de carregamento de página resultante apresentadas no Explorador de métricas são derivadas de intervalo entre as chamadas de início e fim. Está a funcionar para si que intervalo, na verdade, de hora.

### <a name="page-telemetry-in-analytics"></a>Telemetria de página no Analytics

No [análise](app-insights-analytics.md) duas tabelas mostram dados de operações do browser:

* O `pageViews` tabela contém dados sobre o título do URL e página
* O `browserTimings` tabela contém dados sobre o desempenho do cliente, tais como o tempo decorrido para processar os dados de entrada

Para localizar o browser de tempo demora a processar páginas diferentes:

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

Para detetar os popularities dos browsers diferentes:

```
pageViews | summarize count() by client_Browser
```

Associar vistas de página para chamadas AJAX, associar dependências:

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
O servidor SDK utiliza TrackRequest para registar pedidos HTTP.

Pode também chamá-la se pretender simular pedidos num contexto em que não tenha o módulo do serviço web em execução.

No entanto, o modo recomendado para enviar a telemetria de pedido é onde o pedido age como um <a href="#operation-context">contexto das operações</a>.

## <a name="operation-context"></a>Contexto de operação
Pode associar os itens de telemetria em conjunto ligando-se aos mesmos um ID de operação comuns. O módulo de rastreio de pedido padrão efetua este procedimento para exceções e outros eventos que são enviados enquanto está a ser processado um pedido de HTTP. No [pesquisa](app-insights-diagnostic-search.md) e [análise](app-insights-analytics.md), pode utilizar o ID para localizar facilmente quaisquer eventos associados ao pedido.

A forma mais fácil para definir o ID é definido um contexto de operação ao utilizar este padrão:

*C#*

```C#
// Establish an operation context and associated telemetry item:
using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetry.TrackTrace(...); // or other Track* calls
    ...
    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetry.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Juntamente com a definição de um contexto de operação `StartOperation` cria um item de telemetria do tipo que especificar. Envia o item de telemetria quando eliminar a operação, ou se chamar explicitamente `StopOperation`. Se utilizar `RequestTelemetry` como o tipo de telemetria, a sua duração está definida para o intervalo de tempo entre o início e fim.

Não não possível aninhar contextos de operação. Se já houver um contexto de operação, então o respetivo ID está associado a todos os itens nele contidos, incluindo o item criado com `StartOperation`.

Pesquisa, o contexto da operação é utilizado para criar o **itens relacionados** lista:

![Itens relacionados](./media/app-insights-api-custom-events-metrics/21.png)

Consulte [controlar operações personalizadas com o SDK .NET do Application Insights](application-insights-custom-operations-tracking.md) para obter mais informações sobre operações personalizadas de controlo.

### <a name="requests-in-analytics"></a>Pedidos de análise 

No [Application Insights Analytics](app-insights-analytics.md), os pedidos Mostrar cópias de segurança a `requests` tabela.

Se [amostragem](app-insights-sampling.md) está numa operação, a propriedade itemCount apresentará um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas para trackRequest(), o processo de amostragem apenas transmitidos um deles. Para obter uma contagem correta de pedidos e a duração média segmentada por nomes de pedido, utilize, como o código:

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
Envie exceções para o Application Insights:

* Para [contagem-los](app-insights-metrics-explorer.md), como uma indicação de que a frequência de um problema.
* Para [examinar ocorrências individuais](app-insights-diagnostic-search.md).

Os relatórios incluem os rastreios de pilha.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }
    
*Node.js*

    try
    {
       ...
    }
    catch (ex)
    {
       telemetry.trackException({exception: ex});
    }

Os SDKs catch muitas exceções automaticamente, não é sempre necessário chamar TrackException explicitamente.

* ASP.NET: [escrever código para detetar exceções](app-insights-asp-net-exceptions.md).
* J2EE: [as excepções são detectadas automaticamente](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: Exceções são detectadas automaticamente. Se pretender desativar a recolha automática, adicione uma linha para o fragmento de código que inserem na suas páginas Web:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Exceções de análise

No [Application Insights Analytics](app-insights-analytics.md), exceções apresentada no `exceptions` tabela.

Se [amostragem](app-insights-sampling.md) está numa operação, o `itemCount` propriedade mostra um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas para trackexception (), o processo de amostragem apenas transmitidos um deles. Para obter um número correto de exceções segmentado pelo tipo de exceção, utilize, como o código:

```
exceptions | summarize sum(itemCount) by type
```

A maioria das informações importantes pilha já é extraído para variáveis separadas, mas pode solicitar, à excepção de `details` estrutura para obter mais. Uma vez que esta estrutura dinâmica, deve converter o resultado para o tipo esperado. Por exemplo:

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Para associar os pedidos relacionados com exceções, utilize uma associação:

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
Utilize TrackTrace para ajudar a diagnosticar problemas com o envio de um "registo trilho" para o Application Insights. Pode enviar os segmentos de dados de diagnóstico e Inspecione os mesmos em [pesquisa de diagnóstico](app-insights-diagnostic-search.md).

[Iniciar sessão adaptadores](app-insights-asp-net-trace-logs.md) utilizar esta API para enviar registos de terceiros para o portal.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
    
*Node.js*

    telemetry.trackTrace({message: message, severity:applicationInsights.Contracts.SeverityLevel.Warning, properties:properties});


Pode pesquisar o conteúdo da mensagem, mas (ao contrário dos valores de propriedade) não é possível filtrar no mesmo.

O limite do tamanho `message` é muito superior ao limite nas propriedades.
Uma vantagem TrackTrace é que pode colocar dados relativamente longos na mensagem. Por exemplo, pode codificar POST data do não existe.  

Além disso, pode adicionar um nível de gravidade para a mensagem. E, como outra telemetria, pode adicionar valores de propriedade para o ajudar a filtrar ou de pesquisa para diferentes conjuntos de rastreios. Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

No [pesquisa](app-insights-diagnostic-search.md), em seguida, pode facilmente filtrar terminar todas as mensagens de um nível de gravidade específico que se relacionam com a base de dados específica.


### <a name="traces-in-analytics"></a>Rastreios no Analytics

No [Application Insights Analytics](app-insights-analytics.md), as chamadas para TrackTrace apresentada no `traces` tabela.

Se [amostragem](app-insights-sampling.md) está numa operação, a propriedade itemCount mostra um valor superior a 1. Para o exemplo itemCount = = 10 significa que 10 chamadas para `trackTrace()`, o processo de amostragem transmitidos apenas uma delas. Para obter uma contagem de chamadas de rastreio correta, deve utilizar, por conseguinte, código como `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency
Utilize a chamada de TrackDependency para controlar os tempos de resposta e taxas de êxito de chamadas para um fragmento de código externo. Os resultados são apresentados nos gráficos de dependência no portal.

```C#
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

```Javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({dependencyTypeName: "myDependency", name: "myCall", duration: elapsed, success:success});
}
```

Lembre-se de que os SDKs do servidor incluem um [módulo dependência](app-insights-asp-net-dependencies.md) que Deteta e monitoriza determinadas chamadas de dependência automaticamente – por exemplo, para as bases de dados e REST APIs. Tem de instalar um agente no seu servidor para tornar o módulo de trabalho. Utilize esta chamada se pretender controlar chamadas que o controlo automatizado não catch ou se não pretender instalar o agente.

Para desativar o módulo de registo de dependência padrão, editar [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md) e eliminar a referência ao `DependencyCollector.DependencyTrackingTelemetryModule`.

### <a name="dependencies-in-analytics"></a>Dependências de análise

No [Application Insights Analytics](app-insights-analytics.md), trackDependency chama Mostrar cópias de segurança `dependencies` tabela.

Se [amostragem](app-insights-sampling.md) está numa operação, a propriedade itemCount mostra um valor superior a 1. Para o exemplo itemCount = = 10 significa que de 10 chamadas para trackDependency(), o processo de amostragem apenas transmitidos um deles. Para obter uma contagem correta de dependências segmentado pelo componente de destino, utilize, como o código:

```
dependencies | summarize sum(itemCount) by target
```

Para associar os pedidos relacionados dependências, utilize uma associação:

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>Dados Flushing
Normalmente, o SDK envia dados, por vezes escolhidos para minimizar o impacto no utilizador. No entanto, em alguns casos, pode querer remover da cache na memória intermédia – por exemplo, se estiver a utilizar o SDK de uma aplicação que será encerrado.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);
    
*Node.js*

    telemetry.flush();

Tenha em atenção que a função é assíncrona para o [canal do servidor de telemetria](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

## <a name="authenticated-users"></a>Utilizadores autenticados
Numa aplicação web, os utilizadores são (por predefinição) identificados por cookies. Um utilizador pode contar mais do que uma vez se acederem a aplicação a partir de outro computador ou browser ou se poderem eliminar cookies.

Se os utilizadores iniciam sessão sua aplicação, pode obter uma contagem mais exata, definindo o ID de utilizador autenticado com o código do browser:

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Na web do ASP.NET aplicação MVC, por exemplo:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Não é necessário utilizar o nome do utilizador real início de sessão. Só tem de ser um ID exclusivo para esse utilizador. Não pode incluir espaços ou nenhum dos carateres `,;=|`.

O ID de utilizador também é definido um cookie de sessão e enviar para o servidor. Se o servidor SDK está instalado, o ID de utilizador autenticado será enviado como parte das propriedades do contexto de telemetria de cliente e servidor. Em seguida, pode filtrar e procurar na mesma.

Se a sua aplicação grupos de utilizadores em contas, também pode passar um identificador da conta (com as restrições de caráter).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

No [Explorador de métricas](app-insights-metrics-explorer.md), pode criar um gráfico que contagens **utilizadores, autenticado**, e **contas de utilizador**.

Também pode [pesquisa](app-insights-diagnostic-search.md) para pontos de dados de cliente com contas e os nomes de utilizador específica.

## <a name="properties"></a>Filtragem, pesquisa e segmentar os seus dados através da utilização de propriedades
Pode anexar propriedades e valores para os eventos (e também para as métricas, página vistas, exceções e outros dados de telemetria).

*Propriedades* são valores de cadeia que pode utilizar para filtrar a telemetria nos relatórios de utilização. Por exemplo, se a aplicação fornece várias jogos, poderá anexar no jogo do nome of the para cada evento para que possa ver quais jogos são mais populares.

Não há um limite de 8192 ao comprimento da cadeia. (Se pretender enviar grandes segmentos de dados, utilize o parâmetro de mensagem de [TrackTrace](#track-trace).)

*Métricas* são valores numéricos que podem ser apresentados graficamente. Por exemplo, pode querer ver se existe um aumento gradual nas pontuações que sua gamers alcançarem. Os gráficos podem ser segmentados pelas propriedades que são enviadas com o evento, para que pode obter separado ou empilhadas gráficos para jogos diferentes.

Para obter valores métricos para ser apresentado corretamente, estes devem ser maior que ou igual a 0.

Existem algumas [limites no número de propriedades, valores de propriedade e métricas](#limits) que pode utilizar.

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);

*Node.js*

    // Set up some properties and metrics:
    var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
    var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

    // Send the event:
    telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});


*Visual Basic*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> Asseguramos não para registar informações de identificação pessoal nas propriedades.
>
>

*Se utilizou as métricas*, abra o Explorador de métricas e selecione a métrica do **personalizada** grupo:

![Abra o Explorador de métricas, selecione o gráfico e selecione a métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Se a métrica não aparecer, ou se o **personalizada** cabeçalho não existe, feche o painel de seleção e tente novamente mais tarde. Métricas, por vezes, podem demorar uma hora para serem agregados através do pipeline.

*Se utilizou as propriedades e métricas*, segmentar a métrica pela propriedade:

![Definir o agrupamento e, em seguida, selecione a propriedade em Agrupar por](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*Na pesquisa de diagnóstico*, pode ver as propriedades e métricas de ocorrências individuais de um evento.

![Selecione uma instância e, em seguida, selecione "..."](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Utilize o **pesquisa** campo para ver as ocorrências do evento que tenham um valor de propriedade em particular.

![Escreva um termo na pesquisa](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Saiba mais sobre as expressões de pesquisa](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>Maneira para definir as propriedades e métricas
Se for mais prático, é possível recolher os parâmetros de um evento num objeto separado:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Não reutilize a mesma instância de item de telemetria (`event` neste exemplo) para chamar Track*() várias vezes. Isto pode provocar a telemetria para serem enviados com a configuração incorreta.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Medidas personalizadas e propriedades na análise

No [análise](app-insights-analytics.md), métricas personalizadas e propriedades de apresentam a `customMeasurements` e `customDimensions` atributos de cada registo de telemetria.

Por exemplo, se tiver adicionado uma propriedade com o nome "jogo" para a telemetria de pedido, esta consulta a contagem de ocorrências de valores diferentes de "jogo" e mostrar a média da "pontuação" métrica personalizada:

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

Tenha em atenção que:

* Quando um valor a extrair o customDimensions ou customMeasurements JSON, tem o tipo de dinâmico e, por isso, tem de o transmitir- `tostring` ou `todouble`.
* Para efetuar a conta de conhecimento da possibilidade de [amostragem](app-insights-sampling.md), deve utilizar `sum(itemCount)`, não `count()`.



## <a name="timed"></a>Eventos de temporização
Por vezes, pretende quanto tempo demora para efetuar uma ação de gráfico. Por exemplo, pode querer saber utilizadores quanto tempo demoram a considerar as opções de um jogo. Pode utilizar o parâmetro de medida para este.

*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>Propriedades predefinidas para telemetria personalizada
Se pretender definir valores de propriedade de predefinido para alguns dos eventos personalizados que escreve, pode defini-los numa instância TelemetryClient. Estão ligados a todos os itens de telemetria que é enviado a partir de que o cliente.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");
    
*Node.js*

    var gameTelemetry = new applicationInsights.TelemetryClient();
    gameTelemetry.commonProperties["Game"] = currentGame.Name;

    gameTelemetry.TrackEvent({name: "WinGame"});



Chamadas de telemetria individuais podem substituir os valores predefinidos no respetivos dicionários de propriedade.

*Para JavaScript web clientes*, [utilizar inicializadores de telemetria de JavaScript](#js-initializer).

*Para adicionar propriedades a toda a telemetria*, incluindo os dados de módulos de recolha padrão, [implementar `ITelemetryInitializer` ](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Amostragem, a filtragem e o processamento de telemetria
Pode escrever o código para processar a telemetria antes do envio do SDK. O processamento inclui dados enviados a partir de módulos de telemetria padrão, tais como recolha de pedido HTTP e a recolha de dependência.

[Adicionar propriedades](app-insights-api-filtering-sampling.md#add-properties) a telemetria implementando `ITelemetryInitializer`. Por exemplo, pode adicionar valores que são calculados ou números de versão de outras propriedades.

[Filtragem](app-insights-api-filtering-sampling.md#filtering) pode modificar ou eliminar telemetria antes de ser enviada do SDK implementando `ITelemetryProcesor`. Controlar o que é enviado ou eliminado, mas tem em conta o efeito nas suas métricas. Dependendo de como eliminar itens, poderá perder a capacidade de navegar entre itens relacionados.

[Amostragem](app-insights-api-filtering-sampling.md) é uma solução para reduzir o volume de dados enviados para o portal da sua aplicação em pacote. Isto é feito sem afetar as métricas apresentadas. E copia-sem afetar a capacidade de diagnosticar problemas ao navegar entre itens relacionados, tais como exceções, dos pedidos e vistas de página.

[Saiba mais](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Desativar a telemetria
Para *dinamicamente parar e iniciar* a recolha e a transmissão de telemetria:

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Para *desativar os recoletores padrão selecionados*– por exemplo, os contadores de desempenho, os pedidos de HTTP ou dependências - eliminar ou comente as linhas relevantes [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md). Para fazer isto, por exemplo, se pretender enviar os seus próprios dados TrackRequest.

*Node.js*

```Javascript

    telemetry.config.disableAppInsights = true;
```

Para *desativar os recoletores padrão selecionados*– por exemplo, os contadores de desempenho, os pedidos de HTTP ou dependências – durante a inicialização, encadeiam métodos de configuração para o código de inicialização do SDK:

```Javascript

    applicationInsights.setup()
        .setAutoCollectRequests(false)
        .setAutoCollectPerformance(false)
        .setAutoCollectExceptions(false)
        .setAutoCollectDependencies(false)
        .setAutoCollectConsole(false)
        .start();
```

Para desativar estes recoletores após a inicialização, utilize o objeto de configuração:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Modo de programador
Durante a depuração, é útil para que a sua telemetria emitida através do pipeline para que possa ver resultados imediatamente. Pode também obter mensagens adicionais que o ajudam a quaisquer problemas com a telemetria de rastreio. Desactivá-lo na produção, porque pode atrasar da aplicação.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a>Definir a chave de instrumentação para telemetria personalizada selecionada
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a>Chave de instrumentação dinâmica
Para evitar a combinação de telemetria de desenvolvimento, teste e ambientes de produção, pode [criar recursos do Application Insights separados](app-insights-create-new-resource.md) e alterar as respetivas chaves, dependendo do ambiente.

Em vez de obter a chave de instrumentação do ficheiro de configuração, pode defini-lo no seu código. Defina a chave de um método de inicialização, tais como global.aspx.cs num serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



Em páginas Web, pode querer defini-lo a partir do servidor web Estado, em vez de codificação-literalmente para o script. Por exemplo, numa página Web gerada por uma aplicação ASP.NET:

*JavaScript em Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient tem uma propriedade de contexto, que contém valores que são enviados juntamente com todos os dados de telemetria. Normalmente definidos por módulos de telemetria padrão, mas também pode defini-los por si. Por exemplo:

    telemetry.Context.Operation.Name = "MyOperationName";

Se qualquer um destes valores por si, considere remover a linha relevante do [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md), para que os valores e os valores padrão não obter confundidos.

* **Componente**: A aplicação e a respetiva versão.
* **Dispositivo**: dados sobre o dispositivo onde a aplicação está em execução. (Nas web apps, este é o servidor ou dispositivo de cliente a telemetria enviada do.)
* **InstrumentationKey**: recurso do Application Insights no Azure onde a telemetria aparecer. Este é normalmente captado Applicationinsights.
* **Localização**: A localização geográfica do dispositivo.
* **Operação**: nas web apps, o atual pedido HTTP. Em outros tipos de aplicação, pode definir esta opção para agrupar eventos em conjunto.
  * **ID**: itens relacionados com um valor gerado que está correlacionada com diferentes eventos, para que quando inspecionar qualquer evento na pesquisa de diagnóstico, possam localizar.
  * **Nome**: um identificador, normalmente, o URL do pedido HTTP.
  * **SyntheticSource**: Se não for nulo ou vazio, uma cadeia que indica que a origem do pedido foi identificada como um teste robot ou web. Por predefinição, esta é excluída da cálculos no Explorador de métricas.
* **Propriedades**: propriedades que são enviadas com todos os dados de telemetria. Pode ser substituído nas chamadas controlar * individuais.
* **Sessão**: A sessão do utilizador. O ID é definido para um valor gerado, o que é alterado quando o utilizador não tiver sido Active Directory para o tempo.
* **Utilizador**: informações do utilizador.

## <a name="limits"></a>Limites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Para evitar atingir o limite de taxa de dados, utilize [amostragem](app-insights-sampling.md).

Para determinar quanto os dados são mantidos, consulte [retenção de dados e privacidade](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Documentos de referência
* [Referência ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referência de Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referência de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [SDK do Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [SDK do iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Código do SDK
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pacotes do Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Todas as plataformas](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Dúvidas
* *As exceções podem emitir chamadas Track_()*

    nenhum. Não precisa de ser moldado-los em cláusulas try-catch. Se o SDK detetar problemas, irá registar mensagens no resultado da consola de depuração e – se as mensagens de chegar - na pesquisa de diagnóstico.
* *Existe uma API REST para obter dados a partir do portal?*

    Sim, o [API de acesso a dados](https://dev.applicationinsights.io/). Outras formas de extrair dados incluem [exportar de análise para o Power BI](app-insights-export-power-bi.md) e [a exportação contínua](app-insights-export-telemetry.md).

## <a name="next"></a>Passos seguintes
* [Eventos de pesquisa e registos](app-insights-diagnostic-search.md)

* [Resolução de problemas](app-insights-troubleshoot-faq.md)


