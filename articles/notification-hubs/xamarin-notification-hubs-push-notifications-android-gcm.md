---
title: "Introdução aos Hubs de Notificação para aplicações Xamarin Android | Microsoft Docs"
description: "Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Xamarin Android."
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5137e0f33497dfe7ee815bb4bc30929364f6df72


---
# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Introdução aos Notification Hubs com Xamarin para Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Xamarin Android.
Irá criar uma aplicação Xamarin.Android em branco que recebe notificação push através do Google Cloud Messaging (GCM). Quando tiver terminado, poderá utilizar o Notification Hub para difundir notificações push para todos os dispositivos a executar a sua aplicação. O código concluído está disponível no exemplo [aplicação Notification Hubs][GitHub].

Este tutorial demonstra o cenário de difusão simples utilizando Notification Hubs.

## <a name="before-you-begin"></a>Antes de começar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código de conclusão para este tutorial pode ser encontrado [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos
Para este tutorial, necessita do seguinte:

* Visual Studio com Xamarin para Windows ou Xamarin Studio para Mac OS X. Pode consultar as instruções de instalação completas em [Configuração e instalação para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* Conta Google ativa
* [Componente de Mensagens do Azure]
* [Componente do Cliente Google Cloud Messaging]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Notification Hubs para aplicações Xamarin Android.

> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-google-cloud-messaging"></a>Ativar o Google Cloud Messaging
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Configurar o Notification Hub
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">

<li><p>Clique no separador <b>Configurar</b> na parte superior, introduza o valor da <b>Chave de API</b> obtido na secção anterior e, em seguida, clique em <b>Guardar</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

O Notification Hub está agora configurada para trabalhar com GCM e tem as cadeias de ligação para registar a sua aplicação e para receber notificações e enviar notificações push.

## <a name="connect-your-app-to-the-notification-hub"></a>Ligar a aplicação ao Notification Hub
### <a name="create-a-new-project"></a>Criar um novo projeto
1. No Xamarin Studio, clique em **Nova Solução**, clique em **Aplicação Android** e, depois, em **Seguinte**.
   
       ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)
2. Introduza o seu **Nome da Aplicação** e **Identificador**. Clique nas **Plataformas de Destino** que pretende suportar e, em seguida, clique em **Seguinte** e **Criar**.
   
       ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)

    Este processo cria um novo projeto Android.

1. Abra as propriedades do projeto clicando com o botão direito no seu novo projeto na vista Solução e selecione **Opções**. Selecione o item **Aplicação Android** na secção **Compilar**.
   
    Certifique-se de que a primeira letra do seu **Nome do pacote** está em minúscula.
   
   > [!IMPORTANT]
   > A primeira letra do nome do pacote tem de estar em minúscula. Caso contrário, irá receber erros de manifesto da aplicação quando registar o seu **BroadcastReceiver** e **IntentFilter** para as notificações push abaixo.
   > 
   > 
   
       ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)
2. Opcionalmente, defina a **Versão mínima do Android** para outro nível da API.
3. Opcionalmente, defina a **Versão de destino Android** para outra versão da API que pretende utilizar como destino (tem de ser um API de nível 8 ou posterior).

Clique em **OK** e feche a caixa de diálogo Opções do Projeto.

### <a name="add-the-required-components-to-your-project"></a>Adicionar os componentes necessários ao projeto
O Cliente de Google Cloud Messaging disponível no Arquivo de Componentes Xamarin simplifica o processo de suporte das notificações push no Xamarin.Android.

1. Faça duplo clique na pasta Componentes na aplicação Xamarin.Android e escolha **Obter Mais Componentes**.
2. Pesquise o componente **Mensagens do Azure** e adicione-o ao projeto.
3. Pesquise o componente **Cliente Google Cloud Messaging** e adicione-o ao projeto.

