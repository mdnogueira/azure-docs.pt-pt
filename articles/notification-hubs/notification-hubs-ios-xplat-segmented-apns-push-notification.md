---
title: "Os Notification Hubs Tutorial de notícias de última - iOS"
description: "Saiba como utilizar Hubs de notificação de barramento de serviço do Azure para enviar notificações de notícias de última para dispositivos iOS."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: dc47250db6fb3a2853dae24e02bda236154d93fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizar Notification Hubs para enviar notícias de última hora
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como utilizar Notification Hubs do Azure para difundir notificações de notícias de última para uma aplicação iOS. Quando terminar, irá conseguir registar-se de que está interessado de categorias de notícias de última hora e receber notificações de push apenas para as categorias. Este cenário é um padrão comum para muitas aplicações onde notificações devem ser enviadas para grupos de utilizadores que tenham sido anteriormente declarado interesse nos mesmos, por exemplo, leitor de RSS, as aplicações para ventoinhas de música, etc.

Cenários de difusão estão ativados, incluindo um ou mais *etiquetas* quando criar um registo no hub de notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que registou da etiqueta irão receber a notificação. Uma vez que as etiquetas são simplesmente cadeias, tem de ser aprovisionados com antecedência. Para obter mais informações sobre etiquetas, consulte [encaminhamento de Hubs de notificação e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Pré-requisitos
Este tópico baseia-se a aplicação que criou no [introdução aos Hubs de notificação][get-started]. Antes de começar este tutorial, tem de ter já concluído [introdução aos Hubs de notificação][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção da categoria para a aplicação
O primeiro passo consiste em adicionar os elementos de IU para o seu guião gráfico principal existente que permitem ao utilizador selecionar categorias para registar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação for iniciada, um registo do dispositivo é criado no notification hub com as categorias selecionadas como etiquetas.

1. No seu MainStoryboard_iPhone.storyboard adicione os seguintes componentes da biblioteca do objeto:
   
   * Uma etiqueta com o texto "Notícias de última hora",
   * Etiquetas com textos de categoria "Mundo", "Política", "Empresariais", "Tecnologia", "Ciência", "Desporto"
   * Seis comutadores, um por cada categoria, defina cada comutador **estado** ser **desativar** por predefinição.
   * Um botão com a etiqueta "Subscrever"
     
     Seu guião gráfico deve ter o seguinte aspeto:
     
     ![][3]
2. No editor do assistente, criar saídas para todos os comutadores e chamar "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Crie uma ação para o botão chamado "subscrever". O viewcontroller. H deve conter o seguinte:
   
        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;
   
        - (IBAction)subscribe:(id)sender;
4. Crie um novo **Cocoa Touch classe** chamado `Notifications`. Copie o seguinte código na secção de interface do ficheiro Notifications.h:
   
        @property NSData* deviceToken;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;
   
        - (NSSet*)retrieveCategories;
   
        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
5. Adicione a diretiva de importação seguintes Notifications.m:
   
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
6. Copie o seguinte código na secção de implementação do ficheiro Notifications.m.
   
        SBNotificationHub* hub;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{
   
            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];
   
            return self;
        }
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
   
            [self subscribeWithCategories:categories completion:completion];
        }

        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Esta classe utiliza armazenamento local para armazenar e obter as categorias de notícias que irá receber este dispositivo. Além disso, contém um método para registar estas categorias utilizando um [modelo](notification-hubs-templates-cross-platform-push-messages.md) registo.

1. No ficheiro AppDelegate.h, adicione uma instrução de importação para Notifications.h e adicionar uma propriedade de uma instância da classe de notificações:
   
        #import "Notifications.h"
   
        @property (nonatomic) Notifications* notifications;
2. No **didFinishLaunchingWithOptions** método no Appdelegate, adicione o código para inicializar a instância de notificações no início do método.  
   
    `HUBNAME`e `HUBLISTENACCESS` (definido em hubinfo) já deverá ter o `<hub name>` e `<connection string with listen access>` marcadores de posição substituído com o nome do notification hub e a cadeia de ligação para *DefaultListenSharedAccessSignature* que obteve anteriormente
   
        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
   
   > [!NOTE]
   > Porque as credenciais que são distribuídas com uma aplicação de cliente não são geralmente seguras, só deve de distribuir a chave de acesso escuta com a sua aplicação de cliente. Escutar permite o acesso não é possível modificar a sua aplicação para se registar para notificações, mas os registos existentes e não não possível enviar notificações. A chave de acesso total é utilizada num serviço protegidos back-end para envio de notificações e alterar os registos existentes.
   > 
   > 
3. No **didRegisterForRemoteNotificationsWithDeviceToken** método no Appdelegate, substitua o código no método com o seguinte código para passar o token do dispositivo para a classe de notificações. A classe de notificações executará a registar para obter notificações com as categorias. Se o utilizador altera as seleções de categorias, chamamos a `subscribeWithCategories` método em resposta ao **subscrever** botão para atualizá-las.
   
   > [!NOTE]
   > Porque o token do dispositivo atribuído pelo Apple Push Notification Service (APNS) pode possibilidade em qualquer altura, deve registar para notificações frequentemente evitar falhas de notificação. Neste exemplo regista a notificação sempre que a aplicação for iniciada. Para aplicações que são executadas com frequência, mais do que uma vez por dia, pode provavelmente ignorar registo para preservar a largura de banda, se menos de um dia foi efectuada com êxito desde o registo anterior.
   > 
   > 
   
        self.notifications.deviceToken = deviceToken;
   
        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.
   
        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    Tenha em atenção que neste momento não deverá haver nenhum outro código no **didRegisterForRemoteNotificationsWithDeviceToken** método.

