<properties
    pageTitle="Introdução aos Hubs de Notificação do Azure para Aplicações da Plataforma Universal do Windows | Microsoft Azure"
    description="Neste tutorial, irá aprender a utilizar os Hubs de Notificação do Azure para enviar notificações push para uma aplicação da Plataforma Universal do Windows."
    services="notification-hubs"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="07/21/2016"
    ms.author="wesmc"/>

# Introdução aos Hubs de Notificação do Azure para Aplicações da Plataforma Universal do Windows

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Descrição geral

Este tutorial mostra como utilizar os Hubs de Notificação do Azure para enviar notificações push para uma aplicação da Plataforma Universal do Windows (UWP).

Neste tutorial, vai criar uma aplicação da Loja Windows em branco que recebe notificações push utilizando o Serviço de Notificações Push da Microsoft (WNS). Quando tiver terminado, poderá utilizar o Notification Hub para difundir notificações push para todos os dispositivos a executar a sua aplicação.


## Antes de começar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código de conclusão para este tutorial pode ser encontrado [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal) no GitHub.



##Pré-requisitos

Para este tutorial, necessita do seguinte:

+ [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) ou posterior

+ [Ferramentas de Desenvolvimento de Aplicações Universais do Windows instaladas](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)

+ Uma conta ativa do Azure <br/>Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).

+ Uma conta ativa da Loja Windows



A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Hubs de Notificação para aplicações da Plataforma Universal do Windows.

##Registar a aplicação para a Loja Windows

Para enviar notificações push para aplicações UWP, tem de associar a aplicação à Loja Windows. Em seguida, tem de configurar o Notification Hub para se integrar ao WNS.

1. Se ainda não registou a aplicação, navegue para o [Windows Dev Center](https://dev.windows.com/overview), inicie sessão com a conta Microsoft e clique em **Criar uma nova aplicação**.


2. Escreva um nome para a aplicação e clique em **Reservar nome da aplicação**.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-name.png)

    Este procedimento cria um novo registo para a aplicação na Loja Windows.

3. No Visual Studio, crie um novo projeto Aplicações da Loja Visual C# com o modelo **Aplicação Vazia** e clique em **OK**.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png)

4. Aceite as predefinições para as versões de destino e mínimas da plataforma.


5. No Explorador de Soluções, clique com o botão direito do rato no projeto de aplicação da Loja Windows, clique em **Loja** e, em seguida, em **Associar Aplicação à Loja…**

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png)


    É apresentado o assistente **Associar aplicação à Loja Windows**.

6. No assistente, clique em **Iniciar sessão** e inicie sessão com a conta Microsoft.

7. Clique na aplicação que registou no passo 2, clique em **Seguinte** e em **Associar**.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-app-name.png)

    Esta ação adiciona ao manifesto da aplicação as informações de registo da Loja Windows necessárias.

8. De volta à página do [Windows Dev Center](http://go.microsoft.com/fwlink/p/?LinkID=266582) da sua nova aplicação, clique em **Serviços**, em **Notificações push** e em **Site dos Serviços Live** em **Serviços de Notificação Push do Windows (WNS) e Aplicações Móveis do Microsoft Azure**.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-uwp-app-live-services.png)

9. Na página de registo da sua aplicação, aponte a palavra-passe do **Segredo da Aplicação** e o **Identificador de Segurança do Pacote (SID)** localizados nas definições da plataforma da **Loja Windows**.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-uwp-app-push-auth.png)


    > [AZURE.WARNING]
    O segredo da aplicação e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação.

##Configurar o Notification Hub

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Selecione a opção <b>Serviços de Notificação</b> e a opção <b>Windows (WNS)</b>. Em seguida, introduza a palavra-passe do <b>Segredo da aplicação</b> no campo <b>Chave de Segurança</b>. Introduza o valor do <b>SID do Pacote</b> obtido a partir do WNS na secção anterior e, em seguida, clique em <b>Guardar</b>.</p>
</li>
</ol>

