---
title: "Ligar e comunicar com serviços no Azure Service Fabric | Microsoft Docs"
description: "Saiba como resolver, ligar e comunicar com serviços no Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: d0b4ff1959465ade5f57c045d2a005e828638eb2
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Ligar e comunicar com serviços no Service Fabric
No Service Fabric, um serviço é executado algures num cluster de Service Fabric, normalmente distribuído por várias VMs. -Pode ser movida local para outro, pelo proprietário do serviço, ou automaticamente pelo Service Fabric. Serviços estaticamente não estão associados a um determinado computador ou endereço.

Uma aplicação de Service Fabric, geralmente, é composta por vários serviços diferentes, onde cada serviço executa uma tarefa de especializado. Estes serviços podem comunicar entre si para formar uma função concluída, como as diferentes partes composição de uma aplicação web. Também existem aplicações cliente que se ligar a e comunicam com serviços. Este documento descreve como configurar a comunicação com o operador and entre os serviços no Service Fabric.

Este vídeo Microsoft Virtual Academy também descreve comunicação do serviço:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=iYFCk76yC_6706218965">  
<img src="./media/service-fabric-connect-and-communicate-with-services/CommunicationVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="bring-your-own-protocol"></a>Traga a sua própria protocolo
Recursos de infraestrutura do serviço de ajuda a gerir o ciclo de vida dos seus serviços, mas não a tomar decisões sobre o que fazem os serviços. Isto inclui a comunicação. Quando o serviço está aberto por Service Fabric, que é a oportunidade do serviço para configurar um ponto final para pedidos recebidos, utilizando qualquer pilha de protocolo ou comunicação que pretende. O serviço irá escutar num normal **IP:port** utilizando qualquer esquema de endereçamento, tais como um URI de endereço. Várias instâncias de serviço ou réplicas podem partilhar um processo de anfitrião, caso em que estes terão de utilizar portas diferentes ou utilizar um mecanismo de partilha de portas, tais como o controlador de kernel do http.sys do Windows. Em ambos os casos, cada instância de serviço ou a réplica num processo anfitrião tem de ser exclusivamente endereçável.

![pontos finais de serviço][1]

## <a name="service-discovery-and-resolution"></a>Deteção do serviço e resolução
Num sistema distribuído, serviços podem mover de uma máquina para outra ao longo do tempo. Isto pode acontecer por vários motivos, incluindo recursos balanceamento, atualizações, as ativações pós-falha ou Escalamento horizontal. Isto significa que os endereços de ponto final de serviço alterar à medida que o serviço move para nós com endereços IP diferentes, pode abrir portas diferentes, se o serviço utiliza uma porta dinâmica selecionada.

![Distribuição de serviços][7]

O Service Fabric fornece um serviço de deteção e a resolução denominado o serviço de nomenclatura. O serviço de nomenclatura mantém uma tabela que mapeia com o nome de instâncias de serviço para os endereços de ponto final que escutarem. Todas as instâncias de serviço com nome no Service Fabric têm nomes exclusivos representados como os URIs, por exemplo, `"fabric:/MyApplication/MyService"`. O nome do serviço não alteram ao longo da duração do serviço, é apenas os endereços de ponto final que podem ser alteradas quando mover de serviços. Isto é semelhante a Web sites que tenham URLs constantes mas onde pode alterar o endereço IP. E tem de Service Fabric semelhante ao DNS na web, que é resolvido URLs de Web site para endereços IP, uma entidade de registo que mapeia nomes de serviço para o respetivo endereço de ponto final.

![pontos finais de serviço][2]

Resolver e ligar aos serviços envolvem os seguintes passos executados em ciclo:

* **Resolver**: obter o ponto final que publicou um serviço do serviço de nomenclatura.
* **Ligar**: ligar ao serviço de ativação pós-falha independentemente protocolo utiliza esse ponto final.
* **Repita**: uma tentativa de ligação poderá falhar por diversas razões, de exemplo, se o serviço foi movido desde a última vez o endereço de ponto final foi resolvido. Nesse caso, precedente resolver e ligue-se passos têm de ser repetida e este ciclo é repetido até que a ligação é bem-sucedida.

