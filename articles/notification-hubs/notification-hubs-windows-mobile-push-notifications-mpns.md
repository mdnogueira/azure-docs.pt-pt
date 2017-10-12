---
title: "Enviar notificações push com os Hubs de Notificação do Azure no Windows Phone | Microsoft Docs"
description: "Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Windows Phone 8 ou Windows Phone 8.1 Silverlight."
services: notification-hubs
documentationcenter: windows
keywords: "notificação push, notificação push, push window phone"
author: ysxu
manager: erikre
editor: erikre
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: f0bfe81f849813d146d644b32490af657b1071b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Enviar notificações push com os Notification Hubs do Azure no Windows Phone
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).
> 
> 

Este tutorial mostra-lhe como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Windows Phone 8 ou Windows Phone 8.1 Silverlight. Se estiver a segmentar o Windows Phone 8.1 (não Silverlight), consulte a versão [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Neste tutorial, vai criar uma aplicação Windows Phone 8 em branco que recebe notificações push utilizando o Serviço de Notificações Push da Microsoft (MPNS). Quando tiver terminado, poderá utilizar o Notification Hub para difundir notificações push para todos os dispositivos a executar a sua aplicação.

> [!NOTE]
> O SDK dos Notification Hubs do Windows Phone não suporta a utilização do Serviço de Notificações Push do Windows (WNS) com aplicações do Windows Phone 8.1 Silverlight. Para utilizar o WNS (em vez do MPNS) com aplicações do Windows Phone 8.1 Silverlight, siga o [Tutorial Notification Hubs – Windows Phone Silverlight ], que utiliza a APIs REST.
> 
> 

Este tutorial demonstra o cenário de difusão simples na utilização de Notification Hubs.

## <a name="prerequisites"></a>Pré-requisitos
Para este tutorial, necessita do seguinte:

* [Visual Studio 2012 Express para Windows Phone] ou uma versão posterior.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Notification Hubs para aplicações Windows Phone 8.

## <a name="create-your-notification-hub"></a>Criar o Notification Hub
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Clique na secção <b>Serviços de Notificação</b> (em <i>Definições</i>), clique em <b>Windows Phone (MPNS)</b> e, em seguida, clique na caixa de verificação <b>Ativar push não autenticado</b>.</p>
</li>
</ol>

&emsp;&emsp;![Portal do Azure – Ativar notificações push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

O seu hub já está criado e configurado para enviar notificações não autenticadas para Windows Phone.

> [!NOTE]
> Este tutorial utiliza MPNS no modo não autenticado. O modo MPNS não autenticado vem com restrições quanto às notificações que pode enviar para cada canal. Os Notification Hubs suportam o [Modo MPNS autenticado ](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) ao permitirem-lhe carregar o seu certificado.
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Ligar a aplicação ao Notification Hub
1. No Visual Studio, crie uma nova aplicação do Windows Phone 8.
   
       ![Visual Studio - New Project - Windows Phone App][13]
   
    No Visual Studio 2013 Update 2 ou posterior crie, em vez disso, uma aplicação Silverlight do Windows Phone.
   
    ![Visual Studio – Novo Projeto – Aplicação em Branco – Windows Phone Silverlight][11]
2. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, clique em **Gerir Pacotes NuGet**.
   
    É apresentada a caixa de diálogo **Gerir Pacotes NuGet**.
3. Procure `WindowsAzure.Messaging.Managed`, clique em **Instalar** e aceite os termos de utilização.
   
    ![Visual Studio – Gestor de Pacotes NuGet][20]
   
    Esse procedimento transfere, instala e adiciona uma referência à biblioteca de Mensagens do Azure para Windows utilizando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">Pacote NuGet WindowsAzure.Messaging.Managed </a>.
4. Abra o ficheiro App,xaml.cs e adicione as seguintes instruções `using`:
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Adicione o seguinte código no topo do método **Application_Launching** em App.xaml.cs:
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > O valor **MyPushChannel** é um índice que é utilizado para pesquisar um canal existente na coleção [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx). Se não existir, crie uma nova entrada com esse nome.
   > 
   > 
   
    Não se esqueça de inserir o nome do seu hub e a cadeia de ligação denominada **DefaultListenSharedAccessSignature** que obteve na secção anterior.
    Este código obtém o URI do canal para a aplicação a partir do MPNS e, em seguida, regista esse URI de canal no Notification Hub. Esta ação garante também que o URI do canal é registado no Notification Hub sempre que a aplicação for iniciada.
   
   > [!NOTE]
   > Este tutorial envia um alerta de notificação para o dispositivo. Se, em vez disso, enviar uma notificação de mosaico, tem de chamar o método o **BindToShellTile** no canal. Para suportar notificações de alerta e também de mosaico, chame **BindToShellTile** e **BindToShellToast**.
   > 
   > 
6. No Explorador de Soluções, expanda **Propriedades**, abra o `WMAppManifest.xml` ficheiro, clique no separador **Capacidades** e verifique se a capacidade **ID_CAP_PUSH_NOTIFICATION** está selecionada.
   
       ![Visual Studio - Windows Phone App Capabilities][14]
   
       This ensures that your app can receive push notifications. Without it, any attempt to send a push notification to the app will fail.
7. Prima a tecla `F5` para executar a aplicação.
   
    É apresentada na aplicação uma mensagem de registo.
8. Feche a aplicação.  
   
   > [!NOTE]
   > Para receber um alerta de notificação push, a aplicação não pode estar a ser executada em primeiro plano.
   > 
   > 

## <a name="send-push-notifications-from-your-backend"></a>Enviar notificações push a partir do back-end
Pode enviar notificações push com os Notification Hubs a partir de qualquer back-end através da nossa <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">Interface REST</a> pública. Neste tutorial, enviará notificações push utilizando uma aplicação .NET de consola. 

Para obter um exemplo de como enviar notificações push de um back-end de ASP.NET WebAPI que esteja integrado com os Hubs de Notificação, veja [Os Hubs de Notificação do Azure Notificam os Utilizadores com back-end .NET](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Para obter um exemplo sobre como enviar notificações push com as [APIs REST](https://msdn.microsoft.com/library/azure/dn223264.aspx), consulte [Como utilizar os Hubs de Notificação de Java](notification-hubs-java-push-notification-tutorial.md) e [Como utilizar os Hubs de Notificação de PHP](notification-hubs-php-push-notification-tutorial.md).

1. Clique com o botão direito do rato na solução, selecione **Adicionar** e **Novo Projeto…**, e, em seguida, em **Visual C#**, clique em **Windows** e **Aplicação de Consola** e, em seguida, clique em **OK**.
   
       ![Visual Studio - New Project - Console Application][6]
   
    Esta ação adiciona uma nova aplicação da consola Visual C# à solução. Também pode fazer isto numa solução separada.
2. Clique em **Ferramentas**, clique em **Gestor de Pacotes de Biblioteca** e, em seguida, em **Consola do Gestor de Pacotes**.
   
    Esta ação exibe a Consola do Gestor de Pacotes.
3. Na janela **Consola do Gestor de Pacotes**, defina o **Projeto Predefinido** como o seu novo projeto da aplicação de consola e, em seguida, execute o seguinte comando na janela da consola:
   
       Install-Package Microsoft.Azure.NotificationHubs
   
   Esta ação adiciona uma referência ao SDK dos Notification Hubs do Azure utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Pacote NuGet Microsoft.Azure.Notification Hubs</a>.
4. Abra o ficheiro `Program.cs` e adicione a seguinte instrução `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Na classe `Program`, adicione o método seguinte:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }
   
    Não se esqueça de substituir o marcador de posição `<hub name>` pelo nome do Notification Hub que aparece no portal. Substitua, também, o marcador de posição da cadeia de ligação pela cadeia de ligação denominada **DefaultFullSharedAccessSignature**, que obteve na secção “Configurar o Notification Hub”.
   
   > [!NOTE]
   > Certifique-se de que utiliza a cadeia de ligação com acesso **Completo** e não com acesso de **Escuta**. A cadeia de ligação de acesso escuta não concede permissões para enviar notificações push.
   > 
   > 
6. Adicione as seguintes linhas ao seu método `Main`:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Com o emulador do Windows Phone em execução e a aplicação fechada, defina o projeto de aplicação de consola como o projeto de arranque predefinido e, em seguida, prima a tecla `F5` para executar a aplicação.
   
    Receberá um alerta de notificação push. Tocar na faixa do alerta carrega a aplicação.

Pode encontrar todos os payloads possíveis nos tópicos [catálogo de alertas] e [catálogo de mosaicos] no MSDN.

## <a name="next-steps"></a>Passos seguintes
Neste exemplo simples, difundiu notificações push para todos os seus dispositivos Windows Phone 8. 

Para visar utilizadores específicos, consulte o tutorial [Utilizar Notification Hubs para notificações push a utilizadores]. 

Se pretender segmentar os utilizadores por grupos de interesses, pode ler [Utilizar Notification Hubs para enviar notícias de última hora]. 

Saiba mais sobre como utilizar os Notification Hubs em [Documentação de Orientação dos Notification Hubs].

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Documentação de Orientação dos Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Utilizar Notification Hubs para notificações push a utilizadores]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Utilizar Notification Hubs para enviar notícias de última hora]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[catálogo de alertas]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de mosaicos]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Tutorial Notification Hubs – Windows Phone Silverlight ]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

