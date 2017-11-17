---
title: "Comunicação remota do serviço no Service Fabric | Microsoft Docs"
description: "Comunicação remota do Service Fabric permite que os clientes e serviços comunicar com os serviços, utilizando uma chamada de procedimento remoto."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 438eeee7353cbd1d534f27471c9c9054aecc12e8
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="service-remoting-with-reliable-services"></a>Comunicação remota do serviço com Reliable Services
Para os serviços que não estão associados a um protocolo de comunicação específico ou pilha, como end WebAPI, o Windows Communication Foundation (WCF) ou outros recursos, a arquitetura de Reliable Services fornece um mecanismo de comunicação remota de forma rápida e fácil configurar a chamada de procedimento remoto para os serviços.

## <a name="set-up-remoting-on-a-service"></a>Configurar a gestão remota num serviço
Configurar a gestão remota para um serviço é efetuada em dois passos simples:

1. Crie uma interface para o seu serviço implementar. Esta interface define os métodos disponíveis para uma chamada de procedimento remoto do seu serviço. Os métodos devem ser tarefas métodos assíncronos. Tem de implementar a interface `Microsoft.ServiceFabric.Services.Remoting.IService` para assinalar que o serviço possui uma interface de gestão remota.
2. Utilize um serviço de escuta de comunicação remota no seu serviço. RemotingListener é um `ICommunicationListener` implementação que oferece funções de sistema de interação remota. O `Microsoft.ServiceFabric.Services.Remoting.Runtime` espaço de nomes contém um método de extensão,`CreateServiceRemotingListener` para serviços sem monitorização de estado e com monitorização de estado que podem ser utilizados para criar um serviço de escuta da gestão remota utilizando o protocolo de transporte de comunicação remota de predefinição.

>[!NOTE]
>O `Remoting` espaço de nomes está disponível como um pacote NuGet separado denominado`Microsoft.ServiceFabric.Services.Remoting`

Por exemplo, o serviço sem monitorização de estado seguinte expõe um único método para obter "Olá mundo" através de uma chamada de procedimento remoto.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Os argumentos e os tipos de retorno na interface de serviço podem ser qualquer tipos personalizados, simples ou complexos, mas tem de ser serializáveis por .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Chamar os métodos de serviço remoto
Chamar os métodos num serviço utilizando a pilha do sistema de interação remota é feito utilizando um proxy local para o serviço através de `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` classe. O `ServiceProxy` método cria um proxy local utilizando a mesma interface que implementa o serviço. Com esse proxy, pode chamar métodos na interface remotamente.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A arquitetura de sistema de interação remota propaga exceções acionadas o serviço para o cliente. Lógica de processamento de exceções, por isso, o cliente utilizando `ServiceProxy` diretamente pode processar exceções que emite o serviço.

## <a name="service-proxy-lifetime"></a>Duração do Proxy de serviço
Criação de ServiceProxy é uma operação simples, para que os utilizadores podem criar tantas como que precisam. Instâncias de Proxy de serviço podem ser reutilizadas, desde que os utilizadores necessitam. Se uma chamada de procedimento remoto emite uma exceção, os utilizadores ainda podem reutilizar a mesma instância de proxy. Cada ServiceProxy contém um cliente de comunicação utilizado para enviar mensagens através da transmissão. Ao invocar chamadas remotas, iremos internamente Verifique se o cliente de comunicação é válido. Com base no que resultam, vamos voltar a criar o cliente de comunicação se for necessário. Por conseguinte, se ocorrer uma exceção, os utilizadores não precisam de recriar serviceproxy, mas são feitos transparente.

### <a name="serviceproxyfactory-lifetime"></a>Duração de ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) é uma fábrica de que cria instâncias de proxy para interfaces de comunicação remota diferente. Se utilizar a api `ServiceProxy.Create` para criar o proxy, em seguida, a estrutura cria um singleton ServiceProxy.
É útil criar um manualmente quando for necessário substituir [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) propriedades.
A criação do Factory é uma operação dispendiosa. ServiceProxyFactory mantém uma cache do cliente de comunicação interna.
É melhor prática colocar em cache ServiceProxyFactory, desde que possível.

## <a name="remoting-exception-handling"></a>Processamento de exceções de comunicação remota
A exceção de remota emitida pela API do serviço, são enviadas para o cliente como AggregateException. RemoteExceptions deve ser serializáveis DataContract [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) é acionada ao proxy API com o erro de serialização.

ServiceProxy processar todas as exceções de ativação pós-falha para a partição de serviço é criado para. Resolve novamente os pontos finais esteja Exceptions(Non-Transient Exceptions) ativação pós-falha e repetem a chamada com o ponto final correto. Número de tentativas de ativação pós-falha exceção é indefinido.
Se ocorrer exceções transitório, proxy repete a chamada.

Os parâmetros predefinidos de repetição são fornecidos por [OperationRetrySettings]. (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) Transferindo OperationRetrySettings objeto para ServiceProxyFactory construtor, o utilizador pode configurar estes valores.
## <a name="how-to-use-remoting-v2-stack"></a>Como utilizar a pilha do sistema de interação remota V2
Com o pacote de gestão remota do NuGet 2.8, tem a opção para utilizar a pilha do sistema de interação remota V2. Pilha do sistema de interação remota V2 é mais performant e fornece as funcionalidades, como personalizada serializável e mais incorporável Api.
Por predefinição, se não fizer seguintes alterações, continua a utilizar a pilha do sistema de interação remota V1.
Não é compatível com V1 V2 de sistema de interação remota (pilha de comunicação remota anterior), por isso, siga abaixo artigo sobre como atualizar v1 para V2, sem afetar a disponibilidade do serviço.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Utilizar o atributo de assemblagem para utilizar a pilha de V2.

