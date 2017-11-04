---
title: "IOS do Mobile Engagement SDK alcançar integração do Azure | Microsoft Docs"
description: "Mais recentes atualizações e procedimentos para o SDK do iOS do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ba74e0c442ac10f096d465f989e03d2ceae8cd88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>Como integrar o alcance do Engagement no iOS
Tem de seguir o procedimento de integração descrito no [como integrar o Engagement num documento do iOS](mobile-engagement-ios-integrate-engagement.md) antes de seguir este guia.

Esta documentação requer 8 do XCode. Se é realmente depende do XCode 7, em seguida, pode utilizar o [iOS o Engagement SDK v3.2.4](https://aka.ms/r6oouh). Não há um erro conhecido nesta versão anterior ao executar em dispositivos iOS 10: as notificações do sistema não estão alvo de ação. Para corrigir esta situação, terá de implementar a API preterida `application:didReceiveRemoteNotification:` na sua aplicação delegar da seguinte forma:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Não recomendamos esta solução** como este comportamento pode alterar qualquer atualização de versão futura iOS (mesmo secundárias) porque esta API do iOS foi preterido. Deverá mudar para o XCode 8 logo que possível.
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Permitir que a aplicação receba Notificações Push Automáticas
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>Passos de integração
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Incorporar o Engagement alcançar SDK no projeto iOS
* Adicione o sdk do alcance do seu projeto Xcode. No Xcode, aceda a **projeto \> adicionar ao projeto** e escolha o `EngagementReach` pasta.

### <a name="modify-your-application-delegate"></a>Modificar o seu Delegado de Aplicação
* Na parte superior do seu ficheiro de implementação, importe o módulo de alcance do Engagement:

      [...]
      #import "AEReachModule.h"
* Dentro do método `applicationDidFinishLaunching:` ou `application:didFinishLaunchingWithOptions:`, crie um módulo de alcance e transmita-o à sua linha de inicialização do Engagement existente:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* Modificar **'icon.png'** cadeia com o nome da imagem que quiser como o ícone de notificação.
* Se pretender utilizar a opção *valor de distintivo atualização* campanhas de alcance ou se pretender utilizar o push nativo \<formato/nativo de SaaS/alcance API/campanha Push\> campanhas, tem de permitir o alcance módulo gerir o ícone de distintivo propriamente dito (irá desmarcar automaticamente o destaque de aplicação e também repor o valor armazenado pelo Engagement sempre que a aplicação é iniciado ou foregrounded). Isto é feito adicionando a seguinte linha após a inicialização do módulo de alcance:

      [reach setAutoBadgeEnabled:YES];
* Se pretender processar alcance push de dados, tem de permitir que o delegado de aplicação está em conformidade com a `AEReachDataPushDelegate` protocolo. Adicione a seguinte linha após a inicialização do módulo de alcance:

      [reach setDataPushDelegate:self];
* Em seguida, pode implementar os métodos `onDataPushStringReceived:` e `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` no seu delegado de aplicação:

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>Categoria
O parâmetro de categoria é opcional ao criar uma campanha Push de dados e permite-lhe dados de filtro pushes. Isto é útil se pretender emitir diferentes tipos de `Base64` dados e pretender identificar o respetivo tipo antes de a análise-los.

**A aplicação está agora pronta para receber e apresentar conteúdo de alcance!**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>Como receber anúncios e inquéritos em qualquer altura
Engagement pode enviar notificações de alcance aos seus utilizadores finais em qualquer altura utilizando o serviço Apple Push Notification.

Para ativar esta funcionalidade, terá de preparar a sua aplicação para notificações push da Apple e modificar o seu delegado de aplicação.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Preparar a sua aplicação para notificações push da Apple
Siga o guia: [como preparar a sua aplicação para notificações de Push da Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Adicione o código de cliente necessário
*Neste momento a aplicação deve ter um certificado push da Apple registado o Engagement front-end.*

Se este não é feito, tem de registar a aplicação para receber notificações push.

* Importar o `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>
* Adicione a seguinte linha quando a aplicação for iniciada (normalmente no `application:didFinishLaunchingWithOptions:`):

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

Em seguida, terá de fornecer o Engagement o token do dispositivo devolvido pelos servidores da Apple. Isto é feito no método chamado `application:didRegisterForRemoteNotificationsWithDeviceToken:` no seu delegado de aplicação:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Por fim, terá de informar o Engagement SDK quando a aplicação recebe uma notificação remota. Para fazê-lo, chame o método `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` no seu delegado de aplicação:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> Por predefinição, o alcance do Engagement controla o completionHandler. Se pretender responder manualmente à `handler` bloquear no seu código, pode passar nil o `handler` argumento e o controlo a conclusão bloqueiam por si. Consulte o `UIBackgroundFetchResult` tipo para obter uma lista de valores possíveis.
>
>

### <a name="full-example"></a>Exemplo completo
Eis um exemplo completo de integração:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Resolver conflitos de delegado UNUserNotificationCenter

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

## <a name="how-to-customize-campaigns"></a>Como personalizar campanhas
### <a name="notifications"></a>Notificações
Existem dois tipos de notificações: notificações de sistema e na aplicação.

As notificações do sistema são processadas pelo iOS e não podem ser personalizadas.

São efetuadas nas notificações na aplicação de uma vista que dinamicamente é adicionada à janela de aplicação atual. Isto denomina-se numa sobreposição de notificação. Sobreposição de notificação é ótimos para uma integração rápida porque não necessitam de modificar qualquer vista na sua aplicação.

#### <a name="layout"></a>Esquema
Para modificar o aspeto da sua nas notificações na aplicação, basta pode modificar o ficheiro `AENotificationView.xib` às suas necessidades, desde que mantenha os valores de etiqueta e tipos dos subviews existentes.

Por predefinição, as notificações na aplicação são apresentadas na parte inferior do ecrã. Se preferir para as mostrar na parte superior do ecrã, editar fornecido `AENotificationView.xib` e altere o `AutoSizing` propriedade da vista principal para que podem ser mantida na parte superior do respetivo superview.

#### <a name="categories"></a>Categorias
Quando modificar o esquema fornecido, modifique o aspeto de todas as suas notificações. Categorias permitem-lhe definir o aspeto que tem de destino várias (possivelmente comportamentos) para notificações. É possível especificar uma categoria quando cria uma campanha de alcance. Tenha em atenção que categorias também permitem-lhe personalizar anúncios e inquéritos, que é descrita mais à frente neste documento.

Para registar um processador de categoria para as notificações, tem de adicionar uma chamada quando o módulo de alcance está inicializado.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`tem de ser uma instância de um objeto que está em conformidade com o protocolo `AENotifier`.

Pode implementar os métodos de protocolo por si ou pode optar por reimplement classe existente `AEDefaultNotifier` que já faz a maior parte do trabalho.

Por exemplo, se pretender que a redefinir a vista de notificação para uma categoria específica, pode seguir este exemplo:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Neste exemplo simples da categoria partem do princípio de que tem um ficheiro denominado `MyNotificationView.xib` no seu pacote de aplicação principal. Se o método não é possível encontrar um correspondente `.xib`, a notificação não será apresentada e o Engagement gerar uma mensagem na consola do.

O ficheiro nib fornecido deve respeitem as seguintes regras:

* Só deve conter uma vista.
* Subviews deve ser dos mesmos tipos de que as dentro do ficheiro nib fornecido com o nome`AENotificationView.xib`
* Subviews deve ter as mesmas etiquetas como aqueles dentro fornecido com o nome de ficheiro de nib`AENotificationView.xib`

> [!TIP]
> Apenas copiar o ficheiro nib fornecido, denominado `AENotificationView.xib`e começar a trabalhar a partir daí. Seja cuidadoso, a vista dentro deste ficheiro nib é associada à classe `AENotificationView`. Esta classe redefinir o método `layoutSubViews` mover e redimensionar os subviews, de acordo com contexto. Pretende substituí-lo com um `UIView` ou classe vista personalizada.
>
>

Se precisar de mais profunda personalização das suas notificações (se pretender que para a instância para carregar a vista diretamente a partir do código), recomendamos que veja a documentação de código e classe de origem fornecido do `Protocol ReferencesDefaultNotifier` e `AENotifier`.

Tenha em atenção que pode utilizar o mesmo notifier para várias categorias.

Também pode redefinir o notifier predefinida como esta:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Processamento de notificação
Quando a categoria de predefinido a utilizar, alguns métodos de ciclo de vida são denominados no `AEReachContent` de objeto para estatísticas de relatório e atualizar o estado da campanha:

* Quando a notificação é apresentada na aplicação, o `displayNotification` método é denominado (que comunica estatísticas) por `AEReachModule` se `handleNotification:` devolve `YES`.
* Se a notificação é dispensada, o `exitNotification` se chama o método, é reportada estatística e campanhas seguintes agora podem ser processadas.
* Se a notificação é clicada, `actionNotification` é chamado, é reportada estatística e é efetuada a ação associada.

Se a implementação de `AENotifier` ignora o comportamento predefinido, terá de chamar estes métodos de ciclo de vida por si. Os exemplos seguintes mostram alguns casos em que o comportamento predefinido é ignorado:

* Não expande `AEDefaultNotifier`, por exemplo, implementado o processamento de categoria do zero.
* Overrode `prepareNotificationView:forContent:`, lembre-se de que, pelo menos, mapear `onNotificationActioned` ou `onNotificationExited` para um dos seus U.I controla.

> [!WARNING]
> Se `handleNotification:` gera uma exceção, o conteúdo é eliminada e `drop` é chamado, isto é reportado no estatísticas e campanhas seguintes agora podem ser processadas.
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>Incluir notificação como parte de uma vista existente
As sobreposições são ótimos para uma integração rápida, mas podem ser por vezes, não convenientes ou podem ter indesejáveis efeitos secundários.

Se não estiver satisfeito com o sistema de sobreposição em algumas das vistas, pode personalizá-lo para estas vistas.

Pode optar por incluir nosso esquema de notificação no seu vistas existentes. Para tal, há dois estilos de implementação:

1. Adicionar a vista de notificações utilizando o construtor de interface

   * Abra *Builder da Interface*
   * Colocar um 60 x 320 (ou 768 x 60 se estiver no iPad) `UIView` onde pretende que a notificação a aparecer
   * Definir o valor da etiqueta para esta vista para: **36822491**
2. Adicione a vista de notificações através de programação. Adicione apenas o seguinte código quando a vista foi inicializada:

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`a macro pode ser encontrada na `AEDefaultNotifier.h`.

> [!NOTE]
> O notifier predefinido Deteta automaticamente que o esquema de notificação está incluído nesta vista e não irá adicionar uma sobreposição para a mesma.
>
>

### <a name="announcements-and-polls"></a>Anúncios e inquéritos
#### <a name="layouts"></a>Esquemas
Pode modificar os ficheiros `AEDefaultAnnouncementView.xib` e `AEDefaultPollView.xib` , desde que mantenha os valores de etiqueta e tipos dos subviews existentes.

#### <a name="categories"></a>Categorias
##### <a name="alternate-layouts"></a>Esquemas alternativas
Como as notificações, categoria a campanha pode ser utilizada com esquemas alternativas para os anúncios e inquéritos.

Para criar uma categoria para um anúncio, tem de expandir **AEAnnouncementViewController** e registá-lo assim que o módulo de alcance foi inicializado:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> Sempre que um utilizador será clique numa notificação para um anúncio com a categoria "os meus\_categoria", o controlador de vista registado (nesse caso `MyCustomAnnouncementViewController`) irá ser inicializado ao chamar o método `initWithAnnouncement:` e a vista será adicionada à janela de aplicação atual.
>
>

Na sua implementação do `AEAnnouncementViewController` classe tem de ler a propriedade `announcement` ao inicializar os subviews. Considere o exemplo abaixo, onde duas etiquetas são inicializados utilizando `title` e `body` propriedades do `AEReachAnnouncement` classe:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Se não pretender carregar as vistas por si, mas pretender reutilizar o esquema de vista de anúncio predefinida, basta pode tornar o controlador de vista personalizada expande a classe fornecida `AEDefaultAnnouncementViewController`. Nesse caso, duplicar o ficheiro nib `AEDefaultAnnouncementView.xib` e alterá-lo para que podem ser carregada pelo seu controlador de vista personalizada (para um controlador denominado `CustomAnnouncementViewController`, deve contactar o seu ficheiro nib `CustomAnnouncementView.xib`).

Para substituir a categoria de predefinição de anúncios, basta registar o seu controlador de vista personalizada para a categoria de definido no `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Inquérito pode ser personalizado da mesma forma:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Neste momento, fornecido `MyCustomPollViewController` tem de expandir `AEPollViewController`. Ou pode optar por alargar a partir do controlador de predefinição: `AEDefaultPollViewController`.

> [!IMPORTANT]
> Não se esqueça de chamar o `action` (`submitAnswers:` para controladores de vista de consulta personalizada) ou `exit` método antes do controlador de vista é dispensado. Caso contrário, não serão enviadas estatísticas (ou seja, sem análises sobre a campanha) e mais campanhas acima de tudo, seguinte não serão notificadas enquanto o processo de aplicação não for reiniciado.
>
>

##### <a name="implementation-example"></a>Exemplo de implementação
Nesta implementação é carregada a vista de anúncio personalizado de um ficheiro de xib externo.

Como para personalização avançada de notificação, recomenda-para ver o código fonte da implementação padrão.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