### <a name="set-up-notification-hubs-in-your-project"></a>Configurar Notification Hubs no seu projeto
1. Recolha as seguintes informações para a sua aplicação Android e Notification Hub:
   
   * **GoogleProjectNumber**: Obtenha este valor de Número do Projeto a partir da descrição geral da sua aplicação no Portal para Programadores da Google. Apontou este valor anteriormente quando criou a aplicação no portal.
   * **Escutar a cadeia de ligação**: No dashboard, no [Portal Clássico do Azure], clique em **Ver cadeias de ligação**. Copie a cadeia de ligação *DefaultListenSharedAccessSignature* para este valor.
   * **Nome do hub**: Este é o nome do seu hub a partir do [Portal Clássico do Azure]. Por exemplo, *mynotificationhub2*.
     
     Crie uma classe **Constants.cs** para o seu projeto Xamarin e defina os seguintes valores constantes na classe. Substitua os marcadores de posição com os seus valores.
     
       Constantes de classe estática pública   {
     
           public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
       }
2. Adicione o seguinte ao utilizar as instruções **MainActivity.cs**:
   
        using Android.Util;
        using Gcm.Client;
3. Adicione uma variável de instância à classe `MainActivity` que será utilizada para mostrar uma caixa de diálogo de alerta quando a aplicação está em execução:
   
        public static MainActivity instance;
4. Crie o seguinte método na classe **MainActivity**:
   
        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);
   
            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }
5. No método `OnCreate` de **MainActivity.cs**, inicialize a variável `instance` e adicione uma chamada a `RegisterWithGCM`:
   
        protected override void OnCreate (Bundle bundle)
        {
            instance = this;
   
            base.OnCreate (bundle);
   
            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);
   
            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);
   
            RegisterWithGCM();
        }
6. Crie uma nova classe, **MyBroadcastReceiver**.
   
   > [!NOTE]
   > Abaixo, explicaremos como criar uma classe **BroadcastReceiver** a partir do zero. No entanto, uma alternativa rápida para a criação manual de **MyBroadcastReceiver.cs** passa por consultar o ficheiro **GcmService.cs** presente no projeto Xamarin.Android de exemplo, incluído com os [Exemplos de Notification Hubs][GitHub]. Duplicar **GcmService.cs** e alterar os nomes das classes pode ser também uma ótima forma de começar.
   > 
   > 
7. Adicione o seguinte através de instruções para **MyBroadcastReceiver.cs** (referenciando o componente e a assemblagem que adicionou anteriormente):
   
        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;
8. Em **MyBroadcastReceiver.cs**, adicione os seguintes pedidos de permissão entre as instruções **using** e a declaração **espaço de nomes**:
   
        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
   
        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
9. Em **MyBroadcastReceiver.cs**, altere a classe **MyBroadcastReceiver** para fazer corresponder o seguinte:
   
        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };
   
            public const string TAG = "MyBroadcastReceiver-GCM";
        }
10. Adicione outra classe em **MyBroadcastReceiver.cs** com o nome **PushHandlerService**, que deriva de **GcmServiceBase**. Certifique-se de que aplica o atributo **Serviço** à classe:
    
         [Service] // Must use the service tag
         public class PushHandlerService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }
             private NotificationHub Hub { get; set; }
    
             public PushHandlerService() : base(Constants.SenderID)
                {
                 Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
             }
         }
11. **GcmServiceBase** implementa métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** e **OnError()**. A nossa classe de implementação **PushHandlerService** tem de substituir estes métodos e estes serão acionados em resposta à interação com o Notification Hub.
12. Substitua o método **OnRegistered()** em **PushHandlerService** utilizando o seguinte código:
    
         protected override void OnRegistered(Context context, string registrationId)
         {
             Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
             RegistrationID = registrationId;
    
             createNotification("PushHandlerService-GCM Registered...",
                                 "The device has been Registered!");
    
             Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                         context);
             try
             {
                 Hub.UnregisterAll(registrationId);
             }
             catch (Exception ex)
             {
                 Log.Error(MyBroadcastReceiver.TAG, ex.Message);
             }
    
             //var tags = new List<string>() { "falcons" }; // create tags if you want
             var tags = new List<string>() {};
    
             try
             {
                 var hubRegistration = Hub.Register(registrationId, tags.ToArray());
             }
             catch (Exception ex)
             {
                 Log.Error(MyBroadcastReceiver.TAG, ex.Message);
             }
         }
    
    > [!NOTE]
    > No código **OnRegistered()** abaixo, deve ter em atenção a capacidade de especificar etiquetas para se registar em canais de mensagens específicos.
    > 
    > 
