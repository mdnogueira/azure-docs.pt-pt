---
title: "Notificação Push de iOS com Hubs de Notificação para aplicações Xamarin | Microsoft Docs"
description: "Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Xamarin iOS."
services: notification-hubs
keywords: "notificações push ios, mensagens push, notificações push, mensagem push"
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 72a81fa0deb34ace77b8fb9b1a4e6b24ee164b35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>Notificação Push de iOS com Notification Hubs para aplicações Xamarin
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação iOS.
Irá criar uma aplicação Xamarin.iOS em branco que recebe notificações push, utilizando o [Serviço Apple Push Notification (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). Quando tiver terminado, poderá utilizar o Notification Hub para difundir notificações push para todos os dispositivos a executar a sua aplicação. O código concluído está disponível no exemplo [aplicação NotificationHubs][GitHub].

Este tutorial demonstra o cenário de difusão simples de mensagens push com Notification Hubs.

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial necessita do seguinte:

* [Xcode 6.0][Install Xcode]
* Um dispositivo compatível iOS 7.0 (ou versão posterior)
* Filiação do Programa de Programador de iOS
* [Xamarin Studio]
  
  > [!NOTE]
  > Devido aos requisitos de configuração para as notificações push do iOS, deve implementar e testar a aplicação de exemplo num dispositivo iOS físico (iPhone ou iPad) em vez do simulador.
  > 
  > 

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Notification Hubs para aplicações Xamarin iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub"></a>Configurar o Notification Hub
Esta secção explica-lhe como criar um novo Notification Hub e configurar a autenticação com APNs através do certificado push **.p12** que criou. Se pretender utilizar um Notification Hub que já tenha criado, pode avançar para o passo 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">

<li>

<p>Como queremos configurar a ligação do APNS, no Portal do Azure, abra as definições do Notification Hub, clique em <b>Serviços de Notificação</b> e, em seguida, clique no item <b>Apple (APNS)</b> na lista. Depois de concluído, clique em <b>Carregar Certificado</b> e selecione o certificado <b>.p12</b> que exportou anteriormente, bem como a palavra-passe para o certificado.</p>

<p>Certifique-se de que seleciona o modo <b>Sandbox</b>, uma vez que vai enviar mensagens push num ambiente de desenvolvimento. Utilize apenas a definição <b>Produção</b> se pretende enviar notificações push aos utilizadores que já tenham adquirido a aplicação da loja.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)

O Notification Hub já está configurado para trabalhar com APNs e tem as cadeias de ligação para registar a sua aplicação e enviar notificações push.

## <a name="connect-your-app-to-the-notification-hub"></a>Ligar a aplicação ao Notification Hub
#### <a name="create-a-new-project"></a>Criar um novo projeto
1. No Xamarin Studio, crie um novo projeto do iOS e selecione o modelo **API Unificada** > **Aplicação de Vista Única**.
   
     ![Xamarin Studio - Selecione o Tipo de Aplicação][31]
2. Adicione uma referência ao componente de Mensagens do Azure. Na vista Solução, clique com o botão direito na pasta **Componentes** do seu projeto e escolha **Obter Mais Componentes**. Pesquise o componente **Mensagens do Azure** e adicione-o ao projeto.
3. Em **AppDelegate.cs**, adicione o seguinte ao utilizar a instrução:
   
        using WindowsAzure.Messaging;
4. Declare uma instância do **SBNotificationHub**:
   
        private SBNotificationHub Hub { get; set; }
5. Crie uma classe **Constants.cs** com as seguintes variáveis:
   
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
6. Em **AppDelegate.cs**, atualize **FinishedLaunching()** para fazer corresponder o seguinte:
   
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
7. Substitua o método **RegisteredForRemoteNotifications()** em **AppDelegate.cs**:
   
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
8. Substitua o método **ReceivedRemoteNotification()** em **AppDelegate.cs**:
   
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
9. Crie o seguinte método **ProcessNotification()** em **AppDelegate.cs**:
   
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
   
   > [!NOTE]
   > Pode optar por substituir **FailedToRegisterForRemoteNotifications()** para processar situações como falha de ligação de rede. Isto é especialmente importante quando o utilizador inicia a aplicação no modo offline (por exemplo, Avião) e pretende processar cenários de mensagens push específicos para a sua aplicação.
   > 
   > 
10. Execute o aplicativo no seu dispositivo.

## <a name="sending-push-notifications"></a>Enviar Notificações Push
Pode testar a receção de notificações push na sua aplicação através do envio de notificações no [Portal do Azure] através da funcionalidade **Teste de Envio** no conjunto de ferramentas **Resolução de Problemas**, na página do Notification Hub, como mostrado no ecrã abaixo.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Normalmente, as notificações push são enviadas por um serviço de back-end, como Mobile Services ou ASP.NET, utilizando uma biblioteca compatível. Também pode utilizar a API REST diretamente para enviar mensagens push se uma biblioteca não está disponível para o seu cenário. 

