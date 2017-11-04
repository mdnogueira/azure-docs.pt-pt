---
title: Os Notification Hubs do Azure Notificam Utilizadores do iOS com back-end do .NET
description: "Saiba como enviar notificações push para utilizadores no Azure. Exemplos de código escrito em Objective-C e a API .NET para o back-end."
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 0fa7a886e1ecb0a90b6aebc1dbf9ef0c6ce1acf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs-notify-users-for-ios-with-net-backend"></a>Os Notification Hubs do Azure Notificam Utilizadores do iOS com back-end do .NET
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Descrição geral
Suporte de notificação push no Azure permite-lhe aceder a uma fácil de utilizar, multiplatform e infraestrutura push ampliada, o que simplifica bastante a implementação de notificações push para aplicações de consumidor e para empresas para plataformas móveis. Este tutorial mostra-lhe como pode utilizar os Hubs de Notificação do Azure para enviar notificações push para um utilizador da aplicação específico num dispositivo específico. Um back-end de ASP.NET WebAPI end é utilizado para autenticar clientes e gerar notificações, conforme mostrado no tópico de documentação de orientação [registar de back-end da aplicação](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

> [!NOTE]
> Este tutorial parte do princípio de que criou e configurado o notification hub, conforme descrito em [introdução aos Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Este tutorial também é o pré-requisito para a [Secure Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) tutorial.
> Se pretender utilizar Mobile Apps como o serviço de back-end, consulte o [Mobile Apps introdução Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modificar a sua aplicação iOS
1. Abra a aplicação de vista de página única que criou no [introdução aos Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) tutorial.
   
   > [!NOTE]
   > Nesta secção partimos do pressuposto que o projeto está configurado com um nome de organização vazio. Caso contrário, terá de preceder o nome da sua organização para todos os nomes de classe.
   > 
   > 
2. No seu Main.storyboard adicione os componentes mostrados na captura de ecrã abaixo da biblioteca de objeto.
   
    ![][1]
   
   * **Nome de utilizador**: UITextField de A com o texto do marcador de posição, *introduza o nome de utilizador*, imediatamente abaixo de envio resultados etiqueta e com as margens esquerdas e da direita e sob a etiqueta de resultados de envio.
   * **Palavra-passe**: UITextField de A com o texto do marcador de posição, *introduza a palavra-passe*, imediatamente abaixo o nome de utilizador texto campo e restrita para as margens esquerdas e da direita e por baixo do campo de texto do nome de utilizador. Verifique o **seguro de entrada de texto** opção em Inspector atributo, *devolver chave*.
   * **Inicie sessão no**: A UIButton etiqueta imediatamente por baixo do campo de texto de palavra-passe e desmarque o **ativado** opção em Inspector atributos, *conteúdo de controlo*
   * **WNS**: etiqueta e o comutador para ativar a notificação enviada para a serviço de notificação do Windows se de que foi configurado no hub. Consulte o [Windows introdução](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tutorial.
   * **GCM**: etiqueta e o comutador para permitir o envio da notificação para o Google Cloud Messaging se de que foi configurado no hub. Consulte [Android introdução](notification-hubs-android-push-notification-google-gcm-get-started.md) tutorial.
   * **APNS**: etiqueta e o comutador para permitir o envio da notificação para o serviço de notificação de plataforma Apple.
   * **Nome de utilizador Recipent**: UITextField de A com o texto do marcador de posição, *etiqueta de nome de utilizador de destinatário*, imediatamente abaixo o GCM etiqueta e restrita para as margens esquerdas e da direita e por baixo da etiqueta do GCM.

    Foram adicionados alguns componentes no [introdução aos Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) tutorial.

1. **CTRL** arraste dos componentes na vista para viewcontroller. H e adicione estes saídas de novo.
   
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
        @property (weak, nonatomic) IBOutlet UITextField *NotificationField;
   
        // Used to enable the buttons on the UI
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;
   
        // Used to enabled sending notifications across platforms
        @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;
   
        - (IBAction)LogInAction:(id)sender;
2. No viewcontroller. H, adicione a seguinte `#define` imediatamente por baixo declarações de importação. Substitui o *< introduzir o ponto final de back-end\>*  marcador de posição com o URL de destino que utilizou para implementar o seu back-end da aplicação na secção anterior. Por exemplo, *http://you_backend.azurewebsites.net*.
   
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
3. No seu projeto, crie um novo **Cocoa Touch classe** denominado **RegisterClient** a interface com o back-end ASP.NET que criou. Criar a classe a herdar `NSObject`. Em seguida, adicione o seguinte código no RegisterClient.h.
   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
4. Na atualização RegisterClient.m o `@interface` secção:
   
        @interface RegisterClient ()
   
        @property (strong, nonatomic) NSURLSession* session;
        @property (strong, nonatomic) NSURLSession* endpoint;
   
        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion;
        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;
   
        @end
5. Substitua o `@implementation` secção RegisterClient.m com o seguinte código.

        @implementation RegisterClient

        // Globals used by RegisterClient
        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

        -(instancetype) initWithEndpoint:(NSString*)Endpoint
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
                _endpoint = Endpoint;
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                    andCompletion:(void(^)(NSError*))completion
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description]
                stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                    uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
                completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                    tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                    @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                                options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                                encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                    registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                        objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                    _endpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data
                        encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                        [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId
                        forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        @end

    O código acima implementa lógica explicada no artigo de documentação de orientação [registar de back-end da aplicação](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) utilizando NSURLSession para efetuar REST chamadas para o back-end da aplicação e NSUserDefaults para armazenar localmente o registrationId devolvido através do hub de notificação.

    Tenha em atenção que esta classe requer a respetiva propriedade **authorizationHeader** ser definida para que funcionem corretamente. Esta propriedade é definida **ViewController** classe após o início de sessão.

1. No viewcontroller. H, adicione um `#import` declaração para RegisterClient.h. Em seguida, adicione uma declaração para o token do dispositivo e de referência para um `RegisterClient` instância no `@interface` secção:
   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
2. No ViewController.m, adicione uma declaração de método private no `@interface` secção:
   
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end

> [!NOTE]
> O fragmento seguinte não é um esquema de autenticação segura, deve substituir a implementação do **createAndSetAuthenticationHeaderWithUsername:AndPassword:** com o mecanismo de autenticação específicas que gera um token de autenticação a ser consumidos pela registar classe de cliente, por exemplo, OAuth, do Active Directory.
> 
> 

1. Em seguida, no `@implementation` secção ViewController.m adicione o seguinte código que adiciona a implementação para definir o cabeçalho de token e a autenticação do dispositivo.
   
        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }
   
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];
   
            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];
   
            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                        encoding:NSUTF8StringEncoding];
        }
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
   
    Tenha em atenção a como definir o token do dispositivo permite o registo no botão. Isto acontece porque como parte da ação de início de sessão, o controlador de vista regista para notificações push com o back-end da aplicação. Por conseguinte, não pretendemos início de sessão ação para estar acessível até o token do dispositivo foi corretamente configurado. Pode desassociar o início de sessão do registo de push, desde que a primeira ocorre antes desta última opção.
