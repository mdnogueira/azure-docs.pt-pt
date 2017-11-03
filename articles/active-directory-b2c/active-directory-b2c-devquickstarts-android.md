---
title: "O Azure Active Directory B2C: Adquirir um token a utilizar uma aplicação Android | Microsoft Docs"
description: "Este artigo irá mostrar como criar uma aplicação Android que utiliza AppAuth com o Azure Active Directory B2C para gerir identidades de utilizador e autenticar utilizadores."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/06/2017
ms.author: parakhj
ms.openlocfilehash: cd4b8048245be49ea79bcb1b364f2f99c56f8291
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>O Azure AD B2C: Início de sessão utilizando uma aplicação Android

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Isto permite aos programadores tirarem partido de qualquer biblioteca que pretendam integrar aos nossos serviços. Para ajudar os programadores a utilizarem a nossa plataforma com outras bibliotecas, escrevemos algumas instruções como esta para demonstrar como configurar 3rd bibliotecas de terceiros para ligar à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) poderão ligar à plataforma de Identidades da Microsoft.

> [!WARNING]
> A Microsoft não cede correções para 3rd bibliotecas de terceiros e não tiverem efetuado uma revisão desses bibliotecas. Este exemplo está a utilizar uma biblioteca de terceiros 3rd chamada AppAuth foi testado para compatibilidade em cenários básicos com o Azure AD B2C. Problemas e pedidos de funcionalidades devem ser direcionados para o projeto de fonte aberta da biblioteca. Consulte [neste artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) para obter mais informações.  
>
>

Se não estiver familiarizado com o OAuth2 ou o OpenID Connect, este exemplo de configuração pode não fazer muito sentido para si. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, precisa de criar uma aplicação no diretório do B2C. Isto proporciona informações do Azure AD de que necessita para comunicar de forma segura com a sua aplicação. Para criar uma aplicação móvel, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

* Incluir um **Native Client** na aplicação.
* Copiar a **ID da Aplicação** atribuída à aplicação. Irá precisar deste mais tarde.
* Configurar um cliente nativo **URI de redirecionamento** (por exemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Também irá precisar deste mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as políticas

No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém uma experiência de identidade: um combinado início de sessão e inscrição. Tem de criar esta política, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Quando cria a política, certifique-se de que:

* Escolha o **nome a apresentar** como um atributo na política de inscrição.
* Escolher as afirmações de aplicação **Nome a apresentar** e **ID de objeto** em cada política. Também pode escolher outras afirmações.
* Copiar o **Nome** de cada política depois de a criar. Deve ter o prefixo `b2c_1_`.  Precisará do nome da política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as políticas, está pronto para criar a aplicação.

## <a name="download-the-sample-code"></a>Transferir o código de exemplo

Fornecemos um exemplo de trabalho que utiliza AppAuth com o Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Pode transferir o código e executá-la. Pode rapidamente começar a utilizar com a sua própria aplicação utilizando a sua própria configuração do Azure AD B2C ao seguir as instruções no [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

O exemplo é uma modificação de exemplo fornecido pelo [AppAuth](https://openid.github.io/AppAuth-Android/). Visite os respetivos página para saber mais sobre AppAuth e as respetivas funcionalidades.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificar a sua aplicação para utilizar o Azure AD B2C com AppAuth

> [!NOTE]
> AppAuth suporta Android API 16 (Jellybean) e superior. Recomendamos que utilize a API 23 e superior.
>

### <a name="configuration"></a>Configuração

Pode configurar a comunicação com o Azure AD B2C especificando a deteção de URI ou ao especificar o ponto final de autorização e o ponto final de tokens de URI. Em ambos os casos, terá as seguintes informações:

* ID do inquilino (por exemplo, contoso.onmicrosoft.com)
* Nome da política (por exemplo, B2C\_1\_SignUpIn)

Se optar por detetar automaticamente o ponto final de tokens URIs e autorização, terá de obter informações de deteção de URI. A deteção de URI que pode ser gerado ao substituir o inquilino\_ID e a política\_nome no seguinte URL:

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Em seguida, pode adquirir o token de URI do ponto final e autorização e criar um objeto de AuthorizationServiceConfiguration executando o seguinte:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Em vez de utilizar a deteção para obter a autorização e o ponto final de tokens URIs, também pode especificá-los explicitamente, substituindo o inquilino\_ID e a política\_nome no URL do abaixo:

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Execute o seguinte código para criar o objeto de AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorizar

Depois de configurar ou obtenção de uma configuração de serviço de autorização, pode ser construído a um pedido de autorização. Para criar o pedido, terá as seguintes informações:

* ID de cliente (por exemplo, 00000000-0000-0000-0000-000000000000)
* URI de redirecionamento com um esquema personalizado (por exemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Ambos os itens devem ter sido guardados quando era [registar a sua aplicação](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Consulte o [AppAuth guia](https://openid.github.io/AppAuth-Android/) sobre como concluir o processo. Se precisar de para rapidamente começar com uma aplicação de trabalho, consulte [nosso exemplo](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Siga os passos a [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) para introduzir a suas próprias configuração do Azure AD B2C.

Podemos sempre estão abertos para comentários e sugestões! Se tiver dificuldades em causa com este tópico ou tiver recomendações para melhorar este conteúdo, Agradecemos os seus comentários na parte inferior da página. Para pedidos de funcionalidades, adicioná-los ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