1. Os seguintes métodos já devem estar presentes no Appdelegate a conclusão do [introdução aos Hubs de notificação] [ get-started] tutorial.  Caso contrário, adicioná-los.
   
    -(nulo) MessageBox:(NSString *) título mensagem:(NSString *) messageText {
   
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }
   
   * aplicação (nulo):(UIApplication *) aplicação didReceiveRemoteNotification: (NSDictionary *) userInfo {NSLog (@"% @", userInfo);   [Self-MessageBox:@"Notification" mensagem: [[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];}
   
   Este método processa notificações recebidas quando a aplicação está em execução, apresentando um simples **UIAlert**.
2. No ViewController.m, adicione uma instrução de importação para AppDelegate.h e copie o seguinte código para o XCode gerado pelo **subscrever** método. Este código irá atualizar o registo da notificação para utilizar as novas etiquetas de categoria que o utilizador tenha escolhido na interface de utilizador.
   
       ```
       #import "Notifications.h"
       ```
   
       NSMutableArray* categories = [[NSMutableArray alloc] init];
   
       if (self.WorldSwitch.isOn) [categories addObject:@"World"];
       if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
       if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
       if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
       if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
       if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];
   
       Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
   
       [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
           if (!error) {
               [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
           } else {
               NSLog(@"Error subscribing: %@", error);
           }
       }];
   
   Este método cria uma **NSMutableArray** de categorias e utiliza o **notificações** classe para armazenar a lista no armazenamento local e regista correspondente etiquetas com o notification hub. Quando são alteradas categorias, o registo é recriado com as categorias de novo.
3. No ViewController.m, adicione o seguinte código no **viewDidLoad** método para definir a interface de utilizador com base em categorias de guardado anteriormente.

        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



A aplicação agora pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registar o notification hub sempre que a aplicação for iniciada.  O utilizador pode alterar a seleção das categorias em tempo de execução e clique em de **subscrever** método para atualizar o registo para o dispositivo. Em seguida, irá atualizar a aplicação para enviar notificações de notícias de última diretamente na aplicação em si.

## <a name="optional-sending-tagged-notifications"></a>(opcional) Enviar notificações marcadas
Se não tiver acesso para Visual Studio, pode avançar para a secção seguinte e enviar notificações a partir da aplicação em si. Também pode enviar a notificação de modelo adequada do [Portal clássico do Azure] utilizando o separador de depuração do notification hub. 

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(opcional) Enviar notificações a partir do dispositivo
Normalmente, seriam enviadas notificações por um serviço de back-end, mas, pode enviar notificações de notícias de última diretamente a partir da aplicação. Para fazer isto, iremos atualizar o `SendNotificationRESTAPI` método que definimos no [introdução aos Hubs de notificação] [ get-started] tutorial.

1. Numa atualização ViewController.m o `SendNotificationRESTAPI` método como se segue para que aceita um parâmetro para a tag de categoria e envia o adequado [modelo](notification-hubs-templates-cross-platform-push-messages.md) notificação.
   
        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];
   
            NSString *json;
   
            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];
   
            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
   
            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
   
            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];
   
            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];
   
            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];
   
            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
   
            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
   
            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
   
            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];
   
            [dataTask resume];
        }
2. Numa atualização ViewController.m o **Enviar notificação** ação conforme mostrado no código que se segue. Para que irá enviar notificações através de cada etiqueta individualmente e enviar para várias plataformas.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



1. Reconstruir o projeto e certifique-se de que não existem erros de compilação.

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações
1. Prima o botão de execução para compilar o projeto e iniciar a aplicação. Selecione algumas opções de notícias de última para subscrever e, em seguida, prima a **subscrever** botão. Deverá ver uma caixa de diálogo que indica que as notificações foram subscritos.
   
    ![][1]
   
    Quando escolhe **subscrever**, a aplicação converte as categorias selecionadas etiquetas e os pedidos de um novo registo de dispositivo para as etiquetas selecionados do hub de notificação.
2. Introduza uma mensagem para serem enviadas como notícias de última hora, em seguida, prima a **Enviar notificação** botão. Em alternativa, execute a aplicação de consola .NET para gerar notificações.
   
    ![][2]
3. Cada dispositivo subscrito notícias de última hora irá receber as notificações de notícias de última que acabámos de enviar.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a difusão notícias de última hora por categoria. Considere a concluir um dos seguintes tutoriais que realcem os outros cenários avançados de Hubs de notificação:

* **[Utilizar Notification Hubs para difusão notícias de última localizada hora]**
  
    Saiba como expandir a aplicação de notícias de última para ativar o envio de notificações localizadas.

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilizar Notification Hubs para difusão notícias de última localizada hora]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Portal clássico do Azure]: https://manage.windowsazure.com
