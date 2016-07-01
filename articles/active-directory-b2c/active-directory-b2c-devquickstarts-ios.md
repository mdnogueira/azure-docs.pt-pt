<properties
    pageTitle="Pré-visualização do Azure Active Directory B2C: Chamar uma API Web a partir de uma aplicação iOS | Microsoft Azure"
    description="Este artigo irá mostrar como criar uma aplicação de lista de itens pendentes de iOS que chama uma API Web do Node.js utilizando os tokens de portador OAuth 2.0. A aplicação iOS e a API Web utilizam o Azure Active Directory B2C para gerir identidades de utilizador e autenticar utilizadores."
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

    ms.date="05/31/2016"
    ms.author="brandwe"/>

# Pré-visualização do Azure AD B2C: Chamar uma API Web a partir de uma aplicação iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Ao utilizar Azure Active Directory (Azure AD) B2C, pode adicionar funcionalidades de gestão de identidade poderosas self-service às suas aplicações iOS e APIs Web em poucos passos. Este artigo mostra como criar uma aplicação de lista de itens pendentes de iOS que chama uma API Web do Node.js utilizando os tokens de portador OAuth 2.0. A aplicação iOS e a API Web utilizam o Azure AD B2C para gerir identidades de utilizador e autenticar utilizadores.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
    Para funcionar em pleno, este guia de introdução requer que já tenha uma API Web protegida pelo Azure AD B2C. Criámos uma para .NET e Node.js, que pode utilizar. Estas instruções partem do princípio de que a amostra da API Web do Node.js está configurada. Consulte [API Web do Azure Active Directory para amostra Node.js](active-directory-b2c-devquickstarts-api-node.md) para obter mais informações.
).

