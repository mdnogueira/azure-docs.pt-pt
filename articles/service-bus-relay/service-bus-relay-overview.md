<properties
    pageTitle="Descrição geral do reencaminhamento do Service Bus | Microsoft Azure"
    description="Descrição geral do reencaminhamento do Service Bus."
    services="service-bus-relay"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-relay"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>



# Descrição geral do reencaminhamento do Service Bus

O componente principal do Service Bus é um serviço centralizado de *reencaminhamento* (mas com uma carga muito equilibrada) que lhe permite criar aplicações híbridas que se executam tanto num datacenter do Azure como em ambiente empresariais no local.  O reencaminhamento do Service Bus suporta uma variedade de diferentes protocolos de transporte e padrões de serviços web. Inclui SOAP, WS-* e até REST. O serviço de reencaminhamento facilita as aplicações híbridas, permitindo expor, de forma segura, os serviços Windows Communication Foundation (WCF) que se encontram numa rede empresarial na nuvem pública, sem ter de abrir uma ligação de firewall ou sem ter de fazer alterações intrusivas na infraestrutura da rede empresarial. 

![Conceitos de Reencaminhamento](./media/service-bus-relay-overview/sb-relay-01.png)

O serviço de reencaminhamento suporta as tradicionais mensagens unidirecionais, mensagens de pedido/resposta e mensagens de ponto a ponto. Também suporta a distribuição de eventos no âmbito de Internet para permitir cenários de publicação-subscrição e comunicação de socket bidirecional para aumentar a eficiência ponto a ponto. 

No padrão de mensagens retransmitidas, um serviço no local liga-se ao serviço de reencaminhamento através de uma porta de saída e cria um socket bidirecional para comunicação associada a um endereço de encontro específico. Depois, o cliente pode comunicar com o serviço no local enviando mensagens para o serviço de reencaminhamento destinadas ao endereço de encontro. O serviço de reencaminhamento "reencaminhará" as mensagens para o serviço no local através do socket bidirecional existente. O cliente não precisa de uma ligação direta para o serviço local, nem precisa de saber onde reside o serviço e o serviço local não precisa de nenhuma porta de entrada aberta na firewall.

Deve iniciar a ligação entre o serviço local e o serviço de reencaminhamento, utilizando um conjunto de enlaces de "reencaminhamento" WCF. Em segundo plano, os enlaces de reencaminhamento mapeiam para novos elementos de enlace de transporte concebidos para criar os componentes de canal WCF que se integram ao Service Bus na nuvem. 

## Passos seguintes

Para obter detalhes sobre o reencaminhamento do Service Bus, consulte os seguintes tópicos.

- [Descrição Geral da Arquitetura do Service Bus do Azure](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [Como utilizar o serviço de Reencaminhamento do Service Bus](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)

 


<!--HONumber=Sep16_HO4-->


