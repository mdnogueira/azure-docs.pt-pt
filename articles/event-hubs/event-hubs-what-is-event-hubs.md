---
title: "O que é o Azure Event Hubs e por que motivo deve ser utilizado | Microsoft Docs"
description: "Descrição geral e introdução ao Azure Event Hubs – Ingestão de telemetria escalável na cloud a partir de Web sites, aplicações e dispositivos"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm; babanisa
ms.openlocfilehash: 236d49b7d8104b410e2a4c768820c3c9ebcdce96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-event-hubs"></a>O que são os Hubs de Eventos?

Os Hubs de Eventos do Azure são uma plataforma de fluxo de dados altamente dimensionável e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Com a possibilidade de oferecer [capacidades de publicação/subscrição](/biztalk/core/publish-and-subscribe-architecture) com baixa latência e em larga escala, os Hubs de Eventos servem de "rampa"para os macrodados.

## <a name="why-use-event-hubs"></a>Por quê utilizar o Event Hubs?

As capacidades de processamento de eventos e telemetria do Event Hubs são úteis sobretudo para:

* Instrumentação de aplicações
* Experiência de utilizador ou de processamento de fluxo de trabalho
* Cenários Internet das Coisas (IoT)

Por exemplo, os Hubs de Eventos incluem o controlo do comportamento em aplicações móveis, informações de tráfego de web farms, captura de eventos nos jogos de consola ou telemetria recolhida a partir de máquinas industriais, veículos ligados ou outros dispositivos.

## <a name="azure-event-hubs-overview"></a>Descrição geral dos Event Hubs do Azure

Uma das funções comuns que o Event Hubs desempenha nas arquiteturas de solução é ser a "porta de entrada" para um pipeline de eventos, denominado frequentemente *ingestor de eventos*. Um ingestor de eventos é um componente ou serviço que se encontra entre os publicadores de eventos e os consumidores de eventos para desacoplar a produção de uma transmissão de eventos do consumo desses eventos. A imagem seguinte mostra esta arquitetura:

![Event Hubs](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

Os Hubs de Eventos oferecem uma capacidade de processamento da transmissão de mensagens, mas têm características diferentes das mensagens empresariais tradicionais. As capacidades do Event Hubs são criadas em torno dos cenários de alto débito e processamento de eventos. Como tal, os Hubs de Eventos são diferentes das mensagens do [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) e não implementam algumas das capacidades que estão disponíveis para as entidades de [mensagens do Service Bus](/azure/service-bus-messaging/), como os tópicos.

## <a name="event-hubs-features"></a>Funcionalidades dos Hubs de Eventos

Os Hubs de Eventos contêm os seguintes elementos principais:

- [**Produtores/editores de eventos**](event-hubs-features.md#event-publishers): qualquer entidade que envie dados para um hub de eventos. Um evento é publicado através de AMQP 1.0 ou HTTPS.
- [**Captura**](event-hubs-features.md#capture): permite a captura dos dados de transmissão dos Hubs de Eventos e o armazenamento numa conta de armazenamento de Blobs do Azure.
- [**Partições**](event-hubs-features.md#partitions): permite que cada consumidor leia apenas um subconjunto específico, ou partição, do fluxo de dados do evento.
- [**Tokens SAS**](event-hubs-features.md#sas-tokens): identifica e autentica o editor do evento.
- [**Consumidores e eventos**](event-hubs-features.md#event-consumers): qualquer entidade que leia os dados de eventos de um hub de eventos. Os consumidores de eventos ligam-se através do AMQP 1.0. 
- [**Grupos de consumidores**](event-hubs-features.md#consumer-groups): oferece uma vista separada do fluxo de dados do evento relativo a cada aplicação de vários consumidores, permitindo que estes ajam de forma independente.
- [**Unidades de débito**](event-hubs-features.md#capacity): unidades de capacidade previamente adquiridas. Uma única partição tem um tamanho máximo de uma unidade de débito.

Para obter detalhes técnicos sobre estas e outras funcionalidades dos Hubs de Eventos, consulte a [Event Hubs features overview](event-hubs-features.md) (Descrição geral das funcionalidades dos Hubs de Eventos). 

## <a name="next-steps"></a>Passos seguintes

Para obter informações detalhadas sobre os preços dos Hubs de Eventos, veja [Preços de Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 

