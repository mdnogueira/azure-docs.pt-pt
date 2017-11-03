---
title: "Adicionar início de sessão para uma aplicação iOS utilizando o ponto final v 2.0 do Azure AD | Microsoft Docs"
description: "Como criar uma aplicação iOS que inicia sessão dos utilizadores com ambos os conta pessoal da Microsoft e contas profissionais ou escolares, utilizando bibliotecas de terceiros."
services: active-directory
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf1455dc3d55ea3581195f7a315556d134c23a26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adicionar início de sessão a uma aplicação iOS utilizando uma biblioteca de terceiros com Graph API utilizando o ponto final v 2.0
A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Os programadores podem utilizar qualquer biblioteca que pretendem integrar aos nossos serviços. Para ajudar os programadores a utilizar a nossa plataforma com outras bibliotecas, escrevemos algumas instruções como esta para demonstrar como configurar bibliotecas de terceiros para ligar à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação de especificação RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) pode ligar à plataforma de identidade da Microsoft.

Com a aplicação que cria esta explicação passo a passo, os utilizadores podem iniciar sessão na sua organização e, em seguida, procure outras pessoas na sua organização ao utilizar a Graph API.

Se estiver familiarizado com o OAuth2 ou o OpenID Connect, muito este exemplo de configuração poderá não fazer sentido para si. Recomendamos que leia [protocolos de v 2.0 - fluxo de código do OAuth 2.0 autorização](active-directory-v2-protocols-oauth-code.md) para em segundo plano.

> [!NOTE]
> Algumas funcionalidades da nossa plataforma que têm uma expressão as normas de OAuth2 ou o OpenID Connect, como o acesso condicional e gestão de políticas do Intune, tem de utilizar nosso bibliotecas de identidade do Microsoft Azure de código aberto.
> 
> 

O ponto final v 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades.

