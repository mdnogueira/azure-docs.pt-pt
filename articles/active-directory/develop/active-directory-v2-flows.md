---
title: "Tipos de aplicação para o ponto de final de v 2.0 do Azure Active Directory | Microsoft Docs"
description: "Os tipos de aplicações e os cenários suportados pelo ponto final v 2.0 do Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 9d59e7f0e8f326c40be86e199d7712f6c565cc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Tipos de aplicação para o ponto de final de v 2.0 do Azure Active Directory
O ponto de final de v 2.0 do Azure Active Directory (Azure AD) suporta a autenticação para uma variedade de arquiteturas de aplicações modernas, todos eles com base em protocolos de norma da indústria [OAuth 2.0 ou o OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicações que podem ser criados utilizando v 2.0 do Azure AD, independentemente do seu idioma preferencial ou plataforma. As informações neste artigo foi concebidas para ajudar a compreender os cenários de alto nível antes de [começar a trabalhar com o código](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> O ponto final v 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades. Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="the-basics"></a>Noções básicas
Tem de registar cada aplicação que utiliza o ponto final v 2.0 no [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com). O processo de registo de aplicação recolhe e atribui estes valores para a sua aplicação:

* Um **ID da aplicação** que identificam de forma a aplicação
* A **URI de redirecionamento** que pode utilizar para direcionar as respostas de volta para a sua aplicação
* Alguns outros valores específicos do cenário

Para obter detalhes, saiba como [registar uma aplicação](active-directory-v2-app-registration.md).

Depois de registar a aplicação, a aplicação comunica com o Azure AD enviando pedidos ao ponto final v 2.0 do Azure AD. Fornecemos open source estruturas e bibliotecas que processam os detalhes destes pedidos. Também tem a opção para implementar a lógica de autenticação por si através da criação de pedidos com estes pontos finais:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Web Apps
Para as web apps (.NET, PHP, Java, Ruby, Python, nó) que o utilizador acede através de um browser, pode utilizar [OpenID Connect](active-directory-v2-protocols.md) para o início de sessão do utilizador. No OpenID Connect, a aplicação web recebe um token de ID. Um token de ID é um token de segurança que verifica a identidade do utilizador e fornece informações sobre o utilizador no formato de afirmações:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Pode saber sobre todos os tipos de tokens e afirmações disponíveis para uma aplicação no [v 2.0 tokens referência](active-directory-v2-tokens.md).

Nas aplicações de servidor web, o fluxo de autenticação de início de sessão utiliza estes passos de alto nível:

![Fluxo de autenticação de aplicação Web](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Pode certificar-se a identidade do utilizador ao validar o token de ID com uma chave pública de assinatura que é recebida do ponto final v 2.0. Um cookie de sessão está definido, que podem ser utilizadas para identificar o utilizador nos pedidos subsequentes da página.

Para ver este cenário na ação, experimente um dos exemplos de início de sessão do código de aplicação web na nossa v 2.0 [introdução](active-directory-appmodel-v2-overview.md#getting-started) secção.

Para além de sessão simple, uma aplicação de servidor web poderá ter de aceder a outro serviço da web, tal como uma API REST. Neste caso, a aplicação de servidor web envolva um fluxo de OpenID Connect e OAuth 2.0 combinada, utilizando o [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols.md). Para obter mais informações sobre este cenário, leia sobre [introdução a web apps e Web APIs](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>APIs da Web
Pode utilizar o ponto final v 2.0 para proteger serviços da web, tais como Web API RESTful da sua aplicação. Em vez de tokens de identidade e cookies de sessão, uma API Web utiliza um token de acesso de OAuth 2.0 para proteger os dados e para autenticar pedidos recebidos. O autor da chamada de uma API Web acrescenta um token de acesso no cabeçalho de autorização de um pedido HTTP, como esta:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web utiliza o token de acesso para verificar a identidade do emissor de API e extrair informações sobre o emissor de afirmações que são codificadas no token de acesso. Para saber mais sobre todos os tipos de tokens e afirmações disponíveis para uma aplicação, consulte o [v 2.0 tokens referência](active-directory-v2-tokens.md).

Uma API Web pode dar aos utilizadores a capacidade para optar ativamente por participar no ou ativamente dados ou funcionalidades específicas da exposição permissões, também conhecido como [âmbitos](active-directory-v2-scopes.md). Para uma aplicação chamada adquirir a permissão para um âmbito, o utilizador terá de consentir ao âmbito durante um fluxo. O ponto final v 2.0 pede ao utilizador permissão do e, em seguida, regista permissões em todos os tokens de acesso que recebe a API Web. A API Web valida tokens de acesso que recebe em cada chamada e efetua verificações de autorização.

Uma API Web pode receber tokens de acesso de todos os tipos de aplicações, incluindo aplicações de servidor web, ambiente de trabalho e aplicações móveis, as aplicações de página única, daemons do lado do servidor e mesmo a outros APIs da Web. O fluxo de alto nível para uma API Web tem o seguinte aspeto:

![Fluxo de autenticação de API Web](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Para saber como proteger uma API Web utilizando tokens de acesso de OAuth2, consulte os exemplos de código Web API na nossa [introdução](active-directory-appmodel-v2-overview.md#getting-started) secção.

Em muitos casos, as APIs web também precisa de efetuar pedidos de saída para outro web a jusante APIs protegidas pelo Azure Active Directory.  Para tal, as APIs web podem tirar partido do Azure AD **no nome de** fluxo, que permite que a API web trocar um token de acesso recebidos para outro token de acesso a ser utilizado em pedidos de saída.  O ponto final v 2.0 em nome de fluxo está descrito em [detalhe aqui](active-directory-v2-protocols-oauth-on-behalf-of.md).

## <a name="mobile-and-native-apps"></a>Aplicações móveis e nativas
Aplicações instaladas em dispositivos, tais como aplicações de ambiente de trabalho e móveis, muitas vezes necessitam de aceder a serviços de back-end ou APIs da Web que armazenam dados e realizar funções em nome de um utilizador. Estas aplicações podem adicionar início de sessão e autorização para serviços de back-end utilizando a [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Neste fluxo, a aplicação recebe um código de autorização do ponto final v 2.0, quando o utilizador inicia sessão. O código de autorização representa a permissão da aplicação para chamar serviços de back-end em nome do utilizador que tenha sessão iniciada. A aplicação pode trocar o código de autorização em segundo plano para um token de acesso de OAuth 2.0 e um token de atualização. A aplicação pode utilizar o token de acesso para autenticar a Web APIs em pedidos de HTTP e utilizar o token de atualização para obter os tokens de acesso novo quando expirarem os tokens de acesso antigos.

![Fluxo de autenticação de aplicação nativa](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Aplicações de página única (JavaScript)
Muitas aplicações modernas têm uma aplicação de página única front-end que é principalmente escrito em JavaScript. Muitas vezes, é escrito através da utilização de uma estrutura como AngularJS, Ember.js ou Durandal.js. Ponto final v 2.0 do Azure AD suporta estas aplicações ao utilizar o [fluxo implícito de OAuth 2.0](active-directory-v2-protocols-implicit.md).

Neste fluxo, a aplicação recebe tokens diretamente a partir de v 2.0 autorizar o ponto final, sem qualquer trocas de servidor para servidor. Todos os lógica de autenticação e sessão demora a processar colocar inteiramente no cliente do JavaScript, sem redirecionamentos de página adicionais.

![Fluxo de autenticação implícita](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Para ver este cenário na ação, experimente um dos exemplos de código de aplicação de página única na nossa [introdução](active-directory-appmodel-v2-overview.md#getting-started) secção.

## <a name="daemons-and-server-side-apps"></a>Aplicações daemons e aplicações do lado do servidor
As aplicações que tenham processos de execução longa ou que não funcionam sem interação do utilizador também precisam de uma forma de aceder a recursos protegidos, como APIs da Web. Estas aplicações podem autenticar e obter os tokens utilizando a identidade da aplicação, em vez de um utilizador delegado identidade, o fluxo de credenciais de cliente OAuth 2.0.

Neste fluxo, a aplicação interage diretamente com o `/token` ponto final para obter os pontos finais:

![Fluxo de autenticação de aplicação do daemon](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Criar uma aplicação de daemon, consulte a documentação de credenciais do cliente no nosso [introdução](active-directory-appmodel-v2-overview.md#getting-started) secção ou tente um [aplicação de exemplo .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
