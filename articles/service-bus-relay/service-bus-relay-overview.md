---
title: "Descrição geral do reencaminhamento do Service Bus | Microsoft Docs"
description: "Descrição geral do reencaminhamento do Service Bus."
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1038a2d8-5def-4f48-8703-cb0070fc5f10
ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0482096cbec6a5e4b7b13ea662a180cd9b96e85f


---
# <a name="overview-of-service-bus-relay"></a>Descrição geral do reencaminhamento do Service Bus
O componente principal do Service Bus é um serviço centralizado de *reencaminhamento* (mas com uma carga muito equilibrada) que lhe permite criar aplicações híbridas que se executam tanto num datacenter do Azure como em ambiente empresariais no local.  O reencaminhamento do Service Bus suporta uma variedade de diferentes protocolos de transporte e padrões de serviços web. Inclui SOAP, WS-* e até REST. O serviço de reencaminhamento facilita as aplicações híbridas, permitindo expor, de forma segura, os serviços Windows Communication Foundation (WCF) que se encontram numa rede empresarial na nuvem pública, sem ter de abrir uma ligação de firewall ou sem ter de fazer alterações intrusivas na infraestrutura da rede empresarial. 

![Conceitos de Reencaminhamento WCF](./media/service-bus-relay-overview/sb-relay-01.png)

O serviço de reencaminhamento suporta as tradicionais mensagens unidirecionais, mensagens de pedido/resposta e mensagens de ponto a ponto. Também suporta a distribuição de eventos no âmbito de Internet para permitir cenários de publicação-subscrição e comunicação de socket bidirecional para aumentar a eficiência ponto a ponto. 

No padrão de mensagens retransmitidas, um serviço no local liga-se ao serviço de reencaminhamento através de uma porta de saída e cria um socket bidirecional para comunicação associada a um endereço de encontro específico. Depois, o cliente pode comunicar com o serviço no local enviando mensagens para o serviço de reencaminhamento destinadas ao endereço de encontro. O serviço de reencaminhamento "reencaminhará" as mensagens para o serviço no local através do socket bidirecional existente. O cliente não precisa de uma ligação direta para o serviço local, nem precisa de saber onde reside o serviço e o serviço local não precisa de nenhuma porta de entrada aberta na firewall.

Deve iniciar a ligação entre o serviço local e o serviço de reencaminhamento, utilizando um conjunto de enlaces de "reencaminhamento" WCF. Em segundo plano, os enlaces de reencaminhamento mapeiam para novos elementos de enlace de transporte concebidos para criar os componentes de canal WCF que se integram ao Service Bus na nuvem. 

## <a name="next-steps"></a>Passos seguintes
Para obter detalhes sobre o reencaminhamento do Service Bus, consulte os seguintes tópicos.

* [Descrição Geral da Arquitetura do Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Como utilizar o serviço de Reencaminhamento WCF do Service Bus](service-bus-dotnet-how-to-use-relay.md)




<!--HONumber=Nov16_HO2-->