> [AZURE.NOTE]
    Este artigo não aborda como implementar o início de sessão, inscrição e gestão de perfis com o Azure AD B2C. Concentra-se nas chamadas das APIs Web depois de o utilizador ser autenticado. Se ainda não o fez, deve começar com o [Tutorial de introdução da aplicação Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre as tarefas básicas do Azure AD B2C.

## Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de continuar.

## Criar uma aplicação

Em seguida, precisa de criar uma aplicação no diretório do B2C. Isto proporciona informações do Azure AD de que necessita para comunicar de forma segura com a sua aplicação. A aplicação e a API Web são representadas por um único **ID da aplicação** neste caso, uma vez que compõem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

- Incluir uma **aplicação Web/API Web** na aplicação.
- Introduzir `http://localhost:3000/auth/openid/return` como um **URL de resposta**. É o URL predefinido para este exemplo de código.
- Criar um **Segredo de aplicação** para a aplicação e copiá-lo. Precisará dele mais tarde.
- Copiar a **ID da Aplicação** atribuída à aplicação. Também irá precisar deste mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar as políticas

No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém três experiências de identidade: inscrição, início de sessão e início de sessão utilizando o Facebook. Tem de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando cria as três políticas, certifique-se de que:

- Escolher o **nome a apresentar** e atributos de inscrição na política de inscrição.
- Escolher as afirmações de aplicação **Nome a apresentar** e **ID de objeto** em cada política. Também pode escolher outras afirmações.
- Copiar o **Nome** de cada política depois de a criar. Deve ter o prefixo `b2c_1_`.  Precisará destes nomes de políticas mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, está pronto para criar a aplicação.

Tenha em atenção que este artigo não aborda como utilizar as políticas que acabou de criar. Para saber mais sobre como funcionam as políticas no Azure AD B2C, comece com o [Tutorial de introdução da aplicação Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Transferir o código

O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Para criar a amostra à medida que avança, pode [transferir o projeto de estrutura como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Tem de transferir a estrutura para concluir este tutorial.** Devido a complexidade da implementação de uma aplicação totalmente funcional no iOS, a **estrutura** tem código UX que será executado depois de concluir o tutorial. Esta é uma medida que poupa tempo ao programador. O código UX não é relevante para o tópico sobre como adicionar o B2C a uma aplicação iOS.

A aplicação concluída está também [disponível como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) ou no `complete` ramo do mesmo repositório.

Em seguida, carregue `podfile` utilizando o CocoaPods. Isto irá criar uma área de trabalho Xcode nova que irá carregar. Se não tiver o CocoaPods, [visite o Web site para configurá-lo](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Configurar a aplicação de tarefa de iOS

Para obter a aplicação de tarefa do iOS para comunicar com o Azure AD B2C, terá de fornecer alguns parâmetros comuns. Na pasta `Microsoft Tasks` , abra o ficheiro `settings.plist` na raiz do projeto e substitua os valores na secção `<dict>`. Estes valores serão utilizados em toda a aplicação.

```
<dict>
    <key>authority</key>
    <string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
    <key>clientId</key>
    <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    <key>scopes</key>
    <array>
        <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    </array>
    <key>additionalScopes</key>
    <array>
    </array>
    <key>redirectUri</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>taskWebAPI</key>
    <string>http://localhost/tasks:3000</string>
    <key>emailSignUpPolicyId</key>
    <string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
    <key>faceBookSignInPolicyId</key>
    <string><your sign in policy for FB></string>
    <key>emailSignInPolicyId</key>
    <string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
    <key>fullScreen</key>
    <false/>
    <key>showClaims</key>
    <true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Obter os tokens de acesso e chamar a API de tarefa

Esta secção descreve como pode concluir uma troca de tokens OAuth 2.0 numa aplicação Web através da utilização de bibliotecas e estruturas da Microsoft. Se não estiver familiarizado com códigos de autorização e tokens de acesso, pode saber mais na [Referência do protocolo OAuth 2.0](active-directory-b2c-reference-protocols.md).

### Criar ficheiros do cabeçalho utilizando métodos

Irá necessitar de métodos para obter um token com a política selecionada e, em seguida, ligar o servidor de tarefas. Configure-os agora.

Criar um ficheiro denominado `samplesWebAPIConnector.h` em `/Microsoft Tasks` no projeto Xcode

Adicione o código seguinte para definir o que precisa de fazer:

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Estas são operações simples de criar, ler, atualizar e eliminar (CRUD) na sua API, bem como um método `doPolicy`. Ao utilizar este método, pode obter um token com a política que pretende.

Tenha em atenção que tem de ter outros dois ficheiros de cabeçalho definidos. Estes irão armazenar os dados de item e de política de tarefas. Crie-os agora:

Crie o ficheiro `samplesTaskItem.h` com o seguinte código:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Crie também o ficheiro `samplesPolicyData.h` para armazenar os dados de política:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Adicionar uma implementação para os seus itens de tarefa e política

Agora que os seus ficheiros de cabeçalho estão no devido local, pode escrever o código para armazenar os dados que irá utilizar para a sua amostra.

Crie o ficheiro `samplesPolicyData.m` com o seguinte código:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Escrever o código de configuração para a chamada da ADAL para iOS

O código rápido para armazenar os objetos de IU agora está concluído. Em seguida, terá de escrever o código para aceder à Biblioteca de Autenticação do Active Directory (ADAL) para iOS, utilizando os parâmetros que incluir em `settings.plist`. Isto irá obter um token de acesso para fornecer ao seu servidor de tarefas.

Todo o seu trabalho será efetuado num formato `samplesWebAPIConnector.m`.

Primeiro, crie a implementação `doPolicy()` que escreveu no seu ficheiro de cabeçalho `samplesWebAPIConnector.h`:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

O método é simples. Aceita como entradas o objeto `samplesPolicyData` que criou, o elemento principal `ViewController` e uma chamada de retorno. A chamada de retorno é interessante e vamos observá-la mais atentamente.

- Tenha em atenção que `completionBlock` tem `ADProfileInfo` como um tipo que vai ser devolvido através da utilização de um objeto `userInfo`. `ADProfileInfo` é o tipo que contém todas as respostas a partir do servidor, incluindo afirmações.
- Tenha também em atenção `readApplicationSettings`. Isto lê os dados que forneceu em `settings.plist`.
- Por fim, se tiver um grande método `getClaimsWithPolicyClearingCache`. Esta é a chamada real da ADAL para iOS que precisa de escrever. Voltaremos a este assunto mais tarde.

Em seguida, escreva o seu grande método `getClaimsWithPolicyClearingCache`. Este é suficientemente grande para merecer a sua própria secção.

### Criar a chamada de ADAL para iOS

Depois de transferir a estrutura a partir do GitHub, pode ver que temos várias destas chamadas à disposição para o ajudar com a aplicação de exemplo. Todas seguem o padrão de `get(Claims|Token)With<verb>ClearningCache`. Através da utilização de convenções de objetivo C, leem de modo semelhante ao inglês. Por exemplo "Obter um token com parâmetros extra que lhe forneci e limpar a cache" é `getTokenWithExtraParamsClearingCache()`.

Irá escrever "Obter afirmações e um token com a política que lhe forneci e não limpar a cache" ou `getClaimsWithPolicyClearingCache`. Obtém sempre um token de volta da ADAL, pelo que não é necessário especificar "afirmações e um token" no método. No entanto, por vezes, pretende apenas o token sem a sobrecarga de analisar as afirmações, pelo que lhe fornecemos um método sem afirmações denominadas `getTokenWithPolicyClearingCache` na estrutura.

Escreva este código agora:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

A primeira parte deste deverá ter um aspeto familiar.

- Carregue as definições que foram disponibilizadas no `settings.plist` e atribui-as a `data`.
- Configure a `ADAuthenticationError`, que aceita qualquer erro proveniente da ADAL para iOS.
- Crie `authContext`, que configura a sua chamada para a ADAL. Passe a autoridade para a mesma para dar início ao processo.
- Dê `authContext` uma referência ao controlador principal para que possa regressar ao mesmo.
- Converta `redirectURI`, que era uma cadeia em `settings.plist` para o tipo NSURL que a ADAL espera.
- Configure o `correlationId`. Este é um UUID que pode seguir a chamada entre o cliente para o servidor e vice-versa. Esta ação é útil para a depuração.

Em seguida, obtém a chamada real para a ADAL. Este é o local onde a chamada é alterada daquilo que seria de esperar em utilizações anteriores da ADAL para iOS:

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

Pode ver que a chamada é bastante simples.

`scopes`: Os âmbitos que passa ao servidor ao qual pretende fazer o pedido do servidor para um utilizador que inicia sessão. Para a pré-visualização B2C, passa `client_id`. No entanto, espera-se que mude para ler âmbitos no futuro. Planeamos atualizar este documento entretanto.
`additionalScopes`: Estes são âmbitos adicionais que talvez pretenda utilizar para a sua aplicação. Espera-se que estes sejam utilizados no futuro.
`clientId`: O ID da aplicação que recebeu do portal.
`redirectURI`: O redirecionamento onde espera que o token seja publicado novamente.
`identifier`: Uma forma de identificar um utilizador para que possa ver se existe um token utilizável na cache. Isto evita solicitar ao servidor outro token. Isto é efetuado num tipo denominado `ADUserIdentifier`, e pode especificar o que pretende utilizar como um ID. Deve utilizar `username`.
`promptBehavior`: Este é preterido. Deve ser `AD_PROMPT_ALWAYS`.
`extraQueryParameters`: Nada adicional que pretende passar para o servidor no formato com codificação URL.
`policy`: A política que está a invocar. Esta é a parte mais importante destas instruções.

Pode ver na `completionBlock` que passar `ADAuthenticationResult`. Isto não tem as informações de token e perfil (se a chamada foi concluída com êxito).

Utilizando o código acima, pode adquirir um token para a política que fornecer. Em seguida, pode utilizar este token para chamar a API.

### Criar as suas chamadas de tarefa para o servidor

Agora que tem um token, terá de fornecê-lo à API para autorização.

É necessário implementar três métodos:

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` fornece uma matriz que representa as tarefas no seu servidor. `addTask` e `deleteTask` executar as ações posteriores e devolver `true` ou `false` se forem bem sucedidas.

Escrever `getTaskList` primeiro:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

Um debate do código de tarefas está fora do âmbito destas instruções. Mas poderá ter reparado algo interessante: um `craftRequest` método que usa o URL de tarefas. Este método é o que utiliza para criar o pedido para o servidor utilizando o token de acesso que recebeu. Tome nota do mesmo.

Adicione o seguinte código ao ficheiro `samplesWebAPIConnector.m`:

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Esta ação pega num identificador de recurso uniform web (URI), adiciona o token ao mesmo utilizando o `Bearer` cabeçalho de HTTP e, em seguida, devolve-o a si. Pode ligar a API `getTokenClearingCache`. Isto pode parecer estranho, mas basta utilizar esta chamada para obter um token da cache e certificar-se de que ainda é válida. (As chamadas `getToken` fazem isto por si, solicitando à ADAL.) Irá utilizar este código em cada chamada. Em seguida, efetue os métodos de tarefa adicionais.

Escrever `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Este acompanha o mesmo padrão, mas também introduz o método final que precisa de implementar: `convertTaskToDictionary`. Esta ação pega na sua matriz e faz dela um objeto de dicionário. Este objeto é mais facilmente mutado para os parâmetros de consulta que tem de passar para o servidor. O código é simples:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Em seguida, escreva `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Adicionar fim de sessão à aplicação

A última coisa a fazer é implementar o fim de sessão para a sua aplicação. Isto é simples. No ficheiro `sampleWebApiConnector.m`:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## Execute a aplicação de exemplo

Por último, crie e execute a aplicação no Xcode. Inscreva-se ou inicie sessão na aplicação e crie tarefas para um utilizador com sessão iniciada. Termine a sessão e inicie sessão novamente como um utilizador diferente e crie tarefas para esse utilizador.

Tenha em atenção que as tarefas são armazenadas por utilizador na API, porque a API extrai a identidade do utilizador a partir do token de acesso que recebe.

Para sua referência, o exemplo completo [é fornecido como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). Também pode clonar a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Passos seguintes

Agora pode aceder a tópicos de B2C mais avançados. Pode experimentar:

[Ligar uma API Web do Node.js a partir de uma aplicação Web do Node.js]()

[Personalizar o UX para uma aplicação B2C]()



<!--HONumber=Jun16_HO2-->


