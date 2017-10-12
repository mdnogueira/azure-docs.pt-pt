---
title: "Introdução às filas do Azure Service Bus | Microsoft Docs"
description: "Grave uma aplicação da consola C# que utilize filas das mensagens do Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.openlocfilehash: 99a377db6341d90d263b98e14227db61dd9beabd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-service-bus-queues"></a>Introdução às filas do Service Bus
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>O que será efetuado
Este tutorial contém os seguintes passos:

1. Criar um espaço de nomes do Service Bus com o Portal do Azure.
2. Criar uma fila do Service Bus com o portal do Azure.
3. Escrever uma aplicação de consola para enviar uma mensagem.
4. Escrever uma aplicação de consola para receber as mensagens enviadas no passo anterior.

## <a name="prerequisites"></a>Pré-requisitos
1. [Visual Studio 2015 ou superior](http://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
2. Uma subscrição do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um espaço de nomes com o Portal do Azure
Se já tiver criado um espaço de nomes das mensagens do Service Bus, avance para a secção [Criar uma fila com o portal do Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Criar uma fila com o Portal do Azure
Se já tiver criado uma fila do Service Bus, avance para a secção [Enviar mensagens para a fila](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Enviar mensagens para a fila
Para enviar mensagens para a fila, escrevemos uma aplicação de consola C# com o Visual Studio.

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

Abra o Visual Studio e crie um novo projeto de **Aplicação de consola (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Service Bus
1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique no separador **Procurar**, procure **Microsoft Azure Service Bus** e selecione o item **WindowsAzure.ServiceBus**. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.
   
    ![Selecionar um pacote NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Escrever código para enviar uma mensagem para a fila
1. Adicione a seguinte declaração `using` na parte superior do ficheiro Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adicione o seguinte código ao método `Main`. Defina a variável `connectionString` para a cadeia de ligação que obteve ao criar o espaço de nomes e defina `queueName` como o nome da fila que utilizou ao criar a fila.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    O ficheiro Program.cs deve ter o seguinte aspeto.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Execute o programa e verifique o portal do Azure: clique no nome da sua fila no painel **Descrição geral** do espaço de nomes. É apresentado o painel **Aspetos essenciais** da fila. Repare que o valor de **Contagem de Mensagens Ativas** deve ser agora 1. Sempre que executar a aplicação do remetente sem recuperar as mensagens, esse valor aumenta 1. Note também que o tamanho atual da fila aumenta sempre que a aplicação adiciona uma mensagem à fila.
   
      ![Tamanho da mensagem][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Receber mensagens da fila

1. Para receber as mensagens que acabou de enviar, crie uma nova aplicação de consola e adicione uma referência ao pacote NuGet do Service Bus, semelhante à aplicação do remetente anterior.
2. Adicione a seguinte declaração `using` na parte superior do ficheiro Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Adicione o seguinte código ao método `Main`. Defina a variável `connectionString` como a cadeia de ligação que obteve ao criar o espaço de nomes e defina `queueName` como o nome da fila que utilizou ao criar a fila.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    O ficheiro Program.cs deve ter o seguinte aspeto:
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. Execute o programa e consulte novamente o portal. Tenha em atenção que agora o valor da **Contagem de Mensagens Ativas** e de **Atual** é 0.
   
    ![Comprimento da fila][queue-message-receive]

Parabéns! Acabou de criar uma fila, enviar uma mensagem e receber uma mensagem.

## <a name="next-steps"></a>Passos seguintes

Consulte o nosso [repositório do GitHub com exemplos](https://github.com/Azure/azure-service-bus/tree/master/samples) que demonstram algumas das funcionalidades mais avançadas das mensagens do Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