13. Substitua o método **OnMessage** em **PushHandlerService** utilizando o seguinte código:
    
        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");
    
            var msg = new StringBuilder();
    
            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }
    
            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }
14. Adicione os seguintes métodos **createNotification** e **dialogNotify** a **PushHandlerService** para notificar os utilizadores quando é recebida uma notificação.
    
    > [!NOTE]
    > A estrutura de notificação na versão Android 5.0 e posterior representa um distanciamento significativo das versões anteriores. Se o testar em Android 5.0 ou posterior, a aplicação terá de ser executada para receber a notificação. Para obter mais informações, consulte [Notificações do Android](http://go.microsoft.com/fwlink/?LinkId=615880).
    > 
    > 
    
        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
    
            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));
    
            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
    
            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;
    
            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
    
            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }
    
        protected void dialogNotify(String title, String message)
        {
    
            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }
15. Substitua os membros abstratos **OnUnRegistered()**, **OnRecoverableError()** e **OnError()** para que o seu código compile:
    
        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);
    
            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }
    
        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);
    
            return base.OnRecoverableError (context, errorId);
        }
    
        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

## <a name="run-your-app-in-the-emulator"></a>Executar a sua aplicação no emulador
Se executar esta aplicação no emulador, certifique-se de que utiliza um Dispositivo Virtual Android (AVD) que suporte APIs da Google.

