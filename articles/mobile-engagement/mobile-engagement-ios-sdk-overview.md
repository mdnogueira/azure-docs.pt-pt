---
title: "IOS do Mobile Engagement descrição geral do SDK do Azure | Microsoft Docs"
description: "Mais recentes atualizações e procedimentos para o SDK do iOS do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 6acd343782a3ee07750e27ec3022ff81cedfadee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>SDK do iOS do Azure Mobile Engagement
Comece por aqui obter todos os detalhes sobre como integrar o Azure Mobile Engagement numa aplicação iOS. Se gostaria de experimentá-la primeiro, certifique-se de que fazer nosso [tutorial de 15 minutos](mobile-engagement-ios-get-started.md).

Clique para ver o [SDK conteúdo](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>Procedimentos de integração
1. Comece por aqui: [como integrar o Mobile Engagement na sua aplicação iOS](mobile-engagement-ios-integrate-engagement.md)
2. Para obter notificações: [como integrar o alcance (notificações) na sua aplicação iOS](mobile-engagement-ios-integrate-engagement-reach.md)
3. Implementação do plano de etiqueta: [como utilizar o Mobile Engagement avançadas etiquetagem API na sua aplicação iOS](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão
### <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Destaques fixos limpo em segundo plano.
* Avisos fixos no XCode 9 sobre APIs não chamadas na fila principal.
* Fixo uma fuga de memória em inquéritos de alcance.
* Remover o suporte para iOS 6. x. A partir desta versão do destino da implementação da aplicação tem de ser, pelo menos, o iOS 7.

Para a versão anterior, consulte o [concluir notas de versão](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se já tiver integrado uma versão antiga do Engagement na sua aplicação, terá de considerar os seguintes pontos ao atualizar o SDK.

Poderá ter de seguir vários procedimentos, se omitido várias versões de consulte o SDK todo [atualizar procedimentos](mobile-engagement-ios-upgrade-procedure.md).

Para cada versão nova do SDK tem de substituir primeiro (remover e voltar a importar no xcode) as pastas EngagementSDK e EngagementReach.

### <a name="from-300-to-400"></a>De 3.0.0 para 4.0.0
### <a name="xcode-8"></a>XCode 8
XCode 8 é obrigatório iniciados a partir da versão 4.0.0 do SDK.

> [!NOTE]
> Se é realmente depende do XCode 7, em seguida, pode utilizar o [iOS o Engagement SDK v3.2.4](https://aka.ms/r6oouh). Não há um erro conhecido no módulo de alcance nesta versão anterior ao executar em dispositivos iOS 10: as notificações do sistema não estão alvo de ação. Para corrigir esta situação, terá de implementar a API preterida `application:didReceiveRemoteNotification:` na sua aplicação delegar da seguinte forma:
>
>

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Não recomendamos esta solução** como este comportamento pode alterar qualquer atualização de versão futura iOS (mesmo secundárias) porque esta API do iOS foi preterido. Deverá mudar para o XCode 8 logo que possível.
>
>

#### <a name="usernotifications-framework"></a>UserNotifications framework
Tem de adicionar o `UserNotifications` framework nas fases de criação.

no Explorador de projeto, abra o painel de projeto e selecione o destino correto. Em seguida, abra o **"Fases de compilação"** separador e no **"Binário com bibliotecas de ligação"** menu, adicionar framework `UserNotifications.framework` -definir a ligação como`Optional`

#### <a name="application-push-capability"></a>Capacidade de push da aplicação
XCode 8 pode repor a sua aplicação push capacidade, volte a verificar `capability` separador de destino selecionado.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Adicione o novo código de registo de notificação de iOS 10
O fragmento de código anterior para registar a aplicação para enviar notificações ainda funciona, mas está a utilizar APIs preteridas ao executar o iOS 10.

Importar o `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

No seu delegado de aplicação `application:didFinishLaunchingWithOptions` substituição de método:

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

por:

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

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Resolver conflitos de delegado UNUserNotificationCenter

*Se não a aplicação ou uma das suas bibliotecas de terceiros implementa um `UNUserNotificationCenterDelegate` , em seguida, pode ignorar esta parte.*

A `UNUserNotificationCenter` delegado é utilizado pelo SDK para monitorizar o ciclo de vida de notificações de envolvimento em dispositivos com IOS 10 ou superior. O SDK tem a seus próprios uma implementação do `UNUserNotificationCenterDelegate` protocolo, mas pode existir apenas uma `UNUserNotificationCenter` delegar por aplicação. Quaisquer outro delegado adicionado para o `UNUserNotificationCenter` objeto entram em conflito com o Engagement um. Se o SDK Deteta delegado do ou quaisquer outras terceiros, em seguida, não utilizará a seus próprios implementação para lhe dar uma oportunidade para resolver os conflitos. Terá de adicionar lógica o Engagement ao seu próprio delegado para resolver os conflitos.

Existem duas formas de alcançar isto.

Proposta 1, basta pelo seu delegado de reencaminhamento chamadas para o SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Ou proposta 2, pelo herdar o `AEUserNotificationHandler` classe

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Pode determinar se uma notificação provém do Engagement ou não transferindo o `userInfo` dicionário ao agente `isEngagementPushPayload:` método de classe.

Certifique-se de que o `UNUserNotificationCenter` delegado do objeto está definido para o seu delegado dentro de um a `application:willFinishLaunchingWithOptions:` ou `application:didFinishLaunchingWithOptions:` método de delegado de aplicação.
Por exemplo, se tiver implementado a proposta acima 1:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }
