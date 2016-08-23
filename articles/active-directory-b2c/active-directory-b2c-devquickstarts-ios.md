<properties
    pageTitle="Azure Active Directory B2C: chamar uma API Web a partir de uma aplicação iOS, utilizando bibliotecas de terceiros | Microsoft Azure"
    description="Este artigo irá mostrar como criar uma aplicação de lista de itens pendentes de iOS que chama uma API Web do Node.js utilizando os tokens de portador OAuth 2.0 e uma biblioteca de terceiros"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# Azure AD B2C: Chamar uma API Web a partir de uma aplicação iOS, utilizando uma biblioteca de terceiros

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Isto permite aos programadores tirarem partido de qualquer biblioteca que pretendam integrar aos nossos serviços. Para ajudar os programadores a utilizarem a nossa plataforma com outras bibliotecas, escrevemos algumas instruções como esta para demonstrar como configurar bibliotecas de terceiros para ligar à plataforma de identidades da Microsoft. A maioria das bibliotecas que implementam [a especificação RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) poderão ligar à plataforma de Identidades da Microsoft.


Se não estiver familiarizado com o OAuth2 ou o OpenID Connect, este exemplo de configuração pode não fazer muito sentido para si. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Algumas funcionalidades da nossa plataforma que têm uma expressão nestas normas, como a gestão de políticas do Intune e o Acesso Condicional, requerem a utilização das nossas Bibliotecas de Identidade do Microsoft Azure de código aberto. 
   
Nem todos os cenários e funcionalidades do Azure Active Directory são suportados pela plataforma do B2C.  Para determinar se deve utilizar a plataforma do B2C, leia as [limitações do B2C](active-directory-b2c-limitations.md).


## Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de continuar.

## Criar uma aplicação

Em seguida, precisa de criar uma aplicação no diretório do B2C. Isto proporciona informações do Azure AD de que necessita para comunicar de forma segura com a sua aplicação. A aplicação e a API Web são representadas por um único **ID da aplicação** neste caso, uma vez que compõem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

- Incluir um **dispositivo móvel** na aplicação.
- Copiar a **ID da Aplicação** atribuída à aplicação. Também irá precisar deste mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar as políticas

No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém uma experiência de identidade: um início de sessão e inscrição combinados. Tem de criar esta política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando cria a política, certifique-se de que:

- Escolher o **Nome a apresentar** e atributos de inscrição na política.
- Escolher as afirmações de aplicação **Nome a apresentar** e **ID de objeto** em cada política. Também pode escolher outras afirmações.
- Copiar o **Nome** de cada política depois de a criar. Deve ter o prefixo `b2c_1_`.  Precisará do nome da política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as políticas, está pronto para criar a aplicação.


## Transferir o código

