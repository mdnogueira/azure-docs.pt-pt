---
title: "Do Azure Active Directory B2C: Protocolos de autenticação | Microsoft Docs"
description: "Como criar aplicações diretamente utilizando os protocolos que são suportados pelo Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5e407d0a-73a2-4d74-ac81-3aa6c31ddcee
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: 8e7e7bc7633370057f8dc596ad04a3f1d796a7d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# Do Azure AD B2C: Protocolos de autenticação
O Azure Active Directory B2C (Azure AD B2C) fornece identidade como um serviço para as suas aplicações ao suportar dois protocolos padrão de indústria: OpenID Connect e OAuth 2.0. O serviço está em conformidade com as normas, mas qualquer duas implementações destes protocolos podem ter ligeiras diferenças. 

As informações neste guia são útil se escrever o seu código mediante o envio diretamente e processamento de pedidos de HTTP em vez de utilizar uma biblioteca de código aberto. Recomendamos que leia esta página antes de aprofundar os detalhes de cada protocolo específico. Mas se já estiver familiarizado com o Azure AD B2C, pode ir diretamente para [os guias de referência do protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

## Noções básicas
Cada aplicação que utiliza o Azure AD B2C tem de ser registado no diretório do B2C no [portal do Azure](https://portal.azure.com). O processo de registo de aplicação recolhe e atribui alguns valores à sua aplicação:

* Uma **ID de Aplicação** que identifica de modo exclusivo a aplicação.
* A **URI de redirecionamento** ou **identificador do pacote** que pode ser utilizada para direcionar as respostas de volta para a sua aplicação.
* Alguns outros valores específicos de cenário. Para obter mais informações, saiba [como registar a aplicação](active-directory-b2c-app-registration.md).

Depois de registar a sua aplicação, comunica com o Azure Active Directory (Azure AD) enviando pedidos para o ponto final:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Praticamente todos os fluxos de OAuth e o OpenID Connect, quatro partes estão envolvidas do exchange:

![Funções de OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* O **servidor autorização** é o ponto final do Azure AD. Em segurança processa nada relacionados com as informações de utilizador e de acesso. Ele também faz as relações de confiança entre as partes de um fluxo. É responsável por verificar a identidade do utilizador, conceder revogar o acesso a recursos e emitir tokens. É também conhecido como o fornecedor de identidade.

* O **proprietário do recurso** é, geralmente, o utilizador final. É a entidade que tem os dados e tem a capacidade para permitir que terceiros aceder a esse dados ou recursos.

* O **cliente OAuth** é a sua aplicação. For identificado pelo respetivo ID de aplicação. Normalmente, trata-se a entidade que os utilizadores finais interajam com. Também pedidos tokens do servidor de autorização. O proprietário do recurso tem de conceder a permissão de cliente para aceder ao recurso.

* O **servidor recursos** é onde residem a recursos ou dados. Confiança do servidor de autorização para autenticar e autorizar o cliente OAuth em segurança. Também utiliza os tokens de portador acesso para se certificar de que pode ser concedido acesso a um recurso.

## Políticas
Possivelmente, as políticas do Azure AD B2C se as funcionalidades mais importantes do serviço. O Azure AD B2C expande os protocolos padrão de OAuth 2.0 e o OpenID Connect, introduzindo as políticas. Estes cmdlets permitem do Azure AD B2C efetuar muito mais do que simples autenticação e autorização. 

As políticas totalmente descrevem as experiências de identidade do consumidor, incluindo a inscrição, início de sessão e edição de perfis. As políticas podem ser definidas numa IU administrativa. Estes podem ser executados utilizando um parâmetro de consulta especiais em pedidos de autenticação HTTP. 

As políticas não são funcionalidades padrão do OAuth 2.0 e o OpenID Connect, pelo que deve ter o tempo para compreendê-los. Para obter mais informações, consulte o [guia de referência de política do Azure AD B2C](active-directory-b2c-reference-policies.md).

## Tokens
A implementação do Azure AD B2C de OAuth 2.0 e o OpenID Connect faz com que utilize um vasto conjunto de tokens de portador, incluindo os tokens de portador são representados como tokens de web JSON (JWTs). Um token de portador é um token de segurança simples que concede acesso a "portador" para um recurso protegido.

O portador é que podem apresentar o token de terceiros. Azure AD deve primeiro autenticar uma parte antes de pode receber um token de portador. Mas, se não são tidas em conta os passos necessários para proteger o token na transmissão e o armazenamento, pode ser intercetado e utilizado por uma entidade indesejada.

Alguns tokens de segurança tenham mecanismos incorporados que impedem que partes não autorizadas a utilizá-los, mas os tokens de portador tem este mecanismo. Estes têm de ser transportados num canal seguro, como de segurança de camada de transporte (HTTPS). 

Se um token de portador é transmitido fora de um canal seguro, uma parte maliciosa pode utilizar um ataque man-in-the-middle para adquirir o token e utilizá-la para obter acesso não autorizado a um recurso protegido. Os princípios de segurança mesmo se aplica quando os tokens de portador são armazenados ou colocada em cache para utilização posterior. Certifique-se sempre de que a aplicação transmite e armazena os tokens de portador de forma segura.

Para considerações de segurança de token de portador adicionais, consulte [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Obter mais informações sobre os diferentes tipos de tokens que são utilizados no Azure AD B2C estão disponíveis no [a referência de token do Azure AD](active-directory-b2c-reference-tokens.md).

## Protocolos
Quando estiver pronto para rever alguns pedidos de exemplo, pode começar com um dos tutoriais seguintes. Cada corresponde a um cenário de autenticação específica. Se precisar de ajuda para determinar que fluxo é adequado para si, consulte [os tipos de aplicações, pode criar utilizando o Azure AD B2C](active-directory-b2c-apps.md).

* [Criar aplicações móveis e nativas utilizando OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Criar aplicações web utilizando o OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Criar aplicações de página única utilizando o fluxo implícito de OAuth 2.0](active-directory-b2c-reference-spa.md)

