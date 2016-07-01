<properties
    pageTitle="Introdução aos Notification Hubs do Azure para Aplicações Loja Windows | Microsoft Azure"
    description="Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Loja Windows ou Windows Phone 8.1 (não Silverlight)."
    services="notification-hubs"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor="dwrede"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/28/2016"
    ms.author="wesmc"/>

# Introdução aos Notification Hubs do Azure para Aplicações Loja Windows

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Descrição geral

Este tutorial mostra-lhe como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Loja Windows ou Windows Phone 8.1 (não Silverlight). Se tiver como objetivo o Windows Phone 8.1 Silverlight, consulte a versão [Windows Phone](notification-hubs-windows-phone-get-started.md).
Neste tutorial, vai criar uma aplicação da Loja Windows em branco que recebe notificações push utilizando o Serviço de Notificações Push da Microsoft (WNS). Quando tiver terminado, poderá utilizar o Notification Hub para difundir notificações push para todos os dispositivos a executar a sua aplicação.


## Antes de começar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código de conclusão para este tutorial pode ser encontrado [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal) no GitHub.



##Pré-requisitos

Para este tutorial, necessita do seguinte:

+ Microsoft Visual Studio Express 2013 para Windows com o Update 2 ou posterior<br/>Esta versão do Visual Studio é necessária para criar um projeto de aplicação universal. Se pretender criar uma aplicação da Loja Windows, precisa do Visual Studio 2012 Express para Windows 8.

+ Uma conta ativa da Loja Windows

+ Uma conta ativa do Azure <br/>Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Notification Hubs para aplicações Loja Windows.

##Registar a aplicação para a Loja Windows

Para enviar notificações push para aplicações Loja Windows, tem de associar a aplicação à Loja Windows. Em seguida, tem de configurar o Notification Hub para se integrar ao WNS.

1. Se ainda não registou a aplicação, navegue para o [Windows Dev Center](http://go.microsoft.com/fwlink/p/?LinkID=266582), inicie sessão com a conta Microsoft e clique em **Criar uma nova aplicação**.


2. Escreva um nome para a aplicação e clique em **Reservar nome da aplicação**.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-name.png)

    Este procedimento cria um novo registo para a aplicação na Loja Windows.

3. No Visual Studio, crie um novo projeto Aplicações Loja Visual C#, utilizando o modelo **Aplicação Vazia**.

    ![][2]

4. No Explorador de Soluções, clique com o botão direito do rato no projeto de aplicação da Loja Windows, clique em **Loja** e, em seguida, em **Associar Aplicação à Loja…**

    ![][3]

    É apresentado o assistente **Associar aplicação à Loja Windows**.

5. No assistente, clique em **Iniciar sessão** e inicie sessão com a conta Microsoft.

6. Clique na aplicação que registou no passo 2, clique em **Seguinte** e em **Associar**.

    ![][4]

    Esta ação adiciona ao manifesto da aplicação as informações de registo da Loja Windows necessárias.

7. (Opcional) Repita os passos 4 a 6 para o projeto de aplicação Loja Windows Phone.  

