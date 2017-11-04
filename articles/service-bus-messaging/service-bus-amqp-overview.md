---
title: "Descrição geral de AMQP 1.0 no Service Bus do Azure | Microsoft Docs"
description: "Saiba como utilizar o Advanced mensagem colocação protocolo (AMQP) 1.0 no Azure."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: e2d190dddc3b84eb07eaf2ffea1af48bba0ea13a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="amqp-10-support-in-service-bus"></a>Suporte de AMQP 1.0 no Service Bus
O serviço de nuvem de Service Bus do Azure e no local [Service Bus para o Windows Server (1.1 de barramento de serviço)](https://msdn.microsoft.com/library/dn282144.aspx) suportar o Advanced mensagem de colocação de protocolo (AMQP) 1.0. AMQP permite-lhe criar várias plataformas, aplicações híbridas com um protocolo padrão aberto. Pode criar aplicações com os componentes que são criadas com linguagens diferentes e arquiteturas, e que são executadas em sistemas operativos diferentes. Todos estes componentes podem ligar ao Service Bus e totalmente integrada trocam mensagens empresariais estruturados de forma eficiente e a fidelidade completa.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introdução: Qual é AMQP 1.0 e por que motivo é importante?
Tradicionalmente, o middleware orientado para a mensagem de produtos utilizou protocolos proprietários para comunicação entre as aplicações de cliente e os corretores. Isto significa que depois de selecionar Mediador de mensagens de um fornecedor específico, tem de utilizar bibliotecas do fornecedor para ligar as aplicações de cliente que mediador. Isto resulta num grau de dependência fornecedor, uma vez que as transferências de uma aplicação para outro produto necessita de alterações de código em todas as aplicações ligadas. 

Além disso, a ligação de mediadores de mensagens de fornecedores diferentes é tricky. Isto requer, normalmente, ao nível da aplicação bridging movam mensagens a partir de um sistema para outro e a conversão entre os formatos de mensagem proprietárias. Este é um requisito comuns; Por exemplo, quando deve fornecer uma nova interface unificada anteriores a sistemas diferentes, ou integrar os sistemas de TI seguir uma fusão.

Indústria do software é uma empresa de mover o fast; linguagens de programação novas e arquiteturas de aplicações são introduzidas a um ritmo bewildering por vezes. Da mesma forma, os requisitos de sistemas de TI evoluem ao longo do tempo e os programadores pretendem tirar partido das funcionalidades mais recentes de plataforma. No entanto, por vezes, o fornecedor do serviço de mensagens selecionado não suporta estas plataformas. Porque os protocolos de mensagens são proprietários, não é possível para que outros a fornecer bibliotecas para estas plataformas de novo. Por conseguinte, tem de utilizar abordagens, tais como a criação de gateways ou pontes permitem-lhe continuar a utilizar o produto de mensagens.

O desenvolvimento do Advanced mensagem colocação protocolo (AMQP) 1.0 foi motivado por estes problemas. Teve origem no JP Morgan Chase, que, como firms de serviços financeiros mais, são utilizadores pesados middleware orientado para a mensagem. O objetivo foi simple: para criar um protocolo de mensagens de norma open disponibilizada criar aplicações baseada em mensagens através de componentes construido utilizando idiomas diferentes, estruturas e sistemas operativos, utilizando todos os componentes de melhor de breed de um intervalo de fornecedores.

## <a name="amqp-10-technical-features"></a>Funcionalidades de técnicas de AMQP 1.0
AMQP 1.0 é um protocolo de mensagens económicos, fiável e nível de rede que pode utilizar para criar robusta, entre plataformas, as aplicações de mensagens. O protocolo tem um objetivo simple: Definir mechanics da transferência segura, fiável e eficiente de mensagens entre duas partes. As mensagens são codificadas através de uma representação de dados portátil que permite que os remetentes heterogéneos e os recetores trocar mensagens de negócio estruturados com a fidelidade completa. Segue-se um resumo das funcionalidades mais importantes:

* **Eficiente**: AMQP 1.0 é uma orientado para ligação de protocolo que utiliza uma codificação binária para as instruções de protocolo e as mensagens de negócio transferida sobre ele. Incorpora esquemas de controlo de fluxo sofisticadas para maximizar a utilização de rede e os componentes ligados. Dito isto, o protocolo foi concebido para trazem um equilíbrio entre a eficiência, flexibilidade e interoperabilidade.
* **Fiável**: AMQP 1.0 o protocolo permite que as mensagens trocadas com uma variedade de garantias de fiabilidade, de fire-e-se esqueça de fiável, exatamente-uma vez confirmadas entrega.
* **Flexível**: AMQP 1.0 é um protocolo flexível que pode ser utilizado para suportar diferentes topologias. O mesmo protocolo pode ser utilizado para comunicações de cliente para o cliente, o Mediador de cliente e o Mediador de Mediador.
* **Independentemente do modelo de Mediador**: especificação o AMQP 1.0 não fazer quaisquer requisitos no modelo de serviço de mensagens utilizado por um mediador. Isto significa que é possível adicionar facilmente AMQP 1.0 suporte a mediadores de mensagens existentes.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 é uma norma (com um capital')
AMQP 1.0 é uma norma internacional, aprovadas pela ISO e IEC como ISO/IEC 19464:2014.

AMQP 1.0 foi-se no desenvolvimento desde 2008 por um grupo de núcleos de mais de 20 empresas, os fornecedores de tecnologia e firms do utilizador final. Durante este período, firms de utilizador têm contribuído os respetivos requisitos comerciais reais e os fornecedores de tecnologia evoluíram o protocolo a satisfazer essas necessidades. Ao longo do processo, os fornecedores têm participaram na workshops na qual colaborado para validar a interoperabilidade entre as respetivas implementações.

Em Outubro de 2011, o trabalho de desenvolvimento transitado para um técnico AD dentro da organização para o Advancement do Structured informações normas (OASIS) e o padrão OASIS AMQP 1.0 foi lançado em Outubro de 2012. As seguintes firms participaram na técnica AD durante o desenvolvimento da padrão:

* **Os fornecedores de tecnologia**: Axway Software, Huawei tecnologias, IIT Software, INETCO sistemas, Kaazing, a Microsoft, Mitre Corporation Primeton tecnologias, progresso Software, Red Hat, SITA, Software AG, Solace sistemas, VMware, WSO2, Zenika.
* **Firms utilizador**: banco da América, Suisse de crédito, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Algumas das vantagens das normas de abertura normalmente cited incluem:

* Menor probabilidade de bloqueio-in do fornecedor
* Interoperabilidade
* Disponibilidade abrangente de bibliotecas e ferramentas
* Proteção contra obsolescence
* Disponibilidade da equipa de knowledgeable
* Risco inferior e gerível

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 e Service Bus
Suporte de AMQP 1.0 no Service Bus do Azure significa que pode tirar partido de colocação do Service Bus e publicar/subscrever funcionalidades de mensagens mediadas de uma variedade de plataformas utilizando um protocolo de binário eficiente. Além disso, pode criar aplicações compostas por componentes construido utilizando uma combinação de idiomas, estruturas e sistemas operativos.

A figura seguinte ilustra um exemplo de implementação em que os clientes de Java em execução no Linux, escrita utilizando o padrão Java mensagem de serviço (JMS) API e clientes de .NET em execução no Windows, trocam mensagens através do Service Bus através de AMQP 1.0.

![][0]

**Figura 1: Cenário de implementação de exemplo que mostra plataforma mensagens utilizando o Service Bus e AMQP 1.0**

Neste momento, as seguintes bibliotecas de cliente são conhecidas para trabalhar com o Service Bus:

| Idioma | Biblioteca |
| --- | --- |
| Java |Cliente do serviço de mensagens (JMS) do Apache Qpid Java<br/>Cliente IIT Software SwiftMQ Java |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .net Lite |

**Figura 2: Tabela das bibliotecas de cliente AMQP 1.0**

## <a name="summary"></a>Resumo
* AMQP 1.0 é um protocolo de mensagens fiável, abra que pode utilizar para criar várias plataformas, aplicações híbridas. AMQP 1.0 é uma norma OASIS.
* Suporte de AMQP 1.0 está agora disponível no Service Bus do Azure, bem como o Service Bus para o Windows Server (1.1 de barramento de serviço). Preços de é igual para os protocolos existentes.

## <a name="next-steps"></a>Passos seguintes
Está pronto para obter mais informações? Visite as seguintes ligações:

* [Utilização do Service Bus a partir do .NET com AMQP]
* [Utilização do Service Bus do Java com AMQP]
* [Instalar Apache Qpid Proton-C numa VM com Linux do Azure]
* [AMQP no barramento de serviço para o Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Utilização do Service Bus a partir do .NET com AMQP]: service-bus-amqp-dotnet.md
[Utilização do Service Bus do Java com AMQP]: service-bus-amqp-java.md
[Instalar Apache Qpid Proton-C numa VM com Linux do Azure]: service-bus-amqp-apache.md
[AMQP no barramento de serviço para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
