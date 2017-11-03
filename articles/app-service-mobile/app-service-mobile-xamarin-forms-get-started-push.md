---
title: "Adicionar notificações push à aplicação xamarin. Forms | Microsoft Docs"
description: "Saiba como utilizar os serviços do Azure para enviar notificações push de várias plataformas para aplicações xamarin. Forms."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: 912367636f1b26b3b07fbd5fe3fe8ed053218fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Adicionar notificações push à aplicação xamarin. Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial, adicionar notificações push para todos os projetos resultaram da [início rápido do xamarin. Forms](app-service-mobile-xamarin-forms-get-started.md). Isto significa que é enviada uma notificação push para todos os clientes de plataforma, sempre que é inserido um registo.

Se utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Para obter mais informações, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
Para iOS, precisa de um [filiação do programa de programador Apple](https://developer.apple.com/programs/ios/) e um dispositivo iOS físico. O [simulador iOS não suporta notificações push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurar um hub de notificação
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar notificações push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configurar e executar o projeto Android (opcional)
Conclua esta secção para ativar as notificações push para o projeto Droid xamarin. Forms para Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Ativar a nuvem Firebase Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configurar Mobile Apps back-end para enviar pedidos de push utilizando FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Adicionar notificações push para o projeto de Android
Com o back-end configurado com FCM, pode adicionar componentes e códigos no cliente para registar o FCM. Também pode registar-se para notificações push com Notification Hubs do Azure através de back-end das Mobile Apps e receber notificações.

1. No **Droid** do projeto, clique com botão direito do **componentes** pasta e clique em **obter mais componentes...** . Em seguida, procure o **cliente Google Cloud Messaging** componentes e adicione-ao projeto. Este componente suporta notificações push para um projeto Xamarin Android.
2. Abra o ficheiro de projeto mainactivity. cs e adicione a seguinte instrução no topo do ficheiro:

        using Gcm.Client;
3. Adicione o seguinte código para o **OnCreate** método após a chamada para **LoadApplication**:

        try
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }
4. Adicione um novo **CreateAndShowDialog** método de programa auxiliar, da seguinte forma:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. Adicione o seguinte código para o **MainActivity** classe:

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Isto expõe atual **MainActivity** instância, pelo que pode executar o thread de IU principal.
6. Inicializar o `instance` variável no início do **OnCreate** método, da seguinte forma.

        // Set the current instance of MainActivity.
        instance = this;
7. Adicione um novo ficheiro de classe para o **Droid** projeto com o nome `GcmService.cs`e certifique-se de que o seguinte **utilizando** instruções estão presentes na parte superior do ficheiro:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;
8. Adicione os seguintes pedidos de permissão na parte superior do ficheiro, após o **utilizando** instruções e antes do **espaço de nomes** declaração.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. Adicione a seguinte definição de classe para o espaço de nomes.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > Substitua **< PROJECT_NUMBER >** com o seu número de projeto que anotou anteriormente.    
   >
   >
10. Substitua o vazio **GcmService** classe com o código seguinte, que utiliza o recetor difusão novo:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. Adicione o seguinte código para o **GcmService** classe. Isto substitui o **OnRegistered** processador de eventos e implementa um **registar** método.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

    Tenha em atenção que este código utiliza o `messageParam` parâmetro no registo de modelo.
12. Adicione o seguinte código que implementa **OnMessage**:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Isto processa as notificações de entrada e envia-os para o Gestor de notificação para ser apresentado.
13. **GcmServiceBase** também requer que implementar o **OnUnRegistered** e **OnError** métodos de processador, o que pode fazê-lo da seguinte forma:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Agora, está pronto teste notificações de push na aplicação em execução no dispositivo Android ou o emulador.

### <a name="test-push-notifications-in-your-android-app"></a>Notificações de push de teste na sua aplicação Android
Os primeiros dois passos são necessários apenas quando estiver a testar um emulador.

1. Certifique-se de que estão a implementar ou depuração num dispositivo virtual que tenha as APIs da Google definida como destino, conforme mostrado abaixo no Gestor de dispositivo Virtual Android.
2. Adicionar uma conta do Google no dispositivo Android, clicando em **aplicações** > **definições** > **adicionar conta**. Em seguida, siga as instruções para adicionar uma conta do Google existente para o dispositivo ou para criar um novo.
3. No Visual Studio ou no Xamarin Studio, clique com botão direito do **Droid** projeto e clique em **definir como projeto de arranque**.
4. Clique em **executar** para compilar o projeto e iniciar a aplicação no seu dispositivo Android ou emulador.
5. Na aplicação, digite uma tarefa e, em seguida, clique o sinal de adição (**+**) ícone.
6. Certifique-se de que é recebida uma notificação quando é adicionado um item.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurar e executar o projeto iOS (opcional)
Esta secção destina-se à execução do projeto iOS Xamarin para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configurar o notification hub para APNS
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Em seguida, irá configurar a definição de projeto do iOS no Xamarin Studio ou Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Adicionar notificações push à aplicação iOS
1. No **iOS** do projeto, abra appdelegate. cs e adicione a seguinte instrução na parte superior do ficheiro de código.

        using Newtonsoft.Json.Linq;
