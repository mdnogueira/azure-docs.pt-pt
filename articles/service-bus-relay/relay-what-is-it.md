---
title: "Descrição geral sobre o que é o Reencaminhamento do Azure e porquê utilizá-lo | Microsoft Docs"
description: "Descrição Geral do Reencaminhamento do Azure"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 06/14/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 74a0837b7149236faa45ef2cda0a0aee7c46a31b
ms.contentlocale: pt-pt
ms.lasthandoff: 06/16/2017


---
# <a name="what-is-azure-relay"></a>O que é o Reencaminhamento do Azure?

O serviço de Reencaminhamento do Azure facilita as aplicações híbridas, permitindo-lhe expor na cloud pública, de forma segura, os serviços que se encontram numa rede empresarial, sem ter de abrir uma ligação de firewall ou sem ter de fazer alterações intrusivas na infraestrutura da rede empresarial. O Reencaminhamento suporta uma variedade de diferentes protocolos de transporte e de padrões de serviços Web.

O serviço de Reencaminhamento suporta o tradicional tráfego unidirecional, pedido/resposta e ponto a ponto. Também suporta a distribuição de eventos no âmbito de Internet para permitir cenários de publicação-subscrição e comunicação de socket bidirecional para aumentar a eficiência ponto a ponto. 

No padrão de transferências de dados reencaminhadas, um serviço no local liga-se ao serviço de reencaminhamento através de uma porta de saída e cria um socket bidirecional para comunicação associada a um endereço de encontro específico. Depois, o cliente pode comunicar com o serviço no local ao enviar tráfego para o serviço de reencaminhamento destinado ao endereço de encontro. O serviço de reencaminhamento "reencaminha", então, os dados para o serviço no local através de um socket bidirecional dedicado a cada cliente. O cliente não precisa de uma ligação direta para o serviço local, nem precisa de saber onde reside o serviço e o serviço local não precisa de nenhuma porta de entrada aberta na firewall.

Os elementos de capacidades-chave disponibilizados pelo Reencaminhamento são comunicações bidirecionais não registadas em limites de redes com limitação do tipo TCP, deteção de pontos finais, estado da conectividade e segurança de pontos finais sobrepostos. As capacidades do reencaminhamento são diferentes das tecnologias de integração ao nível da rede, como as VPNs, no sentido de que o reencaminhamento pode visar um único ponto final de aplicação num computador individual, ao passo que a tecnologia de VPN é bastante mais intrusiva, pois baseia-se na alteração do ambiente de rede.

O Reencaminhamento do Azure tem duas funcionalidades:

1. [Ligações Híbridas](#hybrid-connections) - utiliza web sockets de padrão aberto para permitir cenários de multiplataformas.
2. [Reencaminhamentos do WCF](#wcf-relays) - utiliza o Windows Communication Foundation (WCF) para ativar chamadas de procedimento remoto. O Reencaminhamento do WCF é a oferta de Reencaminhamento legada e que muitos utilizadores já utilizam nos respetivos modelos de programação do WCF.

Tanto as Ligações Híbridas, como os Reencaminhamentos do WCF, permitem ligações seguras a recursos que existem dentro de uma rede empresarial. A utilização de uma funcionalidade em detrimento da outra depende das suas necessidades específicas, conforme descrito na tabela seguinte:

|  | Reencaminhamento do WCF | Ligações Híbridas |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/NodeJS** | |x |
| **Standards-Based Open Protocol (Protocolo Aberto Baseado em Normas)** | |x |
| **Vários Modelos de Programação de RPC** | |x |

## <a name="hybrid-connections"></a>Ligações Híbridas

A capacidade de [Ligações Híbridas do Reencaminhamento do Azure](relay-hybrid-connections-protocol.md) é uma evolução segura e de protocolo aberto das funcionalidades atuais do Reencaminhamento, que pode ser implementada em qualquer plataforma e em qualquer idioma que tenha capacidade básica para WebSocket, que inclua explicitamente a API WebSocket em browsers comuns. As Ligações Híbridas baseiam-se em HTTP e WebSockets.

## <a name="wcf-relays"></a>Reencaminhamentos do WCF

O Reencaminhamento do WCF funciona para o .NET Framework (NETFX) completo e para o WCF. A ligação entre o serviço no local e o serviço de reencaminhamento é iniciada através de um conjunto de enlaces de "reencaminhamento" do WCF. Em segundo plano, os enlaces de reencaminhamento mapeiam para novos elementos de enlace de transporte concebidos para criar os componentes de canal WCF que se integram ao Service Bus na nuvem.

## <a name="service-history"></a>Histórico do Serviço

As Ligações Híbridas superam a funcionalidade anterior, igualmente chamada “Serviços BizTalk”, que foi criada com base no Reencaminhamento do WCF do Azure Service Bus. A nova capacidade de Ligações Híbridas complementa a funcionalidade de Reencaminhamento do WCF existente e estas duas capacidades do serviço coexistem lado a lado no serviço de Reencaminhamento do Azure num futuro próximo. Partilham um gateway comum, mas, de resto, são implementações diferentes.

## <a name="next-steps"></a>Passos seguintes:

* [FAQ de Reencaminhamento](relay-faq.md)
* [Criar um espaço de nomes](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)


