---
title: "Introdução ao armazenamento de filas e o Visual Studio ligado services (Serviços de cloud) | Microsoft Docs"
description: "Como começar a utilizar o armazenamento de filas do Azure num projeto de serviço em nuvem no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio ligada a serviços"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 7a6e58a62b4cfbf99641559363dd0c860cdf8af2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (projetos de serviços de nuvem)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o armazenamento de filas do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento do Azure num projeto de serviços em nuvem utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo.

Vamos mostrar-lhe como criar uma fila no código. Podemos também mostrar-lhe como realizar operações de fila básica, tais como adicionar, modificar, ler e remover mensagens da fila. Os exemplos são escritos no código c# e utilizar o [biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

O **adicionar serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto.

* Consulte [introdução ao armazenamento de filas do Azure através do .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações sobre a manipulação de filas no código.
* Consulte [documentação do Storage](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Storage do Azure.
* Consulte [documentação dos serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços em nuvem do Azure.
* Consulte [ASP.NET](http://www.asp.net) para obter mais informações sobre a programação de aplicações do ASP.NET.

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento.

## <a name="access-queues-in-code"></a>Aceder a filas no código
Para aceder a filas em projetos de serviços de nuvem do Visual Studio, terá de incluir os seguintes itens para qualquer ficheiro de origem do c#, que acedem ao armazenamento de filas do Azure.

1. Certifique-se de que as declarações de espaço de nomes na parte superior do ficheiro c# incluem estes **utilizando** instruções.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Obter um **CloudQueueClient** objeto referenciar os objetos de fila na sua conta de armazenamento.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Obter um **CloudQueue** objeto para fazer referência a uma fila específica.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Nota:** utilizar todo o código acima à frente o código de nos exemplos seguintes.

## <a name="create-a-queue-in-code"></a>Criar uma fila no código
Para criar a fila no código, basta adicionar uma chamada para **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem para uma fila
Para introduzir uma mensagem numa fila existente, crie um novo **CloudQueueMessage** objeto, em seguida, invoque o **AddMessage** método.

A **CloudQueueMessage** objeto pode ser criado a partir de uma cadeia (no formato UTF-8) ou uma matriz de bytes.

Eis um exemplo que introduz a mensagem "Olá, mundo".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem numa fila
Pode pré-visualizar a mensagem no início de um fila sem a remover da fila ao chamar o método **PeekMessage**.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem numa fila
Pode remover o seu código (anular fila) uma mensagem da fila em dois passos.

1. Chamar **GetMessage** para obterá a seguinte mensagem numa fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos.
2. Para acabar de remover a mensagem da fila, chame **DeleteMessage**.

Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seguinte código chamadas **DeleteMessage** imediatamente após a mensagem ser processada.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Utilize as opções adicionais para processar e remover a fila de mensagens
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

* Pode obter um lote de mensagens (até 32).
* Pode definir um tempo limite de invisibilidade superiores ou inferiores, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o método **GetMessages** para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem através de um ciclo **foreach**. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, como tal, 5 minutos após a chamada para **GetMessages**, as mensagens que não tenham sido eliminadas ficarão visíveis novamente.

Eis um exemplo:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O método **FetchAttributes** pede ao serviço Fila que devolva os atributos de fila, incluindo a contagem de mensagens. O **ApproximateMethodCount** propriedade devolve o último valor obtido pelo **FetchAttributes** método, sem chamar o serviço fila.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Utilizar o padrão Async-Await com APIs de fila comum do Azure
Este exemplo mostra como utilizar o padrão Async-Await com APIs de fila do Azure comuns. O exemplo chama a versão assíncrona de cada um dos métodos em questão, isto pode ser visto pelo **Async** pós-correção de cada método. Quando é utilizado um método async o async-await padrão suspende a execução local até que a chamada seja concluída. Este comportamento permite que o thread atual efetue outro trabalho que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta global da sua aplicação. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Async-Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Eliminar uma fila
Para eliminar uma fila e todas as mensagens nela contidas, chame o método **Eliminar** no objeto de fila.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

