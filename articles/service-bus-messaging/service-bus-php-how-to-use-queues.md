---
title: Como utilizar filas do Service Bus com o PHP | Microsoft Docs
description: "Saiba como utilizar as filas do Service Bus no Azure. Exemplos de código escrito em PHP."
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 3514812f7f087582035dad5d9a4d620652aa4da9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Como utilizar filas do Service Bus com o PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este guia mostra como utilizar as filas do Service Bus. Os exemplos são escritos no PHP e a utilização de [Azure SDK para PHP](../php-download-sdk.md). Os cenários abrangidos incluem **criar filas**, **enviar e receber mensagens**, e **eliminar filas**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para criar uma aplicação PHP que acede ao serviço Blob do Azure é que a referência de classes no [Azure SDK para PHP](../php-download-sdk.md) de dentro do seu código. Pode utilizar as ferramentas de desenvolvimento para criar a aplicação ou o bloco de notas.

> [!NOTE]
> A instalação do PHP também tem de ter o [OpenSSL extensão](http://php.net/openssl) instalado e ativado.
> 
> 

Neste guia, irá utilizar as funcionalidades de serviço que podem ser chamadas de dentro de uma aplicação PHP localmente, ou no código em execução dentro de uma função da web do Azure, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obtenha o cliente do Azure bibliotecas
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Para utilizar a APIs de fila do Service Bus, efetue o seguinte:

1. Referenciar o ficheiro do Carregador automático utilizando o [require_once] [ require_once] instrução.
2. Referência a quaisquer classes que poderá utilizar.

O exemplo seguinte mostra como inclui o Carregador automático ficheiros e a referência a `ServicesBuilder` classe.

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) parte do princípio de que instalou as bibliotecas de cliente do PHP do Azure através do compositor. Se tiver instalado as bibliotecas manualmente ou como um pacote PEAR, tem de fazer referência a **WindowsAzure.php** Carregador automático ficheiros.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos abaixo, o `require_once` instrução será sempre apresentada, mas apenas as classes de necessárias para o exemplo para executar são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de barramento de serviço
Ao instanciar um cliente do Service Bus, primeiro tem de ter uma cadeia de ligação válido neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Onde `Endpoint` é, geralmente, com o formato de `[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço do Azure tem de utilizar o `ServicesBuilder` classe. Pode:

* Passe a cadeia de ligação direta.
* Utilize o **CloudConfigurationManager (CCM)** para verificar a várias origens externas para a cadeia de ligação:
  * Por predefinição vem com suporte para uma origem externa - variáveis de ambientais
  * Pode adicionar novas origens ao alargar o `ConnectionStringSource` classe

Para os exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila
Pode efetuar operações de gestão para filas do Service Bus através de `ServiceBusRestProxy` classe. A `ServiceBusRestProxy` objecto é construído através de `ServicesBuilder::createServiceBusService` método de fábrica com uma cadeia de ligação adequado que encapsula as token permissões para geri-lo.

O exemplo seguinte mostra como ao instanciar um `ServiceBusRestProxy` e chame `ServiceBusRestProxy->createQueue` para criar uma fila com o nome `myqueue` dentro de um `MySBNamespace` espaço de nomes do serviço:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Pode utilizar o `listQueues` método no `ServiceBusRestProxy` objetos para verificar se uma fila com um nome especificado já existe num espaço de nomes.
> 
> 

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do Service Bus, as chamadas de aplicação a `ServiceBusRestProxy->sendQueueMessage` método. O código seguinte mostra como enviar uma mensagem para o `myqueue` fila criada anteriormente no `MySBNamespace` espaço de nomes de serviço.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

As mensagens enviadas para (e recebida do) Service Bus filas são instâncias do [BrokeredMessage] [ BrokeredMessage] classe. [BrokeredMessage] [ BrokeredMessage] objetos têm um conjunto de métodos padrão e propriedades que são utilizadas para reter propriedades personalizadas de específicas da aplicação e um corpo de dados arbitrários da aplicação.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. Este limite superior em tamanho da fila é de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens a partir de uma fila

A melhor forma para receber mensagens de uma fila está a utilizar um `ServiceBusRestProxy->receiveQueueMessage` método. Podem ser recebidas mensagens em dois modos diferentes: [ *ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) e [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock). **PeekLock** é a predefinição.

Quando utilizar [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) modo, receber é uma operação única; ou seja, quando o Service Bus recebe um pedido de leitura de uma mensagem numa fila, marca a mensagem como consumida e devolve a mesma à aplicação. O modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Porque o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha.

Predefinida [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) modo, receber uma mensagem torna-se uma operação de duas fases, o que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a receção outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazena a mesma forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao transmitir a mensagem recebida para `ServiceBusRestProxy->deleteMessage`. Quando o Service Bus vê a `deleteMessage` chamada, irá marcar a mensagem como consumida e removê-lo a partir da fila.

O exemplo seguinte mostra como receber e a processar uma mensagem utilizando [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) modo (o modo predefinido).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, em seguida, pode chamar o `unlockMessage` método à mensagem recebida (em vez do `deleteMessage` método). Isto irá fazer com que o Service Bus desbloqueie a mensagem na fila e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na fila, e se a aplicação não conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o barramento de serviço irá desbloquear automaticamente a mensagem e disponibilizá-lo ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes o `deleteMessage` pedido é emitido, em seguida, a mensagem será reenviada para a aplicação quando esta reiniciar. Isto é frequentemente designado *, pelo menos, uma vez* processamento; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, em seguida, adicionar lógica adicional para aplicações para processar a entrega da mensagem duplicada é recomendado. Isto é, frequentemente, conseguido utilizando o `getMessageId` método da mensagem, que permanece constante nas tentativas de entrega.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas de filas do Service Bus, consulte [filas, tópicos e subscrições] [ Queues, topics, and subscriptions] para obter mais informações.

Para obter mais informações, visite também o [Centro para programadores do PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once


