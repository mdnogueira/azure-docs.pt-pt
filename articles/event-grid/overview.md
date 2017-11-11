---
title: "Descrição geral de grelha de eventos do Azure"
description: Descreve os conceitos e grelha de eventos do Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/06/2017
ms.author: babanisa
ms.openlocfilehash: 5b522b40b136e354c6ca83a56ac7ad690151ad7d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="an-introduction-to-azure-event-grid"></a>Uma introdução à grelha de eventos do Azure

Grelha de eventos do Azure permite-lhe criar facilmente aplicações com arquiteturas baseada em eventos. Selecione o recurso do Azure que pretende subscrever e conceder o processador de eventos ou o ponto final de WebHook para enviar o evento. Grelha de evento têm suporte incorporado para eventos provenientes dos serviços do Azure, como blobs de armazenamento e de grupos de recursos. Grelha de evento também tem suporte personalizado para a aplicação e eventos de terceiros, utilizar tópicos personalizados e webhooks personalizado. 

Pode utilizar filtros para encaminhar os eventos específicos diferentes de pontos finais, multicast para vários pontos finais e certifique-se que os eventos são entregues fiável. Grelha de evento também criou o suporte para eventos personalizados e de terceiros.

Na versão anterior, o Event Grid suporta as localizações **westus2** e **westcentralus**. Serão adicionadas noutras regiões.

Este artigo fornece uma descrição geral da grelha de eventos do Azure. Se pretender começar a grelha de eventos, consulte [criar e rota eventos personalizados com o Azure eventos grelha](custom-event-quickstart.md).

![Modelo de funcional de grelha de eventos](./media/overview/event-grid-functional-model.png)

## <a name="event-publishers"></a>Publicadores de eventos

Atualmente, os seguintes serviços do Azure têm suporte do Editor incorporados para a grelha de evento:

* Grupos de recursos (operações de gestão)
* Subscrições do Azure (operações de gestão)
* Event Hubs
* Blob de armazenamento
* Tópicos personalizados

Outros serviços do Azure serão adicionados este ano.

## <a name="event-handlers"></a>Processadores de eventos

Atualmente, os seguintes serviços do Azure tem suporte de processador incorporados para a grelha de evento: 

* Funções do Azure
* Aplicações Lógicas
* Automatização do Azure
* WebHooks
* Fluxo da Microsoft

Outros serviços do Azure serão adicionados este ano.

## <a name="concepts"></a>Conceitos

Existem cinco conceitos na grelha de eventos do Azure que lhe permitem começar:

* **Eventos** -o que aconteceu.
* **Origens/publicadores de eventos** - onde o evento demorou local.
* **Tópicos** -o ponto final onde os publicadores enviar eventos.
* **Subscrições de evento** -o mecanismo de ponto final ou incorporado para eventos de rota, por vezes, a vários processadores. As subscrições também sejam utilizadas pelo processadores inteligentemente filtrar eventos recebidos.
* **Processadores de eventos** -a aplicação ou serviço reagir ao evento.

Para obter mais informações sobre estes conceitos, consulte [conceitos na grelha de eventos do Azure](concepts.md).

## <a name="capabilities"></a>Capacidades

Aqui estão algumas das principais funcionalidades do Azure grelha de evento:

* **Simplicidade** -ponto e clique em procure eventos a partir do seu recurso do Azure a qualquer processador de eventos ou o ponto final.
* **Avançadas filtragem** -filtro no evento tipo ou evento de publicar o caminho para se certificar de processadores de eventos recebem apenas eventos relevantes.
* **Fan-out** -subscrever múltiplos pontos finais para o mesmo evento envie cópias do evento para o número de casas conforme necessário.
* **Fiabilidade** -utilizar 24 horas de repetições com término exponencial para garantir que os eventos são entregues.
* **Pay-por-o evento** - pague apenas para a quantidade utilizar grelha de eventos.
* **Débito elevado** -criar cargas de trabalho de elevado volume na grelha de eventos com suporte para milhões de eventos por segundo.
* **Eventos incorporados** - obter cópias de segurança e executar rapidamente com recurso definido eventos incorporados.
* **Eventos personalizados** -utilizar rotas de grelha de eventos, de filtro e fiável de eventos personalizados entregar na sua aplicação.

Para ver uma comparação de grelha de eventos, os Event Hubs e Service Bus, consulte [escolha entre os serviços do Azure que entregar mensagens](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>O que posso fazer com grelha de evento?

Grelha de eventos do Azure fornece várias capacidades melhorar muito sem servidor, ops automatização e trabalho de integração: 

### <a name="serverless-application-architectures"></a>Arquiteturas de aplicações sem servidor

![Aplicação sem servidor](./media/overview/serverless_web_app.png)

O Event Grid liga as origens de dados e os processadores de eventos. Por exemplo, utilize o Event Grid para acionar instantaneamente uma função sem servidor para executar imagens de análises sempre que é adicionada uma fotografia nova a um contentor de armazenamento de blobs. 

### <a name="ops-automation"></a>Automatização de ops

![Automatização de operações](./media/overview/Ops_automation.png)

O Event Grid permite-lhe acelerar a automatização e simplificar a aplicação de políticas. Por exemplo, o Event Grid pode notificar a Automatização do Azure quando é criada uma máquina virtual ou quando é preparada uma Base de Dados SQL. Estes eventos podem ser utilizados para verificar automaticamente se as configurações do serviço estão em conformidade, colocar metadados em ferramentas de operações, etiquetar máquinas virtuais ou arquivar itens de trabalho.

### <a name="application-integration"></a>Integração de aplicações

![Integração de aplicações](./media/overview/app_integration.png)

O Event Grid liga a sua aplicação a outros serviços. Por exemplo, criar um tópico personalizado para enviar dados de eventos da aplicação à grelha de evento e tirar partido das respetiva entrega fiável, avançada encaminhamento e integração direta com o Azure. Em alternativa, pode utilizar o Event Grid com o Logic Apps para processar dados em qualquer local sem ter de escrever código. 

## <a name="how-much-does-event-grid-cost"></a>Quanto custo grelha de evento?

Grelha de eventos do Azure utiliza um modelo de preços de pagamento por eventos, pelo que apenas paga a utiliza.

Custos de grelha de evento $0.60 por milhões operações ($0.30 durante pré-visualização) e a operação primeiro 100.000 por mês são gratuitos. Operações são definidas como entrada de evento, avançadas correspondência, tentativa de entrega e chamadas de gestão.  Podem encontrar mais detalhes no [página de preços](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Passos seguintes

* [Eventos de Blob de armazenamento de rota](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Responda a eventos de blob de armazenamento através da utilização de grelha de eventos.
* [Criar e subscrever a eventos personalizados](custom-event-quickstart.md)  
  Avançar diretamente e começar a enviar os seus próprios eventos personalizados para qualquer ponto final com o guia de introdução da grelha de eventos do Azure.
* [Utilizar aplicações lógicas como um processador de eventos](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Um tutorial sobre como criar uma aplicação com as Logic Apps para reagir a eventos enviados pela grelha de eventos.
* [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)  
  Um tutorial que utiliza as funções do Azure para dados de fluxo a partir de Event Hubs ao SQL Data Warehouse.
* [Referência da API de REST de grelha de eventos](/rest/api/eventgrid)  
  Fornece mais informações técnicas sobre a grelha de eventos do Azure e uma referência para gerir subscrições de evento, encaminhamento e filtragem.