> [!NOTE]
> Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Transferir o código a partir do GitHub
O código deste tutorial [é mantido no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Para acompanhar, pode [transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Pode também simplesmente transferir o exemplo e começar imediatamente:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registar uma aplicação
Criar uma nova aplicação, o [portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou siga os passos detalhados em [como registar uma aplicação com o ponto final v 2.0](active-directory-v2-app-registration.md).  Certifique-se de que:

* Copiar o **Id da aplicação** que está atribuído à sua aplicação, porque irá precisar das mesmas em breve.
* Adicionar o **Mobile** plataforma para a sua aplicação.
* Copiar o **URI de redirecionamento** do portal. Tem de utilizar o valor predefinido de `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Transferir a biblioteca de NXOAuth2 de terceiros e criar uma área de trabalho
Nestas instruções, irá utilizar o OAuth2Client do GitHub, o que é uma biblioteca OAuth2 para Mac OS X e iOS (Cocoa e Cocoa touch). Esta biblioteca é baseada no rascunho 10 da especificação do OAuth2. Implementa o perfil de aplicação nativa e suporta o ponto final de autorização do utilizador. Estes são todos os aspetos, terá de integrar com a plataforma de identidade da Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Adicionar a biblioteca ao seu projeto com o CocoaPods
O CocoaPods é um gestor de dependência para projetos do Xcode. Gere automaticamente os passos de instalação anterior.

```
$ vi Podfile
```
1. Adicione o seguinte a este podfile:
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. Carregue o podfile utilizando o CocoaPods. Isto irá criar uma área de trabalho Xcode nova que irá carregar.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Explorar a estrutura do projeto
A seguinte estrutura está configurada para o nosso projeto na estrutura:

* Uma vista principal com uma pesquisa UPN
* Uma vista de detalhes para os dados sobre o utilizador selecionado
* Uma vista de início de sessão em que um utilizador pode iniciar sessão na aplicação para consultar o gráfico

Iremos irá mover para vários ficheiros na estrutura para adicionar a autenticação. Outras partes desse código, tais como o código visual não pertençam à identidade, mas são fornecidas por si.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurar o ficheiro de settings.plst na biblioteca
* No projeto de início rápido, abra o `settings.plist` ficheiro. Substitua os valores dos elementos na secção para refletir os valores que utilizou no portal do Azure. O código será referenciar estes valores, sempre que utiliza a biblioteca de autenticação do Active Directory.
  * O `clientId` é o ID de cliente da aplicação que copiou do portal.
  * O `redirectUri` é o URL de redirecionamento fornecido no portal.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurar a biblioteca NXOAuth2Client no seu LoginViewController
A biblioteca NXOAuth2Client requer alguns valores para obter a configurar. Depois de concluir essa tarefa, pode utilizar o token adquirido para chamar a API de gráfico. Porque `LoginView` será chamado sempre que é necessário para autenticar, faz sentido para colocar os valores de configuração para que o ficheiro.

* Vamos adicionar alguns valores para o `LoginViewController.m` ficheiros ao definir o contexto de autenticação e autorização. Detalhes sobre os valores siga o código.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Vamos ver detalhes sobre o código.

Destina-se a primeira cadeia `scopes`.  O `User.Read` valor permite-lhe ler o perfil básico do utilizador com sessão iniciada.

Pode saber mais sobre todos os âmbitos disponíveis em [âmbitos de permissões de Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Para `authURL`, `loginURL`, `bhh`, e `tokenURL`, deve utilizar os valores fornecidos anteriormente. Se utilizar o bibliotecas de identidade do Microsoft Azure de código aberto, vamos obter estes dados por si através do nosso ponto final de metadados. Tratámos da parte difícil de extrair estes valores para si.

O valor `keychain` é o contentor que a biblioteca NXOAuth2Client irá utilizar para criar um keychain para armazenar os seus tokens. Se gostaria de obter início de sessão único (SSO) em várias aplicações, pode especificar o mesmo keychain em cada uma das suas aplicações e pedir a utilização desse keychain na elegibilidade do Xcode. Isto é explicado na documentação da Apple.

O resto destes valores são necessárias para utilizar a biblioteca e criar locais levar os valores no contexto.

### <a name="create-a-url-cache"></a>Criar uma cache de URL
Dentro de `(void)viewDidLoad()`, sempre que é chamado depois da vista está carregada, o seguinte código primes uma cache para utilização.

Adicione o seguinte código:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Criar um WebView para início de sessão
Um WebView pode pedir ao utilizador fatores adicionais, como mensagem de texto SMS (se configurada) ou devolver mensagens de erro ao utilizador. Aqui iremos definir configurar o WebView e, em seguida, mais tarde, escrever o código para processar as chamadas de retorno que irão surgir no WebView dos serviços de identidade.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Substitua os métodos de WebView para processar a autenticação
Para dizer ao WebView o que acontece quando um utilizador tem de iniciar sessão, tal como abordado anteriormente, pode colar o seguinte código.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Escreva o código para processar o resultado do pedido OAuth2
O seguinte código irá processar o redirectURL devolve do WebView. Se a autenticação não foi concluída com êxito, o código irá tentar novamente. Entretanto, a biblioteca irá fornecer o erro que podem ver na consola ou processar de forma assíncrona.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Configurar o contexto de OAuth (denominada conta arquivo)
Aqui pode chamar `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` no arquivo para cada serviço que pretende que a aplicação para poder aceder à conta partilhada. O tipo de conta é uma cadeia que é utilizada como um identificador para um determinado serviço. Porque estão a aceder a Graph API, o código se refere ao mesmo como `"myGraphService"`. Em seguida, configure um observador que informará quando ocorrem alterações com o token. Depois de obter o token, é devolvido o utilizador novamente para o `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configurar a vista principal para procurar e apresentar os utilizadores da Graph API
Uma aplicação Master-View-Controller (MVC) que apresenta os dados devolvidos na grelha ultrapassa o âmbito destas instruções e muitos tutoriais online explicam como criar um. Todos os este código é no ficheiro de estrutura. No entanto, terá de lidar com algumas coisas nesta aplicação MVC:

* Intercetar quando um utilizador tipos algo no campo de pesquisa
* Fornecer um objeto de dados de volta para o MasterView para poder apresentar os resultados na grelha

Iremos executará as abaixo.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Adicionar uma verificação para ver se está registado no
A aplicação aparece pouco se o utilizador não está assinado, pelo que é inteligente verificar se já houver um token na cache. Caso contrário, redireciona para o LoginView para o utilizador iniciar sessão. Se recorda, a melhor forma de efetuar ações quando carrega uma vista estiver a utilizar o `viewDidLoad()` método Apple fornece-nos.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Atualizar a vista em tabela quando são recebidos dados
Quando a Graph API devolve dados, terá de apresentar os dados. De simplicidade, segue-se todos os o código para atualizar a tabela. Apenas pode colar os valores corretos no seu código automático MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Fornecer uma forma para chamar a API de gráfico quando alguém tipos de campo de procura
Quando um utilizador tipos uma pesquisa na caixa, terá de shove que a ativação pós-falha para a Graph API. O `GraphAPICaller` classe, que irá criar no seguinte código, separa a funcionalidade de pesquisa da apresentação. Por agora, vamos escrever o código que feeds caracteres de pesquisa para a Graph API. Podemos fazê-lo, fornecendo um método chamado `lookupInGraph`, que demora a cadeia que queremos para procurar.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Escrever uma classe de programa auxiliar para aceder à API do gráfico
Este é o núcleo da nossa aplicação. Enquanto o resto foi inserir o código no padrão MVC predefinido da Apple, aqui é escrever código para consultar o gráfico dos tipos de utilizador e, em seguida, devolver os dados. Eis o código e uma explicação detalhada seguinte.

### <a name="create-a-new-objective-c-header-file"></a>Criar um novo ficheiro de cabeçalho de Objective C
Nome de ficheiro `GraphAPICaller.h`e adicione o seguinte código.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Aqui pode ver que um método especificado demora uma cadeia e devolve um completionBlock. Este completionBlock, como o poderá ter adivinhado, irá atualizar a tabela, fornecendo um objeto com a dados preenchidos em tempo real, como a procura de utilizador.

### <a name="create-a-new-objective-c-file"></a>Criar um novo ficheiro de Objective C
Nome de ficheiro `GraphAPICaller.m`e adicione o seguinte método.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Passemos através deste método em detalhe.

As principais deste código está no `NXOAuth2Request`, método que utiliza os parâmetros que que já definiu no ficheiro settings.plist.

O primeiro passo é para construir a chamada de API de gráfico à direita. Porque está a chamar `/users`, especificar que acrescentando-lo para o recurso da Graph API juntamente com a versão. Faz sentido para colocar num ficheiro de definições externo porque estes podem alterar-se à medida que evolui de API.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Em seguida, tem de especificar os parâmetros que também irá fornecer para a chamada de API de gráfico. É *muito importante* que coloque os parâmetros no ponto final recursos porque o que é removida de todos os carateres conforming não URI no tempo de execução. Todo o código de consulta tem de ser fornecido nos parâmetros.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

É possível que repare isto chama um `convertParamsToDictionary` método que ainda não escritas ainda. Vamos fazer agora, por isso, no final do ficheiro:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Em seguida, vamos utilizar o `NXOAuth2Request` método para obter dados de volta a partir de API no formato JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Por fim, vamos ver como devolver os dados para o MasterViewController. Os dados devolve conforme serializada e é necessário anular a serialização e carregar um objeto que o MainViewController pode consumir. Para esta finalidade, a estrutura tem um `User.m/h` ficheiro que cria um objeto de utilizador. Preencher esse objeto de utilizador com as informações de gráfico.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Executar o exemplo
Se tiver utilizado a estrutura ou seguidas juntamente com as instruções que agora deve ser executada a sua aplicação. Iniciar o simulador e clique em **sessão** a utilizar a aplicação.

## <a name="get-security-updates-for-our-product"></a>Obter as atualizações de segurança para o nosso produto
Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando o [TechCenter de segurança](https://technet.microsoft.com/security/dd252948) e subscrever SUBSCREVENDO os alertas de segurança.