> [!IMPORTANT]
> Para receber as notificações push, tem de configurar uma conta Google no seu Dispositivo Virtual Android. (No emulador, navegue até **Definições** e clique em **Adicionar Conta**.) Além disso, certifique-se de que o emulador está ligado à Internet.
> 
> [!NOTE]
> A estrutura de notificação na versão Android 5.0 e posterior representa um distanciamento significativo das versões anteriores. Para obter mais informações, consulte [Notificações do Android](http://go.microsoft.com/fwlink/?LinkId=615880).
> 
> 

1. A partir de **Ferramentas**, clique em **Abrir o Gestor de Emulador Android**, selecione o seu dispositivo e, em seguida, clique em **Editar**.
   
       ![][18]
2. Selecione **APIs Google** em **Destino** e, em seguida, clique em **OK**.
   
       ![][19]
3. Na barra de ferramentas superior, clique em **Executar** e, em seguida, selecione a sua aplicação. Este processo inicia o emulador e executa a aplicação.
   
   A aplicação recupera o *registrationId* de GCM e regista com o Notification Hub.

## <a name="send-notifications-from-your-backend"></a>Enviar notificações a partir do seu back-end
Pode testar a receção das notificações na sua aplicação com o envio de notificações no [Portal Clássico do Azure] através do separador depuração no Notification Hub, conforme apresentado no ecrã abaixo.

![][30]

Normalmente, as notificações push são enviadas num serviço de back-end, como Mobile Services ou ASP.NET, através de uma biblioteca compatível. Também pode utilizar a API REST diretamente para enviar mensagens de notificação se uma biblioteca não está disponível para o seu back-end.

Aqui está uma lista de outros tutoriais que pode querer rever sobre o envio de notificações:

* ASP.NET: Consulte [Utilizar Notification Hubs para notificações push a utilizadores].
* SDK Java dos Hubs de Notificação do Azure: veja [Como utilizar Hubs de Notificação de Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificações de Java. Isto foi testado em Eclipse para Desenvolvimento do Android.
* PHP: veja [How to use Notification Hubs from PHP (Como utilizar Hubs de Notificação de PHP)](notification-hubs-php-push-notification-tutorial.md).

Nas subsecções seguintes do tutorial, pode enviar notificações com uma aplicação de consola .NET e utilizando um serviço móvel através de um script de nó.

#### <a name="optional-send-notifications-by-using-a-net-app"></a>(Opcional) Enviar notificações através de uma aplicação .NET
Nesta secção, iremos enviar notificações através de uma aplicação da consola .NET

1. Crie uma nova aplicação de consola do Visual c#:
   
       ![][20]
2. No Visual Studio, clique em **Ferramentas**, clique em **Gestor de Pacotes NuGet** e, em seguida, clique em **Consola do Gestor de Pacotes**.
   
    É apresentada a Consola do Gestor de Pacotes no Visual Studio.
3. Na janela Consola do Gestor de Pacotes, defina o **Projeto predefinido** como o novo projeto da aplicação de consola e, em seguida, execute o seguinte comando na janela da consola:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Esta ação adiciona uma referência ao SDK dos Notification Hubs do Azure utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Pacote NuGet Microsoft.Azure.Notification Hubs</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
4. Abra o ficheiro Program.cs e adicione a seguinte instrução `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Adicione o método seguinte na sua classe `Program`. Atualize o texto do marcador com a sua cadeia de ligação *DefaultFullSharedAccessSignature* e nome de hub a partir do [Portal Clássico do Azure].
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }
6. Adicione as seguintes linhas no método **Main**:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Prima a tecla F5 para executar a aplicação. Deve receber uma notificação na aplicação.
   
       ![][21]

#### <a name="optional-send-notifications-by-using-a-mobile-service"></a>(Opcional) Enviar notificações através de um serviço móvel
1. Siga [Introdução aos Mobile Services].
2. Inicie a sessão no [Portal Clássico do Azure] e selecione o seu serviço móvel.
3. Selecione o separador **Scheduler** na parte superior.
   
       ![][22]
4. Crie uma nova tarefa agendada, insira um nome e selecione **A pedido**.
   
       ![][23]
5. Quando a tarefa é criada, clique no nome da tarefa. Em seguida, clique no separador **Script** na barra superior.
6. Insira o seguinte script dentro da função de agendador. Certifique-se de que substitui os marcadores com o seu nome do Notification Hub e a cadeia de ligação para *DefaultFullSharedAccessSignature* que adquiriu anteriormente. Clique em **Guardar**.
   
        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );
7. Clique em **Executar Uma Vez** na barra inferior. Deve receber uma notificação de alerta.

## <a name="next-steps"></a>Passos seguintes
Neste exemplo simples, difundiu notificações para todos os seus dispositivos Android. Para abordar utilizadores específicos, consulte o tutorial [Utilizar Notification Hubs para notificações push a utilizadores]. Se pretender segmentar os utilizadores por grupos de interesse, pode ler [Utilizar Notification Hubs para enviar notícias de última hora]. Saiba mais sobre como utilizar Notification Hubs em [Documentação de Orientação dos Notification Hubs] e em [Procedimentos de Notification Hubs para Android].

<!-- Anchors. -->
[Ativar o Google Cloud Messaging]: #register
[Configurar o Hub de Notificação]: #configure-hub
[Ligar a aplicação ao Hub de Notificação]: #connecting-app
[Executar a sua aplicação com o emulador]: #run-app
[Enviar notificações a partir do seu back-end]: #send
[Passos seguintes?]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submeter uma página da aplicação]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[As Minhas Aplicações]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript e HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portal Clássico do Azure]: https://manage.windowsazure.com/
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Orientação dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimentos de Hubs de Notificação para Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users (Utilizar Hubs de Notificação para notificações push a utilizadores)]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news (Utilizar Hubs de Notificação para enviar notícias de última hora)]: /manage/services/notification-hubs/breaking-news-dotnet
[Página do Componente GCMClient]: http://components.xamarin.com/view/GCMClient
[Página GitHub do Xamarin.NotificationHub]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Componente do Cliente Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Componente de Mensagens do Azure]: http://components.xamarin.com/view/azure-messaging



<!--HONumber=Nov16_HO2-->