## <a name="connecting-to-other-services"></a>Ligar a outros serviços
Serviços ligar-se entre si no interior de um cluster, geralmente, podem aceder diretamente aos pontos finais de outros serviços porque os nós num cluster estão na mesma rede local. Para tornar mais fácil ligar entre os serviços, o Service Fabric fornece serviços adicionais que utilizam o serviço de nomenclatura. Um serviço DNS e um serviço de proxy inverso.


### <a name="dns-service"></a>Serviço DNS
Desde a vários serviços, especialmente de serviços, pode ter um nome de URL existente, que está a ser capaz de resolver estes utilizando o DNS padrão protocolo (em vez do protocolo serviço de nomenclatura) é muito conveniente, especialmente em cenários de aplicações "de comparação de precisão e deslocar". Isto é exatamente o que faz o serviço DNS. Permite-lhe mapear os nomes de DNS para um nome de serviço e, por conseguinte, resolva endereços de IP do ponto final. 

Como é mostrado no diagrama seguinte, o serviço DNS, em execução no cluster de Service Fabric, mapeia nomes DNS para nomes de serviço que são, em seguida, resolvidos pelo serviço de nomenclatura para devolver os endereços de ponto final para ligar a. O nome DNS para o serviço é fornecido no momento da criação. 

![pontos finais de serviço][9]

Para obter mais detalhes sobre como utilizar o DNS do serviço consulte [serviço DNS do Azure Service Fabric](service-fabric-dnsservice.md) artigo.

### <a name="reverse-proxy-service"></a>Serviço de proxy inverso
O proxy reverso endereços serviços em cluster que expõe os pontos finais HTTP, incluindo HTTPS. O proxy inverso significativamente simplifica a chamar outros serviços e os respetivos métodos por ter um formato URI específico e processa o resolver, ligar, repita os passos necessários para um serviço comunicar com outro utilizando o serviço de nomenclatura. Por outras palavras, oculta o serviço de nomenclatura do utilizador ao chamar outros serviços ao fazer isto tão simples como chamar um URL.

![pontos finais de serviço][10]

Para obter mais detalhes sobre como utilizar o serviço de proxy inverso consulte [proxy no Azure Service Fabric inverso](service-fabric-reverseproxy.md) artigo.

## <a name="connections-from-external-clients"></a>Ligações de clientes externos
Serviços ligar-se entre si no interior de um cluster, geralmente, podem aceder diretamente aos pontos finais de outros serviços porque os nós num cluster estão na mesma rede local. Em alguns ambientes, no entanto, um cluster pode estar atrás de um balanceador de carga que encaminha o tráfego de entrada externo através de um conjunto limitado de portas. Nestes casos, serviços ainda podem comunicar entre si e resolver endereços utilizando o serviço de nomenclatura, mas devem ser colocados passos adicionais para permitir aos clientes externos se ligam aos serviços.

## <a name="service-fabric-in-azure"></a>Recursos de infraestrutura de serviço no Azure
Um cluster do Service Fabric no Azure é colocado por trás de um balanceador de carga do Azure. Todo o tráfego externo para o cluster tem de passar pelo balanceador de carga. O Balanceador de carga automaticamente irá reencaminhar o tráfego de entrada numa porta especificada para um aleatório *nó* que tenha a mesma porta abrir. O Balanceador de carga do Azure apenas conhece portas abertas no *nós*, não conhecimento de abertura de portas por indivíduo *serviços*.

![Topologia de Balanceador de carga e do Service Fabric do Azure][3]

Por exemplo, para aceitar o tráfego externo na porta **80**, tem de ser configurados os seguintes procedimentos:

1. Escreva um serviço de escuta na porta 80. Configurar a porta 80 no ServiceManifest.xml do serviço e abra um serviço de escuta no serviço, por exemplo, um servidor web Self-alojada.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Criar um Cluster do Service Fabric no Azure e especifique a porta **80** como uma porta de ponto final personalizado para o tipo de nó que irá alojar o serviço. Se tiver mais do que um tipo de nó, pode configurar um *restrição de posicionamento* no serviço para se certificar de que é executado apenas no tipo de nó que tenha a porta do ponto final personalizado aberta.

    ![Abrir uma porta no tipo de nó][4]