2. No ViewController.m, utilize os seguintes fragmentos para implementar o método de ação para sua **início de sessão** botão e um método para enviar a mensagem de notificação com o back-end do ASP.NET.
   
       - (IBAction) LogInAction: remetente (id) {/ / criar o cabeçalho de autenticação e defina-o cliente NSString * nome de utilizador de registo = autónoma. UsernameField.text;   Palavra-passe NSString * = autónoma. PasswordField.text;
   
           [auto createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           __weak ViewController * selfie = Self;   [self.registerClient registerWithDeviceToken:self.deviceToken etiquetas: nil andCompletion:^(NSError* error) {Se (! erro) {dispatch_async(dispatch_get_main_queue(), ^ {selfie. SendNotificationButton.enabled = YES;               [self MessageBox:@"Success" message:@"Registered com êxito!"];});}}];}

        -SendNotificationASPNETBackend (nulo): (NSString*) pns UsernameTag: (NSString*) usernameTag mensagem: (NSString*) mensagem {NSURLSession* sessão = [NSURLSession sessionWithConfiguration: [NSURLSessionConfiguration defaultSessionConfiguration] delegado: nil delegateQueue:nil];

            A tag pns e o nome de utilizador de passar como parâmetros com o URL de REST para o back-end ASP.NET nsurl que * requestURL = [nsurl que URLWithString: [NSString stringWithFormat:@"%@/api/notifications? pns = % @& to_tag = % @", BACKEND_ENDPOINT, pns, usernameTag]];

            NSMutableURLRequest * pedido = [NSMutableURLRequest requestWithURL:requestURL];    [pedido setHTTPMethod:@"POST"];

            Obter o authenticationheader mock do cliente register NSString * authorizationHeaderValue = [NSString stringWithFormat:@"Basic % @", self.registerClient.authenticationHeader];    [pedido setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            Adicionar o corpo da mensagem de notificação [pedido setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];    [setHTTPBody do pedido: [mensagem dataUsingEncoding:NSUTF8StringEncoding]];

            Executar a REST API de notificação de envio no ASP.NET back-end NSURLSessionDataTask * dataTask = [sessão dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) resposta;        Se (erro | | httpResponse.statusCode! = 200) {NSString* Estado = [NSString stringWithFormat:@"Error estado para % @: % d\nError: %@\n", pns, httpResponse.statusCode, erro];            dispatch_async(dispatch_get_main_queue(), ^ {/ / acrescentar texto porque todos os 3 chamadas PNS podem também ter informações para a vista [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];            });            NSLog(status);        }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];    [dataTask retomar]; }


1. Atualizar a ação para o **Enviar notificação** botão para utilizar o back-end do ASP.NET e enviar para qualquer PNS ativada por um comutador.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            //[self SendNotificationRESTAPI];
            [self SendToEnabledPlatforms];
        }


        -(void)SendToEnabledPlatforms
        {
            NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

            [self.sendResults setText:@""];

            if ([self.WNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

            if ([self.GCMSwitch isOn])
                [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

            if ([self.APNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
        }



1. Na função **ViewDidLoad**, adicione o seguinte ao instanciar o RegisterClient instância e definir o delegado para os campos de texto.
   
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
2. Agora no **Appdelegate**, remova todos os conteúdos do método **aplicação: didRegisterForPushNotificationWithDeviceToken:** e substitua-o com o seguinte para se certificar de que o controlador de vista contém o token do dispositivo mais recente obtido a partir do APNs:
   
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
3. Por fim no **Appdelegate**, certifique-se de que o método seguinte:
   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }

## <a name="test-the-application"></a>Testar a aplicação
1. No XCode, execute a aplicação num dispositivo iOS físico (emitir notificações não irão funcionar no simulador do).
2. Na aplicação iOS IU, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia, mas têm ambos de ser o mesmo valor de cadeia. Em seguida, clique em **início de sessão**.
   
    ![][2]
3. Deverá ver um pop-up que o informa da êxito de registo. Clique em **OK**.
   
    ![][3]
4. Na **etiqueta de nome de utilizador de destinatário* texto campo, introduza a etiqueta de nome de utilizador utilizada com o registo a partir de outro dispositivo.
5. Introduza uma mensagem de notificação e clique em **Enviar notificação**.  Apenas os dispositivos que tem um registo com a etiqueta de nome de utilizador destinatários recebem a mensagem de notificação.  É enviada apenas aos utilizadores.
   
    ![][4]

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
