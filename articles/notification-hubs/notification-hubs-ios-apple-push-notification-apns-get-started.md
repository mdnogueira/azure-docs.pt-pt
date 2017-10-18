---
title: "Enviar notificações push para o iOS com Hubs de Notificação do Azure | Microsoft Docs"
description: "Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação iOS."
services: notification-hubs
documentationcenter: ios
keywords: "notificação push, notificações push, notificações push do ios"
author: ysxu
manager: erikre
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: ab0777f859e80afcd61e371056b44d018c7b7ab9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Enviar notificações push para o iOS com Notification Hubs do Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).
> 
> 

Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação iOS. Deverá criar uma aplicação iOS em branco que possa receber notificações push utilizando o [Serviço Apple Push Notification (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Quando tiver terminado, poderá utilizar o Notification Hub para difundir notificações push para todos os dispositivos a executar a sua aplicação.

## <a name="before-you-begin"></a>Antes de começar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código de conclusão deste tutorial pode ser encontrado [no GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial necessita do seguinte:

* [SDK do iOS dos Mobile Services versão 1.2.4]
* Versão mais recente do [Xcode].
* Um dispositivo compatível com iOS 8 (ou versão posterior)
* Associação ao [Programa de Programador da Apple](https://developer.apple.com/programs/)
  
  > [!NOTE]
  > Devido aos requisitos de configuração das notificações push, deve implementar e testar as notificações push num dispositivo iOS físico (iPhone ou iPad) em vez do simulador iOS.
  > 
  > 

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Notification Hubs para aplicações iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurar o Notification Hub para notificações push do iOS
Esta secção explica-lhe como criar um novo Notification Hub e configurar a autenticação com APNs através do certificado push **.p12** que criou. Se pretender utilizar um Notification Hub que já tenha criado, pode avançar para o passo 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Clique no botão <b>Serviços de Notificação</b> no painel <b>Definições</b> e selecione <b>Apple (APNS)</b>. Clique em <b>Carregar Certificado</b> e selecione o ficheiro <b>.p12</b> que exportou anteriormente. Não se esqueça de especificar também a palavra-passe correta.</p>

<p>Certifique-se de que selecionou o modo <b>Sandbox</b>, uma vez que se destina ao desenvolvimento. Utilize o modo <b>Produção</b> apenas se pretender enviar notificações push a utilizadores que já tenham adquirido a aplicação da loja.</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![Configurar o APNS no Portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;&emsp;&emsp;![Configurar certificação APNS no Portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

O Notification Hub já está configurado para trabalhar com APNs e tem as cadeias de ligação para registar a sua aplicação e enviar notificações push.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Ligar a aplicação iOS aos Notification Hubs
1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicação de Vista Única**.
   
    ![Xcode – Aplicação de Vista Única][8]
    
2. Ao definir as opções para o seu novo projeto, não se esqueça de utilizar o mesmo **Nome do Produto** e **Identificador de Organização** que utilizou quando definiu anteriormente o ID de pacote no portal de programador da Apple.
   
    ![Xcode – opções do projeto][11]
    
3. Em **Destinos**, clique no nome do projeto, clique no separador **Definições de Criação**, expanda **Identidade da Assinatura de Código** e, em **Depuração**, defina a identidade de assinatura de código. Alterne os **Níveis** de **Básicos** para **Todos** e defina **Perfil de Aprovisionamento** para o perfil de aprovisionamento que criou anteriormente.
   
    Se não vir o novo perfil de aprovisionamento que criou no Xcode, tente atualizar os perfis para a sua identidade de assinatura. Clique em **Xcode** na barra de menus, clique em **Preferências**, clique em **Conta** clique no botão **Ver Detalhes**, clique na sua identidade de assinatura e clique no botão atualizar no canto inferior direito.
   
    ![Xcode – perfil de aprovisionamento][9]
4. Transfira o [SDK do iOS dos Mobile Services versão 1.2.4] e deszipe o ficheiro. No Xcode, clique com o botão direito do rato no projeto e clique na opção **Adicionar Ficheiros a** para adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto Xcode. Selecione **Copiar itens, se necessário** e clique em **Adicionar**.
   
   > [!NOTE]
   > O SDK dos Notification Hubs não suporta atualmente bitcode no Xcode 7.  Tem de definir **Ativar Bitcode** para **Não** em **Criar Opções** para o seu projeto.
   > 
   > 
   
    ![Deszipar o Azure SDK][10]
5. Adicione um novo ficheiro de cabeçalho ao projeto com o nome `HubInfo.h`. Este ficheiro vai deter as constantes para o Notification Hub.  Adicione as seguintes definições e substitua os marcadores de posição literais de cadeia pelo seu *nome do hub* e pela *DefaultListenSharedAccessSignature* que anotou anteriormente.
   
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
6. Abra o ficheiro `AppDelegate.h` e adicione as diretivas de importação seguintes:
   
         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
7. No `AppDelegate.m file`, adicione o seguinte código ao método `didFinishLaunchingWithOptions`, com base na sua versão do iOS. Este código regista o seu identificador de dispositivo com APNs:
   
    Para iOS 8:
   
         UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
   
    Para versões iOS anteriores à 8:
   
         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
8. No mesmo ficheiro, adicione os seguintes métodos: Este código liga-se ao Notification Hub utilizando as informações de ligação que especificou em HubInfo.h. Em seguida, atribui o token do dispositivo Notification Hub para que este possa enviar notificações:
   
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }
   
        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
9. No mesmo ficheiro, adicione o método seguinte para apresentar um **UIAlert** caso a notificação seja recebida enquanto a aplicação está ativa:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

1. Crie e execute a aplicação no dispositivo para confirmar que não existem falhas.

## <a name="send-test-push-notifications"></a>Enviar notificações push de teste
Pode testar a receção de notificações push na aplicação, enviando-as no [Portal do Azure] através da secção **Resolução de Problemas** no painel do hub (utilize a opção *Envio de Teste*).

![Portal do Azure – Envio de Teste][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-from-the-app"></a>(Opcional) Enviar notificações push a partir da aplicação
> [!IMPORTANT]
> Este exemplo de envio de notificações da aplicação de cliente é fornecido apenas para efeitos de aprendizagem. Uma vez que tal exige que `DefaultFullSharedAccessSignature` esteja presente na aplicação de cliente, o seu hub de notificação fica exposto ao risco de um utilizador obter acesso para enviar notificações não autorizadas aos seus clientes.
> 
> 

Se pretende enviar notificações de push a partir de uma aplicação, esta secção fornece um exemplo de como fazê-lo utilizando a interface REST.

1. No Xcode, abra `Main.storyboard` e adicione os seguintes componentes da IU a partir da biblioteca de objetos, para permitir ao utilizador enviar notificações push na aplicação:
   
   * Uma etiqueta sem texto da etiqueta. Será utilizada para comunicar erros no envio de notificações. A propriedade **Linhas** deve ser definida como **0** para que tenha automaticamente o tamanho restringido às margens direita e esquerda e à parte superior da vista.
   * Um campo de texto com o texto **Marcador de Posição** definido como **Introduzir Mensagem de Notificação**. Restrinja o campo imediatamente por baixo da etiqueta, como mostrado abaixo. Defina o Controlador de Vista como delegado da saída.
   * Um botão intitulado **Enviar Notificação** restringido imediatamente por baixo do campo de texto e no centro horizontal.
     
     A vista deve ter o seguinte aspeto:
     
     ![Estruturador do Xcode][32]
2. [Adicione saídas](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) para a etiqueta e o campo de texto ligados à sua vista e atualize a definição `interface` para suportar `UITextFieldDelegate` e `NSXMLParserDelegate`. Adicione as três declarações de propriedades mostradas abaixo para ajudar a suportar a chamada da API REST e a análise da resposta.
   
    O ficheiro ViewController.h deve ter o seguinte aspeto:
   
        #import <UIKit/UIKit.h>
   
        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }
   
        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;
   
        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;
   
        @end
3. Abra `HubInfo.h` e adicione as seguintes constantes, que serão utilizadas para enviar notificações para o seu hub. Substitua o literal de cadeia do marcador de posição pela cadeia de ligação real *DefaultFullSharedAccessSignature*.
   
        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
4. Adicione as seguintes instruções `#import` ao ficheiro `ViewController.h`:
   
        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"
5. Em `ViewController.m`, adicione o seguinte código à implementação da interface. Este código analisará a cadeia de ligação *DefaultFullSharedAccessSignature*. Como mencionado em [Referência da API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), estas informações analisadas serão utilizadas para gerar um token SaS para o cabeçalho do pedido de **Autorização**.
   
        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;
   
        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;
   
            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];
   
                @throw parseException;
            }
   
            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }
6. Em `ViewController.m`, atualize o método `viewDidLoad` para analisar a cadeia de ligação quando carrega a vista. Adicione também os métodos de utilitário, apresentados abaixo, para a implementação da interface.  

        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





1. Em `ViewController.m`, adicione o seguinte código para a implementação de interface para gerar o token de autorização de SaS que irá ser fornecido no cabeçalho **Autorização**, tal como mencionado em [Referência da API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).
   
        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;
   
            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];
   
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];
   
                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }
   
            return token;
        }