3. Depois do cluster ter sido criado, configure o Balanceador de carga do Azure no grupo de recursos do cluster para encaminhar o tráfego na porta 80. Quando criar um cluster através do portal do Azure, este é configurado automaticamente para cada porta do ponto final personalizada que foi configurada.

    ![Encaminhar o tráfego no balanceador de carga do Azure][5]
4. O Balanceador de carga do Azure utiliza uma sonda para determinar se deve ou não enviar o tráfego para um determinado nó. A sonda verifica periodicamente um ponto final em cada nó para determinar se o nó está a responder. Se a pesquisa não recebeu uma resposta após um número de vezes configurado, o Balanceador de carga para o envio de tráfego para esse nó. Quando criar um cluster através do portal do Azure, uma sonda é configurada automaticamente para cada porta do ponto final personalizada que foi configurada.

    ![Encaminhar o tráfego no balanceador de carga do Azure][8]

É importante lembrar-se de que o Balanceador de carga do Azure e a sonda apenas conhecer o *nós*, não o *serviços* em execução em nós. O Balanceador de carga do Azure irá sempre enviar o tráfego a nós que respondem a pesquisa, pelo que deve ter cuidado para garantir que os serviços estão disponíveis em nós que são capazes de responder para a sonda.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: Opções de API de comunicação incorporada
A estrutura de Reliable Services é fornecido com várias opções de comunicação pré-criadas. A decisão sobre quais um funcionarão melhor para si depende a escolha do modelo de programação, a arquitetura de comunicação e que os serviços são escritos em linguagem de programação.

* **Nenhum protocolo específico:** se não tiver uma escolha específica de estrutura de comunicação, mas pretende obter algo cópias de segurança e executar rapidamente, em seguida, é a opção ideal para si [comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md), que permite que o tipo seguro de procedimento remoto chamadas para Reliable Services e Reliable Actors. Esta é a forma mais fácil e mais rápida para começar com comunicação de serviço. Comunicação remota do serviço processa a resolução de endereços de serviço, ligação, tente novamente e processamento de erros. Trata-se disponível para c# e aplicações de Java.
* **HTTP**: para comunicação de linguagem, HTTP fornece uma opção de norma da indústria com servidores HTTP disponíveis em vários idiomas diferentes, todos suportados pelo serviço de recursos de infraestrutura e ferramentas. Os serviços podem utilizar qualquer pilha HTTP disponível, incluindo [API Web do ASP.NET](service-fabric-reliable-services-communication-webapi.md) para aplicações do c#. Podem tirar partido dos clientes escritos em c# do `ICommunicationClient` e `ServicePartitionClient` classes, enquanto que para Java, utilize o `CommunicationClient` e `FabricServicePartitionClient` classes, [para resolução de serviço, as ligações HTTP e ciclos de repetição](service-fabric-reliable-services-communication.md).
* **WCF**: Se tiver o código existente que utiliza o WCF como o framework de comunicação, em seguida, pode utilizar o `WcfCommunicationListener` no lado do servidor e `WcfCommunicationClient` e `ServicePartitionClient` classes para o cliente. No entanto é apenas disponível para aplicações c# em clusters de baseado no Windows. Para obter mais detalhes, consulte este artigo [implementação baseada no WCF da pilha de comunicação](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Utilizar protocolos personalizados e outras estruturas de comunicação
Os serviços podem utilizar qualquer protocolo ou arquitetura de comunicação, se se trata de um protocolo personalizado de binário através de TCP sockets ou eventos de transmissão em fluxo através de [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/) ou [IoT Hub do Azure](https://azure.microsoft.com/services/iot-hub/). O Service Fabric fornece comunicação APIs que pode fixação a pilha de comunicações para, enquanto todo o trabalho para detetar e ligar é abstracted do utilizador. Consulte este artigo o [modelo de comunicação de serviço fiável](service-fabric-reliable-services-communication.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os conceitos e as APIs disponíveis no [modelo de comunicação Reliable Services](service-fabric-reliable-services-communication.md), em seguida, comece a trabalhar rapidamente com [comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md) ou vá aprofundada para saber mais sobre como escrever um serviço de escuta de comunicação com [Web API com OWIN Self-alojar](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
