---
title: "Registar o utilizador atual para notificações push utilizando a Web API | Microsoft Docs"
description: "Saiba como pedir o registo da notificação push numa aplicação iOS com Notification Hubs do Azure quando o registo é efetuado através da API Web do ASP.NET."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: fd56bb2dd627b31f00363851a4e76484aa382988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registar o utilizador atual para notificações push utilizando o ASP.NET
> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
> 
> 

## <a name="overview"></a>Descrição geral
Este tópico mostra como pedir o registo da notificação push com Notification Hubs do Azure quando o registo é efetuado através da API Web do ASP.NET. Este tópico expande o tutorial [notificar os utilizadores com os Notification Hubs]. Tem já tiver concluído os passos necessários que tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário de utilizadores notificar, consulte [notificar os utilizadores com os Notification Hubs].

## <a name="update-your-app"></a>Atualizar a sua aplicação
1. No seu MainStoryboard_iPhone.storyboard, adicione os seguintes componentes da biblioteca do objeto:
   
   * **Etiqueta**: "Push para utilizadores com os Notification Hubs"
   * **Etiqueta**: "InstallationId"
   * **Etiqueta**: "Utilizador"
   * **Campo de texto**: "Utilizador"
   * **Etiqueta**: "Password"
   * **Campo de texto**: "Password"
   * **Botão**: "Início de sessão"
     
     Neste momento, o storyboard o seguinte aspeto:
     
      ![][0]
2. No editor do assistente, criar saídas para todos os controlos desativados-los, ligar os campos de texto com o controlador de vista (delegado) e criar um **ação** para o **início de sessão** botão.
   
       ![][1]
   
       Your BreakingNewsViewController.h file should now contain the following code:
   
        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;
   
        - (IBAction)login:(id)sender;
3. Crie uma classe com o nome **DeviceInfo**e copie o seguinte código para a secção de interface do ficheiro DeviceInfo.h:
   
        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;
4. Copie o seguinte código na secção de implementação do ficheiro DeviceInfo.m:
   
            @synthesize installationId = _installationId;
   
            - (id)init {
                if (!(self = [super init]))
                    return nil;
   
                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);
   
                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }
   
                return self;
            }
   
            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }
5. No PushToUserAppDelegate.h, adicione o seguinte singleton de propriedade:
   
        @property (strong, nonatomic) DeviceInfo* deviceInfo;
6. No **didFinishLaunchingWithOptions** método PushToUserAppDelegate.m, adicione o seguinte código:
   
        self.deviceInfo = [[DeviceInfo alloc] init];
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
   
    A primeira linha inicializa a **DeviceInfo** singleton. A segunda começa a linha o registo para as notificações push, que já está presente é já tiver concluído a [introdução aos Hubs de notificação] tutorial.
7. No PushToUserAppDelegate.m, implementa o método **didRegisterForRemoteNotificationsWithDeviceToken** no seu AppDelegate e adicione o seguinte código:
   
        self.deviceInfo.deviceToken = deviceToken;
   
    Isto define o token do dispositivo para o pedido.
   
   > [!NOTE]
   > Neste momento, não existe não deve ser quaisquer outras código neste método. Se já tiver uma chamada para o **registerNativeWithDeviceToken** método que foi adicionado ao concluiu a [introdução aos Hubs de notificação](/manage/services/notification-hubs/get-started-notification-hubs-ios/) tutorial, tem comente ou remover esse chamada.
   > 
   > 
8. No ficheiro PushToUserAppDelegate.m, adicione o seguinte método do processador:
   
   * aplicação (nulo):(UIApplication *) aplicação didReceiveRemoteNotification:(NSDictionary *) userInfo {NSLog (@"% @", userInfo);   UIAlertView * alerta = [[UIAlertView alocação] initWithTitle:@"Notification" mensagem: [userInfo objectForKey:@"inAppMessage"] delegado: nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];   [alerta Mostrar]; }
   
   Este método apresenta um alerta na IU quando a aplicação recebe notificações enquanto estiver em execução.
9. Abra o ficheiro de PushToUserViewController.m e devolver o teclado na implementação seguinte:
   
        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }
10. No **viewDidLoad** método no ficheiro PushToUserViewController.m, inicializar a etiqueta de installationId da seguinte forma:
    
         DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
         Self.installationId.text = deviceInfo.installationId;
11. Adicione as seguintes propriedades de interface no PushToUserViewController.m:
    
        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;
12. Em seguida, adicione a seguinte implementação:
    
            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }
    
            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];
    
                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
    
                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;
    
                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;
    
                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }
    
                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }
    
                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }
13. Copie o seguinte código para o **início de sessão** método do processador criado pelo XCode:
    
            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    
            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];
    
            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];
    
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];
    
            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];
    
    Este método obtém um ID de instalação e o canal para notificações push e envia-, juntamente com o tipo de dispositivo, para o método Web API autenticado, que cria um registo dos Notification Hubs. Esta API Web foi definido na [notificar os utilizadores com os Notification Hubs].

Agora que a aplicação de cliente foi atualizada, voltar para o [notificar os utilizadores com os Notification Hubs] e atualizar o serviço móvel para enviar notificações com os Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[notificar os utilizadores com os Notification Hubs]: /manage/services/notification-hubs/notify-users-aspnet

[introdução aos Hubs de notificação]: /manage/services/notification-hubs/get-started-notification-hubs-ios