2. Prima CTRL e arraste do botão **Enviar notificação** para `ViewController.m` para adicionar uma ação com o nome **SendNotificationMessage** para o evento **Toques para Baixo**. Atualize o método com o seguinte código para enviar a notificação utilizando a API REST.
   
        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }
   
        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];
   
            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];
   
            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];
   
            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
   
            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
   
            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];
   
            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
   
            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
   
            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                       [xmlParser parse];
                }
            }];
            [dataTask resume];
        }
3. Em `ViewController.m`, adicione o seguinte método de delegado para suportar o fecho do teclado para o campo de texto. Prima CTRL e arraste o campo de texto para o ícone do Controlador de Vista no estruturador de interface para definir o controlador de vista como delegado de saída.
   
        //===[ Implement UITextFieldDelegate methods ]===
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
4. Em `ViewController.m`, adicione os seguintes métodos de delegado para suportar a análise da resposta utilizando `NSXMLParser`.
   
       //===[ Implement NSXMLParserDelegate methods ]===
   
       -(void)parserDidStartDocument:(NSXMLParser *)parser
       {
           self.statusResult = @"";
       }
   
       -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
           namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
           attributes:(NSDictionary *)attributeDict
       {
           NSString * element = [elementName lowercaseString];
           NSLog(@"*** New element parsed : %@ ***",element);
   
           if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
           {
               self.currentElement = element;
           }
       }
   
       -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
       {
           self.statusResult = [self.statusResult stringByAppendingString:
               [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
       }
   
       -(void)parserDidEndDocument:(NSXMLParser *)parser
       {
           // Set the status label text on the UI thread
           dispatch_async(dispatch_get_main_queue(),
           ^{
               [self.sendResults setText:self.statusResult];
           });
       }
5. Crie o projeto e certifique-se de que não existem erros.

> [!NOTE]
> Se ocorrer um erro de compilação no Xcode7 relativo ao suporte de bitcode, deve alterar **Definições de Criação** > **Ativar Bitcode (ENABLE_BITCODE)** para **Não** no Xcode. O SDK dos Notification Hubs não suporta atualmente bitcode. 
> 
> 

Pode encontrar todos os payloads possíveis no [Guia de Programação de Notificações Push e Local] da Apple.

## <a name="checking-if-your-app-can-receive-push-notifications"></a>Verificar se a aplicação pode receber notificações push
Para testar as notificações push no iOS, tem de implementar a aplicação para um dispositivo iOS físico. Não pode enviar notificações push da Apple utilizando o Simulator iOS.

1. Execute a aplicação, verifique se o registo é concluído com êxito e prima **OK**.
   
    ![Teste do Registo de Notificações Push da Aplicação iOS][33]
2. Pode enviar uma notificação push de teste a partir do [Portal do Azure], como descrito acima. Se tiver adicionado o código para o envio de notificações push na aplicação, toque no dentro do campo de texto para introduzir uma mensagem de notificação. Em seguida, prima o botão **Enviar** no teclado ou o botão **Enviar Notificação** na vista para enviar a mensagem de notificação.
   
    ![Teste de Envio de Notificações Push da Aplicação iOS][34]
3. A notificação push é enviada a todos os dispositivos que estejam registados para receber as notificações do Notification Hub específico.
   
    ![Teste de Receção de Notificações Push da Aplicação iOS][35]

## <a name="next-steps"></a>Passos seguintes
Neste exemplo simples, difundiu notificações push para todos os dispositivos iOS. Sugerimos como passo seguinte na sua aprendizagem que passe para o tutorial [Os Notification Hubs do Azure Notificam Utilizadores do iOS com back-end do .NET], que irá guiá-lo na criação de um back-end para envio notificações push utilizando etiquetas. 

Se pretende segmentar os utilizadores por grupos de interesse, pode, adicionalmente, ler o tutorial [Utilizar Notification Hubs para enviar notícias de última hora]. 

Para obter informações gerais sobre os Notification Hubs, consulte a nossa [Documentação de Orientação dos Notification Hubs].

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[SDK do iOS dos Mobile Services versão 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Documentação de Orientação dos Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Os Notification Hubs do Azure Notificam Utilizadores do iOS com back-end do .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Utilizar Notification Hubs para enviar notícias de última hora]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Guia de Programação de Notificações Push e Local]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portal do Azure]: https://portal.azure.com
