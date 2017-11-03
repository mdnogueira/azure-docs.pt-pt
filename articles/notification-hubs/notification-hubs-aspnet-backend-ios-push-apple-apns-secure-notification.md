---
title: Notification Hubs do Azure segurados Push
description: "Saiba como enviar notificações push segura para uma aplicação iOS a partir do Azure. Exemplos de código escrito em Objective-C e c#."
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: e5f09fb3716303bb21fe7442aa6fa8832174838e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs-secure-push"></a>Notification Hubs do Azure segurados Push
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Descrição geral
Suporte de notificação push no Microsoft Azure permite-lhe aceder a uma infraestrutura de push de fácil utilização, multiplataformas, escalamento horizontal, o que simplifica bastante a implementação de notificações push para aplicações de consumidor e para empresas para plataformas móveis.

Devido a regulamentação ou restrições de segurança, por vezes, uma aplicação pode pretender incluir algo na notificação que não pode ser transmitida através da infraestrutura de notificação push padrão. Este tutorial descreve como obter a mesma experiência, enviando informações confidenciais através de uma ligação segura, autenticada entre o dispositivo de cliente e o back-end da aplicação.

Um nível elevado, o fluxo é:

1. O back-end da aplicação:
   * Payload de arquivos segura na base de dados de back-end.
   * Envia o ID desta notificação para o dispositivo (sem informações seguras são enviadas).
2. A aplicação no dispositivo, quando receber a notificação:
   * O dispositivo contacta o back-end pedir o payload seguro.
   * A aplicação pode mostrar o payload como uma notificação no dispositivo.

É importante ter em atenção que o fluxo anterior (e neste tutorial), partimos do princípio que o dispositivo armazena um token de autenticação no armazenamento local, depois do utilizador iniciar sessão. Esta ação garante uma experiência totalmente integrada, como o dispositivo pode obter payload segura a notificação utilizando este token. Se a aplicação não armazena os tokens de autenticação do dispositivo, ou se estes tokens podem ter expirados, a aplicação de dispositivo, ao receber a notificação deverá apresentar uma notificação genérica pedir ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra o payload de notificação.

Este tutorial de Secure Push mostra como enviar uma notificação push de forma segura. O tutorial baseia-se a [notificam os utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tutorial, pelo que deverá concluir os passos que tutorial primeiro.

> [!NOTE]
> Este tutorial parte do princípio de que criou e configurado o notification hub, conforme descrito em [introdução aos Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificar o projeto iOS
Agora que alterou o seu back-end da aplicação para enviar apenas o *id* de uma notificação, tem de alterar a sua aplicação iOS para processar essa notificação e chamar novamente o back-end para obter a mensagem segura que será apresentado.

Para atingir este objetivo, temos de escrever a lógica para obter o conteúdo seguro do back-end da aplicação.

1. No **Appdelegate**, certifique-se a aplicação se regista notificações silenciosa, de modo que processa o id de notificação enviado a partir do back-end. Adicionar o **UIRemoteNotificationTypeNewsstandContentAvailability** opção na didFinishLaunchingWithOptions:
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. No seu **Appdelegate** adicionar uma secção de implementação na parte superior com a seguinte declaração:
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. Adicionar, em seguida, na secção implementation o código seguinte, substituindo o marcador de posição `{back-end endpoint}` com o ponto final para o back-end que obteve anteriormente:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

1. Agora que poderemos ter de processar a notificação de entrada e utilizar o método acima para obter o conteúdo para apresentar. Em primeiro lugar, temos de ativar a sua aplicação iOS ser executada em segundo plano quando receber uma notificação push. No **XCode**, selecione o seu projeto de aplicação no painel esquerdo, em seguida, clique o destino da sua aplicação principal no **destinos** secção no painel central.
2. Em seguida, clique em seu **capacidades** separador no topo do seu painel central e verifique o **notificações remotas** caixa de verificação.
   
    ![][IOS1]
3. No **Appdelegate** adicione o seguinte método para processar as notificações push:
   
        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);
   
            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];
   
        }
   
    Tenha em atenção que é preferível para processar de acordo com os casos de propriedade de cabeçalho de autenticação em falta ou rejeição back-end. O processamento específico nestes casos dependem principalmente na sua experiência de utilizador de destino. É uma opção apresentar uma notificação com uma linha de comandos genérica para o utilizador autenticar para obter a notificação real.

## <a name="run-the-application"></a>Executar a aplicação
Para executar a aplicação, efetue o seguinte:

1. No XCode, execute a aplicação num dispositivo iOS físico (emitir notificações não irão funcionar no simulador do).
2. Na aplicação iOS IU, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia, mas têm de ser o mesmo valor.
3. Na aplicação iOS IU, clique em **início de sessão**. Em seguida, clique em **enviar push**. Deverá ver a notificação segura que está a ser apresentada no seu centro de notificações.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
