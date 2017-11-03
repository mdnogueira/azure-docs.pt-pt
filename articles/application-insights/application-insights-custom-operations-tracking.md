---
title: "Controlar operações personalizadas com o SDK .NET da Azure Application Insights | Microsoft Docs"
description: "Controlo personalizadas operações com o SDK .NET da Azure Application Insights"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/30/2017
ms.author: sergkanz
ms.openlocfilehash: 6412445f4e7a9b639ae9a38a44ff51038c6fcc00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Controlar operações personalizadas com o Application Insights SDK do .NET

Azure SDKs do Application Insights registar recebidos pedidos de HTTP e automaticamente chamadas para os serviços dependentes, tais como pedidos de HTTP e as consultas SQL. Correlação de pedidos e dependências e o controlo de dar-lhe visibilidade para a capacidade de resposta e a fiabilidade da aplicação toda em todos os micro-serviços que combinam a esta aplicação. 

Há uma classe de padrões de aplicação que não pode ser suportado genericamente. Uma monitorização adequada desses padrões requer instrumentação de código manual. Este artigo abrange alguns padrões que possam exigir instrumentação manual, tais como personalizada fila de processamento e executar tarefas em segundo plano de execução longa.

Este documento fornece orientações sobre como controlar operações personalizadas com o Application Insights SDK. Esta documentação é relevante para:

- Application Insights para .NET (também conhecida como Base SDK da) versão 2.4 +.
- Application Insights para a versão de aplicações (em execução do ASP.NET) web 2.4 +.
- Application Insights para ASP.NET Core versão 2.1 +.

## <a name="overview"></a>Descrição geral
Uma operação é um trabalho de peça lógico executado por uma aplicação. Tem um nome, iniciar a tempo, a duração, resultado e um contexto de execução, como o nome de utilizador, propriedades e resultado. Se a operação A foi iniciada pela operação B, as operação B, em seguida, está definida como principal para A. Uma operação pode ter apenas um elemento principal, mas pode ter muitas operações de subordinados. Para obter mais informações sobre operações e da correlação de telemetria, consulte [correlação de telemetria do Azure Application Insights](application-insights-correlation.md).

