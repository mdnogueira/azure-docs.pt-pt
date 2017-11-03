---
title: "Comunicação remota do serviço no Azure Service Fabric | Microsoft Docs"
description: "Comunicação remota do Service Fabric permite que os clientes e serviços comunicar com os serviços, utilizando uma chamada de procedimento remoto."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: dc4a362b5737bb424ca2c196c85f4c51b6ee5e30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-remoting-with-reliable-services"></a>Comunicação remota do serviço com Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java em Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

A estrutura de Reliable Services fornece um mecanismo de comunicação remota de forma rápida e fácil configurar a chamada de procedimento remoto para os serviços.

## <a name="set-up-remoting-on-a-service"></a>Configurar a gestão remota num serviço
Configurar a gestão remota para um serviço é efetuada em dois passos simples:

1. Crie uma interface para o seu serviço implementar. Esta interface define os métodos disponíveis para uma chamada de procedimento remoto do seu serviço. Os métodos devem ser tarefas métodos assíncronos. Tem de implementar a interface `microsoft.serviceFabric.services.remoting.Service` para assinalar que o serviço possui uma interface de gestão remota.
2. Utilize um serviço de escuta de comunicação remota no seu serviço. Este é um `CommunicationListener` implementação que oferece funções de sistema de interação remota. `FabricTransportServiceRemotingListener`pode ser utilizado para criar um serviço de escuta da gestão remota utilizando o protocolo de transporte de comunicação remota de predefinição.

Por exemplo, o serviço sem monitorização de estado seguinte expõe um único método para obter "Olá mundo" através de uma chamada de procedimento remoto.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Os argumentos e os tipos de retorno na interface de serviço podem ser qualquer tipos personalizados, simples ou complexos, mas tem de ser serializáveis.
>
>

## <a name="call-remote-service-methods"></a>Chamar os métodos de serviço remoto
Chamar os métodos num serviço utilizando a pilha do sistema de interação remota é feito utilizando um proxy local para o serviço através de `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` classe. O `ServiceProxyBase` método cria um proxy local utilizando a mesma interface que implementa o serviço. Com esse proxy, pode chamar simplesmente métodos na interface remotamente.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A arquitetura de sistema de interação remota propaga exceções acionadas o serviço para o cliente. Lógica de processamento de exceções, por isso, o cliente utilizando `ServiceProxyBase` diretamente pode processar exceções que emite o serviço.

## <a name="service-proxy-lifetime"></a>Duração do Proxy de serviço
Criação de ServiceProxy é uma operação simples, para que o utilizador pode criar tantas como precisarem. Proxy de serviço pode ser reutilizado desde que o utilizador precisar dele. Utilizador pode utilizar novamente ao mesmo proxy em caso de exceção. Cada ServiceProxy contém cliente de comunicação utilizada para enviar mensagens através da transmissão. Ao invocar a API, temos de interno Verifique se o cliente de comunicação utilizada é válido. Com base no que resultam, vamos voltar a criar o cliente de comunicação. Por conseguinte, o utilizador não é necessário recriar serviceproxy em caso de exceção.

### <a name="serviceproxyfactory-lifetime"></a>Duração de ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) é uma fábrica de que cria o proxy para interfaces de comunicação remota diferente. Se utilizar a API `ServiceProxyBase.create` para criar o proxy, em seguida, framework cria um `FabricServiceProxyFactory`.
É útil criar um manualmente quando for necessário substituir [ServiceRemotingClientFactory](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory) propriedades.
Fábrica é uma operação dispendiosa. `FabricServiceProxyFactory`mantém a cache de clientes de comunicação.
Melhor prática é cache `FabricServiceProxyFactory` há tempo quanto possível.

## <a name="remoting-exception-handling"></a>Processamento de exceções de comunicação remota
A exceção de remota emitida pela API do serviço, como RuntimeException ou FabricException são enviadas para o cliente.

ServiceProxy processar todas as exceções de ativação pós-falha para a partição de serviço é criado para. Resolve novamente os pontos finais esteja Exceptions(Non-Transient Exceptions) ativação pós-falha e repetem a chamada com o ponto final correto. Número de tentativas de ativação pós-falha exceção é indefinido.
Em caso de TransientExceptions, apenas repete a chamada.

Os parâmetros predefinidos de repetição são fornecidos por [OperationRetrySettings]. (https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) Transferindo OperationRetrySettings objeto para ServiceProxyFactory construtor, o utilizador pode configurar estes valores.

## <a name="next-steps"></a>Passos seguintes
* [Proteger a comunicação para Reliable Services](service-fabric-reliable-services-secure-communication.md)
