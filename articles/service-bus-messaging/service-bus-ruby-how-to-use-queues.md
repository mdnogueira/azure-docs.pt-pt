---
title: Como utilizar as filas do Service Bus do Azure com Ruby | Microsoft Docs
description: "Saiba como utilizar as filas do Service Bus no Azure. Exemplos de código escrito em Ruby."
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 357a7277dd42b6973cf35a9f642b8eec36a745e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Como utilizar filas do Service Bus com Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este guia descreve como utilizar as filas do Service Bus. Os exemplos são escritos no Ruby e utilizam a Azure gem. Os cenários abrangidos incluem **criar filas, enviar e receber mensagens**, e **eliminar filas**. Para obter mais informações sobre as filas do Service Bus, consulte o [passos](#next-steps) secção.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Como criar uma fila
O **Azure::ServiceBusService** objeto permite-lhe funcionar com as filas. Para criar uma fila, utilize o `create_queue()` método. O exemplo seguinte cria uma fila ou imprime os eventuais erros.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Também pode passar uma **Azure::ServiceBus::Queue** objeto com as opções adicionais, que permite-lhe substituir as definições de fila predefinido, tal como o tempo de mensagem para o tamanho da fila em direto ou máximo. O exemplo seguinte mostra como definir o tamanho máximo da fila para 5 GB e tempo TTL para 1 minuto:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do Service Bus, as chamadas de aplicação a `send_queue_message()` método no **Azure::ServiceBusService** objeto. As mensagens enviadas para (e recebida do) Service Bus são filas **Azure::ServiceBus::BrokeredMessage** objetos e têm um conjunto de propriedades padrão (tais como `label` e `time_to_live`), um dicionário utilizado para conter personalizado propriedades específicas da aplicação e um corpo de dados arbitrários da aplicação. Uma aplicação pode definir o corpo da mensagem através da transmissão de um valor de cadeia que a mensagem e eventuais propriedades padrão são povoadas com valores predefinidos.

O exemplo seguinte demonstra como enviar uma mensagem de teste para a fila com o nome `test-queue` utilizando `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila
As mensagens são recebidas a partir de uma fila utilizando o `receive_queue_message()` método no **Azure::ServiceBusService** objeto. Por predefinição, as mensagens são lidos e bloqueadas sem seja eliminado da fila. No entanto, pode eliminar as mensagens da fila como leem definindo a `:peek_lock` opção para **falso**.

O comportamento predefinido torna o ler e eliminar uma operação de duas etapas, que também torna possível suportar aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazena a mesma forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao chamar `delete_queue_message()` método e fornecer a mensagem ser eliminadas como parâmetro. O `delete_queue_message()` método irá marcar a mensagem como consumida e removê-lo a partir da fila.

Se o `:peek_lock` parâmetro está definido como **falso**, ler e eliminar a mensagem torna-se o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Porque o Service Bus marcou a mensagem como consumida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha.

O exemplo seguinte demonstra como recebe e processa mensagens utilizando `receive_queue_message()`. O exemplo recebe e elimina uma mensagem através da utilização de `:peek_lock` definido como **falso**, em seguida, recebe outra mensagem e, em seguida, elimina a mensagem através de `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, em seguida, pode chamar o `unlock_queue_message()` método no **Azure::ServiceBusService** objeto. Esta chamada faz com que o Service Bus desbloqueie a mensagem na fila e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na fila, e se a aplicação não conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus desbloqueia automaticamente a mensagem e torna está disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes o `delete_queue_message()` método é denominado, em seguida, a mensagem é reenviada para a aplicação quando esta reiniciar. Este processo é frequentemente designado *, pelo menos, uma vez processamento*; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido utilizando o `message_id` propriedade da mensagem, que permanece constante nas tentativas de entrega.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas das Filas do Service Bus, siga estas hiperligações para saber mais.

* Descrição geral do [filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md).
* Visite o [Azure SDK para Ruby](https://github.com/Azure/azure-sdk-for-ruby) repositório no GitHub.

Para ver uma comparação entre as filas de Service Bus do Azure, abordadas neste artigo e filas do Azure, abordado o [como utilizar o armazenamento de filas do Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) artigo, consulte [filas do Azure e filas de barramento de serviço do Azure - comparados e Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

