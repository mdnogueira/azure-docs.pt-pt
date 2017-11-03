---
title: "Adquirir um token a utilizar uma aplicação iOS - Azure AD B2C | Microsoft Docs"
description: "Este artigo irá mostrar como criar uma aplicação iOS que utiliza AppAuth com o Azure Active Directory B2C para gerir identidades de utilizador e autenticar utilizadores."
services: active-directory-b2c
documentationcenter: ios
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objectivec
ms.topic: article
ms.date: 03/07/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: ebec5d910b8987dcc8155cd4ead00f87d219941c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>O Azure AD B2C: Início de sessão utilizando uma aplicação iOS

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Utilizar um protocolo padrão aberto oferece mais opções de programador quando selecionar uma biblioteca para integrar aos nossos serviços. Fornecemos esta explicação passo a passo e outros utilizadores como para ajudar os programadores a escrita de aplicações que ligam à plataforma do Microsoft Identity. A maioria das bibliotecas que implementam [a especificação de especificação RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) são capazes de estabelecer ligação com a plataforma do Microsoft Identity.

> [!WARNING]
> A Microsoft não cede corrige para bibliotecas de terceiros e não tiver efetuado uma revisão desses bibliotecas. Este exemplo está a utilizar uma biblioteca de terceiros chamada AppAuth foi testado para compatibilidade em cenários básicos com o Azure AD B2C. Problemas e pedidos de funcionalidades devem ser direcionados para o projeto de fonte aberta da biblioteca. Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Se estiver familiarizado com o OAuth2 ou o OpenID Connect, muito este exemplo de configuração poderá não fazer muito sentido para si. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C
Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos e muito mais. Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação
Em seguida, precisa de criar uma aplicação no diretório do B2C. O registo de aplicação fornece informações do Azure AD que necessita para comunicar de forma segura com a sua aplicação. Para criar uma aplicação móvel, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

* Incluir um **cliente nativo** na aplicação.
* Copiar a **ID da Aplicação** atribuída à aplicação. Este GUID é necessário mais tarde.
* Configurar um **URI de redirecionamento** com um esquema personalizado (por exemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). É necessário este URI mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as políticas
No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém uma experiência de identidade: um combinado início de sessão e inscrição. Crie esta política, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Quando cria a política, certifique-se de que:

* Em **atributos de inscrição**, selecione o atributo **nome a apresentar**.  Pode selecionar, bem como outros atributos.
* Em **afirmações de aplicação**, selecione as afirmações **nome a apresentar** e **ID de objeto do utilizador**. Pode selecionar outras afirmações.
* Copiar o **Nome** de cada política depois de a criar. O nome da sua política é o prefixo `b2c_1_` quando guardar a política.  Terá do nome da política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as políticas, está pronto para criar a aplicação.

## <a name="download-the-sample-code"></a>Transferir o código de exemplo
Fornecemos um exemplo de trabalho que utiliza AppAuth com o Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Pode transferir o código e executá-la. Para utilizar o seu inquilino do Azure AD B2C, siga as instruções de [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Este exemplo foi criado ao seguir as instruções de Leia-me pelo [projeto iOS AppAuth no GitHub](https://github.com/openid/AppAuth-iOS). Para obter mais detalhes sobre como funcionam o exemplo e biblioteca, referência AppAuth ficheiro Leia-me no GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificar a sua aplicação para utilizar o Azure AD B2C com AppAuth

> [!NOTE]
> AppAuth suporta iOS 7 e posterior.  No entanto, para suportar sociais inícios de sessão no Google, SFSafariViewController é necessário que requer o iOS 9 ou superior.
>

### <a name="configuration"></a>Configuração

Pode configurar a comunicação com o Azure AD B2C, especificando o ponto final de autorização e o ponto final de tokens de URI.  Para gerar estes URIs, terá as seguintes informações:
* ID do inquilino (por exemplo, contoso.onmicrosoft.com)
* Nome da política (por exemplo, B2C\_1\_SignUpIn)

Ponto final do token URI que pode ser gerado ao substituir o inquilino\_ID e a política\_nome no seguinte URL:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

O ponto final de autorização URI que pode ser gerado ao substituir o inquilino\_ID e a política\_nome no seguinte URL:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Execute o seguinte código para criar o objeto de AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizar

Depois de configurar ou obtenção de uma configuração de serviço de autorização, pode ser construído a um pedido de autorização. Para criar o pedido, terá as seguintes informações:  
* ID de cliente (por exemplo, 00000000-0000-0000-0000-000000000000)
* URI de redirecionamento com um esquema personalizado (por exemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Ambos os itens devem ter sido guardados quando era [registar a sua aplicação](#create-an-application).

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Para configurar a sua aplicação para processar o redirecionamento para o URI com o esquema personalizado, terá de atualizar a lista de 'Esquemas de URL' no seu ficheiro info. plist:
* Abra o ficheiro info. plist.
* Coloque o cursor sobre uma linha, como 'Código de tipo de SO do pacote' e clique em de \+ símbolo.
* Mudar o nome da nova linha 'URL types'.
* Clique na seta para a esquerda da vírgula 'URL types' para abrir a árvore.
* Clique na seta para a esquerda da vírgula ' Item 0' para abrir a árvore.
* Mudar o nome do primeiro item por baixo do Item 0 para esquemas de URL.
* Clique na seta para a esquerda da vírgula 'Esquemas de URL' para abrir a árvore.
* Na coluna 'Value', é um campo em branco à esquerda da vírgula 'Item 0"por baixo de esquemas de URL.  Defina o valor para o esquema exclusivo da aplicação.  O valor tem de corresponder ao esquema utilizado no redirectURL ao criar o objeto de OIDAuthorizationRequest.  No nosso exemplo, utilizámos o esquema 'com.onmicrosoft.fabrikamb2c.exampleapp'.

Consulte o [AppAuth guia](https://openid.github.io/AppAuth-iOS/) sobre como concluir o processo. Se precisar de para rapidamente começar com uma aplicação de trabalho, consulte [nosso exemplo](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Siga os passos a [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) para introduzir a suas próprias configuração do Azure AD B2C.

Podemos sempre estão abertos para comentários e sugestões! Se tiver dificuldades em causa com este tópico ou tiver recomendações para melhorar este conteúdo, Agradecemos os seus comentários na parte inferior da página. Para pedidos de funcionalidades, adicioná-los ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
