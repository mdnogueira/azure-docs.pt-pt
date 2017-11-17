---
title: "Mensagens de comparação de serviços do Azure"
description: "Grelha de eventos do Azure compara, os Event Hubs e barramento de serviço. Recomenda que serviço a utilizar para diferentes cenários."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/15/2017
ms.author: tomfitz
ms.openlocfilehash: 94771578d94b5b9bc23451049a78506e80c87d26
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="choose-between-azure-services-that-deliver-messages"></a>Escolher entre os serviços do Azure que a entrega de mensagens

O Azure oferece três serviços que ajudar a entrega de mensagens de evento ao longo de uma solução. Estes serviços são:

* [Grelha de eventos](/azure/event-grid/)
* [Hubs de Eventos](/azure/event-hubs/)
* [Service Bus](/azure/service-bus-messaging/)

Embora têm alguns semelhanças, cada serviço foi concebido para determinados cenários. Este artigo descreve as diferenças entre estes serviços e ajuda a compreender qual deles escolher para a sua aplicação. Em muitos casos, os serviços de mensagens são complementares em podem ser utilizados em conjunto.

## <a name="event-vs-message-services"></a>Evento vs. os serviços de mensagens

Há uma distinção importante a ter em atenção entre serviços que proporcionem um evento e serviços que proporcionem uma mensagem.

### <a name="event"></a>Evento

Um evento é uma notificação simples de uma ação ou uma alteração de estado. Os dados de evento contém informações sobre o que aconteceu, mas não tem os dados que acionou o evento. Por exemplo, um evento notifica subscritores que foi criado um ficheiro. Poderá conter informações gerais sobre o ficheiro, mas não contém o ficheiro em si. Geralmente, os eventos acionam processadores de eventos para agir em tempo real.

### <a name="message"></a>Mensagem

Uma mensagem está em bruto dados produzidos por um serviço a ser consumidos ou armazenadas noutro local. A mensagem contém os dados que acionou o pipeline de mensagens. Esta mensagem pode ser qualquer coisa desde uma ordem de comércio eletrónico a telemetria de utilizador. Ao contrário de uma notificação de evento, o publicador de uma mensagem pode esperar uma resposta. Por exemplo, uma mensagem contém os dados não processados, mas espera que a parte seguinte do sistema para criar um ficheiro de dados.

## <a name="comparison-of-services"></a>Comparação de serviços

| Serviço | Objetivo | Tipo | Quando utilizar |
| ------- | ------- | ---- | ----------- |
| Event Grid | Programação reativa | Distribuição de eventos | Reagir a alterações de estado |
| Event Hubs | Pipeline de macrodados | Evento de transmissão em fluxo | Telemetria e de transmissão em fluxo de dados distribuídos |
| Service Bus | Mensagens empresariais de alto valor | Mensagem | Processamento de ordem e de transações financeiras |

### <a name="event-grid"></a>Event Grid

Grelha de eventos é um backplane eventos CCM que permite a programação condicionada por eventos, reativa. Utiliza uma publicação-subscrição modelo. Publicadores de eventos de emissão, mas tem não expectativa sobre os quais os eventos são processados. Os subscritores decidir os eventos que pretendem processar.

Grelha de eventos está profundamente integrada com serviços do Azure e pode ser integrada com serviços de terceiros. Isto simplifica o consumo de eventos e reduz os custos, eliminando a necessidade de consulta constante. Grelha de eventos de forma eficiente e fiável encaminha eventos a partir do Azure e de recursos não do Azure. -Distribui os eventos de pontos finais de subscritor registado. A mensagem de evento contém as informações que necessárias para reagir a alterações em serviços e aplicações. Grelha de eventos não é um pipeline de dados e não fornecer o objecto real que foi atualizado.

Tem as seguintes características:

* dinamicamente dimensionável
* Custo baixo
* Sem servidor

### <a name="event-hubs"></a>Event Hubs

Os Hubs de eventos do Azure é um pipeline de macrodados. -Facilita a captura, a retenção e a repetição de dados de fluxo de telemetria e eventos. Os dados podem ter várias origens em simultâneo. Os Event Hubs permite que os dados de telemetria e eventos a serem disponibilizados a uma variedade de serviços de infraestruturas e análise de processamento de fluxo. Está disponível como fluxos de dados ou eventos integrados em lotes. Este serviço fornece uma solução única que permite a obtenção de dados rápido para o processamento em tempo real, bem como repetida reprodução de dados não processados armazenados. -Pode capturar os dados de transmissão em fluxo para um ficheiro para processamento e análise.

Tem as seguintes características:

* baixa latência
* capacidade para receber e a processar milhões de eventos por segundo

### <a name="service-bus"></a>Service Bus

Barramento de serviço destina-se para as aplicações empresariais tradicionais. Estas aplicações da empresa necessitam de transações, ordenação, deteção duplicada e consistência instantânea. Service Bus permite que aplicações cloud nativo fornecer gestão de transição de estado fiável para os processos de negócios. Quando o processamento de mensagens de alto valor que não podem ser perdidas ou duplicadas, utilize o Service Bus do Azure. Além disso, o Service Bus facilita a comunicação altamente segura em soluções de nuvem híbrida e pode ligar sistemas de no local existentes para soluções de nuvem.

O Service Bus é um sistema de mensagens mediado. Armazena as mensagens no "Mediador" (por exemplo, uma fila) até a parte consumidora está preparada para receber as mensagens.

Tem as seguintes características:

* entrega de mensagens assíncronas fiável (como um serviço de mensagens para empresas) que necessita de consulta
* funcionalidades avançadas de mensagens como FIFO, criação de batches/sessões, transações, controlo de mensagens não entregues, temporal, encaminhamento e filtragem e deteção de duplicados

## <a name="use-the-services-together"></a>Utilizar os serviços em conjunto

Em alguns casos, utilizar os serviços de lado a lado para preencher funções distintas. Por exemplo, um site de ecommerce pode utilizar o Service Bus para processar a ordem, dos Event Hubs para captura de telemetria de site e eventos de grelha para responder a eventos, tal como um item foi enviado.

Noutros casos, a ligá-las em conjunto para formar um pipeline de eventos e os dados. Utilize a grelha de eventos para responder a eventos de outros serviços. Para obter um exemplo de utilização da grelha de eventos com os Event Hubs para migrar dados para um armazém de dados, consulte [transmitir macrodados para um armazém de dados](event-grid-event-hubs-integration.md). A imagem seguinte mostra o fluxo de trabalho para os dados de transmissão em fluxo.

![Descrição geral de dados de fluxo](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre os serviços de mensagens do Azure, consulte a mensagem de blogue [eventos, pontos de dados e mensagens - escolher o serviço de mensagens à direita do Azure para os seus dados](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
* Para uma introdução à grelha de eventos, consulte [sobre eventos grelha](overview.md).
* Para começar a grelha de eventos, consulte o artigo [criar e rota eventos personalizados com o Azure eventos grelha](custom-event-quickstart.md).
* Para começar a utilizar com os Event Hubs, consulte o artigo [criar um espaço de nomes de Event Hubs e um hub de eventos no portal do Azure](../event-hubs/event-hubs-create.md).
* Para começar a utilizar com o Service Bus, consulte o artigo [criar um espaço de nomes do Service Bus com o portal do Azure](../service-bus-messaging/service-bus-create-namespace-portal.md).