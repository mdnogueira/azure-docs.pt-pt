---
title: "Arquitetura de Push de Notification Hubs – Enterprise"
description: "Documentação de orientação sobre como utilizar Notification Hubs do Azure num ambiente empresarial"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: d71c706a7db570e88339c4ff7af05a48c05df65b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enterprise-push-architectural-guidance"></a>Orientação de arquitetura das notificações push empresariais
As empresas hoje em dia pautam gradualmente mover para criar aplicações móveis para qualquer os respetivos utilizadores finais (externo) ou para os empregados (internos). Têm existente back-end sistemas no local seja mainframes ou algumas aplicações de LoB que tem de ser integradas na arquitetura de aplicações móveis. Este guia irá falar sobre como melhor efetuar esta integração Recomendamos possíveis soluções para cenários comuns.

É um requisito frequente para envio de notificações push aos utilizadores através da respetiva aplicação móvel quando ocorre um evento de interesse nos sistemas de back-end. Por exemplo, um cliente bank que tenha a aplicação de banca o banco no respetivo iPhone pretende ser notificado quando é efetuado um débito acima uma determinada quantidade da sua conta ou um cenário de intranet onde pretende não ser um empregado do departamento financeiro que possua uma aplicação de aprovação de orçamento no seu Windows Phone ified quando ele obtém um pedido de aprovação.

A conta de bank ou processamento de aprovação é provável que ser efetuada em algumas do sistema de back-end que tem de iniciar um push para o utilizador. Podem existir vários destes sistemas de back-end que tem todos os criar o mesmo tipo de lógica de implementar push quando um evento é acionado uma notificação. A complexidade aqui reside na integração vários sistemas de back-end, juntamente com um sistema de emissão única onde os utilizadores finais podem ter subscrever notificações diferentes e até mesmo poderá várias aplicações móveis, por exemplo, no caso de aplicações móveis intranet onde um aplicações móveis, poderão querer receber notificações de vários destes sistemas de back-end. Os sistemas de back-end não souber ou precisa de saber semântica/tecnologia de push para uma solução comum aqui tradicionalmente foi apresentar um componente que consulta os sistemas de back-end para quaisquer eventos de interesse e é responsável por enviar as mensagens de push para o cliente.
Aqui iremos falar sobre uma solução ainda mais a utilizar o Service Bus do Azure - modelo tópico/subscrição que irá reduzir a complexidade, ao tornar a solução escalável.

Segue-se a arquitetura geral da solução (generalizado com várias aplicações móveis, mas igualmente aplicáveis quando existe apenas uma aplicação móvel)

## <a name="architecture"></a>Arquitetura
![][1]

A informação de chave neste diagrama da arquitetura é Service Bus do Azure que fornece um modelo de programação tópicos/subscrições (mais informações sobre as-lo no [programação do Service Bus Pub/Sub]). O recetor, que neste caso, é o back-end móvel (normalmente [o serviço móvel do Azure], que vai iniciar um push para aplicações móveis) não receber mensagens diretamente a partir de sistemas de back-end, mas em vez disso, temos um intermédio camada de abstração fornecida pelo [Service Bus do Azure] que permite ao back-end móvel receber mensagens de um ou mais sistemas de back-end. Um tópico de barramento de serviço tem de ser criada para cada um dos sistemas back-end por exemplo, a conta, HR, financeiros que são basicamente "tópicos" de interesse que vai iniciar mensagens a enviar como notificações push. Os sistemas de back-end irão enviar mensagens para estes tópicos. Um back-end do Mobile, pode subscrever uma ou mais tópicos através da criação de uma subscrição do Service Bus. Isto irá entitle o back-end móvel receba uma notificação do sistema de back-end correspondente. Back-end móvel continua a escutar mensagens nas suas subscrições e, assim que chega uma mensagem, fica novamente e envia-o como notificação para o seu hub de notificação. Os Notification hubs, em seguida, eventualmente fornece a mensagem para a aplicação móvel. Por isso, para resumir os componentes chave, temos:

1. Sistemas de back-end (sistemas LoB/legado)
   * Cria o tópico de barramento de serviço
   * Envia a mensagem
2. Back-end móvel
   * Cria a subscrição do serviço
   * Recebe a mensagem (do sistema de back-end)
   * Envia a notificação para clientes (através do Hub de notificação do Azure)
3. Aplicações móveis
   * Recebe e apresentar a notificação

### <a name="benefits"></a>Vantagens:
1. O desemparelhamento entre o recetor (móvel/serviço de aplicações através do Hub de notificação) e o remetente (sistemas de back-end) permite que os sistemas de back-end adicionais que está a ser integrados com alterações mínimas.
2. Isto também facilita o cenário de várias aplicações móveis, a capacidade para receber eventos de um ou mais sistemas de back-end.  

## <a name="sample"></a>Exemplo:
### <a name="prerequisites"></a>Pré-requisitos
Deve efetuar os seguintes tutoriais para familiarizar-com os conceitos, bem como criação comuns & passos de configuração:

1. [programação do Service Bus Pub/Sub] -explica os detalhes do trabalho com tópicos do Service Bus/subscrições, como criar um espaço de nomes que contém tópicos sobre as subscrições, como enviar e receber mensagens dos mesmos.
2. [Notification Hubs – tutorial Windows Universal] -explica como configurar uma aplicação da loja Windows e utilizar os Notification Hubs para registar e, em seguida, receber notificações.

### <a name="sample-code"></a>Código de exemplo
O código de exemplo completa está disponível em [amostras de Hub de notificação]. Este é dividido em três componentes:

1. **EnterprisePushBackendSystem**
   
    a. Este projeto utiliza o *Windowsazure* pacote Nuget e baseado nas [programação do Service Bus Pub/Sub].
   
    b. Esta é uma simple c# aplicação de consola para simular um sistema de LoB que inicia a mensagem a entregar para a aplicação móvel.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic where we will send notifications
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic`é utilizado para criar o tópico de barramento de serviço onde iremos enviar mensagens.
   
        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already
   
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }
   
    d. `SendMessage`é utilizada para enviar as mensagens para este tópico de barramento de serviço. Aqui iremos está simplesmente a enviar um conjunto de mensagens aleatórios para o tópico periodicamente para efeitos de amostra. Normalmente, haverá um sistema de back-end que vai enviar mensagens quando ocorre um evento.
   
        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);
   
            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };
   
            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);
   
                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);
   
                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);
   
                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }
2. **ReceiveAndSendNotification**
   
    a. Este projeto utiliza o *Windowsazure* e *Microsoft.Web.WebJobs.Publish* Nuget pacotes e baseia-se no [programação do Service Bus Pub/Sub].
   
    b. Este é outra c# aplicação de consola que iremos executar como um [trabalho Web do Azure] , uma vez que tem de executar continuamente para escutar mensagens dos sistemas LoB/back-end. Isto fará parte do seu back-end móvel.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription which will receive messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription`é utilizado para criar uma subscrição de barramento de serviço para o tópico, onde o sistema de back-end irá enviar mensagens. Dependendo do cenário de negócio, este componente irá criar uma ou mais subscrições para tópicos correspondentes (por exemplo, alguns podem receber mensagens do sistema de RH, alguns a partir do sistema de finanças e assim sucessivamente)
   
        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }
   
    d. ReceiveMessageAndSendNotification é utilizada para ler a mensagem do tópico através da sua subscrição e se a leitura for bem sucedida craft uma notificação (no cenário de amostra uma notificação de alerta nativo do Windows) para ser enviado para a aplicação móvel utilizar o Azure Hubs de notificação.
   
        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");
   
            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);
   
            Client.Receive();
   
            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";
   
                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");
   
                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);
   
                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }
   
    e. Para publicar-o como um **WebJob**, o botão direito do rato clique na solução no Visual Studio e selecione **publicar como trabalho Web**
   
    ![][2]
   
    f. Selecione o perfil de publicação e criar um novo Web site do Azure, se não existir já que irá alojar esta WebJob e assim que tiver o Web site, em seguida, **publicar**.
   
    ![][3]
   
    g. Configurar a tarefa ser "Executar continuamente", para que quando iniciar sessão a [Portal clássico do Azure] deverá ver algo semelhante ao seguinte:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Esta é uma aplicação da loja Windows que irá receber notificações de alerta do trabalho Web em execução como parte do seu back-end móvel e apresentá-lo. Isto baseia-se no [Notification Hubs – tutorial Windows Universal].  
   
    b. Certifique-se de que a aplicação está ativada para receber notificações de alerta.
   
    c. Certifique-se de que os Hubs de notificação seguinte código de registo está a ser chamado a aplicação iniciar cópia de segurança (depois de substituir o *HubName* e *DefaultListenSharedAccessSignature*:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Exemplo em execução:
1. Certifique-se de que o trabalho Web é executada com êxito e agendada para a opção "Executar continuamente".
2. Execute o **EnterprisePushMobileApp** que irá iniciar a aplicação da loja Windows.
3. Execute o **EnterprisePushBackendSystem** mensagens da aplicação de consola que irá simular o back-end de LoB e começar a enviar e deverá ver notificações de alerta apresentação semelhante ao seguinte:
   
    ![][5]
4. As mensagens foram originalmente enviadas para tópicos do Service Bus que estava a ser monitorizado pelo subscrições do Service Bus na sua tarefa de Web. Depois de uma mensagem foi recebida, uma notificação foi criada e enviada para a aplicação móvel. Pode examine os registos de WebJob para confirmar o processamento quando acede a hiperligação de registos no [Portal clássico do Azure] a tarefa de Web:
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[amostras de Hub de notificação]: https://github.com/Azure/azure-notificationhubs-samples
[o serviço móvel do Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Service Bus do Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[programação do Service Bus Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[trabalho Web do Azure]: ../app-service/web-sites-create-web-jobs.md
[Notification Hubs – tutorial Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portal clássico do Azure]: https://manage.windowsazure.com/
