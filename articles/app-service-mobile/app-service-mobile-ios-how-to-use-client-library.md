---
title: Como utilizar SDK do iOS do Mobile Apps do Azure
description: Como utilizar SDK do iOS do Mobile Apps do Azure
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
ms.openlocfilehash: bd5e2fce31ae597f8ce48fb8f5492e280cbbf28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Como utilizar iOS biblioteca de clientes para Mobile Apps do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia informa efetuar cenários comuns utilizando a versão mais recente [SDK iOS do Mobile Apps do Azure][1]. Se estiver familiarizado com aplicações móveis do Azure, primeiro conclua [Azure Mobile Apps início rápido] para criar um back-end, criar uma tabela e transferir um projeto do iOS pré-criadas Xcode. Neste guia, iremos focar-se no SDK do iOS do lado do cliente. Para saber mais sobre o SDK do lado do servidor para o back-end, consulte o HOWTOs de SDK do servidor.

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o cliente SDK do iOS está localizada aqui: [iOS Mobile Apps do Azure referência cliente][2].

## <a name="supported-platforms"></a>Plataformas suportadas
O SDK iOS suporta projetos de Objective-C, Swift 2.2 projetos e Swift 2.3 projetos para o iOS 8.0 ou posteriores.

A autenticação de "servidor fluxo" utiliza um WebView para a IU apresentada.  Se o dispositivo não for capaz de apresentar a IU de WebView, em seguida, é necessário outro método de autenticação que está fora do âmbito do produto.  
Este SDK não, por conseguinte, é adequado para o tipo veja ou do mesmo modo restritos dispositivos.

## <a name="Setup"></a>A configuração e pré-requisitos
Este guia pressupõe que criou um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema como as tabelas nestes tutoriais. Este guia também parte do princípio que no seu código referenciar `MicrosoftAzureMobile.framework` e importar `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Como: criar o cliente
Para aceder a um back-end do Mobile Apps do Azure no seu projeto, crie um `MSClient`. Substitua `AppUrl` com o URL da aplicação. Pode deixar `gatewayURLString` e `applicationKey` vazio. Se configurar um gateway para a autenticação, preencher `gatewayURLString` com o URL de gateway.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**SWIFT**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


## <a name="table-reference"></a>Como: criar a referência de tabela
Para atualizar ou aceder a dados, crie uma referência para a tabela de back-end. Substitua `TodoItem` pelo nome da sua tabela

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**SWIFT**:

```
let table = client.tableWithName("TodoItem")
```


## <a name="querying"></a>Como: consultar dados
Para criar uma consulta de base de dados, consultar o `MSTable` objeto. A consulta seguinte obtém todos os itens `TodoItem` e regista o texto de cada item.

**Objective-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**SWIFT**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>Como: filtro devolveu dados
Para filtrar os resultados, existem muitas opções disponíveis.

Para utilizar um predicado de filtro, utilize um `NSPredicate` e `readWithPredicate`. Os seguintes filtros devolveu dados para localizar apenas os itens Todo incompletos.

**Objective-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**SWIFT**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>Como: utilizar MSQuery
Para executar uma consulta complexa (incluindo a ordenação e paginação), crie um `MSQuery` objeto, diretamente ou através da utilização de um predicado de:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**SWIFT**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`permite-lhe controlar diversos comportamentos de consulta.

* Especificar a ordem dos resultados
* Limitar os campos a devolver
* Limitar quantos regista a devolver
* Especificar a contagem total na resposta
* Especifique parâmetros de cadeia de consulta personalizada no pedido
* Aplicar funções adicionais

Executar um `MSQuery` consulta chamando `readWithCompletion` no objeto.

## <a name="sorting"></a>Como: ordenar dados com MSQuery
Para ordenar os resultados, vamos ver um exemplo. Para ordenar pelo campo 'text' ascendente e por 'concluída' descendente, invocar `MSQuery` desta forma:

**Objective-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**SWIFT**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Como: limite de campos e expandir os parâmetros de cadeia de consulta com MSQuery
Para limitar os campos devem ser devolvidos numa consulta, especifique os nomes dos campos no **selectFields** propriedade. Neste exemplo devolve apenas o texto e campos concluídos:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**SWIFT**:

```
query.selectFields = ["text", "complete"]
```

