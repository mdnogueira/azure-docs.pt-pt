---
title: "Ajudar a comunicação segura para serviços no Azure Service Fabric | Microsoft Docs"
description: "Descrição geral de como ajudar a proteger a comunicação para fiáveis serviços que estão em execução num cluster do Azure Service Fabric."
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
ms.openlocfilehash: c4634e3d8efb1745fffcfe3e647e43d867038716
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Ajudar a comunicação segura para serviços no Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Ajudar a proteger um serviço quando estiver a utilizar a comunicação remota do serviço
Iremos utilizar um existente [exemplo](service-fabric-reliable-services-communication-remoting-java.md) que explica como configurar a gestão remota para serviços fiáveis. Para ajudar a proteger um serviço quando estiver a utilizar a comunicação remota do serviço, siga estes passos:

1. Criar uma interface, `HelloWorldStateless`, que define os métodos que estarão disponíveis para uma chamada de procedimento remoto do seu serviço. O serviço irá utilizar `FabricTransportServiceRemotingListener`, que está declarada no `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` pacote. Este é um `CommunicationListener` implementação que oferece funções de sistema de interação remota.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Adicione as definições de escuta e credenciais de segurança.

    Certifique-se de que o certificado que pretende utilizar para ajudar a proteger a comunicação de serviço é instalado em todos os nós do cluster. Existem duas formas que pode fornecer as definições de escuta e credenciais de segurança:

   1. Fornecê-los utilizando um [o pacote de configuração](service-fabric-application-model.md):

       Adicionar um `TransportSettings` secção no ficheiro settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       Neste caso, o `createServiceInstanceListeners` método terá este aspeto:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Se adicionar um `TransportSettings` secção no ficheiro settings.xml sem um prefixo `FabricTransportListenerSettings` carregará todas as definições desta secção por predefinição.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Neste caso, o `CreateServiceInstanceListeners` método terá este aspeto:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Quando chamar métodos num serviço protegido através da pilha do sistema de interação remota, em vez de utilizar o `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` classe para criar um proxy de serviço, utilize `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Se o código de cliente está a ser executado como parte de um serviço, pode carregar `FabricTransportSettings` do ficheiro settings.xml. Crie uma secção de TransportSettings que é semelhante ao código do serviço, conforme mostrado anteriormente. Efetue as seguintes alterações ao código de cliente:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
