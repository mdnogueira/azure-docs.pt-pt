---
title: "Introdução ao Azure Mobile Engagement para iOS no Objective C | Microsoft Docs"
description: "Saiba como utilizar o Azure Mobile Engagement com notificações push e de análise para aplicações iOS."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 1b87a2ebb35b31ee3d3139ecead6267e62eb1033
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Introdução ao Azure Mobile Engagement para aplicações iOS no Objective C
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e o envio de notificações push para utilizadores segmentados para uma aplicação iOS.
Neste tutorial, crie uma aplicação iOS em branco que recolhe dados básicos e recebe as notificações push através do Sistema Apple Push Notification (APNS).

Para este tutorial, necessita do seguinte:

* XCode 8, que pode instalar a partir da MAC App Store
* O [SDK iOS do Mobile Engagement]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para aplicações iOS.

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).
>
>

## <a id="setup-azme"></a>Configurar o Mobile Engagement para a aplicação iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement
Este tutorial apresenta uma “integração básica”, o conjunto mínimo necessário para recolher dados e enviar uma notificação push. É possível encontrar toda a documentação da integração na página [Integração do SDK iOS do Mobile Engagement](mobile-engagement-ios-sdk-overview.md).

Iremos criar uma aplicação básica com o XCode para demonstrar a integração.

### <a name="create-a-new-ios-project"></a>Criar um novo projeto iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Ligar a aplicação ao back-end do Mobile Engagement
1. Transfira o [SDK iOS do Mobile Engagement].
2. Extraia o ficheiro .tar.gz para uma pasta no computador.
3. Clique com o botão direito do rato no projeto e selecione **Adicionar ficheiros a...**.

    ![][1]
4. Navegue para a pasta na qual extraiu o SDK, selecione a pasta `EngagementSDK`, clique em **Opções**, no canto inferior esquerdo**, confirme que a caixa de verificação** Copiar itens se necessário **e a caixa de verificação do destino estão assinaladas e prima** OK.

    ![][2]
5. Abra o separador **Fases de Criação** e, no menu **Ligar Binário Com Bibliotecas**, adicione as estruturas conforme é mostrado abaixo:

    ![][3]
6. Volte ao Portal do Azure na página **Informações de Ligação** da aplicação e copie a cadeia de ligação.

    ![][4]
7. Adicione a seguinte linha de código no seu ficheiro **AppDelegate.m**.

        #import "EngagementAgent.h"
8. Agora cole a cadeia de ligação no delegado `didFinishLaunchingWithOptions`.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
              [...]   
              [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
              [...]
        }
9. `setTestLogEnabled` é uma instrução opcional que permite registos do SDK para que identifique problemas.

## <a id="monitor"></a>Ativar a monitorização em tempo real
Para iniciar o envio de dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (Atividade) para o back-end do Mobile Engagement.

1. Abra o ficheiro **ViewController.h** e importe **EngagementViewController.h**:

    `#import "EngagementViewController.h"`
2. Agora substitua a superclasse da interface **ViewController** por `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

## <a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Ativar as notificações push e mensagens na aplicação
O Mobile Engagement permite interagir com os seus utilizadores e ALCANÇAR com notificações push e mensagens na aplicação no contexto das campanhas. Este módulo é designado ALCANCE no portal do Mobile Engagement.
As secções seguintes configuram a aplicação para as receber.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Permitir que a aplicação receba Notificações Push Automáticas
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Adicionar a biblioteca de Alcance ao projeto
1. Clique com o botão direito do rato no projeto.
2. Selecione **Adicionar ficheiro a**.
3. Navegue para a pasta onde extraiu o SDK.
4. Selecione a pasta `EngagementReach`.
5. Clique em **Adicionar**.

### <a name="modify-your-application-delegate"></a>Modificar o seu Delegado de Aplicação
1. Novamente no ficheiro **AppDeletegate.m**, importe o módulo de Alcance do Engagement.

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>
2. Dentro do método `application:didFinishLaunchingWithOptions`, crie um módulo de Alcance e transmita-o à sua linha de inicialização do Engagement existente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Permitir que a aplicação receba Notificações Push do APNS
1. Adicionar a linha seguinte ao método `application:didFinishLaunchingWithOptions`:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }
2. Adicionar o método `application:didRegisterForRemoteNotificationsWithDeviceToken` da seguinte forma:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
             [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }
3. Adicionar o método `didFailToRegisterForRemoteNotificationsWithError` da seguinte forma:

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           NSLog(@"Failed to get token, error: %@", error);
        }
4. Adicionar o método `didReceiveRemoteNotification:fetchCompletionHandler` da seguinte forma:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SDK iOS do Mobile Engagement]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
