---
title: Como utilizar as filas do Service Bus do Azure com o Python | Microsoft Docs
description: Saiba como utilizar as filas do Service Bus do Azure do Python.
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm;lmazuel
ms.openlocfilehash: e1e81ad1d7b4fe0e044917f090cac59dfd5b6332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Como utilizar filas do Service Bus com o Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar as filas do Service Bus. Os exemplos são escritos no Python e utilize o [pacote Python Azure Service Bus][Python Azure Service Bus package]. Os cenários abrangidos incluem **criar filas, enviar e receber mensagens**, e **eliminar filas**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

> [!NOTE]
> Para instalar o Python ou [pacote Python Azure Service Bus][Python Azure Service Bus package], consulte o [guia de instalação do Python](../python-how-to-install.md).
> 
> 

## <a name="create-a-queue"></a>Criar uma fila
O **ServiceBusService** objeto permite-lhe funcionar com as filas. Adicione o seguinte código perto da parte superior de qualquer ficheiro de Python no qual pretende aceder programaticamente ao Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Queue
```

O código seguinte cria um **ServiceBusService** objeto. Substitua `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` com o espaço de nomes, o nome da chave de acesso partilhado (SAS) de assinatura e o valor.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Os valores para o nome da chave SAS e o valor podem ser encontrados no [portal do Azure] [ Azure portal] informações de ligação, ou no Visual Studio **propriedades** painel quando selecionar o serviço Espaço de nomes de barramento no Explorador de servidores (conforme ilustrado na secção anterior).

```python
bus_service.create_queue('taskqueue')
```

O `create_queue` método também suporta opções adicionais, que permitem-lhe substituir as definições predefinidas da fila, tais como a hora da mensagem em direto (TTL) ou o tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila de 5 GB e o valor de TTL para 1 minuto:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do Service Bus, as chamadas de aplicação a `send_queue_message` método no **ServiceBusService** objeto.

O exemplo seguinte demonstra como enviar uma mensagem de teste para a fila com o nome `taskqueue` utilizando `send_queue_message`:

```python
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para mais informações sobre as quotas, consulte [quotas de Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens a partir de uma fila
As mensagens são recebidas a partir de uma fila utilizando o `receive_queue_message` método no **ServiceBusService** objeto:

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

As mensagens são eliminadas da fila, que são de leitura quando o parâmetro `peek_lock` está definido como **falso**. Pode ler (peek) e bloquear a mensagem sem eliminá-lo a partir da fila, definindo o parâmetro `peek_lock` para **verdadeiro**.

O comportamento de ler e eliminar a mensagem como parte da operação de receção é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Porque o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha.

Se o `peek_lock` parâmetro está definido como **verdadeiro**, a receção torna-se uma operação de duas fases, o que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazena a mesma forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao chamar o **eliminar** método no **mensagem** objeto. O **eliminar** método irá marcar a mensagem como consumida e removê-lo a partir da fila.

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, em seguida, pode chamar o **desbloquear** método no **mensagem** objeto. Isto irá fazer com que o Service Bus desbloqueie a mensagem na fila e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na fila, e se a aplicação não conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o barramento de serviço irá desbloquear automaticamente a mensagem e disponibilizá-lo ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes o **eliminar** método é denominado, em seguida, a mensagem será reenviada para a aplicação quando esta reiniciar. Isto é frequentemente designado **, pelo menos, uma vez processamento**, ou seja, cada mensagem será processada, pelo menos, uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido utilizando o **MessageId** propriedade da mensagem, que permanecerá constante nas tentativas de entrega.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas de filas do Service Bus, consulte estes artigos para saber mais.

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

