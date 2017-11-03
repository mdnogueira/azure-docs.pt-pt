---
title: "Introdução ao armazenamento de filas e o Visual Studio ligado serviços (ASP.NET Core) | Microsoft Docs"
description: "Como começar a utilizar o armazenamento de filas do Azure num projeto ASP.NET Core no Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 394344c0e126472b97c2e8f721c8c8d6514a17dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao armazenamento de filas e o Visual Studio ligado serviços (ASP.NET Core)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o armazenamento de filas do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento do Azure num projeto ASP.NET Core utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo. O **adicionar serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto.

Armazenamento de filas do Azure é um serviço para armazenar grandes quantidades de mensagens que podem ser acedidas de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ser até 64 quilobytes (KB) e uma fila pode conter milhões de mensagens, até ao limite de capacidade total de uma conta de armazenamento.

Para começar, terá primeiro de criar uma fila do Azure na sua conta de armazenamento. Vamos mostrar-lhe como criar uma fila no código. Podemos também mostrar-lhe como realizar operações de fila básica, tais como adicionar, modificar, ler e remover mensagens da fila. Os exemplos são escritos no C\# código e utilizar a biblioteca de clientes de armazenamento do Azure para .NET. Para mais informações sobre o ASP.NET, consulte [ASP.NET](http://www.asp.net).

**Nota:** algumas das APIs que efetuar chamadas para o armazenamento do Azure no ASP.NET Core são assíncronas. Consulte [programação assíncrona com Async-Await](http://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código abaixo parte do princípio de métodos de programação assíncrona estão a ser utilizados.

* Consulte [introdução ao armazenamento de filas do Azure através do .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações sobre manipular programaticamente filas.
* Consulte [documentação do Storage](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Storage do Azure.
* Consulte [documentação dos serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços em nuvem do Azure.
* Consulte [ASP.NET](http://www.asp.net) para obter mais informações sobre a programação de aplicações do ASP.NET.

## <a name="access-queues-in-code"></a>Aceder a filas no código
Para aceder a filas em projetos de ASP.NET Core, tem de incluir os seguintes itens para qualquer origem ficheiro c# que acede ao armazenamento de filas do Azure.

1. Certifique-se de que as declarações de espaço de nomes na parte superior do ficheiro c# incluem estes **utilizando** instruções.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Obter um **CloudQueueClient** objeto referenciar os objetos de fila na sua conta de armazenamento.  
   
        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Obter um **CloudQueue** objeto para fazer referência a uma fila específica.
   
        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Nota:** utilizar todo o código acima à frente o código de nos exemplos seguintes.

### <a name="create-a-queue-in-code"></a>Criar uma fila no código
Para criar a fila do Azure no código, basta adicionar uma chamada para **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem para uma fila
Para introduzir uma mensagem numa fila existente, crie um novo **CloudQueueMessage** objeto, em seguida, invoque o **AddMessageAsync** método.

A **CloudQueueMessage** objeto pode ser criado a partir de uma cadeia (no formato UTF-8) ou uma matriz de bytes.

Eis um exemplo que introduz a mensagem "Olá, mundo".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem numa fila
Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila ao chamar o **PeekMessageAsync** método.

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem numa fila
Pode remover o seu código (anular) uma mensagem da fila em dois passos.

1. Chamar **GetMessageAsync** para obterá a seguinte mensagem numa fila. Uma mensagem devolvida por **GetMessageAsync** torna-se invisível para quaisquer outras código ao ler as mensagens desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos.
2. Para acabar de remover a mensagem da fila, chame **DeleteMessageAsync**.

Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seguinte código chamadas **DeleteMessageAsync** imediatamente após a mensagem ser processada.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>Tirar partido das opções adicionais para mensagens dequeuing
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.
Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o método **GetMessages** para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem através de um ciclo **foreach**. Também define o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, transmitido como tal, após 5 minutos após a chamada para **GetMessages**, as mensagens que não tenham sido eliminadas ficam visíveis novamente.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O **FetchAttributes** método pede ao serviço de fila para obter os atributos de fila, incluindo a contagem de mensagens. O **ApproximateMethodCount** propriedade devolve o último valor obtido pelo **FetchAttributes** método, sem chamar o serviço fila.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utilizar o padrão Async-Await com APIs de fila comum
Este exemplo mostra como utilizar o padrão Async-Await com APIs de fila comum. O exemplo chama a versão assíncrona de cada um dos métodos em questão. Isto pode ser visto pela pós-correção assíncrono de cada método. Quando é utilizado um método assíncrono, o padrão Async-Await suspende a execução local até que a chamada ser concluída. Este comportamento permite que o thread atual efetue outro trabalho que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta global da sua aplicação. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Async e -Await (c# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to add to the queue.
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message.
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## <a name="delete-a-queue"></a>Eliminar uma fila
Para eliminar uma fila e todas as mensagens nela contidas, chame o método **Eliminar** no objeto de fila.

    // Delete the queue.
    messageQueue.Delete();


## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