2. No **AppDelegate** classe, adicione uma substituição para o **RegisteredForRemoteNotifications** eventos para se registar para notificações:

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. No **AppDelegate**, também adicione a seguinte substituição para o **DidReceiveRemoteNotification** processador de eventos:

        public override void DidReceiveRemoteNotification(UIApplication application,
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Este método processa as notificações recebidas enquanto a aplicação está em execução.
4. No **AppDelegate** classe, adicione o seguinte código para o **FinishedLaunching** método:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Isto permite que o suporte para notificações remotas e pedidos de registo de push.

A aplicação agora é atualizada para suportar notificações push.

#### <a name="test-push-notifications-in-your-ios-app"></a>Notificações de push de teste na sua aplicação iOS
1. Clique com o botão direito no projeto iOS e clique em **definir como projeto de arranque**.
2. Prima a **executar** botão ou **F5** no Visual Studio para compilar o projeto e iniciar a aplicação no dispositivo iOS. Em seguida, clique em **OK** para aceitar as notificações push.

   > [!NOTE]
   > Tem de aceitar explicitamente notificações push da sua aplicação. Este pedido só ocorre na primeira vez que a aplicação é executada.
   >
   >
3. Na aplicação, digite uma tarefa e, em seguida, clique o sinal de adição (**+**) ícone.
4. Certifique-se de que uma notificação é recebida e, em seguida, clique em **OK** a dispensa da notificação.

## <a name="configure-and-run-windows-projects-optional"></a>Configurar e executar o projeto Windows (opcional)
Esta secção destina-se a executar a WinApp xamarin. Forms e projetos de WinPhone81 para dispositivos Windows. Estes passos também suportam projetos da plataforma Universal do Windows (UWP). Pode ignorar esta secção se não estiver a trabalhar com dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registar a aplicação do Windows para notificações push com o serviço de notificação de Windows (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o notification hub para WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações push à aplicação do Windows
1. No Visual Studio, abra **App.xaml.cs** em janelas de projeto e adicione as seguintes declarações.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Substitua `<your_TodoItemManager_portable_class_namespace>` com o espaço de nomes do seu projeto portátil que contém o `TodoItemManager` classe.
2. No App.xaml.cs, adicione a seguinte **InitNotificationsAsync** método:

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS =
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Este método obtém o canal de notificação push e regista um modelo para receber notificações de modelo do seu hub de notificação. Uma notificação de modelo que suporta *messageParam* irá ser entregue ao cliente.
3. No App.xaml.cs, atualize o **OnLaunched** definição de método do processador de eventos, adicionando o `async` modificador. Em seguida, adicione a seguinte linha de código no final do método:

        await InitNotificationsAsync();

    Isto garante que o registo da notificação push é criado ou atualizado sempre que a aplicação é iniciada. É importante fazê-lo para garantir que o canal de push do WNS sempre está ativo.  
4. No Explorador de soluções para o Visual Studio, abra o **Package. appxmanifest** de ficheiros e defina **com capacidade de alerta** para **Sim** em **notificações**.
5. Criar a aplicação e certifique-se de que tem sem erros. A aplicação cliente deve agora registar-se para as notificações de modelo de back-end das Mobile Apps. Repita esta secção para cada projeto Windows na sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Notificações de push de teste na sua aplicação do Windows
1. No Visual Studio, clique com o botão direito um projeto do Windows e clique em **definir como projeto de arranque**.
2. Prima o botão **Executar** para compilar o projeto e iniciar a aplicação.
3. Na aplicação, escreva um nome para um todoitem novo e, em seguida, clique no sinal de adição (**+**) ícone para adicioná-lo.
4. Certifique-se de que é recebida uma notificação quando o item foi adicionado.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre as notificações push:

* [Diagnosticar problemas de notificação push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Existem vários motivos por que razão as notificações podem obter removidas ou não terminar em dispositivos. Este tópico mostra como analisar e descobrir a causa de raiz de falhas de notificação push.

Também pode continuar a sessão de um dos seguintes tutoriais:

* [Adicionar autenticação à aplicação ](app-service-mobile-xamarin-forms-get-started-users.md)  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação com um back-end de Aplicações Móveis. Com a sincronização offline, os utilizadores poderão interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
