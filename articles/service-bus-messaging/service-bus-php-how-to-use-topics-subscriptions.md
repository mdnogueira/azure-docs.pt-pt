---
title: "Como utilizar os tópicos do Service Bus com o PHP | Microsoft Docs"
description: "Saiba como utilizar os tópicos do Service Bus com o PHP no Azure."
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: 
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: 4763b172375668213372e6f4f8fc87431c430e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Como utilizar os tópicos do Service Bus e as subscrições com PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo mostra como utilizar os tópicos do Service Bus e subscrições. Os exemplos são escritos no PHP e a utilização de [Azure SDK para PHP](../php-download-sdk.md). Os cenários abrangidos incluem **criação de tópicos e subscrições**, **criar filtros de subscrição**, **envio de mensagens para um tópico**, **receber mensagens de uma subscrição**, e **eliminar tópicos e subscrições**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para criar uma aplicação PHP que acede ao serviço Blob do Azure está para efeitos de referência de classes no [Azure SDK para PHP](../php-download-sdk.md) de dentro do seu código. Pode utilizar as ferramentas de desenvolvimento para criar a aplicação ou o bloco de notas.

> [!NOTE]
> A instalação do PHP também tem de ter o [OpenSSL extensão](http://php.net/openssl) instalado e ativado.
> 
> 

Este artigo descreve como utilizar funcionalidades do serviço que podem ser chamadas dentro de uma aplicação PHP localmente, ou no código em execução dentro de uma função da web do Azure, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obtenha o cliente do Azure bibliotecas
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Para utilizar as APIs de barramento de serviço:

1. Referenciar o ficheiro do Carregador automático utilizando o [require_once] [ require-once] instrução.
2. Referência a quaisquer classes que poderá utilizar.

O exemplo seguinte mostra como inclui o Carregador automático ficheiros e a referência a **ServiceBusService** classe.

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) parte do princípio de que instalou as bibliotecas de cliente do PHP do Azure através do compositor. Se tiver instalado as bibliotecas manualmente ou como um pacote PEAR, tem de fazer referência a **WindowsAzure.php** Carregador automático ficheiros.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos a seguir, a `require_once` instrução sempre é apresentada, mas apenas as classes de necessárias para o exemplo para executar são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de barramento de serviço
Ao instanciar um cliente do Service Bus tem de ter uma cadeia de ligação válido neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Onde `Endpoint` é, geralmente, com o formato de `https://[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço do Azure tem de utilizar o `ServicesBuilder` classe. Pode:

* Passe a cadeia de ligação direta.
* Utilize o **CloudConfigurationManager (CCM)** para verificar a várias origens externas para a cadeia de ligação:
  * Por predefinição vem com suporte para uma origem externa - variáveis de ambientais.
  * Pode adicionar novas origens ao alargar o `ConnectionStringSource` classe.

Para os exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Criar um tópico
Pode efetuar operações de gestão para tópicos do Service Bus através de `ServiceBusRestProxy` classe. A `ServiceBusRestProxy` objecto é construído através de `ServicesBuilder::createServiceBusService` método de fábrica com uma cadeia de ligação adequado que encapsula as token permissões para geri-lo.

O exemplo seguinte mostra como ao instanciar um `ServiceBusRestProxy` e chame `ServiceBusRestProxy->createTopic` para criar um tópico com o nome `mytopic` dentro de um `MySBNamespace` espaço de nomes:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> Pode utilizar o `listTopics` método no `ServiceBusRestProxy` objetos para verificar se um tópico com um nome especificado já existe num espaço de nomes do serviço.
> 
> 

## <a name="create-a-subscription"></a>Criar uma subscrição
Subscrições de tópico também são criadas com o `ServiceBusRestProxy->createSubscription` método. As subscrições têm um nome e podem ter um filtro opcional que restringe o conjunto de mensagens transmitidas para a fila virtual da subscrição.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
O filtro **MatchAll** é o filtro predefinido utilizado se não for especificado qualquer filtro na criação de uma nova subscrição. Quando o **MatchAll** filtro é utilizado, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição com o nome 'mysubscription' e utiliza a predefinição **MatchAll** filtro.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode definir filtros que permitem especificar quais as mensagens enviadas para um tópico a serem apresentadas numa subscrição de tópico específica. O tipo mais flexível do filtro suportado pelas subscrições é a [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter), que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para mais informações sobre SqlFilters, consulte [Sqlexpression propriedade][sqlfilter].

> [!NOTE]
> Cada regra em subscrições processa mensagens a receber de forma independente, adicionar as mensagens de resultado para a subscrição. Além disso, cada nova subscrição tem uma predefinição **regra** objeto com um filtro que adiciona todas as mensagens do tópico à subscrição. Para receber apenas as mensagens que correspondam ao seu filtro, tem de remover a regra predefinida. Pode remover a regra predefinida, utilizando o `ServiceBusRestProxy->deleteRule` método.
> 
> 

O exemplo seguinte cria uma subscrição designada `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens com um personalizado `MessageNumber` propriedade superior a 3. Consulte [enviar mensagens para um tópico](#send-messages-to-a-topic) para obter informações sobre como adicionar propriedades personalizadas para as mensagens.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Tenha em atenção que este código requer a utilização do espaço de nomes adicionais: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Da mesma forma, o exemplo seguinte cria uma subscrição designada `LowMessages` com um `SqlFilter` que seleciona apenas as mensagens com um `MessageNumber` propriedade inferior ou igual a 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Agora, quando é enviada uma mensagem para o `mytopic` tópico, é sempre entregue para o `mysubscription` subscrição e entregue seletivamente subscrever o `HighMessages` e `LowMessages` subscrições (consoante o após o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico de barramento de serviço, as chamadas de aplicação a `ServiceBusRestProxy->sendTopicMessage` método. O código seguinte mostra como enviar uma mensagem para o `mytopic` tópico que criou anteriormente no `MySBNamespace` espaço de nomes de serviço.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

As mensagens enviadas para tópicos do Service Bus são instâncias do [BrokeredMessage] [ BrokeredMessage] classe. [BrokeredMessage] [ BrokeredMessage] objetos têm um conjunto de propriedades padrão e métodos, bem como propriedades que podem ser utilizadas para reter propriedades específicas de aplicações personalizadas. O exemplo seguinte mostra como enviar 5 mensagens de teste para o `mytopic` tópico que criou anteriormente. O `setProperty` método é utilizado para adicionar uma propriedade personalizada (`MessageNumber`) para cada mensagem. Tenha em atenção que o `MessageNumber` varia de acordo com o valor da propriedade em cada mensagem (pode utilizar este valor para determinar quais as subscrições que recebem, conforme mostrado no [criar uma subscrição](#create-a-subscription) secção):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. Este limite superior no tamanho do tópico é de 5 GB. Para mais informações sobre as quotas, consulte [quotas de Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
A melhor forma para receber mensagens de uma subscrição é utilizar um `ServiceBusRestProxy->receiveSubscriptionMessage` método. Podem ser recebidas mensagens em dois modos diferentes: [ *ReceiveAndDelete* e *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** é a predefinição.

Na utilização do modo [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode), a receção é uma operação única; ou seja, quando o Service Bus recebe um pedido de leitura para uma mensagem numa subscrição, aquele marca a mensagem como consumida e devolve a mesma à aplicação. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * modo é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Porque o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha.

Predefinida [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) modo, receber uma mensagem torna-se uma operação de duas fases, o que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazena a mesma forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao transmitir a mensagem recebida para `ServiceBusRestProxy->deleteMessage`. Quando o Service Bus vê a `deleteMessage` chamada, marca a mensagem como consumida e removê-lo a partir da fila.

O exemplo seguinte mostra como receber e a processar uma mensagem utilizando [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) modo (o modo predefinido). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como: processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, em seguida, pode chamar o `unlockMessage` método à mensagem recebida (em vez do `deleteMessage` método). Tal faz com que o Service Bus desbloqueie a mensagem na fila e torna a mesma disponível para ser novamente recebida, quer pela mesma aplicação de consumo quer por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na fila, e se a aplicação não conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus desbloqueia automaticamente a mensagem e torná-lo está disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes o `deleteMessage` pedido é emitido, em seguida, a mensagem é reenviada para a aplicação quando esta reiniciar. Isto é frequentemente designado *, pelo menos, uma vez* processamento; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, em seguida, os programadores de aplicações devem acrescentar uma lógica adicional para aplicações para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido utilizando o `getMessageId` método da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Para eliminar um tópico ou uma subscrição, utilize o `ServiceBusRestProxy->deleteTopic` ou `ServiceBusRestProxy->deleteSubscripton` métodos, respetivamente. Tenha em atenção que também eliminar um tópico elimina quaisquer subscrições registadas com o tópico.

O exemplo seguinte mostra como eliminar um tópico com o nome `mytopic` e respetivas subscrições registadas.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

Utilizando o `deleteSubscription` método, pode eliminar uma subscrição de forma independente:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas de filas do Service Bus, consulte [filas, tópicos e subscrições] [ Queues, topics, and subscriptions] para obter mais informações.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