Eis os passos a seguir para alterar a pilha de V2.

1. Adicione um recurso de ponto final com o nome como "ServiceEndpointV2" no manifesto de serviço.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Utilize o método de extensão de comunicação remota para criar o serviço de escuta de comunicação remota.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Adicione o atributo de assemblagem em Interfaces de sistema de interação remota.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
Não são necessárias alterações no projeto de cliente.
Criar a assemblagem de cliente com a assemblagem de interface, para certifica-se de que acima assemblagem atributo está a ser utilizado.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Utilizar Classes de V2 explícita para criar o serviço de escuta / ClientFactory
Eis os passos a seguir.
1.  Adicione um recurso de ponto final com o nome como "ServiceEndpointV2" no manifesto de serviço.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Utilize [V2Listener de comunicação remota](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingistener?view=azure-dotnet). Nome de recurso de ponto final de serviço predefinido utilizado é "ServiceEndpointV2" e tem de ser definido no Service Manifest.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Utilizar V2 [fábrica do cliente](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Como atualizar V1 de comunicação remota para comunicação remota V2.
Para atualizar v1 para V2, são necessárias atualizações do passo 2. Passos seguintes para ser executada na sequência listada.

1. Atualize o serviço de V1 V2 serviço utilizando o atributo CompactListener.
Esta alteração certifica-se de que o serviço está à escuta em V1 e V2 serviço de escuta.

    a) adicione um recurso de ponto final com o nome como "ServiceEndpointV2" no manifesto de serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) utilize o seguinte método de extensão para criar o serviço de escuta do sistema de interação remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) adicione o atributo de assemblagem em Interfaces de comunicação remota para utilizar CompatListener e cliente V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Atualize o cliente de V1, V2 cliente, utilizando o atributo de cliente V2.
Este passo certifica-se de que cliente está a utilizar V2 pilha.
É necessária qualquer alteração no projeto/serviço de cliente. A criar os projetos de cliente com a assemblagem de interface atualizado é suficiente.

3. Este passo é opcional. Utilize o atributo V2Listener e, em seguida, atualizar o serviço de V2.
Este passo certifica-se de que o serviço está á escuta apenas no serviço de escuta V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Como utilizar a serialização personalizada com o sistema de interação remota V2.
Exemplo seguinte utiliza serialização Json com o sistema de interação remota V2.
1. Implementa a interface de IServiceRemotingMessageSerializationProvider para implementação de serialização personalizada.
    Eis o fragmento de código em como implementação aspeto.

    ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
      public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
          IEnumerable<Type> requestBodyTypes)
      {
          return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
      }

      public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
          IEnumerable<Type> responseBodyTypes)
      {
          return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
      }

      public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
      {
          return new JsonMessageFactory();
      }
     }

  class JsonMessageFactory: IServiceRemotingMessageBodyFactory
  {
      public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
          int numberOfParameters)
      {
          return new JsonRemotingRequestBody(new JObject());
      }

      public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
      {
          return  new JsonRemotingResponseBody();
      }
   }

  class ServiceRemotingRequestJsonMessageBodySerializer: IServiceRemotingRequestMessageBodySerializer
  {
      public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
      {
          if (serviceRemotingRequestMessageBody == null)
          {
              return null;
          }

          var json = serviceRemotingRequestMessageBody.ToString();
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var segments = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(segments);
      }

      public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (JsonReader reader = new JsonTextReader(sr))
          {
              var serializer = new JsonSerializer();
              var ob = serializer.Deserialize<JObject>(reader);
              var ob2 = new JsonRemotingRequestBody(ob);
              return ob2;
          }
      }
  }

  class ServiceRemotingResponseJsonMessageBodySerializer: IServiceRemotingResponseMessageBodySerializer
  {

      public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
      {
          var json = JsonConvert.SerializeObject(responseMessageBody,new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
          });
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var list = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(list);
      }

      public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (var reader = new JsonTextReader(sr))
          {
              var serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                  TypeNameHandling = TypeNameHandling.All
              });
              return serializer.Deserialize<JsonRemotingResponseBody>(reader);
          }
      }
  }
  internal class JsonRemotingResponseBody: IServiceRemotingResponseMessageBody
  {
      public object Value;

      public void Set(object response)
      {
          this.Value = response;
      }

      public object Get(Type paramType)
      {
          return this.Value;
      }
  }

  class JsonRemotingRequestBody: IServiceRemotingRequestMessageBody
    {
      private readonly JObject jobject;

        public JsonRemotingRequestBody(JObject ob)
        {
            this.jobject = ob;
        }

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.jobject.Add(parameName, JToken.FromObject(parameter));
        }

        public object GetParameter(int position, string parameName, Type paramType)
       {
           var ob = this.jobject[parameName];
           return ob.ToObject(paramType);
       }

       public override string ToString()
        {
            return this.jobject.ToString();
        }
    }
    ```

2.    Substitua o fornecedor predefinido de serialização com JsonSerializationProvider para serviço de escuta do sistema de interação remota.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Substitua o fornecedor predefinido de serialização com JsonSerializationProvider de fábrica do cliente de sistema de interação remota.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Passos seguintes
* [Web API com OWIN nos serviços de fiáveis](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação de WCF com Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Proteger a comunicação para Reliable Services](service-fabric-reliable-services-secure-communication.md)
