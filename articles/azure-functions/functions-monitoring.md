---
title: "Monitorizar as funções do Azure"
description: "Saiba como utilizar o Azure Application Insights com as funções do Azure para monitorizar a execução de função."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 33d4a193cc3152bfab1f03dde32ad4f1bcb0afe1
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="monitor-azure-functions"></a>Monitorizar as funções do Azure

## <a name="overview"></a>Descrição geral 

[As funções do Azure](functions-overview.md) oferece integração incorporada com [Azure Application Insights](../application-insights/app-insights-overview.md) para funções de monitorização. Este artigo mostra como configurar funções para enviar dados de telemetria para o Application Insights.

![Explorador de métricas do Application Insights](media/functions-monitoring/metrics-explorer.png)

As funções também tem a monitorização incorporada que não a utilizar o Application Insights. Recomendamos o Application Insights porque lhe oferece mais dados e uma melhor formas para analisar os dados. Para obter informações sobre a monitorização incorporadas, consulte o [última secção deste artigo](#monitoring-without-application-insights).

## <a name="enable-application-insights-integration"></a>Ativar a integração do Application Insights

Para uma aplicação de função enviar dados para o Application Insights, tem de saber a chave de instrumentação de uma instância do Application Insights. Existem duas formas para se certificar de que a ligação no [portal do Azure](https://portal.azure.com):

* [Criar uma instância ligada do Application Insights ao criar a aplicação de função](#new-function-app).
* [Ligar uma instância do Application Insights para uma aplicação de função existente](#existing-function-app).
 
### <a name="new-function-app"></a>Nova aplicação de função

Ativar o Application Insights na aplicação de função **criar** página:

1. Definir o **Application Insights** comutador **no**.

2. Selecione um **Application Insights localização**.

   ![Ativar o Application Insights ao criar uma aplicação de função](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>Aplicação de função existente

Obter a chave de instrumentação e guardá-lo na aplicação de função:

1. Crie a instância do Application Insights. Definir o tipo de aplicação **geral**.

   ![Criar uma instância do Application Insights, escreva geral](media/functions-monitoring/ai-general.png)

2. Copie a chave de instrumentação do **Essentials** página da instância do Application Insights. Coloque o cursor sobre o fim do valor da chave de apresentadas para obter um **clique para copiar** botão.

   ![Copie a chave de instrumentação do Application Insights](media/functions-monitoring/copy-ai-key.png)

1. A aplicação de função **definições da aplicação** página, [adicionar uma definição de aplicação](functions-how-to-use-azure-function-app-settings.md#settings) clicando **Adicionar nova definição**. Nome da nova definição APPINSIGHTS_INSTRUMENTATIONKEY e cole a chave de instrumentação copiado.

   ![Adicionar a chave de instrumentação definições de aplicação](media/functions-monitoring/add-ai-key.png)

1. Clique em **Guardar**.

## <a name="view-telemetry-data"></a>Ver dados de telemetria

Para navegar para a instância ligada do Application Insights a partir de uma aplicação de função no portal, selecione o **Application Insights** ligação da aplicação de função **descrição geral** página.

Para obter informações sobre como utilizar o Application Insights, consulte o [documentação do Application Insights](https://docs.microsoft.com/azure/application-insights/). Esta secção mostra alguns exemplos de como ver dados no Application Insights. Se já estiver familiarizado com o Application Insights, pode ir diretamente para [secções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

No [Explorador de métricas](../application-insights/app-insights-metrics-explorer.md), pode criar gráficos e alertas com base nas métricas, tais como o número de invocações de função, tempo de execução e a taxa de êxito.

![Explorador de métricas](media/functions-monitoring/metrics-explorer.png)

No [falhas](../application-insights/app-insights-asp-net-exceptions.md) separador, pode criar gráficos e alertas com base nas falhas de função e o servidor de exceções. O **nome da operação** é o nome de função. Falhas de dependências não são apresentadas, a menos que implementar [telemetria personalizada](#custom-telemetry-in-c-functions) para dependências.

![falhas](media/functions-monitoring/failures.png)

No [desempenho](../application-insights/app-insights-performance-counters.md) separador, pode analisar problemas de desempenho.

![Desempenho](media/functions-monitoring/performance.png)

O **servidores** separador mostra a utilização de recursos e débito por servidor. Estes dados podem ser úteis para cenários em que as funções são bogging para baixo do seu recursos subjacentes de depuração. Servidores são denominados **instâncias de função da nuvem**.

![Servidores](media/functions-monitoring/servers.png)

O [métricas em fluxo em direto](../application-insights/app-insights-live-stream.md) separador apresenta dados de métricas, é criado em tempo real.

![Transmissão em direto](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Dados de telemetria de consulta

[Application Insights Analytics](../application-insights/app-insights-analytics.md) dá-lhe acesso a todos os dados de telemetria sob a forma de tabelas numa base de dados. Análise de fornece uma linguagem de consulta para extrair, a manipulação e a visualizar os dados.

![Selecione a análise](media/functions-monitoring/select-analytics.png)

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Eis um exemplo de consulta. Este mostra a distribuição de pedidos por trabalho nos últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

As tabelas que estão disponíveis são apresentadas as **esquema** separador do painel esquerdo. Pode encontrar dados gerados por invocações de função nas tabelas seguintes:

* **os rastreios** -registos criados pelo runtime e por código de função.
* **pedidos** -uma para cada invocação de função.
* **exceções** - eventuais exceções geradas pelo runtime.
* **customMetrics** -contagem de êxito e falha invocações, a taxa de êxito, a duração.
* **customEvents** -eventos controlada pelo tempo de execução, por exemplo: pedidos de HTTP que acionam uma função.
* **performanceCounters** -informações sobre o desempenho dos servidores que executem as funções.

São as outras tabelas de testes de disponibilidade e a telemetria de browser do cliente. Pode implementar a telemetria personalizada para adicionar dados aos mesmos.

Dentro de cada tabela, alguns dos dados específicos de funções está a ser um `customDimensions` campo.  Por exemplo, a consulta seguinte obtém todos os rastreios que tenham o nível de registo `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

O tempo de execução fornece `customDimensions.LogLevel` e `customDimensions.Category`. Pode fornecer os campos adicionais nos registos de escrita no código da função. Consulte [estruturados registo](#structured-logging) posteriormente neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configurar as categorias e níveis de registo

Pode utilizar o Application Insights sem qualquer configuração personalizada, mas a configuração predefinida que pode resultar num elevado volumes de dados. Se estiver a utilizar uma subscrição do Azure do Visual Studio, poderá atingiu o limite de dados para o Application Insights. O resto deste artigo mostra como configurar e personalizar os dados que as suas funções enviam para o Application Insights.

### <a name="categories"></a>Categorias

O registo das funções do Azure inclui um *categoria* para cada registo. A categoria indica que parte do código de tempo de execução ou o código de função escreveu o registo. 

O tempo de execução de funções cria registos que tenham um início de categoria com "Anfitrião". Por exemplo, a "função iniciada," "executar a função" e os registos de "função foi concluída" possuem a categoria "Host.Executor". 

Se escrever os registos no seu código de função, os respetivos categoria é "Função".

### <a name="log-levels"></a>Níveis de registo

O registo de funções do Azure também inclui um *nível de registo* com cada registo. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) é uma enumeração, e o código de número inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreio       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhuma        | 6 |

Nível de registo `None` é explicada na secção seguinte. 

### <a name="configure-logging-in-hostjson"></a>Configurar o registo no host.json

O *host.json* ficheiro configura quanto registo envia uma aplicação de função para o Application Insights. Para cada categoria, indica o nível de registo mínimo para enviar. Eis um exemplo:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Este exemplo configura as seguintes regras:

1. Registos com a categoria "Host.Results" ou "Função", enviar apenas `Error` nível e superior para o Application Insights. Os registos para `Warning` nível e abaixo são ignorados.
2. Registos com a categoria de anfitrião. O agregador, enviar apenas `Information` nível e superior para o Application Insights. Os registos para `Debug` nível e abaixo são ignorados.
3. Para todos os outros registos, enviar apenas `Information` nível e superior para o Application Insights.

O valor de categoria na *host.json* controla o registo para todas as categorias que começam com o mesmo valor. Por exemplo, "anfitrião" no *host.json* controla o registo para "Host.General", "Host.Executor", "Host.Results" e assim sucessivamente.

Se *host.json* inclui várias categorias que começam com a mesma cadeia, dos mais são correspondidos primeiro. Por exemplo, suponha que pretende que tudo o tempo de execução, exceto "Host.Aggregator" iniciar sessão em `Error` nível, enquanto "Host.Aggregator" regista a `Information` nível:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Para suprimir todos os registos para uma categoria, pode utilizar o nível de registo `None`. Não existem registos são escritos dessa categoria e não existe nenhum nível de registo acima.

As secções seguintes descrevem as categorias principais de registos que cria o tempo de execução. 

### <a name="category-hostresults"></a>Categoria Host.Results

Estes registos mostram como "pedidos" no Application Insights. Indicam o êxito ou falha de uma função.

![Gráfico de pedidos](media/functions-monitoring/requests-chart.png)

Todos estes registos são escritos no `Information` nível, por isso, se a filtrar no `Warning` ou superior, não verão qualquer um destes dados.

### <a name="category-hostaggregator"></a>Categoria Host.Aggregator

Estes registos fornecem contagens e médias de invocações de função através de um [configuráveis](#configure-the-aggregator) período de tempo. O período predefinido é 30 segundos ou 1000 resultados, o que ocorrer primeiro. 

Os registos estão disponíveis no **customMetrics** tabela no Application Insights. Os exemplos são número de execuções, a taxa de êxito e a duração.

![consulta de customMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos estes registos são escritos no `Information` nível, por isso, se a filtrar no `Warning` ou superior, não verão qualquer um destes dados.

### <a name="other-categories"></a>Outras categorias

Todos os registos de categorias sem ser aqueles já listados estão disponíveis no **rastreios** tabela no Application Insights.

![consulta de rastreios](media/functions-monitoring/analytics-traces.png)

Todos os registos com categorias que começam por "Anfitrião" são escritos pelo tempo de execução de funções. Os registos de "Função concluída" e "Iniciar função" possuem a categoria "Host.Executor". Para é executado com êxito, estes registos são `Information` nível; exceções são registadas no `Error` nível. O tempo de execução também cria `Warning` nível registos, por exemplo: fila de mensagens enviadas para a fila nocivas.

Os registos de escritas pelo código da função têm a categoria "Função" e podem ser qualquer nível de registo.

## <a name="configure-the-aggregator"></a>Configurar o agregador

Conforme indicado na secção anterior, o tempo de execução agrega dados sobre execuções de função durante um período de tempo. O período predefinido é 30 segundos ou 1000 é executado, o que ocorrer primeiro. Pode configurar esta definição no *host.json* ficheiro.  Eis um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar a amostragem

O Application Insights tem um [amostragem](../application-insights/app-insights-sampling.md) funcionalidade que pode protegê-lo contra produzir demasiados dados de telemetria, por vezes de pico de carga. Quando o número de itens de telemetria excede uma velocidade especificada, o Application Insights começa a aleatoriamente ignorar alguns dos itens de entrada. Pode configurar amostragem no *host.json*.  Eis um exemplo:

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>Escrever registos nas funções de c#

É possível escrever os registos no seu código de função que aparecem como os rastreios no Application Insights.

### <a name="ilogger"></a>ILogger

Utilize um [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) parâmetro nas suas funções em vez de um `TraceWriter` parâmetro. Os registos criados utilizando `TraceWriter` aceda ao Application Insights, mas `ILogger` permite-lhe fazer [estruturados registo](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um `ILogger` objeto tem de chamar `Log<level>` [métodos de extensão no ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) para criar registos. Por exemplo, o seguinte código escritas `Information` nos registos com a categoria "Função".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registo estruturado

A ordem dos marcadores de posição, não os respetivos nomes, determina quais os parâmetros são utilizados na mensagem de registo. Por exemplo, suponha que tem o seguinte código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se manter a mesma cadeia de mensagem e inverter a ordem dos parâmetros, o texto da mensagem resultante iria ter os valores nos locais errados.

Marcadores de posição são processadas desta forma, para que pode efetuar o registo structured. Application Insights armazena os pares de valor de nome de parâmetro para além da cadeia de mensagem. O resultado é que os argumentos de mensagem campos que pode consultar no.

Por exemplo, se a chamada de método de registo aspeto do exemplo anterior, foi possível consultar o campo `customDimensions.prop__rowKey`. O `prop__` prefixo é adicionado para se certificar de que não existem nenhum colisões entre campos o tempo de execução adiciona e campos o código de função adiciona.

Também pode consultar da cadeia original da mensagem ao consultar o campo `customDimensions.prop__{OriginalFormat}`.  

Eis uma representação JSON de exemplo de `customDimensions` dados:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>Métricas personalizadas do registo  

Em c# funções de script, pode utilizar o `LogMetric` método de extensão no `ILogger` para criar métricas personalizadas no Application Insights. Eis uma chamada de método de exemplo:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Este código é uma alternativa ao chamar `TrackMetric` utilizando [a API do Application Insights para .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Escrever registos de funções de JavaScript

Nas funções de Node.js, utilize `context.log` ao escrever os registos. Registo estruturado não está ativado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Métricas personalizadas do registo  

Nas funções de Node.js, pode utilizar o `context.log.metric` método para criar métricas personalizadas no Application Insights. Eis uma chamada de método de exemplo:

```javascript
context.log.metric("TestMetric", 1234); 
```

Este código é uma alternativa ao chamar `trackMetric` utilizando [o SDK Node.js para o Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Telemetria personalizada nas funções de c#

Pode utilizar o [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) pacote NuGet ao enviar dados de telemetria personalizada para o Application Insights.

Eis um exemplo de código c# que utiliza o [API de telemetria personalizada](../application-insights/app-insights-api-custom-events-metrics.md). O exemplo é para uma biblioteca de classe de .NET, mas o código do Application Insights é o mesmo para o script do c#.

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetry = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;

            telemetry.Context.Operation.Id = context.InvocationId.ToString();
            telemetry.Context.Operation.Name = "cs-http";
            if (!String.IsNullOrEmpty(name))
            {
                telemetry.Context.User.Id = name;
            }
            telemetry.TrackEvent("Function called");
            telemetry.TrackMetric("Test Metric", DateTime.Now.Millisecond);
            telemetry.TrackDependency("Test Dependency", 
                "swapi.co/api/planets/1/", 
                start, DateTime.UtcNow - start, true);

            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
    }
}
```

Não chame `TrackRequest` ou `StartOperation<RequestTelemetry>`, porque irá ver pedidos de duplicação para uma invocação de função.  O tempo de execução de funções controla automaticamente pedidos.

Definir `telemetry.Context.Operation.Id` para a invocação ID sempre que a sua função é iniciada. Isto torna possível correlacionar todos os itens de telemetria para uma invocação de função especificada.

```cs
telemetry.Context.Operation.Id = context.InvocationId.ToString();
```

## <a name="custom-telemetry-in-javascript-functions"></a>Telemetria personalizada nas funções de JavaScript

O [SDK Node.js do Application Insights](https://www.npmjs.com/package/applicationinsights) está atualmente na versão beta. Eis alguns códigos de exemplo que envia telemetria personalizada para o Application Insights:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

O `tagOverrides` conjuntos de parâmetros `operation_Id` para ID de invocação. de função Esta definição permite-lhe correlacionar todas a telemetria gerada automaticamente e personalizada para uma invocação de função especificada.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="dependencies"></a>Dependências

Dependências que a função tem de outros serviços não apareçam automaticamente, mas pode escrever código personalizado para mostrar as dependências. Código de exemplo a [secção de telemetria personalizada c#](#custom-telemetry-in-c-functions) mostra como. O código de exemplo resulta num *o mapeamento de aplicações* no Application Insights que se parece com isto:

![Mapeamento de aplicações](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Problemas de relatório

A comunicar um problema com a integração do Application Insights nas funções, ou para fazer uma sugestão ou a pedido, [criar um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Monitorização sem Application Insights

Recomendamos que Application Insights para funções de monitorização, porque este oferece mais dados e uma melhor formas para analisar os dados. Mas também pode encontrar telemetria e dados de registo nas páginas do portais do Azure para uma aplicação de função. 

Selecione o **Monitor** separador para uma função e obter uma lista de execuções de função. Selecione uma execução de função para consultar a duração, dados de entrada, erros e ficheiros de registo associados.

> [!IMPORTANT]
> Ao utilizar o [consumo plano de alojamento](functions-overview.md#pricing) para as funções do Azure, o **monitorização** mosaico na aplicação de função não mostra todos os dados. Isto acontece porque a plataforma dinamicamente dimensiona e gere instâncias de computação por si. Estas métricas não são significativas um plano de consumo.

### <a name="real-time-monitoring"></a>A monitorização em tempo real

A monitorização em tempo real está disponível clicando **fluxo de eventos em direto** da função de **Monitor** separador. O fluxo de eventos em direto é apresentado no gráfico de num novo separador do browser.

> [!NOTE]
> Não há um problema conhecido que pode fazer com que os dados conseguir ser preenchido. Poderá ter de fechar o separador do browser que contém o fluxo de eventos em direto e, em seguida, clique em **fluxo de eventos em direto** novamente para permitir que seja corretamente preencher os dados de fluxo de eventos. 

Estas estatísticas são em tempo real, mas o graphing real dos dados de execução pode ter cerca de 10 segundos de latência.

### <a name="monitor-log-files-from-a-command-line"></a>Ficheiros de registo do Monitor de uma linha de comandos

Pode transmitir ficheiros de registo para uma sessão de linha de comandos numa estação de trabalho local utilizando a Interface de linha de comandos (CLI do Azure) 1.0 ou PowerShell.

### <a name="monitor-function-app-log-files-with-the-azure-cli-10"></a>Monitorizar os ficheiros de registo de aplicação de função com a CLI do Azure 1.0

Para começar, [instalar a CLI do Azure 1.0](../cli-install-nodejs.md) e [iniciar sessão no Azure](../xplat-cli-connect.md).

Utilize os seguintes comandos para ativar o modo clássico de gestão de serviço, escolha a sua subscrição e transmitir ficheiros de registo:

```
azure config mode asm
azure account list
azure account set <subscriptionNameOrId>
azure site log tail -v <function app name>
```

### <a name="monitor-function-app-log-files-with-powershell"></a>Monitorizar os ficheiros de registo de aplicação de função com o PowerShell

Para começar, [instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Utilize os seguintes comandos para adicionar a sua conta do Azure, escolha a sua subscrição e transmitir ficheiros de registo:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail
```

Para obter mais informações, consulte [como: transmitir os registos para aplicações web](../app-service/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o Application Insights](https://docs.microsoft.com/azure/application-insights/)

> [!div class="nextstepaction"]
> [Saiba mais sobre a arquitetura de registo que utiliza as funções](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)
