---
title: "Descrição geral de mensagens do Service Bus do Azure | Microsoft Docs"
description: "Descrição das mensagens do Service Bus e Reencaminhamento do Azure"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 08/30/2017
ms.author: sethm
ms.openlocfilehash: 462fa0a6e86237b8c9d9ba97aa377f8d112a8b8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Mensagens do Service Bus: entrega flexível de dados na nuvem

O Microsoft Azure Service Bus é um serviço fiável de entrega de informações. O objetivo deste serviço é facilitar a comunicação. Quando duas ou mais entidades querem trocar informações, precisam de um facilitador de comunicação. O Service Bus é um mecanismo de comunicação mediado ou de terceiros. É algo semelhante a um serviço postal de entrega de cartas no mundo físico. Os serviços postais facilitam o envio de diferentes tipos de cartas e pacotes, com uma variedade de garantias de entrega, em qualquer lugar do mundo.

O Service Bus é semelhante ao serviço postal de entrega de cartas, pois trata da entrega flexível de informações entre o remetente e o destinatário. O serviço de mensagens assegura que a informação é entregue, mesmo se as duas partes nunca estão online ao mesmo tempo ou se não estão disponíveis no mesmo momento exato. Desta forma, enviar mensagens é semelhante a enviar uma carta, enquanto a comunicação não mediada é semelhante a fazer uma chamada telefónica (ou como uma chamada telefónica costumava ser - antes da criação de serviços de identificação de chamada ou chamada de espera, que são muito mais semelhantes a mensagens mediadas).

O remetente da mensagem também pode exigir uma variedade de características de entrega incluindo transações, deteção de duplicados, expiração com base no tempo e processamento de lotes. Estes padrões também têm analogias postais: repetição de entrega, assinatura necessária, alteração de endereço ou devolução de chamada.

O Service Bus suporta dois padrões de mensagens distintos: *Reencaminhamento do Azure* e *Mensagens do Service Bus*.

## <a name="azure-relay"></a>Reencaminhamento do Azure

O componente [Reencaminhamento de WCF](../service-bus-relay/relay-what-is-it.md) do Reencaminhamento do Azure é um serviço centralizado (mas com grande balanceamento de carga) que suporta uma variedade de protocolos diferentes de transporte e normas de serviços Web. Inclui SOAP, WS-* e até REST. O [serviço de reencaminhamento](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) proporciona uma variedade de opções de conectividade de reencaminhamento diferentes e podem ajudá-lo na negociação de ligações diretas ponto a ponto sempre que for possível. O Service Bus foi otimizado para os programadores do .NET que utilizam o Windows Communication Foundation (WCF), tanto em termos de desempenho com em facilidade de utilização, e fornece acesso total ao respetivo serviço de reencaminhamento através de interfaces SOAP e REST. Isto permite que qualquer ambiente de programação SOAP ou REST se integre com o Service Bus.

O serviço de reencaminhamento suporta as tradicionais mensagens unidirecionais, mensagens de pedido/resposta e mensagens de ponto a ponto. Também suporta a distribuição de eventos no âmbito de Internet para permitir cenários de publicação-subscrição e comunicação de socket bidirecional para aumentar a eficiência ponto a ponto. No padrão de mensagens retransmitidas, um serviço no local liga-se ao serviço de reencaminhamento através de uma porta de saída e cria um socket bidirecional para comunicação associada a um endereço de encontro específico. Depois, o cliente pode comunicar com o serviço no local enviando mensagens para o serviço de reencaminhamento destinadas ao endereço de encontro. O serviço de reencaminhamento "reencaminhará" as mensagens para o serviço no local através do socket bidirecional existente. O cliente não precisa de uma ligação direta para o serviço no local, nem precisa de saber onde reside o serviço e o serviço no local não precisa de nenhuma porta de entrada aberta na firewall.

Deve iniciar a ligação entre o serviço no local e o serviço de reencaminhamento através de um conjunto de enlaces de "reencaminhamento" do WCF. Em segundo plano, os enlaces de reencaminhamento mapeiam para elementos de enlace de transporte concebidos para criar os componentes de canal WCF que se integram ao Service Bus na nuvem.

O Reencaminhamento do WCF tem muitas vantagens, mas requer que, tanto o servidor, como o cliente, estejam online ao mesmo tempo para enviar e receber mensagens. Esta não é situação ideal para a comunicação de estilo HTTP, na qual os pedidos normalmente não podem ser de longa duração, nem para os clientes que só se ligam ocasionalmente, tais como navegadores, aplicações móveis, etc. As mensagens mediadas suportam comunicação desacoplada e tem as suas próprias vantagens; os clientes e servidores podem ligar-se quando seja necessário e efetuar as operações de forma assíncrona.

## <a name="brokered-messaging"></a>Mensagens mediadas

Ao contrário do esquema do reencaminhamento, mensagens do Service Bus com [filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md) podem considerar-se como assíncronas ou “temporariamente dissociadas”. Os produtores (remetentes) e os consumidores (recetores) não têm de estar online ao mesmo tempo. A infraestrutura de mensagens armazena mensagens, de modo fiável, no "mediador" (por exemplo, numa fila) até que a parte consumidora esteja preparada para recebê-las. Isto permite que os componentes da aplicação distribuída estejam desconectados, quer voluntariamente, por exemplo, para manutenção, quer devido a uma falha de componente, sem afetar todo o sistema. Além disso, é possível que a aplicação recetora só tenha de ficar online durante determinadas horas do dia, por exemplo, um sistema de gestão de stocks que só é necessário executar no fim do dia.

Os componentes principais da infraestrutura de mensagens do Service Bus são filas, tópicos e subscrições. A principal diferença é que os tópicos suportam funcionalidades de publicação/subscrição que podem ser utilizadas para o encaminhamento com base em conteúdo sofisticado e lógica de entrega, incluindo o envio para vários destinatários. Estes componentes permitem novos cenários de mensagens assíncronas, tais como desacoplamento temporal, publicação/subscrição e balanceamento de carga. Para mais informações sobre estas entidades de mensagens, consulte o artigo [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md).

Tal como acontece com a infraestrutura do Reencaminhamento de WCF, a capacidade de mensagens mediadas é fornecida para os programadores de WCF e de .NET Framework, bem como através de REST.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