O código deste tutorial [é mantido no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Para continuar, pode [descarregar a aplicação como um .zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) ou cloná-la:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Ou simplesmente descarregar o código de conclusão e começar imediatamente: 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## Descarregue o nxoauth2 da biblioteca de terceiros e inicie uma área de trabalho

Nestas instruções utilizamos o OAuth2Client do GitHub, uma biblioteca OAuth2 para Mac OS X & iOS (Cocoa & Cocoa touch). Esta biblioteca é baseada no rascunho 10 da especificação do OAuth2. Implementa o perfil da aplicação nativa e suporta o ponto final de autorização do utilizador final. Isto é tudo o que precisamos para integrar com a plataforma de identidades da Microsoft.

### A adicionar a biblioteca ao seu projeto com o CocoaPods

O CocoaPods é um gestor de dependência para projetos do Xcode. Gere automaticamente os passos de instalação acima.

```
$ vi Podfile
```
Adicione o seguinte a este podfile:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Agora carregue o podfile com o cocoapods. Isto irá criar uma Área de Trabalho Xcode nova que irá carregar.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## A estrutura do projeto

Temos a seguinte estrutura configurada para o nosso projeto na estrutura:

* Um **Vista Principal** com um painel de tarefas
* A opção **Adicionar Vista de Tarefas** para os dados sobre a tarefa selecionada
* A opção **Vista de Início de Sessão** que permite que um utilizador inicie sessão na aplicação.

Iremos avançar para vários ficheiros no projeto para adicionar a autenticação. Outras partes desse código, como o código visual não é importante para a identidade e é-lhe enviado.

## Crie o ficheiro `settings.plist` para a sua aplicação

É mais fácil configurar a aplicação se tivermos uma localização centralizada para colocar os nossos valores de configuração. Também o ajuda a compreender o que cada definição faz na aplicação. Iremos tirar partido da *Lista de Propriedades* como uma forma de dar estes valores à aplicação.

* Crie/abra o ficheiro `settings.plist` em `Supporting Files`, na sua área de trabalho da aplicação

* Introduza os seguinte valores (iremos aprodundar-nos sobre eles em breve)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Vamos entrar em detalhe sobre estes.


Para `authURL`, `loginURL`, `bhh`, `tokenURL` irá notar que precisa preencher o nome do seu inquilino. Este é o nome de inquilino do seu inquilino do B2C que lhe foi atribuído. Por exemplo, `kidventusb2c.onmicrosoft.com`. Se utiliza as nossas Bibliotecas de Identidade do Microsoft Azure de código aberto, iríamos buscar estes dados para si, através do nosso ponto final de metadados. Tratámos da parte difícil de extrair estes valores para si.

Para obter mais informações sobre os nomes de inquilino do B2C dê uma vista de olhos em: [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)

O valor `keychain` é o contentor que a biblioteca NXOAuth2Client irá utilizar para criar um keychain para armazenar os seus tokens. Se gostaria de obter SSO em várias aplicações, pode especificar o mesmo keychain em cada uma das suas aplicações, bem como pode pedir a utilização desse keychain na elegibilidade do XCode. Isto é explicado na documentação da Apple.

O `<policy name>` no final de cada URL são os locais onde pode colocar a política criada acima. A aplicação irá chamar estas políticas consoante o fluxo.

O `taskAPI` é o Ponto Final do REST que iremos chamar com o token de B2C para adicionar tarefas ou consultar tarefas existentes. Isto foi configurado especificamente para este exemplo. Não tem de alterar para o exemplo funcionar.

O resto destes valores tem de utilizar a biblioteca e simplesmente criar locais para levar os valores para o contexto.

Agora que temos o ficheiro `settings.plist` criado, o código tem de o ler.

## Configure uma classe do AppData para ler as nossas definições

Vamos criar um ficheiro simples que analisa apenas o nosso ficheiro `settngs.plist` criado acima e tornar essas definições disponíveis no futuro para qualquer classe. Uma vez que não queremos criar uma nova cópia dos dados sempre que uma classe o pedir, iremos utilizar um padrão de Singleton e devolver apenas a mesma instância criada, sempre que for efetuado um pedido para as definições

* Criar um ficheiro `AppData.h`:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Criar um ficheiro `AppData.m`:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Agora podemos chegar facilmente aos nossos dados, chamando simplesmente `  AppData *data = [AppData getInstance];` em qualquer uma das nossas classes, conforme verá abaixo.



## Configure a biblioteca NXOAuth2Client no seu AppDelegate

A biblioteca NXOAuthClient requer que alguns valores sejam configurados. Assim que estiver concluído, pode utilizar o token adquirido para chamar a API REST. Uma vez que sabemos que o `AppDelegate` será chamado a qualquer altura ao carregar a aplicação, faz sentido colocarmos os nossos valores de configuração nesse ficheiro.
* Abra o ficheiro `AppDelegate.m`

* Importe alguns ficheiros de cabeçalho que utilizaremos mais tarde.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Adicione o método `setupOAuth2AccountStore` no AppDelegate

Temos de criar um AccountStore e, em seguida, colocar os dados que acabamos de ler a partir do ficheiro `settings.plist`.

Existem algumas coisas que deve saber nesta altura sobre o serviço de B2C, que tornará este código mais compreensível:


1. O Azure AD B2C utiliza a *política*, conforme fornecida pelos parâmetros de consulta para atender o seu pedido. Isto permite que o Azure Active Directory aja como um serviço independente apenas para a sua aplicação. Para fornecer estes parâmetros de consulta adicionais, temos de fornecer o método `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` com os nossos parâmetros de política personalizada. 

2. O Azure AD B2C utiliza âmbitos em grande parte da mesma forma que os outros servidores do OAuth2. No entanto, uma vez que a utilização do B2C se trata tanto da autenticação de um utilizador, como do acesso a recursos, alguns âmbitos são totalmente necessários para que o fluxo funcione corretamente. Este é o âmbito `openid`. Os nossos SDKs de identidade da Microsoft fornecem automaticamente o âmbito `openid` para si, para que não veja na nossa configuração do SDK. No entanto, uma vez que estamos a utilizar uma biblioteca de terceiros, temos de especificar este âmbito.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Em seguida, certifique-se de que efetua a chamada no AppDelegate no método `didFinishLaunchingWithOptions:`. 

```
[self setupOAuth2AccountStore];
```


## Crie uma classe `LoginViewController`, que iremos utilizar para processar pedidos de autenticação

Utilizamos uma webview para início de sessão na conta. Isto permite-nos solicitar ao utilizador fatores adicionais, como mensagem de texto SMS (se configurada) ou enviar mensagens de erro para o utilizador. Aqui iremos configurar o webview e, mais tarde, escrever o código para processar as chamadas de retorno que irão surgir no WebView do Serviço de Identidade da Microsoft.

* Crie uma classe `LoginViewController.h`

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Iremos criar cada um destes métodos abaixo.

> [AZURE.NOTE] 
    Certifique-se de que liga o `loginView` ao webview real que está no interior do seu guião gráfico. Caso contrário, não terá um webview que possa aparecer quando está na altura de autenticar.

* Crie uma classe `LoginViewController.m`

* Adicione algumas variáveis para processar o estado enquanto autenticamos

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Substitua os métodos de WebView para processar a autenticação

Precisamos de dizer ao webview o comportamento que queremos quando um utilizador tem de iniciar sessão, tal como descrito acima. Pode simplesmente cortar e colar o código abaixo.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Escreva o código para processar o resultado do pedido OAuth2

Iremos precisar do código que irá processar o redirectURL retornado do WebView. Se não for efetuado com êxito, tentaremos novamente. Entretanto, a biblioteca irá fornecer o erro que pode ver na consola ou processar de forma assíncrona. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configure as fábricas de Notificação.

Criamos o mesmo método que utilizámos no `AppDelegate` acima, mas desta vez adicionamos alguns `NSNotification`s para nos dizer o que está a acontecer no nosso serviço. Configuramos um observador que nos irá dizer quando ocorrem alterações com o token. Assim que tivermos o token, devolvemos o utilizador para o `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Adicione o código que processa o utilizador sempre que inicia um pedido de início de sessão nativo

Vamos criar um método que será chamado sempre que tivermos um pedido de autenticação. Este será o método que, na verdade, cria um webview

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Por fim, vamos chamar todos estes métodos escritos acima sempre que o `LoginViewController` carregar. Podemos fazê-lo ao adicionar estes métodos ao nosso método `viewDidLoad` que a Apple nos dá

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Terminou agora a criação do caminho principal, iremos interagir com a nossa aplicação para iniciar de sessão. Depois de iniciar sessão, precisamos que utilize os nossos tokens recebidos. Para isso, vamos criar alguns códigos de programa auxiliar que irá chamar as APIs REST para utilizarmos esta biblioteca.


## Crie uma classe `GraphAPICaller` para processar os nossos pedidos para uma API REST

Temos uma configuração carregada sempre carregamos a nossa aplicação. Agora, temos de fazer algo com a mesma assim que tivermos um token. 

* Crie um ficheiro `GraphAPICaller.h`

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Vê a partir deste código que iremos criar dois métodos: um para obter as tarefas de uma API e outro para adicionar tarefas à API.

Agora que configurámos a nossa interface, vamos adicionar a implementação real:

* Criar um `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## Execute a aplicação de exemplo

Por último, crie e execute a aplicação no Xcode. Inscreva-se ou inicie sessão na aplicação e crie tarefas para um utilizador com sessão iniciada. Termine a sessão e inicie sessão novamente como um utilizador diferente e crie tarefas para esse utilizador.

Tenha em atenção que as tarefas são armazenadas por utilizador na API, porque a API extrai a identidade do utilizador a partir do token de acesso que recebe.


## Passos seguintes

Agora pode aceder a tópicos de B2C mais avançados. Pode experimentar:

[Ligar uma API Web do Node.js a partir de uma aplicação Web do Node.js]()

[Personalizar o UX para uma aplicação B2C]()



<!--HONumber=Aug16_HO1-->


