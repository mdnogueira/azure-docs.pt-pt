---
title: "Introdução aos Hubs de Notificação do Azure para Aplicações Kindle | Microsoft Docs"
description: "Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Kindle."
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 7206f152ed7270abc62536a9ee164f7227833bcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Introdução aos Notification Hubs para Aplicações Kindle
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Kindle.
Deverá criar uma aplicação Kindle que receba notificações push através do Amazon Device Messaging (ADM).

## <a name="prerequisites"></a>Pré-requisitos
Para este tutorial, necessita do seguinte:

* Obtenha o Android SDK (presumimos que utiliza o Eclipse) no <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Site do Android</a>.
* Siga os passos em <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Configurar o Ambiente de Desenvolvimento</a> para configurar o ambiente de desenvolvimento para o Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Adicionar uma nova aplicação ao portal de programador
1. Primeiro, crie uma aplicação no [Portal de programador da Amazon].
   
    ![][0]
2. Copie a **Chave da Aplicação**.
   
    ![][1]
3. No portal, clique no nome da aplicação e, em seguida, clique no separador **Device Messaging**.
   
    ![][2]
4. Clique em **Criar um Novo Perfil de Segurança** e crie um novo perfil de segurança (por exemplo, **Perfil de segurança TestAdm**). Em seguida, clique em **Guardar**.
   
    ![][3]
5. Clique em **Perfis de Segurança** para ver o perfil de segurança que acabou de criar. Copie os valores do **ID de Cliente** e **Segredo do Ciente** para utilização posterior.
   
    ![][4]

## <a name="create-an-api-key"></a>Criar uma chave de API
1. Abra uma linha de comandos com privilégios de administrador.
2. Navegue para a pasta Android SDK.
3. Introduza o seguinte comando:
   
        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
   
    ![][5]
4. Para a palavra-passe **keystore**, escreva **android**.
5. Copie a identificação digital **MD5**.
6. De novo no portal de programador, no separador **Messaging**, clique em **Android/Kindle** e introduza o nome do pacote da sua aplicação (por exemplo, **com.sample.notificationhubtest**) e o valor **MD5** e, em seguida, clique em **Gerar Chave de API**.

## <a name="add-credentials-to-the-hub"></a>Adicionar credenciais ao hub
No portal, adicione o segredo de cliente e o ID de cliente no separador **Configurar** do Notification Hub.

## <a name="set-up-your-application"></a>Configurar a aplicação
> [!NOTE]
> Quando estiver a criar uma aplicação, utilize no mínimo uma API de nível 17.
> 
> 

Adicione as bibliotecas ADM ao projeto Eclipse:

1. Para obter a biblioteca ADM, deverá [transferir o SDK]. Extraia o ficheiro zip do SDK.
2. No Eclipse, clique com o botão direito do rato no projeto e, em seguida, clique em **Propriedades**. Selecione **Caminho de Compilação de Java** à esquerda e selecione o separador **Bibliotecas** na parte superior. Clique em **Adicionar Jar Externo** e selecione o ficheiro `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` no diretório de onde extraiu o Amazon SDK.
3. Transfira o Android SDK NotificationHubs (ligação).
4. Deszipe o pacote e arraste o ficheiro `notification-hubs-sdk.jar` para a pasta `libs` no Eclipse.

Edite o manifesto da aplicação para suportar ADM:

1. Adicione o espaço de nomes Amazon no elemento raiz do manifesto:

        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

1. Adicione permissões como o primeiro elemento sob o elemento manifesto. Substitua **[NOME DO SEU PACOTE]** pelo pacote que utilizou para criar a aplicação.
   
        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />
   
        <uses-permission android:name="android.permission.INTERNET"/>
   
        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />
   
        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />
   
        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />
2. Insira o elemento seguinte como o primeiro subordinado do elemento aplicação. Não se esqueça de substituir **[NOME DO SEU SERVIÇO]** pelo nome do processador de mensagens ADM que criará na secção seguinte (incluindo o pacote) e de substituir **[NOME DO SEU PACOTE]** pelo nome do pacote com que criou a aplicação.
   
        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />
   
        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />
   
            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >
   
            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
   
          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>Criar o processador de mensagens ADM
1. Crie uma nova classe que herda de `com.amazon.device.messaging.ADMMessageHandlerBase` e dê-lhe o nome `MyADMMessageHandler`, como mostrado na figura seguinte:
   
    ![][6]
2. Adicione as seguintes instruções `import`:
   
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub
3. Adicione o seguinte código na classe que criou. Não se esqueça de substituir o nome e a cadeia ligação do hub (escutar):
   
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
          private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }
   
        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }
   
            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }
   
            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();
   
                mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
   
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);
   
            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.mipmap.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                  .setContentText(msg);
   
             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
4. Adicione o seguinte código ao método `OnMessage()`:
   
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
5. Adicione o seguinte código ao método `OnRegistered`:
   
            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }
6. Adicione o seguinte código ao método `OnUnregistered`:
   
         try {
             getNotificationHub(getApplicationContext()).unregister();
         } catch (Exception e) {
             Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
         }
7. No método `MainActivity`, adicione as seguintes declarações de importação:
   
        import com.amazon.device.messaging.ADM;
8. Adicione o seguinte código no final do método `OnCreate`:
   
        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                         MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Adicionar a chave de API à aplicação
1. No Eclipse, crie um novo ficheiro denominado **api_key.txt** nos ativos do diretório do projeto.
2. Abra o ficheiro e copie a chave de API que gerou no portal de programador da Amazon.

## <a name="run-the-app"></a>Executar a aplicação
1. Inicie o emulador.
2. No emulador, percorra a partir da parte superior e clique em **Definições**, clique em **Minha conta** e registe-se com uma conta válida da Amazon.
3. No Eclipse, execute a aplicação.

> [!NOTE]
> Se ocorrer um problema, verifique a hora do emulador (ou dispositivo). O valor da hora tem de ser exato. Para alterar a hora do emulador do Kindle, pode executar o seguinte comando no diretório de ferramentas da plataforma Android SDK:
> 
> 

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Enviar uma mensagem
Para enviar uma mensagem utilizando o .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Portal de programador da Amazon]: https://developer.amazon.com/home.html
[transferir o SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
