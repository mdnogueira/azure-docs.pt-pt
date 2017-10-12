---
title: "Introdução aos Hubs de Notificação do Azure para aplicações da Plataforma Universal do Windows | Microsoft Docs"
description: "Neste tutorial, irá aprender a utilizar os Hubs de Notificação do Azure para enviar notificações push para uma aplicação da Plataforma Universal do Windows."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: erikre
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: e18a810bcdbd97c79418f53c647df8723ecb6076
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-notification-hubs-for-universal-windows-platform-apps"></a>Introdução aos Hubs de Notificação para aplicações da Plataforma Universal do Windows

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este artigo mostra-lhe como utilizar os Hubs de Notificação do Azure para enviar notificações push para uma aplicação da Plataforma Universal do Windows (UWP).

Neste artigo, vai criar uma aplicação da Loja Windows em branco que recebe notificações push utilizando o Serviço de Notificações Push da Microsoft (WNS). Quando tiver terminado, poderá utilizar o seu hub de notificação para difundir notificações push para todos os dispositivos que executem a sua aplicação.

## <a name="before-you-begin"></a>Antes de começar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código concluído deste artigo está disponível no [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

## <a name="prerequisites"></a>Pré-requisitos
Para este tutorial, necessita do seguinte:

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) ou posterior
* [Ferramentas de desenvolvimento de aplicações do UWP instaladas](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* Uma conta ativa do Azure  
    Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais informações, veja [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F) (Avaliação Gratuita do Azure).
* Uma conta ativa da Loja Windows

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Hubs de Notificação para aplicações do UWP.

## <a name="register-your-app-for-the-windows-store"></a>Registar a aplicação para a Loja Windows
Para enviar notificações push para aplicações do UWP, associe a aplicação à Loja Windows. Em seguida, configure o Hub de Notificação para se integrar no WNS.

1. Se ainda não registou a aplicação, navegue para o [Windows Dev Center](https://dev.windows.com/overview), inicie sessão com a conta Microsoft e selecione **Criar uma nova aplicação**.

2. Escreva um nome para a aplicação e selecione **Reservar nome da aplicação**. Desta forma, é criado um registo novo da Loja Windows para a sua aplicação.

3. No Visual Studio, crie um projeto novo Aplicações da Loja Visual C# com o modelo **Aplicação Vazia** do UWP selecione **OK**.

4. Aceite as predefinições para as versões de destino e mínimas da plataforma.

5. No Explorador de Soluções, clique com o botão direito do rato no projeto de aplicação da Loja Windows, selecione **Loja** e, em seguida, selecione **Associar Aplicação à Loja**.  
    É apresentado o assistente **Associar aplicação à Loja Windows**.

6. No assistente, inicie sessão com a sua conta Microsoft.

7. Selecione a aplicação que registou no passo 2, selecione **Seguinte** e, por fim, **Associar**. Desta forma, é adicionado ao manifesto da aplicação as informações de registo da Loja Windows necessárias.

8. Novamente na página [Windows Dev Center](http://dev.windows.com/overview) da aplicação nova, selecione **Serviços**, **Notificações push** e, em seguida, **WNS/MPNS**.

9. Selecione **Nova Notificação**.

10. Selecione o modelo **Vazio (Alerta)** e selecione **OK**.

11. Introduza um **Nome** para a notificação e uma mensagem de **Contexto** Visual e, em seguida, selecione **Guardar como rascunho**.

12. Aceda ao [Portal de Registo de Aplicações](http://apps.dev.microsoft.com) e inicie sessão.

13. Selecione o nome da sua aplicação. Nas definições da plataforma **Loja Windows**, aponte a palavra-passe **Segredo da Aplicação** e o **Identificador de segurança de pacotes (SID)**.

    >[!WARNING]
    >O segredo da aplicação e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação.

## <a name="configure-your-notification-hub"></a>Configurar o Notification Hub
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="5">
<li><p>Selecione <b>Serviços de Notificação</b> > <b>Windows (WNS)</b> e, em seguida, introduza a palavra-passe do segredo da aplicação na caixa <b>Chave de Segurança</b>. Na caixa <b>SID do Pacote</b>, introduza o valor obtido a partir do WNS, na secção anterior, e selecione <b>Guardar</b>.</p>
</li>
</ol>

![Caixas do SID do Pacote e Chave de Segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

O seu hub de notificação está agora configurado para funcionar com o WNS. Tem as cadeias de ligação para registar a sua aplicação e enviar notificações.

## <a name="connect-your-app-to-the-notification-hub"></a>Ligar a aplicação ao Notification Hub
1. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, Selecione **Gerir Pacotes NuGet**.  
    É aberta a janela **Gerir Pacotes NuGet**.

2. Na caixa de pesquisa, introduza **WindowsAzure.Messaging.Managed**, selecione **Instalar** e aceite os termos de utilização.
   
    ![Janela Gerir Pacotes NuGet][20]
   
    Esta ação transfere, instala e adiciona uma referência à biblioteca de mensagens do Azure para Windows mediante a utilização do [Pacote NuGet WindowsAzure.Messaging.Managed ](http://nuget.org/packages/WindowsAzure.Messaging).

3. Abra o ficheiro de projeto App.xaml.cs e adicione as seguintes instruções `using`: 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. No App.xaml.cs, adicione também a seguinte definição de método **InitNotificationsAsync** à classe **App**:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
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
   
    >[!NOTE]
    >* Substitua o marcador de posição **nome do hub** pelo nome do hub de notificação que aparece no portal do Azure. 
    >* Substitua, também, o marcador de posição da cadeia de ligação pela cadeia de ligação **DefaultListenSharedAccessSignature**, que obteve na página **Políticas de Acesso** do seu hub de notificação numa secção anterior.
   > 
   > 
5. No topo do processador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada ao novo método **InitNotificationsAsync**:
   
        InitNotificationsAsync();
   
    Esta ação garante que o URI do canal é registado no seu hub de notificações sempre que a aplicação for iniciada.

6. Para executar a aplicação, selecione a tecla **F5**. É apresentada uma caixa de diálogo que contém a chave de registo.

A aplicação já está pronta para receber notificações de alerta.

## <a name="send-notifications"></a>Enviar notificações
Pode testar rapidamente a receção das notificações na sua aplicação ao enviar notificações no [portal do Azure](https://portal.azure.com/). Utilize o botão **Testar Envio** no hub de notificação, conforme mostrado na imagem seguinte:

![Painel Testar Envio](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Normalmente, as notificações push são enviadas num serviço de back-end como os Serviços Móveis ou o ASP.NET através da utilização de uma biblioteca compatível. Se não estiver disponível uma biblioteca para o seu back-end, também pode enviar mensagens de notificação com a API REST diretamente. 

Este tutorial, demonstra como testar a sua aplicação cliente mediante o simples envio de notificações que utilizam o .NET SDK para os hubs de notificação numa aplicação de consola em vez de um serviço de back-end. Recomendamos o tutorial [Use Notification Hubs to push notifications to users ] (Utilizar os Hubs de Notificação para enviar notificações push aos utilizadores) como passo seguinte para enviar notificações de um back-end do ASP.NET. No entanto, pode utilizar as abordagens abaixo para enviar notificações:

* **Interface REST**: pode suportar notificações em qualquer plataforma de back-end com a [Interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **SDK .NET dos Hubs de Notificação do Microsoft Azure**: no Gestor de Pacotes do NuGet para Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: veja [How to use Notification Hubs from Node.js](notification-hubs-nodejs-push-notification-tutorial.md) (Como utilizar os Hubs de Notificação a partir do Node.js).
* **Mobile Apps do Azure**: para obter um exemplo de como enviar notificações de uma aplicação móvel do Azure integrada nos Hubs de Notificação, consulte [Add push notifications for Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) (Adicionar notificações push a Aplicações Móveis).

* **Java ou PHP**: para obter exemplos de como enviar notificações com as APIs REST, veja:
    * [Java](notification-hubs-java-push-notification-tutorial.md)
    * [PHP](notification-hubs-php-push-notification-tutorial.md)

## <a name="optional-send-notifications-from-a-console-app"></a>(Opcional) Enviar notificações de uma aplicação de consola
Para enviar notificações através de uma aplicação de consola .NET, faça o seguinte: 

1. Clique com o botão direito do rato na solução, selecione **Adicionar** > **Novo Projeto**, em **Visual C#**, selecione **Windows** e **Aplicação de Consola** e selecione **OK**.
   
    É adicionada uma nova aplicação de consola Visual C# à solução. Também pode adicionar o projeto numa solução separada.

2. No Visual Studio, selecione **Ferramentas**, selecione **Gestor de Pacotes NuGet** e, em seguida, selecione **Consola do Gestor de Pacotes**.
   
    É aberta a Consola do Gestor de Pacotes no Visual Studio.

3. Na janela Consola do Gestor de Pacotes, defina o **Projeto predefinido** como o novo projeto de aplicação de consola e, em seguida, execute o comando seguinte na janela da consola:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Esta ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure mediante a utilização do [Pacote NuGet Microsoft.Azure.Notification Hubs](http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
   
    ![O nome "Projeto predefinido"](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

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
   
    >[!NOTE]
    >* Substitua o marcador de posição **nome do hub** pelo nome do hub de notificação que aparece no portal do Azure. 
    >* Substitua o marcador de posição da cadeia de ligação pela cadeia de ligação **DefaultFullSharedAccessSignature**, que obteve na página **Políticas de Acesso** do seu hub de notificação, na secção “Configurar o hub de notificação”.
    >* Utilize a cadeia de ligação que tem acesso *completo* e não acesso de *escuta*. A cadeia de ligação de acesso escuta não concede permissões para enviar notificações.
   > 
   > 
6. No método **Principal**, adicione as linhas seguintes:
   
         SendNotificationAsync();
         Console.ReadLine();

7. No Visual Studio, clique com o botão direito do rato no projeto de aplicação de consola e selecione **Definir como Projeto de Arranque** para o definir como o projeto de arranque. Em seguida, selecione a tecla **F5** para executar a aplicação.
   
    Receberá uma notificação de alerta em todos os dispositivos registados. Selecionar ou tocar na faixa do alerta carrega a aplicação.

Pode encontrar todos os payloads possíveis nos tópicos [catálogo de alertas] e [catálogo de mosaicos] e [descrição geral de destaque] no MSDN.

## <a name="next-steps"></a>Passos seguintes
Neste exemplo simples, enviou notificações de difusão para todos os seus dispositivos Windows através do portal ou de uma aplicação de consola. Como próximo passo, recomendamos o tutorial [Use Notification Hubs to push notifications to users ] (Utilizar os Hubs de Notificação para enviar notificações push aos utilizadores). Demonstra como enviar notificações de um back-end ASP.NET mediante a utilização de etiquetas para segmentar utilizadores específicos.

Se pretende segmentar os utilizadores por grupos de interesses, consulte [Utilizar Notification Hubs para enviar notícias de última hora]. 

Para obter informações mais gerais sobre os Hubs de Notificação, veja [Notification Hubs guidance](notification-hubs-push-notification-overview.md) (Orientações dos Hubs de Notificação).

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Use Notification Hubs to push notifications to users ]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Utilizar Notification Hubs para enviar notícias de última hora]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[catálogo de alertas]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[catálogo de mosaicos]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[descrição geral de destaque]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
