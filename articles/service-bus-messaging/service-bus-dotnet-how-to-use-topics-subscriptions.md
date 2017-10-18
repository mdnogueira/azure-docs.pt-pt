---
title: "Introdução aos tópicos e subscrições do Azure Service Bus | Microsoft Docs"
description: "Grave uma aplicação da consola C# que utilize tópicos e subscrições das mensagens do Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 3646d14be662af0fdf80790cb53ddc581b33a146
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-service-bus-topics"></a>Introdução aos tópicos do Service Bus

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este tutorial contém os seguintes passos:

1. Criar um espaço de nomes do Service Bus com o Portal do Azure.
2. Criar um tópico do Service Bus com o portal do Azure.
3. Criar uma subscrição do Service Bus com o portal do Azure.
4. Gravar uma aplicação de consola para enviar uma mensagem ao tópico.
5. Gravar uma aplicação de consola para receber uma mensagem da subscrição.

## <a name="prerequisites"></a>Pré-requisitos

1. [Visual Studio 2015 ou superior](http://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
2. Uma subscrição do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um espaço de nomes com o Portal do Azure

Se já tiver criado um espaço de nomes das mensagens do Service Bus, avance para a secção [Criar um tópico com o portal do Azure](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Criar um tópico com o portal do Azure

1. Inicie sessão no [Portal do Azure][azure-portal].
2. No painel de navegação à esquerda do portal, clique em **Service Bus** (se não vir **Service Bus**, clique em **Mais serviços**).
3. Clique no espaço de nomes no qual gostaria de criar o tópico. Será apresentado o painel de descrição geral do espaço de nomes:
   
    ![Criar um tópico][createtopic1]
4. No painel **Espaço de nomes do Service Bus**, clique em **Tópicos** e clique em **Adicionar tópico**.
   
    ![Selecionar tópicos][createtopic2]
5. Introduza um nome para o tópico e desmarque a opção **Ativar criação de partições**. Deixe as outras opções com os valores predefinidos.
   
    ![Selecionar Novo][createtopic3]
6. Na parte inferior do painel, clique em **Criar**.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Criar uma subscrição para o tópico

1. No painel de recursos do portal, clique no espaço de nomes que criou no passo 1 e clique no nome do tópico que criou no passo 2.
2. Na parte superior do painel de descrição geral, clique no sinal "+" junto a **Subscrição**, para adicionar uma subscrição para esse tópico.

    ![Criar subscrição][createtopic4]

3. Introduza um nome para a subscrição. Deixe as outras opções com os valores predefinidos.

## <a name="4-send-messages-to-the-topic"></a>4. Enviar mensagens para o tópico

Para enviar mensagens para o tópico, gravamos uma aplicação de consola C# com o Visual Studio.

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

Abra o Visual Studio e crie um novo projeto de **Aplicação de consola (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Service Bus

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique no separador **Procurar**, procure **Windows Azure Service Bus** e selecione o item **WindowsAzure.ServiceBus**. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.
   
    ![Selecionar um pacote NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>Escreva um código para enviar uma mensagem para o tópico

1. Adicione a seguinte declaração `using` na parte superior do ficheiro Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adicione o seguinte código ao método `Main`. Defina a variável `connectionString` para a cadeia de ligação que obteve ao criar o espaço de nomes e defina `topicName` para o nome que utilizou ao criar o tópico.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Eis o aspeto do ficheiro Program.cs.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Execute o programa e verifique o portal do Azure: clique no nome do seu tópico no painel **Descrição geral** do espaço de nomes. É apresentado o painel **Aspetos essenciais** do tópico. Nas subscrições apresentadas na parte inferior do painel, tenha em atenção que o valor da **Contagem de Mensagens** relativamente a cada subscrição deve agora ser 1. Sempre que executar a aplicação do remetente sem recuperar as mensagens (conforme descrito na próxima secção), esse valor aumenta 1. Note também que o tamanho atual do tópico aumenta o valor **Atual** no painel **Aspetos essenciais** sempre que a aplicação adicionar uma mensagem ao tópico/subscrição.
   
      ![Tamanho da mensagem][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Receber mensagens da subscrição

1. Para receber a mensagem ou as mensagens que acabou de enviar, crie uma nova aplicação de consola e adicione uma referência ao pacote NuGet do Service Bus, semelhante à aplicação do remetente anterior.
2. Adicione a seguinte declaração `using` na parte superior do ficheiro Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Adicione o seguinte código ao método `Main`. Defina a variável `connectionString` para a cadeia de ligação que obteve ao criar o espaço de nomes e defina `topicName` para o nome que utilizou ao criar o tópico. Certifique-se também de substituir `<your subscription name>` pelo nome da subscrição que criou no passo 3. 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. Execute o programa e consulte novamente o portal. Tenha em atenção que agora o valor da **Contagem de Mensagens** e de **Atual** é 0.
   
    ![Comprimento do tópico][topic-message-receive]

Parabéns! Criou agora um tópico e uma subscrição, enviou uma mensagem e recebeu essa mensagem.

## <a name="next-steps"></a>Passos seguintes

Consulte o nosso [repositório do GitHub com exemplos](https://github.com/Azure/azure-service-bus/tree/master/samples) que demonstram algumas das funcionalidades mais avançadas das mensagens do Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
