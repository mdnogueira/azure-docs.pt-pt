<properties
    pageTitle="Introdução aos Notification Hubs do Azure utilizando o Baidu | Microsoft Azure"
    description="Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para emitir notificações via push para dispositivos Android que utilizam o Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="05/05/2016"
    ms.author="wesmc"/>

# Introdução aos Notification Hubs utilizando o Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Descrição geral

O Baidu Cloud Push é um serviço em nuvem chinês que pode utilizar para enviar notificações push para dispositivos móveis. Este serviço é particularmente útil na China, onde é complexo emitir notificações push para o Android devido à presença de várias lojas de aplicações e serviços push, para além da disponibilidade dos dispositivos Android que não estão normalmente ligados ao GCM (Google Cloud Messaging).

##Pré-requisitos

Para este tutorial, necessita do seguinte:

+ Android SDK (partimos do princípio que utiliza o Eclipse), que pode transferir a partir do <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site do Android</a>
+ [Android SDK dos Mobile Services]
+ [Android SDK do Baidu Push]

>[AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##Criar uma conta do Baidu

Para utilizar o Baidu, tem de ter uma conta do Baidu. Se já tiver uma, inicie sessão no [Portal do Baidu] e avance para o passo seguinte. Caso contrário, consulte as instruções abaixo sobre como criar uma conta do Baidu.  

1. Aceda ao [Portal do Baidu] e clique na ligação **登录** (**Início de sessão**). Clique em **立即注册** para iniciar o processo de registo da conta.

    ![][1]

2. Introduza os detalhes necessários (telefone/endereço de e-mail, palavra-passe e código de verificação) e clique em **Inscrição**.

    ![][2]

3. Será enviado um e-mail para o endereço que introduziu com uma ligação para ativar a sua conta do Baidu.

    ![][3]

4. Inicie sessão na conta de e-mail, abra a mensagem de ativação do Baidu e clique na ligação para ativar a conta do Baidu.

    ![][4]

Assim que tiver uma conta do Baidu ativada, inicie sessão no [Portal do Baidu].

##Registar-se como programador do Baidu

1. Depois de ter sessão iniciada no [Portal do Baidu], clique em **更多 >>** (**mais**).

    ![][5]

2. Desloque o ecrã para baixo na secção **站长与开发者服务 (Serviços do Programador ou Webmaster)** e clique em **百度开放云平台** (**Abrir plataforma em nuvem do Baidu**).

    ![][6]

3. Na página seguinte, clique em **开发者服务** (**Serviços do Programador**) no canto superior direito.

    ![][7]

4. Na página seguinte, clique em **注册开发者** (**Programadores Registados**) no canto superior direito do menu.

    ![][8]

5. Introduza o seu nome, uma descrição e um número de telemóvel para receber uma mensagem de texto de verificação e clique em **送验证码** (**Enviar Código de Verificação**). Tenha em atenção que para números de telefone internacionais, tem de incluir o indicativo do país entre parênteses. Por exemplo, para um número dos Estados Unidos, será **(1) 1234567890**.

    ![][9]

6. Deverá receber uma mensagem de texto com um número de verificação, como mostrado no seguinte exemplo:

    ![][10]

7. Introduza o número de verificação da mensagem em **验证码** (**Código de confirmação**).

8. Por fim, conclua o registo de programador aceitando o contrato do Baidu e clicando em **提交** (**Submeter**). Ao concluir o registo com êxito, verá a seguinte página:

    ![][11]

##Criar um projeto Baidu Cloud Push

Quando cria um projeto Baidu Cloud Push, recebe o ID da aplicação, a chave de API e uma chave secreta.

1. Depois de ter sessão iniciada no [Portal do Baidu], clique em **更多 >>** (**mais**).

    ![][5]

2. Desloque o ecrã para baixo na secção **站长与开发者服务 ** (**Serviços do Programador e Webmaster**) e clique em **百度开放云平台 ** (**Abrir plataforma do Baidu na nuvem**).

    ![][6]

3. Na página seguinte, clique em **开发者服务** (**Serviços do Programador**) no canto superior direito.

    ![][7]

4. Na página seguinte, clique em **云推送** (**Cloud Push**) na secção **云服务** (**Cloud Services**).

    ![][12]

5. Mal seja um programador registado, verá a **管理控制台** (**Consola de Gestão**) no menu superior. Clique em **开发者服务管理** (**Gestão de Serviços de Programadores**).

    ![][13]

6. Na página seguinte, clique em **创建工程** (**Criar Projeto**).

    ![][14]

7. Introduza um nome para a aplicação e clique em **创建** (**Criar**).

    ![][15]

8. Após a criação com êxito de um projeto Baidu Cloud Push, será apresentada uma página com o **ID da Aplicação**, a **Chave de API** e a **Chave Secreta**. Tome nota da chave de API e da chave secreta, que utilizaremos mais tarde.

    ![][16]

9. Configure o projeto para as notificações push clicando em **云推送** (**Cloud Push**) no painel esquerdo.

    ![][31]

10. Na página seguinte, clique no botão **推送设置** (**Definições Push**).

    ![][32]  

11. Na página de configuração, adicione o nome do pacote que pretende utilizar no projeto Android no campo **应用包名** (**Pacote de aplicação**) e clique em **保存设置** (**Guardar**).  

    ![][33]

Verá a mensagem **保存成功!** (**Guardado com êxito!**).

##Configurar o Notification Hub

1. Inicie sessão no [Portal Clássico do Azure] e clique em **+NOVO** na parte inferior do ecrã.

2. Clique em **App Service**, em **Service Bus**, em **Notification Hub** e em **Criação Rápida**.

3. Forneça um nome para o **Notification Hub**, selecione a **Região** e o **Espaço de nomes** onde este Notification Hub será criado e clique em **Criar um Novo Notification Hub**.  

    ![][17]

4. Clique no espaço de nomes onde criou o Notification Hub e, em seguida, clique em **Notification Hubs** na parte superior.

    ![][18]

5. Selecione o Notification Hub que criou e clique em **Configurar** no menu superior.

    ![][19]

6. Desloque o ecrã para baixo até à secção **definições de notificação do baidu** e introduza a chave de API e a chave secreta que obteve anteriormente na consola do Baidu para o projeto Baidu Cloud Push. Clique em **Guardar**.

    ![][20]

7. Clique no separador **Dashboard** na parte superior do Notification Hub e, em seguida, em **Ver Cadeia de Ligação**.

    ![][21]

8. Anote a **DefaultListenSharedAccessSignature** e **DefaultFullSharedAccessSignature** da janela **Aceder às informações de ligação**.

    ![][22]

##Ligar a aplicação ao Notification Hub

1. No Eclipse ADT, crie um novo projeto Android (**Ficheiro** > **Novo** > **Projeto de Aplicação Android**).

    ![][23]

2. Introduza um **Nome da Aplicação** e certifique-se de que a versão **SDK Mínimo Necessário** está definida como **API 16: Android 4.1**.

    ![][24]

3. Clique em **Seguinte** e continue a seguir o assistente até aparecer a janela **Criar Atividade**. Certifique-se de que a opção **Atividade em Branco** está selecionada e, finalmente, selecione **Concluir** para criar uma nova Aplicação Android.

    ![][25]

4. Certifique-se de que a **Compilação do Projeto de Destino** está definida corretamente.

    ![][26]

5. Transfira o ficheiro notification-hubs-0.4.jar do separador **Ficheiros** do [Notification-Hubs-Android-SDK na Gaveta](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Adicione o ficheiro à pasta **libs** do projeto Eclipse e atualize a pasta *libs*.

6. Transfira e deszipe o [Android SDK do Baidu Push], abra a pasta **libs** e copie o ficheiro jar **pushservice-x.y.z** e as pastas **armeabi** & **mips** para a pasta **libs** da aplicação Android.

7. Abra o ficheiro **AndroidManifest.xml** do projeto Android e adicione as permissões necessárias para o SDK Baidu.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Adicione a propriedade **android:name** ao elemento **aplicação** em **AndroidManifest.xml**, substituindo o *nomedoprojeto* (por ex., **com.exemplo.BaiduTeste**). Certifique-se de que este nome de projeto corresponde ao que configurou na consola do Baidu.

        <application android:name="yourprojectname.DemoApplication"

9. Adicione a seguinte configuração no elemento da aplicação depois do elemento de atividade ** MainActivity**, substituindo o *nomedoprojeto* (por ex., **com.exemplo.BaiduTeste**):

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Adicione uma nova classe denominada **ConfigurationSettings.java** ao projeto.

    ![][28]

    ![][29]

10. Adicione o seguinte código:

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    Defina o valor de **API_KEY** com o que obteve anteriormente do projeto Baidu Cloud, **NotificationHubName** com o nome do Notification Hub do Portal Clássico do Azure e **NotificationHubConnectionString** com a DefaultListenSharedAccessSignature do Portal Clássico do Azure.

11. Adicione uma nova classe denominada **DemoApplication.java** e adicione o código seguinte:

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Adicione outra classe nova denominada **MyPushMessageReceiver.java** e adicione o código abaixo. Esta é a classe que processa as notificações push que são recebidas do servidor push do Baidu.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Abra **MainActivity.java** e adicione o seguinte ao método **onCreate**:

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Abra as seguintes instruções de importação na parte superior:

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##Enviar notificações para a sua aplicação


Pode testar se recebe notificações na aplicação enviando notificações no Portal Clássico do Azure através do separador de depuração do Notification Hub, como mostrado no ecrã abaixo.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-debug.png)

Normalmente, as notificações push são enviadas num serviço de back-end como os Mobile Services ou ASP.NET utilizando uma biblioteca compatível. Também pode utilizar a API REST diretamente para enviar mensagens de notificação, caso não esteja disponível uma biblioteca para o back-end.

Neste tutorial, vamos manter tudo simples e demonstrar apenas o modo de testar a aplicação cliente através do envio de notificações utilizando o SDK .NET para os Notification Hubs numa aplicação de consola em vez de um serviço de back-end. Recomendamos o tutorial [Utilizar Notification Hubs para notificações push a utilizadores](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) como passo seguinte para enviar notificações de um back-end do ASP.NET. No entanto, as seguintes abordagens podem ser utilizadas para enviar notificações:

* **Interface REST**: pode suportar notificações em qualquer plataforma de back-end utilizando a [Interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **SDK .NET dos Notification Hubs do Microsoft Azure**: no Gestor de Pacotes Nuget para Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: [Como utilizar os Notification Hubs do Node.js](notification-hubs-nodejs-how-to-use-notification-hubs.md).

* **Mobile Services do Azure**: para obter um exemplo de como enviar notificações de um back-end dos Mobile Services do Azure integrado com os Notification Hubs, consulte [Adicionar notificações push à aplicação Mobile Services](../mobile-services/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md).

* **Java/PHP**: para obter um exemplo de como enviar notificações utilizando as APIs REST, consulte “Como utilizar os Notification Hubs de Java/PHP” ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

##(Opcional) Envie notificações a partir de uma aplicação de consola do .NET.

Nesta secção, mostramos como enviar uma notificação com uma aplicação de consola do .NET.

1. Crie uma nova aplicação de consola do Visual c#:

    ![][30]

2. Na janela Consola do Gestor de Pacotes, defina o **Projeto predefinido** como o novo projeto da aplicação de consola e, em seguida, execute o seguinte comando na janela da consola:

        Install-Package Microsoft.Azure.NotificationHubs

    Esta ação adiciona uma referência ao SDK dos Notification Hubs do Azure utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Pacote NuGet Microsoft.Azure.Notification Hubs</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Abra o ficheiro **Program.cs** e adicione o seguinte utilizando a instrução:

        using Microsoft.Azure.NotificationHubs;

4. Na classe `Program`, adicione o seguinte método e substitua *DefaultFullSharedAccessSignatureSASConnectionString* e *NotificationHubName* pelos valores que tem.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Adicione as seguintes linhas ao método **Principal**:

         SendNotificationAsync();
         Console.ReadLine();

##Testar a aplicação

Para testar esta aplicação com um telefone real, basta ligar o telefone ao computador utilizando um cabo USB. Esta ação carregue a aplicação para o telefone anexado.

Para testar esta aplicação com o emulador, na barra de ferramentas superior do Eclipse, clique em **Executar** e selecione a aplicação. Esta ação inicia o emulador e carrega e executa a aplicação.

A aplicação obtém o “userId” e o “'channelId” a partir do serviço de notificações Push do Baidu e regista-as com o Notification Hub.

Para enviar uma notificação de teste, pode utilizar o separador de depuração do Portal Clássico do Azure. Se criou a aplicação de consola do .NET para Visual Studio, basta premir a tecla F5 no Visual Studio para executar a aplicação. A aplicação envia uma notificação que será apresentada na área de notificação superior do dispositivo ou emulador.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Android SDK dos Mobile Services]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Android SDK do Baidu Push]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Portal do Baidu]: http://www.baidu.com/



<!--HONumber=Jun16_HO2-->


