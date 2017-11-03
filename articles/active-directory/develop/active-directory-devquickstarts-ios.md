---
title: "Integrar o Azure AD para uma aplicação iOS | Microsoft Docs"
description: "Como criar uma aplicação iOS que se integra com o Azure AD para início de sessão e chamadas do Azure AD protegido APIs através da utilização de OAuth."
services: active-directory
documentationcenter: ios
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 57f465df99ac234466459b8031f61805d8334b59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrar o Azure AD para uma aplicação iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

> [!TIP]
> Experimentar a pré-visualização do nosso novo [portal do programador](https://identity.microsoft.com/Docs/iOS) que ajuda-o a começar a trabalhar com o Azure Active Directory em apenas alguns minutos!  O portal do programador orienta-o ao longo do processo de registar uma aplicação e a integração do Azure AD para o seu código.  Quando tiver terminado, terá uma aplicação simples que pode autenticar utilizadores no seu inquilino e um back-end que podem aceitar tokens e efetuar a validação. 
> 
> 

Azure Active Directory (Azure AD) fornece a biblioteca de autenticação do Active Directory ou a ADAL para clientes de iOS que precisam de aceder aos recursos protegidos. ADAL simplifica o processo que utiliza a sua aplicação para obter os tokens de acesso. Para demonstrar como é fácil, neste artigo iremos criar uma aplicação de lista de tarefas do objetivo C que:

* Obtém acesso tokens para chamar a API do Azure AD Graph, utilizando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Procura um diretório para os utilizadores com um alias especificado.

Para criar a aplicação de trabalho concluída, tem de:

1. Registe a sua aplicação com o Azure AD.
2. Instalar e configurar a ADAL.
3. Utilize a ADAL para obter os tokens do Azure AD.

Para começar, [transferir a estrutura de aplicação](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [transferir o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Também precisa de um inquilino do Azure AD, onde pode criar utilizadores e registar uma aplicação. Se ainda não tiver um inquilino, [saber como obter um](active-directory-howto-tenant.md).


> [!TIP]
> Experimentar a pré-visualização do nosso novo [portal do programador](https://identity.microsoft.com/Docs/iOS) que ajuda-o a começar a trabalhar com o Azure AD em apenas alguns minutos. O portal do programador orienta-o ao longo do processo de registar uma aplicação e a integração do Azure AD para o seu código. Quando tiver terminado, terá de uma aplicação simples que pode autenticar os utilizadores no seu inquilino e um back end que pode aceitar tokens e efetuar a validação. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Determinar que o redirecionamento é URI para iOS
Para iniciar em segurança as aplicações em determinados cenários SSO, tem de criar um *URI de redirecionamento* num formato específico. Um URI de redirecionamento é utilizado para garantir que os tokens de voltar para a aplicação correta que pediu para os mesmos.


O formato de iOS para um redirecionamento URI é:

```
<app-scheme>://<bundle-id>
```

* **esquema de aplicação** -Isto está registado no projeto XCode. É como outras aplicações podem chamar-lhe. Pode encontrar isto em Info. plist -> tipos de URL -> URL de identificador. Deve criar um, se ainda não tiver uma ou mais configurado.
* **id do pacote** -este é o identificador de pacote localizado em "identity" anular as definições do projeto XCode.

Um exemplo para este código de início rápido: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Registar a aplicação de DirectorySearcher
Para configurar a sua aplicação para obter os tokens, terá primeiro de registá-lo no seu inquilino do Azure AD e conceder permissão para aceder à API do Azure AD Graph:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. Sob o **diretório** lista, escolha o inquilino do Active Directory onde é necessário registar a sua aplicação.
3. Clique em **mais serviços** no painel de navegação mais à esquerda e, em seguida, selecione **do Azure Active Directory**.
4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.
5. Siga as instruções para criar um novo **aplicação cliente nativa**.
  * O **nome** da aplicação descreve a aplicação aos utilizadores finais.
  * O **Uri de redirecionamento** é uma combinação de esquema e a cadeia que utiliza o Azure AD para devolver respostas token.  Introduza um valor que é específica para a aplicação e é baseado nas informações de URI de redirecionamento anteriores.
6. Após concluir o registo, o Azure AD atribui a aplicação um ID de aplicação único.  Irá precisar deste valor nas secções seguintes, por isso, copie-o de separador da aplicação.
7. Do **definições** página, selecione **permissões obrigatórias** e, em seguida, selecione **adicionar**. Selecione **Microsoft Graph** como a API e, em seguida, adicione o **ler dados de diretório** permissão em **permissões delegadas**.  Esta ação configura a aplicação para consultar a AD Graph API do Azure para utilizadores.

## <a name="3-install-and-configure-adal"></a>3. Instalar e configurar a ADAL
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escrever o seu código relacionadas com identidade.  Da ADAL comunicar com o Azure AD, terá de fornecê-lo com algumas informações sobre o registo da aplicação.

1. Comece por adicionar ADAL para o projeto de DirectorySearcher utilizando o CocoaPods.

    ```
    $ vi Podfile
    ```
2. Adicione o seguinte a este podfile:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. Agora, carregue o podfile utilizando o CocoaPods. Este passo cria uma área de trabalho XCode nova que carrega.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. No projeto de início rápido, abra o ficheiro plist `settings.plist`.  Substitua os valores dos elementos na secção para refletir os valores que introduziu no portal do Azure. O código referencia estes valores, sempre que utilizar a ADAL.
  * O `tenant` é o domínio de inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com.
  * O `clientId` é o ID de cliente da aplicação que copiou do portal.
  * O `redirectUri` é o URL de redirecionamento registado no portal.

## <a name="4----use-adal-to-get-tokens-from-azure-ad"></a>4.    Utilizar a ADAL para obter os tokens do Azure AD
O princípio básico atrás ADAL é que sempre que a aplicação tem um token de acesso, basta chama um completionBlock `+(void) getToken : `, e o resto efetua a ADAL.  

1. No `QuickStart` projeto, abra `GraphAPICaller.m` e localize o `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` comentário perto da parte superior.  Este é onde passa ADAL as coordenadas através de um CompletionBlock, para comunicar com o Azure AD e informá-lo como colocar em cache de tokens.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Agora, é necessário utilizar este token para procurar utilizadores no gráfico. Localizar o `// TODO: implement SearchUsersList` comentário. Este método envia um pedido GET para a Azure AD Graph API a consulta para os utilizadores cujo UPN começa com o termo de pesquisa especificada.  Para consultar a AD Graph API do Azure, terá de incluir um access_token no `Authorization` cabeçalho do pedido. Este é onde ADAL é apresentada no.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
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

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

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
         }];

    }

    ```


3. Quando a aplicação solicita um token ao chamar `getToken(...)`, ADAL tenta devolver um token sem pedir ao utilizador as credenciais.  Se ADAL determina que o utilizador tem de iniciar sessão para obter um token, irá apresentar uma caixa de diálogo de início de sessão, recolher as credenciais do utilizador e, em seguida, devolve um token após a autenticação com êxito.  Se a ADAL não é capaz de devolver um token por qualquer motivo, emite um `AdalException`.

> [!Note] 
> O `AuthenticationResult` objeto contém uma `tokenCacheStoreItem` objeto que pode ser utilizado para recolher as informações que poderá ter a sua aplicação. No início rápido, `tokenCacheStoreItem` é utilizado para determinar se a autenticação já foi efetuada.
>
>

## <a name="5-build-and-run-the-application"></a>5. Compilar e executar a aplicação
Parabéns! Tem agora uma aplicação do iOS de trabalho que pode autenticar os utilizadores, em segurança chamar APIs da Web através da utilização de OAuth 2.0 e obter informações básicas sobre o utilizador.  Se ainda não o fez, agora é o tempo para preencher o seu inquilino com alguns utilizadores.  Iniciar a sua aplicação de início rápido e, em seguida, inicie sessão com uma esses utilizadores.  Procurar outros utilizadores com base no respetivo UPN.  Feche a aplicação e, em seguida, inicie-o novamente.  Tenha em atenção que a sessão do utilizador permanece intacta.

ADAL torna mais fácil incorporar todas estas funcionalidades de identidade comuns na sua aplicação.  -Encarrega-se de todo o trabalho de dirty para si, como a gestão da cache, suporte de protocolo de OAuth, que apresenta uma IU para iniciar sessão, o utilizador e a atualização expirou tokens.  É tudo o que realmente precisa de saber uma única chamada API, `getToken`.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido no [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="next-steps"></a>Passos seguintes
Agora pode passar para cenários adicionais.  Poderá querer experimentar:

* [Proteger uma API com o Azure AD da Web de Node.JS](active-directory-devquickstarts-webapi-nodejs.md)
* Saiba [como ativar a SSO em várias aplicações no iOS utilizam a ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