&emsp;&emsp;![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


O Notification Hub já está configurado para trabalhar com WNS e tem as cadeias de ligação para registar a aplicação e enviar notificações.

##Ligar a aplicação ao Notification Hub

1. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, clique em **Gerir Pacotes NuGet**.

    É apresentada a caixa de diálogo **Gerir Pacotes NuGet**.

2. Procure `WindowsAzure.Messaging.Managed`, clique em **Instalar** e aceite os termos de utilização.

    ![][20]

    Esse procedimento transfere, instala e adiciona uma referência à biblioteca de Mensagens do Azure para Windows utilizando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">Pacote NuGet WindowsAzure.Messaging.Managed </a>.

3. Abra o ficheiro App.xaml.cs e adicione as seguintes instruções `using`: 

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;



4. Também no App.xaml.cs, adicione a seguinte definição de método **InitNotificationsAsync** à classe **App**:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("< your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
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

    >[AZURE.NOTE] Não se esqueça de substituir o marcador de posição “nome do seu hub” pelo nome do hub de notificação que aparece no Portal do Azure. Substitua, também, o marcador de posição da cadeia de ligação pela cadeia de ligação **DefaultListenSharedAccessSignature**, que obteve na página **Políticas de Acesso** do Hub de Notificação numa secção anterior.

5. No topo do processador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada ao novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Esta ação garante que o URI do canal é registado no Notification Hub sempre que a aplicação for iniciada.

6. Prima a tecla **F5** para executar a aplicação. É apresentada uma caixa de diálogo de pop-up que contém a chave de registo.

    ![][19]




A aplicação já está pronta para receber notificações de alerta.

##Enviar notificações 

Pode testar rapidamente a receção das notificações na sua aplicação com o envio de notificações no [Portal do Azure](https://portal.azure.com/) através do botão **Testar Envio** no Hub de Notificação, conforme mostrado no ecrã abaixo.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Normalmente, as notificações push são enviadas num serviço de back-end como os Mobile Services ou ASP.NET utilizando uma biblioteca compatível. Também pode utilizar a API REST diretamente para enviar mensagens de notificação, caso não esteja disponível uma biblioteca para o back-end. 

Neste tutorial, vamos manter tudo simples e demonstrar apenas o modo de testar a aplicação cliente através do envio de notificações utilizando o SDK .NET para os Notification Hubs numa aplicação de consola em vez de um serviço de back-end. Recomendamos o tutorial [Utilizar Notification Hubs para notificações push a utilizadores] como passo seguinte para enviar notificações de um back-end do ASP.NET. No entanto, as seguintes abordagens podem ser utilizadas para enviar notificações:

* **Interface REST**: pode suportar notificações em qualquer plataforma de back-end utilizando a [Interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **SDK .NET dos Notification Hubs do Microsoft Azure**: no Gestor de Pacotes Nuget para Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: [como utilizar os Hubs de Notificação do Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

* **Mobile Apps do Azure**: para obter um exemplo de como enviar notificações de uma Aplicação Móvel do Azure integrada aos Notification Hubs, consulte [Adicionar notificações push para Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java/PHP**: para obter um exemplo de como enviar notificações com as APIs REST, consulte “Como utilizar os Hubs de Notificação de Java/PHP” ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).


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

    Não se esqueça de substituir o marcador de posição “nome do hub” pelo nome do hub de notificação que aparece no Portal do Azure. Substitua, também, o marcador de posição da cadeia de ligação pela cadeia de ligação **DefaultFullSharedAccessSignature**, que obteve na página **Políticas de Acesso** do Hub de Notificação na secção “Configurar o hub de notificação”.

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

Para obter informações mais gerais sobre os Hubs de Notificação, consulte [Documentação de Orientação dos Hubs de Documentação](notification-hubs-push-notification-overview.md).






<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Utilizar Notification Hubs para notificações push a utilizadores]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Utilizar Notification Hubs para enviar notícias de última hora]: notification-hubs-windows-store-dotnet-send-breaking-news.md

[catálogo de alertas]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[catálogo de mosaicos]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[descrição geral de destaque]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx



<!--HONumber=ago16_HO4-->


