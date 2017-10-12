---
title: "Introdução ao Azure Mobile Engagement para Xamarin.iOS"
description: "Saiba como utilizar o Azure Mobile Engagement com Notificações Push e de Análise para Aplicações Xamarin.iOS."
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0448209e-fff6-47bd-985c-2cf074bac12f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9938c3e994acf31244825b1afb347f8c9f90ebe3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Introdução ao Azure Mobile Engagement para Aplicações Xamarin.iOS
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e o envio de notificações push para utilizadores segmentados numa aplicação Xamarin.iOS.
Neste tutorial, crie uma aplicação Xamarin.iOS em branco que recolhe dados básicos e recebe as notificações push através do Sistema Apple Push Notification (APNS).

> [!NOTE]
> O serviço Azure Mobile Engagement será extinto em março de 2018 e, atualmente, apenas está disponível para os clientes existentes. Para obter mais informações, veja [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Para este tutorial, necessita do seguinte:

* [Xamarin Studio](http://xamarin.com/studio). Também pode utilizar o Visual Studio com Xamarin, mas este tutorial utiliza o Xamarin Studio. Para obter instruções de instalação, consulte [Configuração e Instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
* [SDK Xamarin do Mobile Engagement](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para a aplicação iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement
Este tutorial apresenta uma “integração básica”, o conjunto mínimo necessário para recolher dados e enviar uma notificação push.

Iremos criar uma aplicação básica com o Xamarin para demonstrar a integração:

### <a name="create-a-new-xamarinios-project"></a>Criar um novo projeto Xamarin.iOS
1. Execute o Xamarin Studio. Aceda a **Ficheiro** -> **Novo** -> **Solução** 
   
    ![][1]
2. Selecione **Aplicação de Vista Única**, certifique-se de que a linguagem selecionada é **C#** e, em seguida, clique em **Seguinte**.
   
    ![][2]
3. Preencha o **Nome da Aplicação** e o **Identificador de Organização** e, em seguida, clique em **seguinte**. 
   
    ![][3]
   
   > [!IMPORTANT]
   > Certifique-se de que o perfil de publicação que acaba por utilizar para implementar a aplicação iOS está a utilizar um ID de Aplicação que corresponde exatamente ao Identificador de Pacote que tem aqui. 
   > 
   > 
4. Atualize o **Nome do Projeto**, o **Nome da Solução** e a **Localização** se necessário e clique em **Criar**.
   
    ![][4]

O Xamarin Studio criará a aplicação de demonstração na qual iremos integrar o Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a aplicação ao back-end do Mobile Engagement
1. Clique com o botão direito do rato na pasta **Pacotes** nas janelas Solução e selecione **Adicionar Pacotes...**
   
    ![][5]
2. Procure o **SDK Xamarin do Microsoft Azure Mobile Engagement** e adicione-o à sua solução.  
   
    ![][6]
3. Abra **AppDelegate.cs** e adicione o seguinte ao utilizar a instrução:
   
        using Microsoft.Azure.Engagement.Xamarin;
4. No método **FinishedLaunching**, adicione o seguinte ao inicializar a ligação com o back-end do Mobile Engagement. Certifique-se de que adiciona a sua **ConnectionString**. Este código também utiliza um **NotificationIcon** fictício que é adicionado pelo SDK do Mobile Engagement que pode pretender substituir. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a id="monitor"></a>Ativar a monitorização em tempo real
Para iniciar o envio de dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã para o back-end do Mobile Engagement.

1. Abra **ViewController.cs** e adicione o seguinte ao utilizar a instrução:
   
        using Microsoft.Azure.Engagement.Xamarin;
2. Substitua a classe a partir da qual `ViewController` herda de `UIViewController` para `EngagementViewController`. 

## <a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Ativar as notificações push e mensagens na aplicação
O Mobile Engagement permite interagir com os seus utilizadores e ALCANÇAR com notificações push e mensagens na aplicação no contexto das campanhas. Este módulo é designado ALCANCE no portal do Mobile Engagement.
As secções seguintes configuram a aplicação para as receber.

### <a name="modify-your-application-delegate"></a>Modificar o seu Delegado de Aplicação
1. Abra o **AppDelegate.cs** e adicione o seguinte ao utilizar a instrução:
   
        using System; 
2. Agora, dentro do método `FinishedLaunching`, adicione o seguinte para se registar para mensagens de push após `EngagementAgent.init(...)`
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. Por fim, atualize ou adicione os seguintes métodos:
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. No seu ficheiro **Info.plist** na solução, confirme que o **Identificador de Pacote** corresponde ao **ID da Aplicação** que possui no seu perfil de aprovisionamento no Apple Dev Center. 
   
    ![][7]
5. No mesmo ficheiro **Info.plist**, certifique-se de que assinalou as opções **Ativar Modos de Segundo Plano** e **Notificações Remotas**. 
   
     ![][8]
6. Execute a aplicação no dispositivo que tiver associado a este perfil de publicação. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
