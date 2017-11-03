---
title: "Deteção de mensagens duplicadas do Service Bus do Azure | Microsoft Docs"
description: Detetar mensagens duplicadas do Service Bus
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: 91a6e62a03ffe39e456129ea78821250b65091e4
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="duplicate-detection"></a>Deteção de duplicados

Se uma aplicação encontra um erro fatal imediatamente depois envia uma mensagem e a instância da aplicação reiniciar, por conseguinte, indica Deteta que a entrega de mensagens anteriores não ocorriam, uma atividade send subsequente faz com que a mesma mensagem a aparecer no sistema duas vezes.

Também é possível para um erro ao nível do cliente ou rede ocorra anteriormente um momento e para uma mensagem enviada para ser consolidadas numa fila, com a confirmação não tornando-a novamente para o cliente com êxito. Este cenário deixa o cliente em dúvida sobre o resultado da operação de envio.

Deteção duplicada demora dúvida fora nestas situações, ativando o remetente reenviar a mesma mensagem e a fila ou um tópico elimina quaisquer cópias duplicadas.

Ativar a deteção duplicada ajuda a manter um registo do controlado de aplicações *MessageId* de todas as mensagens enviadas para uma fila ou um tópico durante uma janela de tempo especificado. Se qualquer nova mensagem é enviada as devidas um *MessageId* que já foi registado durante a janela de tempo, a mensagem é reportada como aceite (a operação de envio for bem sucedida), mas a mensagem enviada recentemente instantaneamente é ignorada e removida. Não existem outras partes da mensagem diferente de *MessageId* são considerados.

Controlo de aplicação do identificador é essencial, porque apenas que permite que a aplicação associar o *MessageId* a um contexto de processo do negócio partir do qual se é possível previsibilidade reconstruir em caso de falha.

Para um processo empresarial em que são enviadas mensagens de vários MBS processamento algum contexto de aplicação, o *MessageId* pode ser um compostos do identificador de contexto ao nível da aplicação, tais como um número de ordem de compra e o assunto da mensagem; Por exemplo, **12345.2017/pagamento**.

O *MessageId* pode sempre ser algumas GUID, mas anchoring o identificador do que o processo de negócios gera repetibilidade previsível, que, se pretendido para tirar partido da funcionalidade de deteção duplicada eficazmente.

## <a name="enable-duplicate-detection"></a>Ativar a deteção de duplicados

No portal, a funcionalidade está ativada durante a criação de entidade com o **ativar a deteção duplicada** caixa de verificação, que está desativado por predefinição. A definição para criar novos tópicos é equivalente.

![][1]

Através de programação, defina o sinalizador com o [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) propriedade Framework completa .NET API. Com a API do Azure Resource Manager, o valor é definido com o [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) propriedade.

O histórico de tempo da deteção de duplicados está predefinida para 30 segundos para filas e tópicos, com um valor máximo de 7 dias. Pode alterar esta definição na janela de propriedades da fila e tópico no portal do Azure.

![][2]

Programaticamente, pode configurar o tamanho da janela de deteção duplicada durante os quais são retidos ids de mensagem, utilizando o [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) propriedade com o .NET Framework API completa . Com a API do Azure Resource Manager, o valor é definido com o [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) propriedade.

Tenha em atenção que afetam o débito de fila (e tópico), uma vez que todos os ids de mensagem registados devem ser comparados com o identificador da mensagem recentemente submetido ativar a deteção duplicada e o tamanho da janela diretamente.

Manter a janela pequenos significa que menos de ids de mensagem tem de ser mantidos e correspondência e débito é afetado inferior. Para entidades de débito elevado que requerem a deteção duplicada, deve manter a janela tão reduzida quanto possível.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
