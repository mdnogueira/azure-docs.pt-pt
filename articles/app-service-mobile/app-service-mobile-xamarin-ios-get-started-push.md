---
title: "Adicionar notificações push à aplicação xamarin. IOS com o App Service do Azure"
description: "Saiba como utilizar o App Service do Azure para enviar notificações push à aplicação xamarin. IOS"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: glenga
ms.openlocfilehash: bf922e49c4c92d0065817a5dd6c7d10a04737304
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Adicionar notificações push à aplicação xamarin. IOS
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial, adicionar notificações push para o [início rápido do xamarin. IOS](app-service-mobile-xamarin-ios-get-started.md) projeto para que uma notificação push é enviada para o dispositivo sempre que é inserido um registo.

Se utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos
* Concluir o [início rápido do xamarin. IOS](app-service-mobile-xamarin-ios-get-started.md) tutorial.
* Um dispositivo iOS físico. Notificações push não são suportadas pelo simulador de iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registar a aplicação para notificações push no portal de programador da Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Configure a sua aplicação móvel para enviar notificações push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar notificações push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Configurar o seu projeto xamarin. IOS
[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações push à aplicação
1. No **QSTodoService**, adicione a seguinte propriedade para que **AppDelegate** pode adquirir o cliente móvel:
   
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }
2. Adicione o seguinte `using` declaração na parte superior do **appdelegate. cs** ficheiro.
   
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
3. No **AppDelegate**, substituir o **FinishedLaunching** eventos:
   
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());
   
            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
   
            return true;
        }
4. No mesmo ficheiro, substituir o **RegisteredForRemoteNotifications** eventos. Neste código que está a registar a notificação um modelo simples que serão enviadas em todas as plataformas suportadas pelo servidor.
   
    Para obter mais informações sobre modelos com os Notification Hubs, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


1. Em seguida, substituir o **DidReceivedRemoteNotification** eventos:
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;
   
            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();
   
            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

A aplicação agora é atualizada para suportar notificações push.

## <a name="test"></a>Notificações de push de teste na sua aplicação
1. Prima a **executar** botão para compilar o projeto e iniciar a aplicação no dispositivo iOS com capacidade de, em seguida, clique em **OK** para aceitar as notificações push.
   
   > [!NOTE]
   > Tem de aceitar explicitamente notificações push da sua aplicação. Este pedido só ocorre na primeira vez que a aplicação é executada.
   > 
   > 
2. Na aplicação, digite uma tarefa e, em seguida, clique o sinal de adição (**+**) ícone.
3. Certifique-se de que uma notificação é recebida, em seguida, clique em **OK** a dispensa da notificação.
4. Repita o passo 2 e imediatamente feche a aplicação, em seguida, certifique-se de que é apresentada uma notificação.

Concluiu com êxito este tutorial.

<!-- Images. -->

<!-- URLs. -->