8. De volta à página do [Windows Dev Center](http://go.microsoft.com/fwlink/p/?LinkID=266582) da sua nova aplicação, clique em **Serviços**, em **Notificações push** e em **Site dos Serviços Live** em **Serviços de Notificação Push do Windows (WNS) e Mobile Services do Microsoft Azure**.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-live-services.png)

9. No separador **Definições da Aplicação**, tome nota dos valores do **Segredo de cliente** e **Identificador de segurança do pacote (SID)**.

    ![][6]

    > [AZURE.WARNING]
    O segredo de cliente e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação.

##Configurar o Notification Hub

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">
<li><p>Selecione o separador <b>Configurar</b> na parte superior, introduza os valores do <b>Segredo de cliente</b> e do <b>SID do pacote</b> obtidos no WNS na secção anterior e clique em <b>Guardar</b>.</p>
</li>
</ol>

&emsp;&emsp;![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


O Notification Hub já está configurado para trabalhar com WNS e tem as cadeias de ligação para registar a aplicação e enviar notificações.

##Ligar a aplicação ao Notification Hub

1. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, clique em **Gerir Pacotes NuGet**.

    É apresentada a caixa de diálogo **Gerir Pacotes NuGet**.

2. Procure `WindowsAzure.Messaging.Managed` e clique em **Instalar**, selecione todos os projetos na solução e aceite os termos de utilização.

    ![][20]

    Esta ação transfere, instala e adiciona uma referência à biblioteca de Mensagens do Azure para Windows a todos os projetos, utilizando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">Pacote NuGet WindowsAzure.Messaging.Managed </a>.

3. Abra o ficheiro App.xaml.cs e adicione as seguintes instruções `using`: Num projeto universal, este ficheiro está localizado na pasta `<project_name>.Shared`.

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;



4. Também no App.xaml.cs, adicione a seguinte definição de método **InitNotificationsAsync** à classe **App**:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    Este código obtém o URI do canal para a aplicação a partir do WNS e, em seguida, regista esse URI de canal no Notification Hub.

    >[AZURE.NOTE]Não se esqueça de substituir o marcador de posição “nome do hub” pelo nome do Notification Hub que aparece no [Portal Clássico do Azure] no separador **Notification Hubs** (por exemplo, **mynotificationhub2** no exemplo anterior). Substitua, também, o marcador de posição da cadeia de ligação pela cadeia de ligação **DefaultListenSharedAccessSignature**, que obteve na secção anterior.

5. No topo do processador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada ao novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Esta ação garante que o URI do canal é registado no Notification Hub sempre que a aplicação for iniciada.

6. No Explorador de Soluções, faça duplo clique em **Package.appxmanifest** da aplicação Loja Windows e, em seguida, em **Notificações**, defina **Compatível com alertar** como **Sim**:

    ![][18]

    No menu **Ficheiro**, clique em **Guardar Tudo**.

7. (Opcional) Repita os passos anteriores para o projeto de aplicação Loja Windows Phone.

8. Prima a tecla **F5** para executar a aplicação. É apresentada uma caixa de diálogo de pop-up que contém a chave de registo.

    ![][19]

9. (Opcional) Repita o passo anterior para executar o projeto do Windows Phone para registar a aplicação num dispositivo Windows Phone.



A aplicação já está pronta para receber notificações de alerta.

##Enviar notificações 

Pode testar a receção das notificações na sua aplicação com o envio de notificações no [Portal Clássico do Azure] através do separador depuração no Notification Hub, como mostrado no ecrã abaixo.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-debug.png)

Normalmente, as notificações push são enviadas num serviço de back-end como os Mobile Services ou ASP.NET utilizando uma biblioteca compatível. Também pode utilizar a API REST diretamente para enviar mensagens de notificação, caso não esteja disponível uma biblioteca para o back-end. 

Neste tutorial, vamos manter tudo simples e demonstrar apenas o modo de testar a aplicação cliente através do envio de notificações utilizando o SDK .NET para os Notification Hubs numa aplicação de consola em vez de um serviço de back-end. Recomendamos o tutorial [Utilizar Notification Hubs para notificações push a utilizadores] como passo seguinte para enviar notificações de um back-end do ASP.NET. No entanto, as seguintes abordagens podem ser utilizadas para enviar notificações:

* **Interface REST**: pode suportar notificações em qualquer plataforma de back-end utilizando a [Interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **SDK .NET dos Notification Hubs do Microsoft Azure**: no Gestor de Pacotes Nuget para Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: [Como utilizar os Notification Hubs do Node.js](notification-hubs-nodejs-how-to-use-notification-hubs.md).

* **Mobile Apps do Azure**: para obter um exemplo de como enviar notificações de uma Aplicação Móvel do Azure integrada aos Notification Hubs, consulte [Adicionar notificações push para Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java/PHP**: para obter um exemplo de como enviar notificações utilizando as APIs REST, consulte “Como utilizar os Notification Hubs de Java/PHP” ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).


## (Opcional) Enviar notificações de uma aplicação de consola


Para enviar notificações através da utilização de uma aplicação de consola .NET, siga estes passos. 

1. Clique com o botão direito do rato na solução, selecione **Adicionar** e **Novo Projeto…**, e, em seguida, em **Visual C#**, clique em **Windows** e **Aplicação de Consola** e, em seguida, clique em **OK**.

    ![][13]

    Esta ação adiciona uma nova aplicação da consola Visual C# à solução. Também pode fazer isto numa solução separada.

2. No Visual Studio, clique em **Ferramentas**, clique no **Gestor de Pacotes NuGet** e, em seguida, em **Consola do Gestor de Pacotes**.

    É apresentada a Consola do Gestor de Pacotes no Visual Studio.

3. Na janela Consola do Gestor de Pacotes, defina o **Projeto predefinido** como o novo projeto da aplicação de consola e, em seguida, execute o seguinte comando na janela da consola:

        Install-Package Microsoft.Azure.NotificationHubs

    Esta ação adiciona uma referência ao SDK dos Notification Hubs do Azure utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Pacote NuGet Microsoft.Azure.Notification Hubs</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Abra o ficheiro Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.Azure.NotificationHubs;

5. Adicione o seguinte método à classe **Program**:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    Não se esqueça de substituir o marcador de posição “nome do hub” pelo nome do Notification Hub que aparece no [Portal Clássico do Azure] no separador **Notification Hubs**. Substitua, também, o marcador de posição da cadeia de ligação pela cadeia de ligação denominada **DefaultFullSharedAccessSignature**, que obteve na secção “Configurar o Notification Hub”.

    >[AZURE.NOTE]Certifique-se de que utiliza a cadeia de ligação com acesso **Completo** e não com acesso de **Escuta**. A cadeia de ligação de acesso escuta não concede permissões para enviar notificações.

6. Adicione as seguintes linhas ao método **Principal**:

         SendNotificationAsync();
         Console.ReadLine();

7. No Visual Studio, clique com o botão direito do rato no projeto de aplicação de consola e clique em **Definir como Projeto de Arranque** para o configurar como o projeto de arranque. Em seguida, prima a tecla **F5** para executar a aplicação.

    ![][14]

    Receberá uma notificação de alerta em todos os dispositivos registados. Clicar ou tocar na faixa do alerta carrega a aplicação.

Pode encontrar todos os payloads possíveis nos tópicos [catálogo de alertas] e [catálogo de mosaicos] e [descrição geral de destaque] no MSDN.

##Passos seguintes

Neste exemplo simples, enviou notificações de difusão para todos os seus dispositivos Windows utilizando o portal ou uma aplicação de consola. Recomendamos o tutorial [Utilizar Notification Hubs para notificações push a utilizadores] como o passo seguinte. Esta operação irá mostrar como enviar notificações de um back-end ASP.NET, utilizando as etiquetas para utilizadores específicos de destino.

Se pretende segmentar os utilizadores por grupos de interesses, consulte [Utilizar Notification Hubs para enviar notícias de última hora]. 

Para obter informações mais gerais sobre Notification Hubs, consulte [Documentação de Orientação dos Notification Hubs].






<!-- Images. -->
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Documentação de Orientação dos Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx

[Utilizar Notification Hubs para notificações push a utilizadores]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Utilizar Notification Hubs para enviar notícias de última hora]: notification-hubs-windows-store-dotnet-send-breaking-news.md

[catálogo de alertas]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[catálogo de mosaicos]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[descrição geral de destaque]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx



<!--HONumber=Jun16_HO2-->