Para incluir os parâmetros de cadeia de consulta adicionais no pedido de servidor (por exemplo, uma vez que um script do lado do servidor personalizado utiliza-las), preencher `query.parameters` desta forma:

**Objective-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**SWIFT**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Como: configurar o tamanho da página
As Mobile Apps do Azure, o tamanho de página controla o número de registos que são solicitados numa altura das tabelas de back-end. Uma chamada para `pull` dados, em seguida, teriam de lote dos dados, com base no tamanho nesta página, até existem não existem mais registos para extrair.

É possível configurar um tamanho de página utilizando **MSPullSettings** conforme mostrado abaixo. O tamanho da página predefinido é 50 e o exemplo abaixo o alterado para 3.

Pode configurar um tamanho de outra página por motivos de desempenho. Se tiver um grande número de registos de dados pequena, um tamanho de página elevada reduz o número de ida e volta de servidor.

Esta definição controla apenas o tamanho de página do lado do cliente. Se o cliente pede-lhe um maior tamanho da página que suporta o back-end do Mobile Apps, o tamanho da página está limitado no máximo, que o back-end estiver configurado para suportar.

Esta definição é também o *número* de registos de dados, não o *tamanho bytes*.

Se aumentar o tamanho de página do cliente, também deve aumentar o tamanho de página no servidor. Consulte ["como: ajustar o tamanho de paginação da tabela"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter os passos efetuar este procedimento.

**Objective-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```


**SWIFT**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Como: Inserir dados
Para introduzir uma nova linha de tabela, crie um `NSDictionary` e invocar `table insert`. Se [esquema dinâmica] está ativada, o back-end móvel do App Service do Azure gera automaticamente novas colunas com base no `NSDictionary`.

Se `id` não for fornecido, o back-end gera automaticamente um novo ID exclusivo. Forneça as suas próprias `id` para utilizar o e-mail endereços, nomes de utilizador, ou a seus próprios personalizada valores como ID. Fornecer a suas próprias ID pode facilitar associações e a lógica da base de dados orientados para negócios.

O `result` contém o novo item que foi inserido. Dependendo da lógica de servidor, pode ter dados adicionais ou modificada em comparação com a qual foi transmitido para o servidor.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**SWIFT**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>Como: modificar dados
Para atualizar uma linha existente, modificar um item e a chamada `update`:

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**SWIFT**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Em alternativa, forneça o ID de linha e o campo atualizado:

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**SWIFT**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

No mínimo, o `id` tem de ser definido quando efetuar atualizações.

## <a name="deleting"></a>Como: eliminar dados
Para eliminar um item, invocar `delete` com o item:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Em alternativa, elimine, fornecendo um ID de linha:

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

No mínimo, o `id` tem de ser definido quando elimina a efetuar.

## <a name="customapi"></a>Como: chamar a API personalizada
Com uma API personalizada, pode expor qualquer funcionalidade de back-end. Não tem de mapear uma operação de tabela. Não só obtenha a mais controlo sobre mensagens, pode mesmo leitura/conjunto de cabeçalhos e altere o formato de corpo de resposta. Para saber como criar uma API personalizada no back-end, leia [APIs personalizadas](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Para chamar uma API personalizada, chamar `MSClient.invokeAPI`. O pedido e resposta conteúda são tratadas como JSON. Para utilizar outros tipos de suportes de dados, [utilize a outra sobrecarga do `invokeAPI` ] [ 5].  Para tornar um `GET` pedido em vez de um `POST` pedido, o parâmetro de conjunto `HTTPMethod` para `"GET"` e o parâmetro `body` para `nil` (uma vez que pedidos GET não dispõe de corpos de mensagens.) Se a API personalizada suportar outros verbos HTTP, alterar `HTTPMethod` adequadamente.

**Objective-C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**SWIFT**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>Como: registar modelos de push para enviar notificações de plataforma
Para registar modelos, transmitir modelos com o seu **client.push registerDeviceToken** método na sua aplicação de cliente.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**SWIFT**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Os modelos do tipo NSDictionary e podem conter vários modelos no seguinte formato:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**SWIFT**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Todas as etiquetas estão repartidas do pedido de segurança.  Para adicionar etiquetas ao instalações ou modelos dentro de instalações, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure][4].  Para enviar notificações utilizando estes modelos registados, trabalhar com [APIs de Hubs de notificação][3].

## <a name="errors"></a>Como: processar erros
Quando chamar um back-end móvel do App Service do Azure, o bloco de conclusão contém um `NSError` parâmetro. Quando ocorre um erro, este parâmetro é não é nil. No seu código, deve verificar este parâmetro e lidar com erro conforme necessário, como é demonstrado nos fragmentos de código anterior.

O ficheiro [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] define as constantes `MSErrorResponseKey`, `MSErrorRequestKey`, e `MSErrorServerItemKey`. Para obter mais dados relacionados com o erro:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**SWIFT**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Além disso, o ficheiro define constantes para cada código de erro:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**SWIFT**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Como: autenticar os utilizadores com a biblioteca de autenticação do Active Directory
Pode utilizar o Active Directory Authentication Library (ADAL) para a sessão dos utilizadores na sua aplicação com o Azure Active Directory. Autenticação de fluxo de cliente através de um fornecedor de identidade SDK é preferível a utilizar o `loginWithProvider:completion:` método.  Autenticação de cliente do fluxo fornece uma funcionalidade do UX mais nativa e permite a personalização adicional.

1. Configurar o back-end da aplicação móvel para o início de sessão do AAD ao seguir o [como configurar o serviço de aplicações para início de sessão do Active Directory] [ 7] tutorial. Certifique-se concluir o passo opcional de registar uma aplicação cliente nativa. Para iOS, recomendamos que o URI tem o formato de redirecionamento `<app-scheme>://<bundle-id>`. Para obter mais informações, consulte o [início rápido do ADAL iOS][8].
2. Instale a ADAL com o Cocoapods. Editar o Podfile para incluir a definição seguinte, substituindo **seu PROJETO** com o nome do seu projeto Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   e o Pod:

        pod 'ADALiOS'
3. Utilizar o Terminal, executar `pod install` do diretório que contém o projeto e, em seguida, abra a área de trabalho do Xcode gerada (não o projeto).
4. Adicione o seguinte código à sua aplicação, de acordo com o idioma que está a utilizar. Em cada um, efetuar estas substituições:

   * Substitua **aqui de autoridade de inserção** com o nome do inquilino no qual que aprovisionou a sua aplicação. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Este valor pode ser copiado a partir do separador de domínio no Azure Active Directory no [portal clássico do Azure].
   * Substitua **INSERT-RESOURCE-ID-aqui** com o ID de cliente para o back-end da aplicação móvel. Pode obter o ID de cliente do **avançadas** separador em **definições do Azure Active Directory** no portal.
   * Substitua **INSERT-cliente ID aqui** com o ID de cliente que copiou da aplicação cliente nativa.
   * Substitua **INSERT-REDIRECIONAMENTO-URI-aqui** com o seu site */.auth/login/done* ponto final, utilizando o esquema de HTTPS. Este valor deve ser semelhante *https://contoso.azurewebsites.net/.auth/login/done*.

**Objective-C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**SWIFT**:

    // add the following imports to your bridging header:
    //        #import <ADALiOS/ADAuthenticationContext.h>
    //        #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Como: autenticar os utilizadores com o SDK do Facebook para iOS
Pode utilizar o SDK do Facebook para iOS para a sessão dos utilizadores na sua aplicação utilizando o Facebook.  A utilização de uma autenticação de fluxo de cliente é preferível a utilizar o `loginWithProvider:completion:` método.  A autenticação do cliente fluxo fornece uma funcionalidade do UX mais nativa e permite a personalização adicional.

1. Configurar o back-end da aplicação móvel para Facebook início de sessão, seguindo o [como configurar o serviço de aplicações para início de sessão do Facebook] [ 9] tutorial.
2. Instalar o SDK do Facebook para iOS, seguindo o [SDK Facebook para iOS - introdução] [ 10] documentação. Em vez de criar uma aplicação, pode adicionar a plataforma iOS ao seu registo existente.
3. Documentação do Facebook inclui algum código em Objective-C o delegado de aplicação. Se estiver a utilizar **Swift**, pode utilizar as seguintes traduções para AppDelegate.swift:

        // Add the following import to your bridging header:
        //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }
4. Para além de adicionar `FBSDKCoreKit.framework` ao seu projeto, também adicionar uma referência a `FBSDKLoginKit.framework` da mesma forma.
5. Adicione o seguinte código à sua aplicação, de acordo com o idioma que está a utilizar.

**Objective-C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {        
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**SWIFT**:

    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Como: autenticar os utilizadores com recursos de infraestrutura do Twitter para iOS
Pode utilizar recursos de infraestrutura para iOS para a sessão dos utilizadores na sua aplicação com o Twitter. Autenticação de fluxo de cliente é preferível a utilizar o `loginWithProvider:completion:` método, dado que fornece uma funcionalidade do UX mais nativa e permite a personalização adicional.

1. Configurar o back-end da aplicação móvel para Twitter início de sessão, seguindo o [como configurar o serviço de aplicações para início de sessão do Twitter](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) tutorial.
2. Adicionar recursos de infraestrutura ao seu projeto, seguindo o [recursos de infraestrutura para iOS - introdução] configurar TwitterKit e a documentação.

   > [!NOTE]
   > Por predefinição, os recursos de infraestrutura cria uma aplicação do Twitter para si. Pode evitar criar uma aplicação ao registar a chave de consumidor e o segredo de consumidor que criou anteriormente utilizando os seguintes fragmentos de código.    Em alternativa, pode substituir os valores de chave de consumidor e o segredo de consumidor que fornecem ao serviço de aplicações com os valores que vê no [Dashboard de recursos de infraestrutura]. Se escolher esta opção, não se esqueça de definir o URL de chamada de retorno para um valor do marcador de posição, tal como `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.
   >
   >

    Se optar por utilizar os segredos que criou anteriormente, adicione o seguinte código para o delegado de aplicação:

    **Objective-C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }

    **SWIFT**:

        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
3. Adicione o seguinte código à sua aplicação, de acordo com o idioma que está a utilizar.

**Objective-C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**SWIFT**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Como: autenticar os utilizadores com o SDK de início de sessão do Google para iOS
Pode utilizar o SDK de início de sessão do Google para iOS para a sessão dos utilizadores na sua aplicação utilizando uma conta do Google.  Google anunciou recentemente alterações para as suas políticas de segurança de OAuth.  Estas alterações de política irão exigir a utilização do Google SDK no futuro.

1. Configurar o back-end da aplicação móvel para o Google início de sessão, seguindo o [como configurar o serviço de aplicações para início de sessão do Google](../app-service/app-service-mobile-how-to-configure-google-authentication.md) tutorial.
2. Instalar o SDK Google para iOS, seguindo o [Google início de sessão para iOS - começar a integrar](https://developers.google.com/identity/sign-in/ios/start-integrating) documentação. Pode ignorar a secção "Autenticar com o servidor de back-end de".
3. Adicione o seguinte ao seu delegado `signIn:didSignInForUser:withError:` método, de acordo com o idioma que está a utilizar.

**Objective-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };

        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**SWIFT**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

1. Certifique-se também de adicionar o seguinte procedimento para `application:didFinishLaunchingWithOptions:` no seu delegado de aplicação, substituindo "SERVER_CLIENT_ID" com o mesmo ID que utilizou para configurar o serviço de aplicações no passo 1.

**Objective-C**:

         [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";

 **SWIFT**:

        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"


1. Adicione o seguinte código à sua aplicação num UIViewController que implementa o `GIDSignInUIDelegate` protocolo, de acordo com o idioma que está a utilizar.  São terminada antes de ser assinado novamente e, apesar de não precisa de introduzir as suas credenciais novamente, verá uma caixa de diálogo de consentimento.  Só é possível chame este método quando o token de sessão expirou.

   **Objective-C**:

       #import <Google/SignIn.h>
       // ...
       - (void)authenticate
       {
               [GIDSignIn sharedInstance].uiDelegate = self;
               [[GIDSignIn sharedInstance] signOut];
               [[GIDSignIn sharedInstance] signIn];
        }

   **SWIFT**:

       // ...
       func authenticate() {
           GIDSignIn.sharedInstance().uiDelegate = self
           GIDSignIn.sharedInstance().signOut()
           GIDSignIn.sharedInstance().signIn()
       }

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile Apps início rápido]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[esquema dinâmica]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Dashboard de recursos de infraestrutura]: https://www.fabric.io/home
[recursos de infraestrutura para iOS - introdução]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
