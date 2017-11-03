---
title: Como utilizar o armazenamento de filas do PHP | Microsoft Docs
description: "Saiba como utilizar o serviço de armazenamento de filas do Azure para criar e eliminar filas, inserir, obter e eliminar as mensagens. Exemplos são escritos no PHP."
documentationcenter: php
services: storage
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 7582b208-4851-4489-a74a-bb952569f55b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 5fa4e35184b39bd672bfc8b19b2d41acb164abdf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-queue-storage-from-php"></a>Como utilizar o Armazenamento de filas do PHP
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como efetuar cenários comuns utilizando o serviço de armazenamento de filas do Azure. Os exemplos são escritos através de classes do [biblioteca de clientes do Storage do Azure para PHP][download]. Os cenários cobertos incluem a inserir, leitura, obter e eliminar mensagens de filas, bem como criar e eliminar filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para criar uma aplicação PHP que acede ao armazenamento de filas do Azure é que a referência de classes no [biblioteca de clientes do Storage do Azure para PHP] [ download] de dentro do seu código. Pode utilizar as ferramentas de desenvolvimento para criar a sua aplicação, incluindo o bloco de notas.

Neste guia, utilize as funcionalidades de serviço de armazenamento de fila que podem ser chamadas dentro de uma aplicação PHP localmente, ou no código em execução dentro de uma função da web do Azure, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de cliente do Azure
[!INCLUDE [get-client-libraries](../../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar a sua aplicação para aceder ao armazenamento de filas
Para utilizar as APIs para armazenamento de filas do Azure, tem de:

1. O ficheiro de Carregador automático de referência utilizando o [require_once] instrução.
2. Referência a quaisquer classes que poderá utilizar.

O exemplo seguinte mostra como inclui o Carregador automático ficheiros e a referência a **ServicesBuilder** classe.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Common\ServicesBuilder;

```

Nos exemplos a seguir, a `require_once` instrução é sempre apresentada, mas apenas as classes que são necessárias para o exemplo para executar são referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
Ao instanciar um cliente de armazenamento de filas do Azure, primeiro tem de ter uma cadeia de ligação válido. Segue-se o formato da cadeia de ligação de serviço fila.

Para aceder a um serviço em direto:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para aceder ao armazenamento emulador:

```php
UseDevelopmentStorage=true
```

Para criar qualquer cliente de serviço do Azure, tem de utilizar o **ServicesBuilder** classe. Pode utilizar as seguintes técnicas:

* Passe a cadeia de ligação direta.
* Utilize variáveis de ambiente na sua aplicação Web para armazenar a cadeia de ligação. Consulte [definições de configuração de aplicação web do Azure](../../app-service/web-sites-configure.md) documento para configurar as cadeias de ligação.
Para os exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila
A **QueueRestProxy** objeto permite-lhe criar uma fila utilizando o **createQueue** método. Quando criar uma fila, pode definir as opções na fila, mas fazê-lo, por isso, não é necessário. (O exemplo abaixo mostra como definir os metadados de uma fila.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueRestProxy->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Não deverá confiar na sensibilidade às maiúsculas e minúsculas para chaves de metadados. Todas as chaves são lidos a partir do serviço em minúsculas.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem para uma fila
Para adicionar uma mensagem para uma fila, utilize **QueueRestProxy -> createMessage**. O método aceita o nome da fila, o texto da mensagem e opções de mensagens (que são opcionais).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Create message.
    $builder = new ServicesBuilder();
    $queueRestProxy->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Pré-visualização da mensagem seguinte
Pode pré-visualizar uma mensagem (ou mensagens) no início de um fila sem a remover da fila ao chamar **QueueRestProxy -> peekMessages**. Por predefinição, o **peekMessage** método devolve uma única mensagem, mas pode alterar esse valor utilizando o **PeekMessagesOptions -> setNumberOfMessages** método.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Remover a mensagem seguinte da fila
O código remove uma mensagem da fila em dois passos. Em primeiro lugar, tem de chamar **QueueRestProxy -> listMessages**, o que torna a mensagem invisível para quaisquer outro código que está a ler da fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. (Se a mensagem não está a ser eliminada neste período de tempo, torna visível na fila de novo.) Para acabar de remover a mensagem da fila, tem de chamar **QueueRestProxy -> deleteMessage**. Este processo de dois passos da remoção de uma mensagem garante que quando o seu código não consegue processar uma mensagem devido uma falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tente novamente. As chamadas de código **deleteMessage** imediatamente após a mensagem ser processada.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueRestProxy->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Alterar os conteúdos de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila ao chamar **QueueRestProxy -> updateMessage**. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O seguinte código atualiza a mensagem de filas com novos conteúdos e define o tempo limite de visibilidade para expandir a 60 segundos. Isto guarda o estado do trabalho que está associada a mensagem e atribui ao cliente outro minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e, se a mensagem fosse repetida mais do que *n* vezes, eliminá-la-ia. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueRestProxy->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueRestProxy->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Opções adicionais para anular a colocação de mensagens
Existem duas formas de personalizar a obtenção de mensagens numa fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Segundo, pode definir um tempo limite de visibilidade superiores ou inferiores, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o **getMessages** método para obter 16 mensagens numa chamada. Em seguida, processa cada mensagem utilizando um **para** ciclo. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueRestProxy->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O **QueueRestProxy -> getQueueMetadata** método pede ao serviço de fila para devolver os metadados sobre a fila. Chamar o **getApproximateMessageCount** método no objeto devolvido fornece uma contagem de é quantidade de mensagens numa fila. A contagem apenas é aproximada porque as mensagens podem ser adicionadas ou removidas depois de ter o serviço fila responde ao seu pedido.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Eliminar uma fila
Para eliminar uma fila e todas as mensagens na mesma, chame o **QueueRestProxy -> deleteQueue** método.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Delete queue.
    $queueRestProxy->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do armazenamento de filas do Azure, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas:

* Visite o [referência da API para a biblioteca de clientes do Storage do Azure PHP](http://azure.github.io/azure-storage-php/)
* Consulte o [exemplo avançadas fila](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Para obter mais informações, consulte também o [Centro para programadores do PHP](/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com

