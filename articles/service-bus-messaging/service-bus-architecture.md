---
title: "Descrição geral da arquitetura de processamento de mensagens do Azure Service Bus | Microsoft Docs"
description: Descreve a arquitetura de processamento de mensagens e do reencaminhamento do Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: ced46c64c1c105aa987759e05ab3680bc399f9a0
ms.contentlocale: pt-pt
ms.lasthandoff: 05/18/2017


---
# <a name="service-bus-architecture"></a>Arquitetura do Service Bus
Este artigo descreve a arquitetura de processamento de mensagens do Service Bus do Azure.

## <a name="service-bus-scale-units"></a>Unidades de escala do Service Bus
O Service Bus está organizado por *unidades de escala*. Uma unidade de escala é uma unidade de implementação e contém todos os componentes necessários para executar o serviço. Cada região implementa uma ou mais unidades de escala do Service Bus.

Um espaço de nomes do Service Bus é mapeado para uma unidade de escala. A unidade de escala processa todos os tipos de entidades do Service Bus: reencaminhamentos e entidades de mensagens mediadas (filas, tópicos, subscrições). A unidade de escala do Service Bus é constituída pelos seguintes componentes:

* **Um conjunto de nós de gateway.** Os nós de gateway autenticam pedidos recebidos e processam pedidos de reencaminhamento. Cada nó de gateway tem um endereço IP público.
* **Um conjunto de nós do mediador de mensagens.** Os nós do mediador de mensagens processam pedidos relativos a entidades de mensagens.
* **Um arquivo de gateway.** O arquivo de gateway contém os dados para cada entidade que está definida nessa unidade de escala. O arquivo de gateway é implementado por cima de uma SQL Database do Azure.
* **Vários arquivos de mensagens.** Os arquivos de mensagens contêm as mensagens de todas as filas, tópicos e subscrições definidas nessa unidade de escala. Além disso, contêm todos os dados de subscrição. A menos que a opção [entidades de mensagens particionadas](service-bus-partitioning.md) esteja ativada, uma fila ou um tópico é mapeado para um arquivo de mensagens. As subscrições são armazenadas no mesmo arquivo de mensagens do respetivo tópico principal. Exceto as [Mensagens Premium](service-bus-premium-messaging.md) do Service Bus, os arquivos de mensagens são implementados por cima de bases de dados SQL Azure.

## <a name="containers"></a>Contentores
A cada entidade de mensagens é atribuído um contentor específico. Um contentor é uma construção lógica que utiliza exatamente um arquivo de mensagens para armazenar todos os dados relevantes para esse contentor. Cada contentor é atribuído a um nó do mediador de mensagens. Normalmente, existem mais contentores do que nós do mediador de mensagens. Por conseguinte, cada nó do mediador de mensagens carrega vários contentores. A distribuição de contentores para um nó do mediador de mensagens está organizada de modo que todos os nós do mediador de mensagens sejam equitativamente carregados. Caso o padrão de carga seja alterado (por exemplo, um dos contentores fique muito ocupado) ou caso um nó do mediador de mensagens fique temporariamente indisponível, os contentores são redistribuídos entre os nós do mediador de mensagens.

## <a name="processing-of-incoming-messaging-requests"></a>Processamento de pedidos de mensagens recebidos
Quando um cliente envia um pedido para o Service Bus, o Azure Load Balancer encaminha o mesmo para qualquer um dos nós de gateway. O nó de gateway autoriza o pedido. Caso o pedido seja relativo a uma entidade de mensagens (fila, tópico, subscrição), o nó de gateway procura a entidade no arquivo de gateway e determina em que arquivo de mensagens está localizada a entidade. Em seguida, procura o nó do mediador de mensagens que está atualmente a utilizar esse contentor e envia o pedido para esse nó do mediador de mensagens. O nó do mediador de mensagens processa o pedido e atualiza o estado da entidade no contentor. O nó do mediador de mensagens envia a resposta novamente para o nó de gateway, o qual envia uma resposta adequada novamente para o cliente que emitiu o pedido original.

![Processamento de Pedidos de Mensagens Recebidos](./media/service-bus-architecture/ic690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Processamento de pedidos de reencaminhamento recebidos
Quando um cliente envia um pedido para o serviço de [reencaminhamento do Azure](/azure/service-bus-relay/), o Azure Load Balancer encaminha o mesmo para qualquer um dos nós de gateway. Caso o pedido seja um pedido de escuta, o nó de gateway cria um novo reencaminhamento. Se o pedido for um pedido de ligação para um reencaminhamento específico, o nó de gateway reencaminha o pedido de ligação para o nó de gateway que detém o reencaminhamento.  O nó de gateway que detém o reencaminhamento envia um pedido de encontro para o cliente de escuta, pedindo para o serviço de escuta criar um canal temporário para o nó de gateway que recebeu o pedido de ligação.

Quando a ligação de reencaminhamento é estabelecida, os clientes podem trocar mensagens através do nó de gateway utilizado para o encontro.

![Processamento de Pedidos de Reencaminhamento de WCF Recebidos](./media/service-bus-architecture/ic690645.png)

## <a name="next-steps"></a>Passos seguintes
Agora que já leu uma descrição geral da arquitetura do Service Bus, visite as seguintes ligações para obter mais informações:

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Descrição Geral do Reencaminhamento do Azure](../service-bus-relay/relay-what-is-it.md)
* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Uma solução de mensagens em fila a utilizar filas do Service Bus](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



