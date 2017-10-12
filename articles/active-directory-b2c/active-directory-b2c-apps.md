---
title: "Tipos de Aplicações - Azure AD B2C | Microsoft Docs"
description: "Os tipos de aplicações que pode criar no Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: dastrock
ms.openlocfilehash: 51001feb17ae99d3bd391a9f980d514e07f97099
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C: tipos de aplicações
O Azure Active Directory (Azure AD) B2C suporta a autenticação de uma variedade de arquiteturas de aplicações modernas. Todos elas baseiam-se nos protocolos padrão da indústria [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID Connect](active-directory-b2c-reference-protocols.md). Este documento descreve brevemente os tipos de aplicações que podem ser criados, independentemente do idioma ou plataforma preferida. Também ajuda-o a compreender os cenários de alto nível antes de [começar a construir aplicações](active-directory-b2c-overview.md#get-started).

## <a name="the-basics"></a>Noções básicas
Todas as aplicações que utilizam o Azure AD B2C devem ser registadas no seu [diretório do B2C](active-directory-b2c-get-started.md) através do [Portal do Azure](https://portal.azure.com/). O processo de registo de aplicação recolhe e atribui alguns valores à sua aplicação:

* Uma **ID de Aplicação** que identifica de modo exclusivo a aplicação.
* Uma **URI de Redirecionamento** que pode ser utilizada para direcionar as respostas de volta à aplicação.
* Quaisquer outros valores específicos do cenário. Para obter mais detalhes, saiba como [registar uma aplicação](active-directory-b2c-app-registration.md).

Depois de registar a aplicação, esta comunica com o Azure AD enviando pedidos ao ponto final v2.0 do Azure AD:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Cada pedido enviado para o Azure AD B2C especifica uma **política**. Uma política controla o comportamento do Azure AD. Também pode utilizar estes pontos finais para criar um conjunto altamente personalizável de experiências de utilizador. Políticas comuns incluem inscrição, início de sessão e edição de perfil. Se não estiver familiarizado com as políticas, deverá ler sobre a [estrutura de política extensível](active-directory-b2c-reference-policies.md) do Azure AD B2C antes de continuar.

A interação de cada aplicação com um ponto final de v2.0 segue um padrão semelhante de alto nível:

1. A aplicação direciona o utilizador para o ponto final v2.0 para executar uma [política](active-directory-b2c-reference-policies.md).
2. O utilizador conclui a política de acordo com a sua definição.
3. A aplicação recebe algum tipo de token de segurança do ponto final v2.0.
4. A aplicação utiliza o token de segurança para aceder a informações protegidas ou a um recurso protegido.
5. O servidor de recurso valida o token de segurança para verificar que o acesso pode ser concedido.
6. A aplicação atualiza periodicamente o token de segurança.

<!-- TODO: Need a page for libraries to link to -->
Estes passos podem diferir ligeiramente dependendo do tipo de aplicação que está a criar. As bibliotecas de open source também podem ser uma ajuda.

## <a name="web-apps"></a>Web Apps
Para as Web Apps (incluindo .NET, PHP, Java, Ruby, Python e Node.js) que estão alojadas num servidor e são acedidas através de um navegador, o Azure AD B2C suporta o [OpenID Connect](active-directory-b2c-reference-protocols.md) para todas as experiências de utilizadores. Isto inclui a gestão de início de sessão, inscrição e perfil. Na implementação do OpenID Connect do Azure AD B2C, a aplicação web inicia estes experiências do utilizador através da emissão de pedidos de autenticação para o Azure AD. O resultado do pedido é um `id_token`. Este token de segurança representa a identidade do utilizador. Também fornece informações sobre o utilizador na forma de afirmações:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Saiba mais sobre os tipos de tokens e afirmações disponíveis para uma aplicação na [referência de token do B2C](active-directory-b2c-reference-tokens.md).

Na aplicação web, cada execução de uma [política](active-directory-b2c-reference-policies.md) segue por estes passos de alto nível:

![Imagem de pistas de diagrama de aplicação Web](./media/active-directory-b2c-apps/webapp.png)

A validação do `id_token` utilizando uma chave pública de assinatura que é recebida do Azure AD é suficiente para verificar a identidade do utilizador. Isto também define um cookie de sessão que pode ser utilizado para identificar o utilizador nos pedidos subsequentes da página.

Para ver este cenário na ação, experimente um dos exemplos de início de sessão do código de aplicação web na nossa [Secção de introdução](active-directory-b2c-overview.md#get-started).

Além de facilitar o início simples de sessão, uma aplicação de servidor web poderá ter de aceder a um serviço web de back-end. Neste caso, a aplicação web pode efetuar um [fluxo de OpenID Connect](active-directory-b2c-reference-oidc.md) ligeiramente diferente e adquirir tokens utilizando códigos de autorização e tokens de atualização. Este cenário é descrito na seguinte [secção APIs da Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>APIs da Web
Pode utilizar o Azure AD B2C para proteger serviços da web, tais como a API Web RESTful da sua aplicação. As APIs Web podem utilizar o OAuth 2.0 para proteger os seus dados, ao autenticar pedidos HTTP recebidos utilizando tokens. O autor da chamada de uma API web acrescenta um token no cabeçalho de autorização de um pedido HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API web pode utilizar o token para verificar a identidade do autor da chamada da API e extrair informações sobre o autor da chamada com base nas afirmações codificadas no token. Saiba mais sobre os tipos de tokens e afirmações disponíveis para uma aplicação na [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> Atualmente, o Azure AD B2C suporta apenas APIs Web acedidas pelos próprios clientes conhecidos. Por exemplo, a sua aplicação completa pode incluir uma aplicação iOS, uma aplicação do Android e uma API web de back-end. Esta arquitetura é totalmente suportada. Atualmente não há suporte para permitir que um cliente de parceiro, tal como outra aplicação do iOS aceda à mesma API web. Todos os componentes da sua aplicação completa têm de partilhar uma ID de aplicação única.
>
>

Uma API web pode receber tokens de vários tipos de clientes, incluindo Web Apps, aplicações móveis e de ambiente de trabalho, aplicações de página única, daemons do lado do servidor e outras APIs web. Eis um exemplo de fluxo completo de uma aplicação web que chama uma API web:

![Imagem de pistas de diagrama de aplicação Web da API Web](./media/active-directory-b2c-apps/webapi.png)

Para obter mais informações sobre códigos de autorização, tokens de atualização e os passos para obter os tokens, leia sobre o [protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para saber como proteger uma API web utilizando o Azure AD B2C, consulte os tutoriais sobre web API na nossa [secção Introdução](active-directory-b2c-overview.md#get-started).

## <a name="mobile-and-native-apps"></a>Aplicações móveis e nativas
As aplicações instaladas em dispositivos, tais como aplicações móveis e de ambiente de trabalho, geralmente necessitam de aceder a serviços de back-end ou APIs web em nome dos utilizadores. Pode adicionar experiências de gestão de identidade personalizada às suas aplicações nativas e chamar com segurança serviços de back-end utilizando o Azure AD B2C e o [fluxo de código de autorização do OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

Neste fluxo, a aplicação executa [políticas](active-directory-b2c-reference-policies.md) e recebe um `authorization_code` do Azure AD depois do utilizador concluir a política. O `authorization_code` representa a permissão da aplicação para chamar serviços de back-end em nome do utilizador que tem atualmente sessão iniciada. Depois, a aplicação pode trocar o `authorization_code` em segundo plano por um `id_token` e um `refresh_token`.  A aplicação pode utilizar o `id_token` para autenticar uma API de web de back-end em pedidos de HTTP. Também pode utilizar o `refresh_token` para obter um novo `id_token` quando o antigo expira.

> [!NOTE]
> Atualmente, o Azure AD B2C suporta apenas tokens utilizados para aceder a um serviço Web de back-end da aplicação. Por exemplo, a sua aplicação completa pode incluir uma aplicação iOS, uma aplicação do Android e uma API web de back-end. Esta arquitetura é totalmente suportada. Atualmente não há suporte para permitir que a aplicação iOS aceda a uma API web do parceiro através da utilização de tokens de acesso de OAuth 2.0. Todos os componentes da sua aplicação completa têm de partilhar uma ID de aplicação única.
>
>

![Imagem de pistas de diagrama de aplicação nativa](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitações atuais
O Azure AD B2C não suporta atualmente os seguintes tipos de aplicações, mas estão no mapa. 

### <a name="daemonsserver-side-apps"></a>Aplicações daemons /do lado do servidor
As aplicações que contêm processos de execução longa ou que não funcionam sem a presença de um utilizador também necessitam de uma maneira para aceder a recursos protegidos, como as APIs web. Estas aplicações podem autenticar e obter os tokens utilizando a identidade da aplicação (em vez da identidade delegada de um utilizador) e utilizando o fluxo de credenciais do cliente OAuth 2.0.

Este fluxo não é atualmente suportado pelo Azure AD B2C. Estas aplicações podem obter tokens apenas após a ocorrência de um fluxo de utilizador interativo.

### <a name="web-api-chains-on-behalf-of-flow"></a>Cadeias de API Web (fluxo em-nome-de)
Muitas arquiteturas incluem uma API web que precisa chamar outra API web a jusante, estando ambas protegidas pelo Azure AD B2C. Este cenário é comum em clientes nativos que têm uma back-end de API Web. Isto chama então um serviço online da Microsoft, como o Azure AD Graph API.

Este cenário de API web em cadeia pode ser suportado utilizando a concessão de credencial de portador do OAuth 2.0 JWT, também conhecido como fluxo em-nome-de.  No entanto, o fluxo em nome de não está atualmente implementado no Azure AD B2C.