Neste tutorial, vamos manter tudo simples e demonstrar apenas o modo de testar a aplicação cliente através do envio de notificações utilizando o SDK .NET para os Notification Hubs numa aplicação de consola em vez de um serviço de back-end. Recomendamos o tutorial [Utilizar Notification Hubs para notificações push a utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) como passo seguinte para enviar notificações de um back-end do ASP.NET. No entanto, as seguintes abordagens podem ser utilizadas para enviar notificações:

* **Interface REST**: Pode suportar notificações push em qualquer plataforma de back-end utilizando a [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **SDK .NET dosNotification Hubs do Microsoft Azure**: no Gestor de Pacotes Nuget para Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHub](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js**: [como utilizar os Hubs de Notificação do Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

**Aplicações Móveis**: para obter um exemplo de como enviar notificações de um back-end das Aplicações Móveis do Serviço de Aplicações do Azure integrado aos Hubs de Notificação, veja [Add push notifications to your mobile app (Adicionar notificações push à sua aplicação móvel)](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Java/PHP**: para obter um exemplo sobre como enviar notificações push com as APIs REST, veja “How to use Notification Hubs from Java/PHP” (Como utilizar os Hubs de Notificação de Java/PHP) ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

#### <a name="optional-send-push-notifications-from-a-net-console-app"></a>(Opcional) Enviar Notificações Push a partir de uma Aplicação da Consola .NET
Nesta secção, iremos enviar notificações push através de uma aplicação simples da consola .NET Neste exemplo, vamos mudar para um ambiente de desenvolvimento do Windows com o Visual Studio já instalado.

1. No Visual Studio, crie uma nova aplicação da consola Visual C#
   
       ![Visual Studio - Create a new console application][213]
2. No Visual Studio, clique em **Ferramentas**, clique no **Gestor de Pacotes NuGet** e, em seguida, em **Consola do Gestor de Pacotes**.
   
    A consola do gestor de pacote deve aparecer ancorada à parte inferior da sua área de trabalho do Visual Studio.
3. Na janela Consola do Gestor de Pacotes, defina o **Projeto predefinido** como o novo projeto da aplicação de consola e, em seguida, execute o seguinte comando na janela da consola:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Esta ação adiciona uma referência ao SDK dos Notification Hubs do Azure utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Pacote NuGet Microsoft.Azure.Notification Hubs</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
4. Abra o ficheiro `Program.cs` e adicione a seguinte instrução `using`, garantindo que podemos utilizar classes e funções do Azure dentro da sua classe principal:
   
        using Microsoft.Azure.NotificationHubs;
5. Na sua classe `Program`, adicione o seguinte método (não se esqueça de substituir a **cadeia de ligação** e o **nome do hub**):
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }
6. Adicione as seguintes linhas no método `Main`:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Prima a tecla F5 para executar a aplicação. Num espaço de segundos, deve aparecer uma notificação push no seu dispositivo. Quer esteja a utilizar uma rede Wi-Fi ou dados móveis, certifique-se de que tem uma ligação à Internet ativa no dispositivo.

Pode encontrar todos os payloads possíveis no [Guia de Programação de Notificação Push e Local] da Apple.

#### <a name="optional-send-notifications-from-a-mobile-service"></a>(Opcional) Enviar Notificações através de um Serviço Móvel
Nesta secção, iremos enviar notificações push com um serviço móvel através de um script de nó.

Para enviar uma notificação utilizando um serviço móvel, siga [Introdução aos Mobile Services] e, em seguida:

1. Inicie a sessão no [Portal Clássico do Azure] e selecione o seu serviço móvel.
2. Selecione o separador **Scheduler** na parte superior.
   
       ![Azure Classic Portal - Scheduler][215]
3. Crie uma nova tarefa agendada, insira um nome e selecione **A pedido**.
   
       ![Azure Classic Portal - Create new job][216]
4. Quando a tarefa é criada, clique no nome da tarefa. Em seguida, clique no separador **Script** na barra superior.
5. Insira o seguinte script dentro da função de agendador. Certifique-se de que substitui os marcadores com o seu nome do Notification Hub e a cadeia de ligação para *DefaultFullSharedAccessSignature* que adquiriu anteriormente. Clique em **Guardar**.
   
        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                  "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );
6. Clique em **Executar Uma Vez** na barra inferior. Deverá receber um alerta no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
Neste exemplo simples, difundiu notificações push para todos os seus dispositivos iOS. Para abordar utilizadores específicos, consulte o tutorial [Utilizar Notification Hubs para notificações push a utilizadores]. Se pretender segmentar os utilizadores por grupos de interesse, pode ler [Utilizar Notification Hubs para enviar notícias de última hora]. Saiba mais sobre como utilizar Notification Hubso em [Documentação de Orientação dos Notification Hubs] e em [Procedimentos de Notification Hubs para iOS].

<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introdução aos Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Documentação de Orientação dos Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimentos de Notification Hubs para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Utilizar Notification Hubs para notificações push a utilizadores]: /manage/services/notification-hubs/notify-users-aspnet
[Utilizar Notification Hubs para enviar notícias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet

[Guia de Programação de Notificação Push e Local]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Portal do Azure]: https://portal.azure.com
