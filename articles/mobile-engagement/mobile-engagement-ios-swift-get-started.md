---
title: "Introdução ao Azure Mobile Engagement para iOS no Swift | Microsoft Docs"
description: "Saiba como utilizar o Azure Mobile Engagement com Notificações Push e de Análise para Aplicações iOS."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
ms.openlocfilehash: 1011b9823333e79a52cd2d187df4f8d063b1f799
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Introdução ao Azure Mobile Engagement para Aplicações iOS no Swift
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e o envio de notificações push para utilizadores segmentados para uma aplicação iOS.
Neste tutorial, crie uma aplicação iOS em branco que recolhe dados básicos e recebe as notificações push através do Sistema Apple Push Notification (APNS).

Para este tutorial, necessita do seguinte:

* XCode 8, que pode instalar a partir da MAC App Store
* O [SDK iOS do Mobile Engagement]
* Certificado de notificação push (.p12) que pode obter no Apple Dev Center

> [!NOTE]
> Este tutorial utiliza a versão 3.0 do Swift. 
> 
> 

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para aplicações iOS.

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para a aplicação iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement
Este tutorial apresenta uma “integração básica”, o conjunto mínimo necessário para recolher dados e enviar uma notificação push. É possível encontrar toda a documentação da integração na página [Integração do SDK iOS do Mobile Engagement](mobile-engagement-ios-sdk-overview.md).

Iremos criar uma aplicação básica com o XCode para demonstrar a integração:

### <a name="create-a-new-ios-project"></a>Criar um novo projeto iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a aplicação ao back-end do Mobile Engagement
1. Transfira o [SDK iOS do Mobile Engagement]
2. Extraia o ficheiro .tar.gz para uma pasta no computador
3. Clique com o botão direito do rato no projeto e selecione “Adicionar ficheiros a...”
   
    ![][1]
4. Navegue para a pasta onde extraiu o SDK e selecione a pasta `EngagementSDK` e, em seguida, prima OK.
   
    ![][2]
5. Abra o separador `Build Phases` e, no menu `Link Binary With Libraries`, adicione as estruturas conforme é mostrado abaixo:
   
    ![][3]
6. Crie um cabeçalho de Bridging para poder utilizar as APIs de Objective C do SDK ao escolher Ficheiro > Novo > Ficheiro > iOS > Origem > Ficheiro de Cabeçalho.
   
    ![][4]
7. Edite o ficheiro de cabeçalho de bridging para expor o código em Objective-C do Mobile Engagement ao seu código do Swift, adicione as seguintes importações:
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. Em Definições de Criação, certifique-se de que a definição de criação de Cabeçalho de Bridging em Objective-C no Compilador Swift – Geração de Código tem um caminho para este cabeçalho. Eis um exemplo de caminho: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (consoante o caminho)**
   
   ![][6]
9. Volte ao Portal do Azure na página *Informações de Ligação* da aplicação e copie a Cadeia de Ligação
   
   ![][5]
10. Agora cole a cadeia de ligação no delegado `didFinishLaunchingWithOptions`
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a id="monitor"></a>Ativar a monitorização em tempo real
Para iniciar o envio de dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (Atividade) para o back-end do Mobile Engagement.

1. Abra o ficheiro **ViewController.swift** e substitua a classe base de **ViewController** para se tornar **EngagementViewController**:
   
    `class ViewController : EngagementViewController {`

## <a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Ativar Notificações Push e mensagens na aplicação
O Mobile Engagement permite interagir e ALCANÇAR os seus utilizadores com Notificações Push e Mensagens na Aplicação no contexto das campanhas. Este módulo é designado ALCANCE no portal do Mobile Engagement.
As secções seguintes irão configurar a aplicação para as receber.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Permitir que a aplicação receba Notificações Push Automáticas
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Adicionar a biblioteca de Alcance ao projeto
1. Clique com o botão direito do rato no projeto
2. Selecione `Add file to ...`
3. Navegue para a pasta onde extraiu o SDK
4. Selecione a pasta `EngagementReach`
5. Clique em Adicionar
6. Edite o ficheiro de cabeçalho de bridging para expor os cabeçalhos de Alcance em Objective-C do Mobile Engagement e adicione as seguintes importações:
   
        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modificar o seu Delegado de Aplicação
1. Dentro do `didFinishLaunchingWithOptions` – crie um módulo de alcance e transmita-o à sua linha de inicialização do Engagement existente:
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Permitir que a aplicação receba Notificações Push do APNS
1. Adicionar a linha seguinte ao método `didFinishLaunchingWithOptions`:
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. Adicionar o método `didRegisterForRemoteNotificationsWithDeviceToken` da seguinte forma:
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. Adicionar o método `didReceiveRemoteNotification:fetchCompletionHandler:` da seguinte forma:
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SDK iOS do Mobile Engagement]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
