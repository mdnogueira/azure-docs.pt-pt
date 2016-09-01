<properties
    pageTitle="O que são os Event Hubs do Azure? | Microsoft Azure"
    description="Descrição geral e descrição dos Event Hubs do Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="sethm"/>

# O que são os Event Hubs do Azure?

Os Event Hubs do Azure são um serviço de entrada de dados altamente dimensionável, que pode ingerir milhões de eventos por segundo para que possa processar e analisar os quantidades enormes de dados produzidos pelos dispositivos e aplicações ligados. Os Event Hubs atuam como a “porta de entrada” para um pipeline de eventos. E, quando os dados são recolhidos para um Hub de Eventos, podem ser transformados e armazenados através de qualquer fornecedor de análise em tempo real ou adaptadores de criação de batches/armazenamento. Os Event Hubs desacoplam a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam aceder aos eventos de acordo com seu próprio agendamento. Para obter mais informações e detalhes técnicos, veja [Descrição geral dos Event Hubs](event-hubs-overview.md).

## Capacidades dos Event Hubs

Os Event Hubs são um serviço de processamento de eventos que permitem o processamento em grande escala de telemetria e eventos, com baixa latência e alta fiabilidade. Este serviço é especialmente útil para:

- Instrumentação de aplicações
- Experiência de utilizador ou de processamento de fluxo de trabalho
- Cenários Internet das Coisas (IoT)

Mais algumas capacidades chave dos Event Hubs incluem o controlo do comportamento em aplicações móveis, as informações de tráfego de Web farms, a captura de eventos nos jogos de consola ou a telemetria recolhida a partir de máquinas industriais ou veículos ligados.

Ao contrário dos [tópicos e filas do Service Bus](../service-bus/service-bus-messaging-overview.md), os Event Hubs centram-se em permitir o processamento de transmissão das mensagem à escala. Por exemplo, as capacidades dos Event Hubs diferem dos tópicos do Service Bus na medida em que estes são totalmente direcionados para os cenários de processamento de eventos de débito alto. Como resultado, os Event Hubs não implementam algumas das capacidades de mensagens que estão disponíveis para os [tópicos](../service-bus/service-bus-fundamentals-hybrid-solutions.md#topics). Se precisar dessas funcionalidades, os tópicos continuam a ser a escolha ideal.

## Passos seguintes

Para obter informações detalhadas sobre os Event Hubs, consulte os tópicos seguintes.

- [Descrição geral dos Event Hubs](event-hubs-overview.md)
- [Guia de programação dos Event Hubs](event-hubs-programming-guide.md)
- [FAQ de disponibilidade e suporte dos Event Hubs](event-hubs-availability-and-support-faq.md)
- Introdução a um [Tutorial dos Event Hubs][]
- Uma [aplicação de exemplo que utiliza Event Hubs][] completa

[Tutorial dos Event Hubs]: event-hubs-csharp-ephcs-getstarted.md
[aplicação de exemplo que utiliza Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=ago16_HO4-->


