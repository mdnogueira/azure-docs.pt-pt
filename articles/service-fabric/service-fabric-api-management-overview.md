---
title: "Azure Service Fabric com descrição geral da gestão de API | Microsoft Docs"
description: "Este artigo é uma introdução à utilização de API Management do Azure como um gateway para as aplicações de Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: ea3b1f50bada3c1301f8661f8f0b4866cb1c732c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Recursos de infraestrutura de serviço com descrição geral da API Management do Azure

Aplicações em nuvem, normalmente, precisam de um gateway de front-end para fornecer um único ponto de entrada para os utilizadores, dispositivos ou outras aplicações. No Service Fabric, um gateway pode ser qualquer serviço sem monitorização de estado, tal como um [aplicação ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço concebida para a entrada de tráfego, tais como [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/), ou [API Management do Azure](https://docs.microsoft.com/azure/api-management/).

Este artigo é uma introdução à utilização de API Management do Azure como um gateway para as aplicações de Service Fabric. Gestão de API integra-se diretamente com o Service Fabric, permitindo-lhe publicar APIs com um vasto conjunto de regras de encaminhamento para os serviços de recursos de infraestrutura do serviço de back-end. 

## <a name="architecture"></a>Arquitetura
Uma arquitetura comuns do Service Fabric utiliza uma aplicação web de página única que faz com que chamadas HTTP para os serviços de back-end que expõem as APIs de HTTP. O [introdução ao exemplo de aplicação de Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) mostra um exemplo desta arquitetura.

Neste cenário, um serviço web sem monitorização de estado serve como o gateway para a aplicação de Service Fabric. Esta abordagem requer a escrever um serviço web que pode pedidos de HTTP de proxy para serviços de back-end, conforme mostrado no diagrama seguinte:

![Recursos de infraestrutura de serviço com descrição geral da topologia de API Management do Azure][sf-web-app-stateless-gateway]

À medida que ficam aplicações na complexidade, por isso, efetue os gateways que tem de apresentar uma API à frente de serviços do conjunto back-end. Gestão de API do Azure foi concebido para processar APIs complexas com regras de encaminhamento, controlo de acesso, limitação de taxa, monitorização, registo de eventos e colocação em cache de resposta com o mínimo de trabalho da sua parte. Gestão de API do Azure suporta a deteção de serviço do Service Fabric, resolução de partição e seleção de réplica inteligentemente encaminhar pedidos diretamente aos serviços de back-end no Service Fabric para que não tenha de escrever o seu próprio gateway da API sem monitorização de estado. 

Neste cenário, o web IU ainda servida através de um serviço web, enquanto chamadas da API de HTTP são geridas e encaminhadas através de API Management do Azure, conforme mostrado no diagrama seguinte:

![Recursos de infraestrutura de serviço com descrição geral da topologia de API Management do Azure][sf-apim-web-app]

## <a name="application-scenarios"></a>Cenários de aplicações

Serviços de no Service Fabric podem estar sem monitorização de estado ou com monitorização de estado e podem ser particionados utilizando um dos três esquemas: singleton, intervalo de int 64 e sem nome. Resolução de ponto final de serviço requer que identifica uma partição específica de uma instância de serviço específicos. Ao resolver um ponto final de um serviço, tanto o nome de instância de serviço (por exemplo, `fabric:/myapp/myservice`), bem como a partição específica do serviço tem de ser especificada, exceto no caso da partição singleton.

Gestão de API do Azure pode ser utilizado com qualquer combinação de serviços sem monitorização de estado, os serviços com monitorização de estado e qualquer esquema de partições.

## <a name="send-traffic-to-a-stateless-service"></a>Enviar o tráfego para um serviço sem monitorização de estado

No caso mais simples, o tráfego seja reencaminhado para uma instância de serviço sem estado. Para atingir esse objetivo, uma operação de gestão de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia para uma instância de serviço sem monitorização de estado específico no Service Fabric back-end. Pedidos enviados para esse serviço são enviados para uma réplica aleatória de instância de serviço sem estado.

#### <a name="example"></a>Exemplo
No cenário seguinte, uma aplicação de Service Fabric contém um nome de serviço sem monitorização de estado `fabric:/app/fooservice`, que expõe uma API de HTTP interno. O nome de instância de serviço é bem conhecido e pode ser codificados diretamente na política de processamento de entrada de API Management. 

![Recursos de infraestrutura de serviço com descrição geral da topologia de API Management do Azure][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Enviar o tráfego para um serviço com estado

Semelhante para o cenário de serviço sem monitorização de estado, o tráfego pode ser reencaminhado para uma instância de serviço com monitorização de estado. Neste caso, uma operação de gestão de API contém uma política de processamento de entrada com um back-end de Service Fabric que um pedido é mapeado para uma partição específica de um específico *com monitorização de estado* instância de serviço. A partição de cada pedido de é calculada através de um método de lambda com algumas entradas do pedido de HTTP recebido, tais como um valor no caminho do URL do mapa. A política pode ser configurada para enviar pedidos para a réplica primária apenas, ou para uma réplica aleatória para operações de leitura.

#### <a name="example"></a>Exemplo

No cenário seguinte, uma aplicação de Service Fabric contém um serviço com estado particionado com o nome `fabric:/app/userservice` que expõe uma API de HTTP interno. O nome de instância de serviço é bem conhecido e pode ser codificados diretamente na política de processamento de entrada de API Management.  

O serviço está particionado com o esquema de partição Int64 duas partições e um intervalo de chaves que abranja `Int64.MinValue` para `Int64.MaxValue`. A política de back-end calcula uma chave de partição dentro desse intervalo convertendo a `id` valor fornecido no caminho de pedido de URL para um número inteiro de 64 bits, embora qualquer algoritmo pode ser aqui utilizado para calcular a chave de partição. 

![Recursos de infraestrutura de serviço com descrição geral da topologia de API Management do Azure][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Enviar o tráfego para vários serviços sem monitorização de estado

Em cenários mais avançados, pode definir uma operação de gestão de API que mapeia pedidos a mais do que uma instância de serviço. Neste caso, cada operação contém uma política que mapeia pedidos para uma instância de serviço específicos com base nos valores do pedido de HTTP recebido, tais como a cadeia de caminho ou a consulta de URL e, no caso de serviços com monitorização de estado, uma partição dentro de instância de serviço. 

Para atingir esse objetivo, uma operação de gestão de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia para uma instância de serviço sem monitorização de estado no back-end de Service Fabric com base nos valores obtidos a partir do pedido HTTP a receber. Pedidos para uma instância de serviço são enviados para uma réplica aleatória da instância do serviço.

#### <a name="example"></a>Exemplo

Neste exemplo, é criada uma nova instância de serviço sem monitorização de estado para cada utilizador de uma aplicação com um nome gerado dinamicamente utilizando a fórmula seguinte:
 
 - `fabric:/app/users/<username>`

 Cada serviço tem um nome exclusivo, mas os nomes não são conhecidos prévio porque os serviços são criados em resposta ao utilizador ou administrador de entrada e, por conseguinte, não pode ser hard-coded para políticas APIM ou as regras de encaminhamento. Em vez disso, o nome do serviço ao qual pretende enviar um pedido é gerado na definição de política de back-end do `name` valor fornecido no caminho de pedido de URL. Por exemplo:

  - Um pedido para `/api/users/foo` é encaminhado para a instância de serviço`fabric:/app/users/foo`
  - Um pedido para `/api/users/bar` é encaminhado para a instância de serviço`fabric:/app/users/bar`

![Recursos de infraestrutura de serviço com descrição geral da topologia de API Management do Azure][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Enviar o tráfego para vários serviços com monitorização de estado

Semelhante ao exemplo de serviço sem monitorização de estado, uma operação de gestão de API pode mapear pedidos de mais do que um **com monitorização de estado** instância de serviço, nesse caso, também poderá ter de efetuar a resolução de partição para cada instância de serviço com monitorização de estado.

Para atingir esse objetivo, uma operação de gestão de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia para uma instância de serviço com estado no back-end de Service Fabric com base nos valores obtidos a partir do pedido HTTP a receber. Para além de mapeamento de um pedido para a instância de serviço específica, o pedido também pode ser mapeado para uma partição específica na instância de serviço e, opcionalmente, a réplica primária ou uma réplica secundária aleatória na partição.

#### <a name="example"></a>Exemplo

Neste exemplo, é criada uma nova instância de serviço com estado para cada utilizador da aplicação com um nome gerado dinamicamente utilizando a fórmula seguinte:
 
 - `fabric:/app/users/<username>`

 Cada serviço tem um nome exclusivo, mas os nomes não são conhecidos prévio porque os serviços são criados em resposta ao utilizador ou administrador de entrada e, por conseguinte, não pode ser hard-coded para políticas APIM ou as regras de encaminhamento. Em vez disso, o nome do serviço ao qual pretende enviar um pedido é gerado na definição de política de back-end do `name` valor indicado o caminho de pedido de URL. Por exemplo:

  - Um pedido para `/api/users/foo` é encaminhado para a instância de serviço`fabric:/app/users/foo`
  - Um pedido para `/api/users/bar` é encaminhado para a instância de serviço`fabric:/app/users/bar`

Cada instância de serviço também está particionada com o esquema de partição Int64 duas partições e um intervalo de chaves que abranja `Int64.MinValue` para `Int64.MaxValue`. A política de back-end calcula uma chave de partição dentro desse intervalo convertendo a `id` valor fornecido no caminho de pedido de URL para um número inteiro de 64 bits, embora qualquer algoritmo pode ser aqui utilizado para calcular a chave de partição. 

![Recursos de infraestrutura de serviço com descrição geral da topologia de API Management do Azure][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Passos seguintes

Siga o [tutorial](service-fabric-tutorial-deploy-api-management.md) para configurar o primeiro cluster de Service Fabric com pedidos de API Management e o fluxo através da gestão de API para os serviços.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png