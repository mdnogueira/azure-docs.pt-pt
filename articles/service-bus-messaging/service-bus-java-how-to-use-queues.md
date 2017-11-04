---
title: Como utilizar as filas do Service Bus do Azure com Java | Microsoft Docs
description: "Saiba como utilizar as filas do Service Bus no Azure. Exemplos de código escrito em Java."
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 170f431525ffdc93a01fc085e48e69c3a774968e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Como utilizar filas do Service Bus com Java
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar as filas do Service Bus. Os exemplos são escritos em Java e utilize o [Azure SDK para Java][Azure SDK for Java]. Os cenários abrangidos incluem **criar filas**, **enviar e receber mensagens**, e **eliminar filas**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Certifique-se de que instalou o [Azure SDK para Java] [ Azure SDK for Java] antes de criar este exemplo. Se estiver a utilizar Eclipse, pode instalar o [Toolkit do Azure para o Eclipse] [ Azure Toolkit for Eclipse] que inclua o Azure SDK para Java. Em seguida, pode adicionar o **bibliotecas do Microsoft Azure para Java** ao seu projeto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adicione o seguinte `import` declarações na parte superior do ficheiro de Java:

```java
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Criar uma fila
Operações de gestão para filas do Service Bus podem ser efetuadas através de **ServiceBusContract** classe. A **ServiceBusContract** objecto é construído com uma configuração apropriada que encapsula o token SAS com permissões para geri-lo, e o **ServiceBusContract** classe é o único ponto de comunicação com o Azure.

O **ServiceBusService** classe fornece métodos para criar, enumerar e eliminar filas. O exemplo abaixo mostra como um **ServiceBusService** objeto pode ser utilizado para criar uma fila com o nome `TestQueue`, com um espaço de nomes com o nome `HowToSample`:

```java
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Existem métodos em `QueueInfo` que permitem que as propriedades da fila para ser otimizados (por exemplo: definir o valor (TTL) predefinido time-to-live a ser aplicado às mensagens enviadas para a fila). O exemplo seguinte mostra como criar uma fila com o nome `TestQueue` com um tamanho máximo de 5 GB:

````java
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Tenha em atenção que pode utilizar o `listQueues` método no **ServiceBusContract** objetos para verificar se uma fila com um nome especificado já existe num espaço de nomes do serviço.

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do Service Bus, a aplicação obtém um **ServiceBusContract** objeto. O código seguinte mostra como enviar uma mensagem o `TestQueue` fila criada anteriormente no `HowToSample` espaço de nomes:

```java
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

As mensagens enviadas para e recebido do Service Bus filas são instâncias do [BrokeredMessage] [ BrokeredMessage] classe. [BrokeredMessage] [ BrokeredMessage] objetos têm um conjunto de propriedades padrão (tais como [etiqueta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) e [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.timetolive#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), um dicionário utilizado para conter personalizado propriedades específicas da aplicação e um corpo de dados arbitrários da aplicação. Uma aplicação pode definir o corpo da mensagem através da transmissão de qualquer objeto serializável para o construtor do [BrokeredMessage][BrokeredMessage], e o serializador adequado, em seguida, será utilizado para serializar o objeto. Em alternativa, pode fornecer um **java. E/S. InputStream** objeto.

O exemplo seguinte mostra como enviar cinco mensagens de teste para o `TestQueue` **MessageSender** obteve no fragmento de código anterior:

```java
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens a partir de uma fila
A forma primária para receber mensagens de uma fila é utilizar um **ServiceBusContract** objeto. Mensagens recebidas podem funcionar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao utilizar o **ReceiveAndDelete** modo, receber é uma operação única - ou seja, quando o Service Bus recebe um pedido de leitura de uma mensagem numa fila, marca a mensagem como consumida e devolve a mesma à aplicação. **ReceiveAndDelete** modo (que é o modo predefinido) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento.
Porque o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha.

No **PeekLock** modo, receção torna-se uma operação de duas fases, o que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazena a mesma forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao chamar **eliminar** à mensagem recebida. Quando o Service Bus vê a **eliminar** chamada, irá marcar a mensagem como consumida e removê-lo a partir da fila.

O exemplo seguinte demonstra como podem ser recebidas mensagens e processados utilizando **PeekLock** modo (não o predefinido). O exemplo abaixo não um ciclo infinito e processa mensagens à medida que chegam no nosso `TestQueue`:

```java
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                 service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, em seguida, pode chamar o **unlockMessage** método à mensagem recebida (em vez do **deleteMessage** método). Tal faz com que o Service Bus desbloqueie a mensagem na fila e torna a mesma disponível para ser novamente recebida, quer pela mesma aplicação de consumo quer por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na fila, e se a aplicação não conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus desbloqueia automaticamente a mensagem e torna está disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes o **deleteMessage** pedido é emitido, em seguida, a mensagem é reenviada para a aplicação quando esta reiniciar. Isto é frequentemente designado *, pelo menos, uma vez processamento*; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido utilizando o **getMessageId** método da mensagem, que permanece constante nas tentativas de entrega.

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas de filas do Service Bus, consulte [filas, tópicos e subscrições] [ Queues, topics, and subscriptions] para obter mais informações.

Para obter mais informações, veja [Centro para Programadores do Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
