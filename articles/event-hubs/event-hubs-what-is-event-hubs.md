---
title: "O que são os Event Hubs do Azure? | Microsoft Docs"
description: "Descrição geral e descrição dos Event Hubs do Azure"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 4391d750-5bbe-456d-9091-b416127bc754
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 05ca343cfdfc602759eb3ea30a7186a0bb47bd74
ms.openlocfilehash: eddc3e7c4914936a8a83a042dc0f7d528b91f059


---
# <a name="what-is-azure-event-hubs"></a>O que são os Event Hubs do Azure?
Os Event Hubs do Azure são um serviço de entrada de dados altamente dimensionável, que pode ingerir milhões de eventos por segundo para que possa processar e analisar os quantidades enormes de dados produzidos pelos dispositivos e aplicações ligados. Os Event Hubs atuam como a “porta de entrada” para um pipeline de eventos. E, quando os dados são recolhidos para um Hub de Eventos, podem ser transformados e armazenados através de qualquer fornecedor de análise em tempo real ou adaptadores de criação de batches/armazenamento. Os Event Hubs desacoplam a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam aceder aos eventos de acordo com seu próprio agendamento. Para obter mais informações e detalhes técnicos, veja [Descrição geral dos Event Hubs](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Capacidades dos Event Hubs
Os Event Hubs são um serviço de processamento de eventos que permitem o processamento em grande escala de telemetria e eventos, com baixa latência e alta fiabilidade. Este serviço é especialmente útil para:

* Instrumentação de aplicações
* Experiência de utilizador ou de processamento de fluxo de trabalho
* Cenários Internet das Coisas (IoT)

Mais algumas capacidades chave dos Event Hubs incluem o controlo do comportamento em aplicações móveis, as informações de tráfego de Web farms, a captura de eventos nos jogos de consola ou a telemetria recolhida a partir de máquinas industriais ou veículos ligados.

## <a name="next-steps"></a>Passos seguintes
Para obter informações detalhadas sobre os Event Hubs, consulte os tópicos seguintes.

* [Descrição geral dos Hubs de Eventos](event-hubs-overview.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [FAQ de disponibilidade e suporte dos Event Hubs](event-hubs-availability-and-support-faq.md)
* Introdução a um [Tutorial dos Hubs de Eventos][Event Hubs tutorial]
* Uma [Aplicação de exemplo que utiliza os Hubs de Eventos][sample application that uses Event Hubs] completa

[Event Hubs tutorial]: event-hubs-csharp-ephcs-getstarted.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=Dec16_HO1-->


