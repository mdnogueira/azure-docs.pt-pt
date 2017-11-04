---
title: "Pilha de comunicação de serviços WCF fiável | Microsoft Docs"
description: "A pilha de comunicação de WCF incorporada no Service Fabric fornece comunicações de WCF do serviço de cliente para Reliable Services."
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7037620ebdc26a9f18531064bf45d058f5060e39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pilha de comunicação baseada em WCF para Reliable Services
A estrutura de Reliable Services permite que os autores de serviço escolher a pilha de comunicação que pretende utilizar para o seu serviço. Estes podem plug-in a pilha de comunicação da sua eleição através o **ICommunicationListener** devolvidos pelo [CreateServiceReplicaListeners ou CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) métodos. A estrutura fornece uma implementação da pilha de comunicação com base no Windows Communication Foundation (WCF) para autores de serviço que pretendem utilizar a comunicação baseada em WCF.

## <a name="wcf-communication-listener"></a>Serviço de escuta de comunicação de WCF
A implementação de WCF específicos de **ICommunicationListener** é fornecida pelo **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** classe.

Lest diga temos um contrato de serviço do tipo`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Podemos criar um serviço de escuta de comunicação de WCF no serviço da seguinte forma.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Escrever os clientes para a pilha de comunicação de WCF
Para escrever os clientes comunicam com os serviços através de WCF, a estrutura fornece **WcfClientCommunicationFactory**, que é a implementação de WCF específicos de [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

O canal de comunicação de WCF pode ser acedido a partir de **WcfCommunicationClient** criados pelo **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Código de cliente pode utilizar o **WcfCommunicationClientFactory** juntamente com o **WcfCommunicationClient** que implementa **ServicePartitionClient** para determinar o ponto final de serviço e comunicar com o serviço.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> A predefinição ServicePartitionResolver parte do princípio de que o cliente está em execução no mesmo cluster que o serviço. Se isto não for o caso, crie um objeto de ServicePartitionResolver e atribua os pontos finais de ligação de cluster.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Chamada de procedimento remoto com o sistema de interação remota Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Web API com OWIN nos serviços de fiáveis](service-fabric-reliable-services-communication-webapi.md)
* [Proteger a comunicação para Reliable Services](service-fabric-reliable-services-secure-communication.md)

