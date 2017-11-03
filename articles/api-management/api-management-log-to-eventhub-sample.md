---
title: Monitorizar APIs com API Management do Azure, os Event Hubs e Runscope | Microsoft Docs
description: "Aplicação de exemplo que demonstra a política de registo para eventhub por ligação API Management do Azure, Event Hubs do Azure e Runscope para HTTP de registo e monitorização"
services: api-management
documentationcenter: 
author: darrelmiller
manager: erikre
editor: 
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 70ee752c5639c90f77dde104ce85eec0a1062300
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Monitorizar as suas APIs com API Management do Azure, os Event Hubs e Runscope
O [serviço de API Management](api-management-key-concepts.md) oferece muitas funcionalidades para melhorar o processamento de pedidos HTTP enviados para a API de HTTP. No entanto, a existência de pedidos e respostas são transitório. O pedido é efetuado e fluem através do serviço de API Management para a API de back-end. A API processa o pedido e uma resposta flui através de volta para o consumidor da API. O serviço de API Management mantém algumas estatísticas importantes sobre as APIs para apresentação no dashboard do portal do publicador, mas beyond, os detalhes são removidos.

Utilizando o [registo-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) [política](api-management-howto-policies.md) no serviço de API Management, pode enviar quaisquer detalhes do pedido e resposta a um [Hub de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Existem diversos motivos por que razão poderá pretender gerar eventos de mensagens HTTP que está a ser enviadas para as suas APIs. Alguns exemplos incluem registo de auditoria de atualizações, a análise de utilização, a alertas de exceção e a 3rd integrações de terceiros.   

Este artigo demonstra como capturar a mensagem de pedido e resposta HTTP toda, enviá-lo para um Hub de eventos e, em seguida, essa mensagem terceiros um serviço que fornece HTTP de registo e monitorização dos serviços de reencaminhamento.

## <a name="why-send-from-api-management-service"></a>Por que razão a enviar do serviço de gestão de API?
É possível escrever o middleware HTTP que pode ligue estruturas de API de HTTP para capturar e respostas de pedidos de HTTP e feed-los no registo e monitorização sistemas. Downside para esta abordagem é o middleware HTTP tem de ser integradas no back-end da API e tem de corresponder da plataforma da API. Se existirem várias APIs cada um deles tem de implementar o middleware. Muitas vezes, existem motivos por que motivo não é possível atualizar os APIs de back-end.

Utilizar o serviço de API Management do Azure para integrar com a infraestrutura de registo fornece uma solução centralizada e independentes da plataforma. Também é dimensionável, em parte devido ao [georreplicação](api-management-howto-deploy-multi-region.md) capacidades de API Management do Azure.

## <a name="why-send-to-an-azure-event-hub"></a>Por que razão a enviar para um Hub de eventos do Azure?
É um valor razoável pedir, Porquê criar uma política que é específica para Event Hubs do Azure? Existem vários locais diferentes em que queira os meus pedidos de registo. Por que motivo não enviar apenas os pedidos diretamente para o destino final?  Que é uma opção. No entanto, quando efetuar pedidos de registo de um serviço de gestão de API, é necessário considerar como mensagens de registo terá impacto no desempenho da API. Gradual aumentos de carga podem ser processados através do aumento instâncias disponíveis dos componentes do sistema ou ao tirar partido da replicação geográfica. No entanto, picos curtos no tráfego podem causar pedidos para significativamente sofrer um atraso se iniciar pedidos para a infraestrutura de registo mais lento em carga.

Os Event Hubs do Azure foi concebido para entrada grandes volumes de dados, com capacidade para lidar com um número muito superior de eventos que o número de HTTP pedidos maior parte dos processos APIs. O Hub de eventos atua como um tipo de memória intermédia sofisticada entre o serviço de gestão de API e a infraestrutura que irá armazenar e processar as mensagens. Isto garante que o desempenho da sua API não será afetado negativamente devido à infraestrutura de registo.  

Depois dos dados foi transmitidos para um Hub de eventos que é persistente e irá aguardar que os consumidores de Event Hub processá-la. O Hub de eventos não se a forma como serão processado, apenas cares sobre certificar-se de que a mensagem será entregue com êxito.     

Os Event Hubs têm a capacidade de eventos de fluxo a vários grupos de consumidores. Isto permite que os eventos a serem processados pelos sistemas completamente diferentes. Isto permite que suporta vários cenários de integração sem atrasos de adição a ocasionar no processamento do pedido de API no serviço de API Management como apenas um evento tem de ser gerado.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Uma política para enviar mensagens de aplicações/http
Um Hub de eventos aceita dados de eventos como uma cadeia simple. O conteúdo dessa cadeia é completamente até que. Para poder empacotar um pedido de HTTP e enviar para os Event Hubs é preciso formatar a cadeia com as informações de pedido ou resposta. Em situações como esta, se existir um formato existente podemos reutilizar, em seguida, possível ter ao escrever a nossa próprio código de análise. Inicialmente considerados posso utilizar o [HAR](http://www.softwareishard.com/blog/har-12-spec/) para enviar pedidos e respostas HTTP. No entanto, este formato é otimizado para armazenar uma sequência de pedidos de HTTP num formato JSON com base. Continha um número de elementos obrigatórios que adicionar complexidade desnecessária para o cenário de transmitir a mensagem HTTP através da transmissão.  

Uma opção alternativa consistia em utilizar o `application/http` tipo de suporte, conforme descrito na especificação HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Este tipo de suporte de dados utiliza o mesmo formato exato que é utilizado para, efetivamente, enviar mensagens HTTP através da transmissão, mas a mensagem completa pode ser colocado no corpo de outro pedido HTTP. No nosso caso, apenas vamos utilizar o corpo como nosso mensagem para enviar para os Event Hubs. Comodamente, há um analisador que existe na [cliente do Microsoft ASP.NET Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) bibliotecas que podem analisar este formato e convertê-lo para o nativo `HttpRequestMessage` e `HttpResponseMessage` objetos.

Para conseguir criar esta mensagem é necessário tirar partido de c# com base [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) na API Management do Azure. Segue-se a política que envia uma mensagem de pedido HTTP para Event Hubs do Azure.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Declaração de política
Não existe algumas coisas específicas mencionar sobre esta expressão de política. A política de registo para eventhub tem um atributo denominado logger-id refere-se com o nome do registo que foi criado no âmbito do serviço de API Management. Os detalhes sobre como configurar um registo de Hub de eventos no serviço de API Management podem ser encontrados no documento [como eventos de registo para os Hubs de eventos do Azure na API Management do Azure](api-management-howto-log-event-hubs.md). O segundo atributo é um parâmetro opcional, que instrui os Hubs de eventos de partição para armazenar a mensagem. Hubs de eventos utilizam partições para ativar scalabilty e exigir um mínimo de dois. A entrega ordenada de mensagens em fila fique apenas dentro de uma partição. Se Vamos dar instruções ao Hub de eventos na partição de colocar a mensagem, irá utilizar um algoritmo de round robin para distribuir a carga. No entanto, que pode fazer com que algumas das nossas mensagens para ser processado fora de ordem.  

### <a name="partitions"></a>Partições
Para garantir que o nosso mensagens são entregues para consumidores por ordem e tirar partido da capacidade de distribuição de carga de partições, posso optar por enviar mensagens de pedido HTTP para uma partição e mensagens de resposta HTTP para uma segunda partição. Isto irá garantir uma distribuição de carga do mesmo e podemos pode garantir que todos os pedidos serão consumidos por ordem e todas as respostas serão consumidas por ordem. É possível que uma resposta a ser consumidos antes do pedido correspondente, mas à medida que não é um problema temos um mecanismo diferentes para correlacionar os pedidos para respostas e sabemos que sempre voltar pedidos antes de respostas.

### <a name="http-payloads"></a>Payloads HTTP
Depois de criar o `requestLine` verificamos para ver se o corpo do pedido deve ser truncado. O corpo do pedido é truncado para apenas 1024. Isto pode ser aumentado, no entanto, as mensagens de Hub de eventos individuais estão limitadas a 256KB, pelo que é provável que algumas mensagem HTTP corpos irá não caber numa única mensagem. Ao efetuar o registo e análise de uma quantidade significativa de informações pode ser derivada da apenas a linha de pedido HTTP e os cabeçalhos. Além disso, muitos pedidos de API devolverem apenas os corpos de pequenos e, por isso, a perda de valor de informações por truncar corpos de grande bastante mínima em comparação com a redução de transferência, processamento e os custos de armazenamento para manter todo o conteúdo do corpo. Uma nota final sobre processar o corpo é que precisamos de passar `true` para o<string>método () porque foi são ao ler os conteúdos de corpo, mas foi também pretende o API para conseguir ler o corpo de back-end. Mediante a transmissão de TRUE para este método é fazer com que o corpo ser colocado na memória intermédia para que possa ser lido uma segunda vez. Isto é importante ter em conta se tiver uma API que o carregamento de ficheiros grandes ou utiliza o tempo de consulta. Nestes casos seria aconselhável evitar ao ler o corpo de todo.   

### <a name="http-headers"></a>Cabeçalhos de HTTP
Os cabeçalhos de HTTP podem simplesmente transferidos para o formato de mensagem num formato par chave/valor simples. Escolhemos da faixa saída determinados campos confidenciais de segurança, para evitar a fuga de dados, desnecessariamente, informações de credenciais. Não é provável que as chaves de API e outras credenciais seriam utilizadas para fins de análise. Se pretendemos efetuar análise num produto específico e o utilizador que estão a utilizar, em seguida, iremos foi possível obter que do `context` de objeto e adicionar que a mensagem.     

### <a name="message-metadata"></a>Metadados de mensagem
Ao criar a mensagem completa para enviar para o hub de eventos, a primeira linha não é, na verdade, faça parte do `application/http` mensagem. A primeira linha é constituída por se a mensagem é um pedido ou mensagem de resposta e um id de mensagem que é utilizado para correlacionar os pedidos para respostas de metadados adicionais. O id da mensagem é criado utilizando outra política que tem este aspeto:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Iremos foi ter criado a mensagem de pedido, que armazenada numa variável até que a resposta foi devolvida e, em seguida, basta enviada o pedido e resposta como uma única mensagem. No entanto, ao enviar o pedido e resposta de forma independente e utilizar um id de mensagem para correlacionar os dois, obtemos um pouco mais flexibilidade no tamanho da mensagem, a capacidade para tirar partido das várias partições enfrenta mantém o pedido e a ordem da mensagem serão apresentados no nosso dashboard mais cedo do registo. Também poderão existir alguns cenários em que uma resposta válida nunca é enviada para o hub de eventos, possivelmente devido a um erro fatal pedido no serviço de API Management, mas ainda temos um registo do pedido.

A política para enviar a mensagem de resposta de HTTP muito semelhante ao pedido e, por isso, a configuração da política concluída tem o seguinte aspeto:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

O `set-variable` política cria um valor que esteja acessível por ambos os `log-to-eventhub` política no `<inbound>` secção e o `<outbound>` secção.  

## <a name="receiving-events-from-event-hubs"></a>Receber eventos de Event Hubs
Eventos a partir do Hub de eventos do Azure são recebidos utilizando o [protocolo AMQP](http://www.amqp.org/). A equipa do Service Bus do Microsoft efetuou cliente bibliotecas disponíveis para o facilitar os eventos de consumo. Existem duas abordagens diferentes suportadas, uma está a ser um *consumidor direto* e o outro está a utilizar o `EventProcessorHost` classe. Exemplos destas duas abordagens podem ser encontrados no [guia de programação de Hubs de eventos](../event-hubs/event-hubs-programming-guide.md). A versão abreviada das diferenças é, `Direct Consumer` dá-lhe controlo total e o `EventProcessorHost` não parte do trabalho canalização para mas faz com que certos pressupostos sobre a forma como irá processar esses eventos.  

### <a name="eventprocessorhost"></a>EventProcessorHost
Neste exemplo, utilizaremos o `EventProcessorHost` de simplicidade, no entanto, poderá não a melhor opção para este cenário específico. `EventProcessorHost`não o trabalho de certificar-se de que não têm de se preocupar threading problemas dentro de uma classe de processador de eventos específico. No entanto, no nosso cenário, iremos são simplesmente converter a mensagem para outro formato e passou-ao longo para outro serviço utilizando um método async. Não é necessário para atualizar o estado partilhado e, por conseguinte, não risco de problemas de thread. Para a maioria dos cenários, `EventProcessorHost` provavelmente é a melhor opção e certamente é a opção mais fácil.     

### <a name="ieventprocessor"></a>IEventProcessor
O conceito central quando utilizar `EventProcessorHost` é criar um uma implementação do `IEventProcessor` interface que contém o método `ProcessEventAsync`. Essence esse método é mostrada aqui:

```c#
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

   foreach (EventData eventData in messages)
   {
       _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

       try
       {
           var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
           await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
       }
       catch (Exception ex)
       {
           _Logger.LogError(ex.Message);
       }
   }
    ... checkpointing code snipped ...
}
```

Uma lista de objetos de EventData são transmitidos para o método e iremos iterar essa lista. Os bytes de cada método são analisados para um objeto de HttpMessage e esse objeto é passado para uma instância de IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
O `HttpMessage` instância contém três tipos de dados:

```c#
public class HttpMessage
{
   public Guid MessageId { get; set; }
   public bool IsRequest { get; set; }
   public HttpRequestMessage HttpRequestMessage { get; set; }
   public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

O `HttpMessage` instância contém um `MessageId` GUID que permite-nos ligar os pedidos de HTTP para a resposta HTTP correspondente e um valor booleano que identifica se o objeto contém uma instância de um HttpRequestMessage e HttpResponseMessage. Ao utilizar o incorporado em classes HTTP de `System.Net.Http`, consegui tirar partido do `application/http` ao analisar o código que está incluído na `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
O `HttpMessage` instância, em seguida, é reencaminhada para a implementação de `IHttpMessageProcessor` que é uma interface criei para desassociar a receção e a interpretação do evento do Hub de eventos do Azure e o processamento real das-lo.

## <a name="forwarding-the-http-message"></a>A mensagem HTTP de reencaminhamento
Para este exemplo, posso decidiu seria interessante emitir o pedido de HTTP ao longo para [Runscope](http://www.runscope.com). Runscope é um serviço baseado na nuvem que specializes de HTTP de depuração, registo e monitorização. Têm um escalão gratuito, por conseguinte, é fácil experimentar e permite-nos ver os pedidos HTTP em tempo real fluir através do nosso serviço de API Management.

O `IHttpMessageProcessor` implementação aspeto,

```c#
public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
{
   private HttpClient _HttpClient;
   private ILogger _Logger;
   private string _BucketKey;
   public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
   {
       _HttpClient = httpClient;
       var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
       _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
       _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
       _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
       _Logger = logger;
   }

   public async Task ProcessHttpMessage(HttpMessage message)
   {
       var runscopeMessage = new RunscopeMessage()
       {
           UniqueIdentifier = message.MessageId
       };

       if (message.IsRequest)
       {
           _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
           runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
       }
       else
       {
           _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
           runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
       }

       var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
       messagesLink.BucketKey = _BucketKey;
       messagesLink.RunscopeMessage = runscopeMessage;
       var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
       _Logger.LogDebug("Request sent to Runscope");
   }
}
```

Consegui tirar partido de uma [biblioteca de clientes existentes para Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) que torna mais fácil push `HttpRequestMessage` e `HttpResponseMessage` instâncias de cópia de segurança para o seu serviço. Para aceder à API do Runscope terá uma conta e uma chave de API. Pode encontrar instruções para obter uma chave de API no [criar aplicações API do acesso Runscope](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) screencast.

## <a name="complete-sample"></a>Exemplo completo
O [código fonte](https://github.com/darrelmiller/ApimEventProcessor) e testes para o exemplo no GitHub. Precisa de um [serviço de gestão de API](api-management-get-started.md), [um Hub de eventos ligado](api-management-howto-log-event-hubs.md)e um [conta de armazenamento](../storage/common/storage-create-storage-account.md) para executar o exemplo para si próprio.   

O exemplo é apenas uma simple aplicação de consola que escuta de eventos provenientes de Hub de eventos, converte-los para um `HttpRequestMessage` e `HttpResponseMessage` objetos e, em seguida, encaminha-os para a API de Runscope.

Na imagem seguinte animada, pode ver um pedido que está a ser efetuado a uma API no Portal do programador, a aplicação de consola que mostra a mensagem a ser recebidos, processados e reencaminhados e, em seguida, o pedido e resposta ser apresentado no inspector Runscope tráfego.

![Demonstração de pedido reencaminhado para Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Resumo
Serviço de API Management do Azure fornece um local ideal para capturar o tráfego HTTP viajar de e para as suas APIs. Os Hubs de eventos do Azure é uma solução altamente dimensionável e de baixo custo para esse tráfego a capturar e feeding-lo para sistemas de processamento secundário para o registo, monitorização e outros sofisticados análise. A ligar ao tráfego de terceiros 3rd sistemas como Runscope é uma simples como algumas dozen linhas de código de monitorização.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os Event Hubs do Azure
  * [Introdução ao Event Hubs do Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com o EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração de API Management e do Event Hubs
  * [Como registar eventos aos Hubs de eventos do Azure na API Management do Azure](api-management-howto-log-event-hubs.md)
  * [Referência de entidade de registo](https://msdn.microsoft.com/library/azure/mt592020.aspx)
  * [referência de política de registo para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