No Application Insights SDK de .NET, a operação é descrita pela classe abstrata [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) e respetivos descendentes [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) e [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Operações de entrada de registo 
Web Application Insights SDK recolhe automaticamente pedidos HTTP para aplicações de ASP.NET que executam um pipeline do IIS e todas as aplicações de ASP.NET Core. Existem soluções suportado de Comunidade para outras plataformas e estruturas. No entanto, se a aplicação não é suportada por qualquer uma das soluções suportado de Comunidade ou padrão, pode instrumentá-lo manualmente.

Outro exemplo requer controlo personalizado é o trabalho que recebe os itens da fila. Para algumas filas, a chamada para adicionar uma mensagem para esta fila é registada como uma dependência. No entanto, a operação de alto nível que descreve o processamento da mensagem não é recolhida automaticamente.

Vamos ver como podemos pode controlar as operações.

Num nível elevado, a tarefa consiste em criar `RequestTelemetry` e definir as propriedades conhecidas. Uma vez concluída a operação, controlar a telemetria. O exemplo seguinte demonstra esta tarefa.

### <a name="http-request-in-owin-self-hosted-app"></a>Pedido HTTP na aplicação personalizada alojada Owin
Neste exemplo, siga o [protocolo HTTP para correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Deverá receber cabeçalhos descritas não existe.

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

O protocolo HTTP para correlação também declara o `Correlation-Context` cabeçalho. No entanto, se for omitido aqui de simplicidade.

## <a name="queue-instrumentation"></a>Instrumentação de fila
Para comunicação HTTP, criámos um protocolo para passar os detalhes de correlação. Com protocolos dos algumas filas, pode passar de metadados adicionais, juntamente com a mensagem e com outras pessoas que não é possível.

### <a name="service-bus-queue"></a>Fila do Service Bus
Com o Azure [fila do Service Bus](../service-bus-messaging/index.md), pode passar de uma matriz de propriedades, juntamente com a mensagem. Utilizamos transmita o ID de correlação.

A fila do Service Bus utiliza protocolos de baseada em TCP. Application Insights automaticamente não regista as operações de fila, pelo que iremos controlam-los manualmente. A operação dequeue é uma API de push-estilo e não é possível controlá-lo.

#### <a name="enqueue"></a>Colocar em fila

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Processo
```C#
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Fila de armazenamento do Azure
O exemplo seguinte mostra como controlar o [fila de armazenamento do Azure](../storage/queues/storage-dotnet-how-to-use-queues.md) operações e correlacionar a telemetria entre o produtor, consumidor e Storage do Azure. 

A fila de armazenamento tem uma API de HTTP. Todas as chamadas para a fila são registadas pelo Recoletor de dependência do Application Insights para pedidos de HTTP.
Certifique-se de que tem `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` no `applicationInsights.config`. Se não o tiver, adicione-o através de programação conforme descrito em [filtragem e pré-processamentos no Azure Application Insights SDK](app-insights-api-filtering-sampling.md).

Se configurar manualmente o Application Insights, certifique-se de que cria e inicializar `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` de forma semelhante a:
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Também poderá pretender correlacionar o ID de operação de Application Insights com o ID de pedido de armazenamento. Para obter informações sobre como definir e obter um cliente de pedido de armazenamento e um ID de pedido do servidor, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Colocar em fila
Porque as filas de armazenamento suportam a API de HTTP, todas as operações com a fila automaticamente são registadas pelo Application Insights. Em muitos casos, este instrumentação deve ser suficientes. No entanto, para correlacionar rastreios no lado do consumidor com rastreios produtor, tem de transmitir algum contexto de correlação da mesma forma como podemos fazê-lo no protocolo HTTP para correlação. 

Neste exemplo, vamos controlar o opcional `Enqueue` operação. Pode:

 - **Correlacionar tentativas (se aplicável)**: têm um comuns principal que tem o `Enqueue` operação. Caso contrário, estão registados como subordinados do pedido a receber. Se existirem vários pedidos lógicos para a fila, poderão ser difíceis de encontrar que invocação resultou na tentativas.
 - **Correlacionar os registos de armazenamento (se sejam e quando for necessário)**: Se estiver a correlacionadas com telemetria do Application Insights.

O `Enqueue` operação é o subordinado de uma operação de principal (por exemplo, um pedido HTTP recebido). A chamada de dependência HTTP é o elemento subordinado do `Enqueue` operação e grandchild do pedido a receber:

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Para reduzir a quantidade de telemetria a aplicação relatórios ou se não quiser controlar o `Enqueue` operação por outros motivos, utilize o `Activity` diretamente a API:

- Criar (e iniciar) um novo `Activity` em vez de iniciar a operação do Application Insights. Fizer *não* necessário atribuir propriedades no mesmo, exceto o nome de operação.
- Serializar `yourActivity.Id` para o payload da mensagem em vez de `operation.Telemetry.Id`. Também pode utilizar `Activity.Current.Id`.


#### <a name="dequeue"></a>Anular
Tal `Enqueue`, um pedido HTTP real para a fila de armazenamento é automaticamente registado pelo Application Insights. No entanto, o `Enqueue` operação possivelmente ocorre no contexto principal, tal como um contexto de pedido de entrada. SDKs do Application Insights correlacionar automaticamente esse uma operação (e a parte HTTP) com o pedido de principal e outra telemetria comunicado no mesmo âmbito.

O `Dequeue` operação é tricky. O Application Insights SDK controla automaticamente os pedidos de HTTP. No entanto, não sabe o contexto de correlação até que a mensagem é analisada. Não é possível correlacionar os pedidos de HTTP para obter a mensagem com o resto da telemetria.

Em muitos casos, poderá ser útil correlacionar os pedidos de HTTP para a fila com outros rastreios bem. O exemplo seguinte demonstra como fazê-lo:

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Processo

No exemplo seguinte, vamos analisar uma mensagem a receber de forma semelhante para como podemos rastrear um pedido HTTP recebido:

```C#
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Da mesma forma, podem ser instrumentadas outras operações de fila. Uma operação peek deve instrumentada, de forma semelhante, como uma operação dequeue. Instrumentação operações de gestão da fila não é necessário. Application Insights controla operações como HTTP e, na maioria dos casos, é suficiente.

Quando instrumente a eliminação de mensagem, certifique-se de que define a operação de identificadores (correlação). Em alternativa, pode utilizar o `Activity` API. Em seguida, não tem de definir os identificadores de operação sobre os itens de telemetria devido Application Insights, para:

- Crie um novo `Activity` depois de já tem um item da fila.
- Utilize `Activity.SetParentId(message.ParentId)` para correlacionar os registos de consumidor e produtor.
- Iniciar o `Activity`.
- Controlar anular, processar e eliminar operações utilizando `Start/StopOperation` programas de ajuda. Fazê-lo a partir do mesmo fluxo de controlo assíncrona (contexto de execução). Desta forma, estes correlacionado estão corretamente.
- Parar o `Activity`.
- Utilize `Start/StopOperation`, ou chamar `Track` telemetria manualmente.

### <a name="batch-processing"></a>Processamento de batches
Com algumas filas, pode anular várias mensagens com um pedido. Processar estas mensagens, supostamente, independente e pertence as operações de lógico diferente. Neste caso, não é possível correlacionar os `Dequeue` operação para o processamento da mensagem específica.

Cada mensagem deve ser processada no seu próprio fluxo de controlo assíncrona. Para obter mais informações, consulte o [dependências enviar controlo](#outgoing-dependencies-tracking) secção.

## <a name="long-running-background-tasks"></a>Tarefas de segundo plano de execução longa
Algumas aplicações iniciar as operações de execução longa que podem ser causadas por pedidos de utilizador. Da perspetiva rastreio/instrumentation, não é diferente da instrumentação de pedido ou dependência: 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

Neste exemplo, utilizamos `telemetryClient.StartOperation` criar `RequestTelemetry` e preencher o contexto de correlação. Imaginemos que tem uma operação de principal que foi criada por pedidos recebidos que a operação agendada. Desde que `BackgroundTask` começa a mesma assíncrona controla o fluxo como um pedido recebido, é correlacionado com essa operação principal. `BackgroundTask`e todos os itens de telemetria aninhadas são automaticamente correlacionados com o pedido que, mesmo depois do pedido termina.

Quando a tarefa é iniciado a partir do thread de segundo plano que não tem qualquer operação (`Activity`) associados à mesma, `BackgroundTask` não tem qualquer principal. No entanto, este pode ter aninhadas operações. Todos os itens de telemetria comunicados pela tarefa estão correlacionados com o `RequestTelemetry` criado no `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Dependências de saída de controlo
Pode controlar o seu próprio tipo de dependência ou uma operação que não é suportada pelo Application Insights.

O `Enqueue` método na fila de barramento de serviço ou a fila de armazenamento pode servir como exemplos de tal controlo personalizado.

A abordagem geral para a dependência personalizada de controlo é:

- Chamar o `TelemetryClient.StartOperation` método (extensão) que se preenche a `DependencyTelemetry` propriedades que são necessários para correlação e algumas outras propriedades (hora de início carimbo, a duração).
- Definir outras propriedades personalizadas no `DependencyTelemetry`, tais como o nome e todos os contextos precisa.
- Efetuar uma dependência chamar e aguarde pela mesma.
- Parar a operação com `StopOperation` quando estiver concluída.
- Lidar com exceções.

`StopOperation`apenas interrompe a operação que foi iniciada. Se a operação de execução atual não corresponde à que pretende parar, `StopOperation` não produz qualquer efeito. Esta situação pode acontecer se iniciar múltiplas operações em paralelo no mesmo contexto de execução:

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Certifique-se de que sempre chamar `StartOperation` e execute a tarefa no seu próprio contexto:
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // Update status code and success as appropriate.
    }
    catch(...) 
    {
        // Update status code and success as appropriate.
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- Aprender as noções básicas de [correlação de telemetria](application-insights-correlation.md) no Application Insights.
- Consulte o [modelo de dados](application-insights-data-model.md) para o modelo de tipos e os dados do Application Insights.
- Relatório personalizado [métricas e eventos](app-insights-api-custom-events-metrics.md) ao Application Insights.
- Veja padrão [configuração](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) para a coleção de propriedades de contexto.
- Verifique o [Guia do utilizador System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para ver como podemos correlacionar a telemetria.
