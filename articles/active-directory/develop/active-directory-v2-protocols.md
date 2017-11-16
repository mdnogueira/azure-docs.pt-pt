---
title: "Saiba mais sobre os protocolos de autorização suportados pelo Azure AD v 2.0 | Microsoft Docs"
description: Um guia para protocolos suportados pelo ponto final v 2.0 do Azure AD.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3750f975600575349e5ea9de249cf4521636fd2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>v 2.0 protocolos - OAuth 2.0 & OpenID Connect
O ponto final v 2.0 pode utilizar o Azure AD para identidade-como-um-serviço com protocolos padrão da indústria, OpenID Connect e OAuth 2.0.  Enquanto o serviço está em conformidade com as normas, pode ser ligeiras diferenças entre as duas implementações destes protocolos.  As informações aqui será útil se optar por escrever código enviando diretamente & processar pedidos de HTTP ou utilize um 3rd terceiros abrir biblioteca de origem, em vez de utilizar uma das nossas bibliotecas de open source para.
<!-- TODO: Need link to libraries above -->

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
>
>

## <a name="the-basics"></a>As noções básicas
Praticamente todos os fluxos de OAuth & OpenID Connect, existem quatro partes envolvidos do exchange:

![Funções de OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* O **autorização servidor** é o ponto final v 2.0.  É responsável por assegurar que a identidade do utilizador, conceder revogar o acesso a recursos e emitir tokens.  É também conhecido como o fornecedor de identidade - -lo em segurança processa qualquer coisa a fazer com as informações do utilizador, o acesso e as relações de confiança entre as partes de um fluxo.
* O **proprietário do recurso** é, geralmente, o utilizador final.  É a entidade que tem os dados e de que tem a capacidade para permitir que terceiros aceder a esse dados ou recursos.
* O **cliente OAuth** é a sua aplicação, identificada por um ID de aplicação.  Normalmente, é a entidade que o utilizador final interage com e que solicita tokens a partir do servidor de autorização.  O cliente tem de possuir permissão para aceder ao recurso pelo proprietário do recurso.
* O **servidor recursos** é onde residem a recursos ou dados.  Confianças de autorização para autenticar e autorizar o cliente OAuth em segurança e utiliza access_tokens de portador para se certificar de que pode ser concedido acesso a um recurso.

## <a name="app-registration"></a>Registo de aplicação
Cada aplicação que utiliza o ponto final v 2.0 tem de ser registado no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) antes de pode interagir com OAuth ou o OpenID Connect.  O processo de registo de aplicação irão recolher & atribuir alguns valores para a sua aplicação:

* Um **Id da aplicação** que identificam de forma a aplicação
* A **URI de redirecionamento** ou **identificador de pacote** que pode ser utilizada para direcionar as respostas de volta para a sua aplicação
* Alguns outros valores específicos de cenário.

Para obter mais detalhes, saiba como [registar uma aplicação](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Pontos Finais
Depois de registado, a aplicação comunica com o Azure AD enviando pedidos para o ponto final v 2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Onde o `{tenant}` pode efetuar uma das quatro valores diferentes:

| Valor | Descrição |
| --- | --- |
| `common` |Permite que os utilizadores com contas Microsoft pessoais e de trabalho/escola contas do Azure Active Directory para iniciar sessão na aplicação. |
| `organizations` |Permite que apenas os utilizadores com contas de trabalho/escola do Azure Active Directory para iniciar sessão na aplicação. |
| `consumers` |Permite que apenas os utilizadores com contas Microsoft pessoais (MSA) para iniciar sessão na aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` |Permite que apenas os utilizadores com contas de trabalho/profissional de um determinado inquilino do Azure Active Directory para iniciar sessão na aplicação.  O nome de domínio amigável dos inquilino do Azure AD ou identificador de guid do inquilino pode ser utilizado. |

Para obter mais informações sobre como interagir com estes pontos finais, escolha um tipo de aplicação específica abaixo.

## <a name="tokens"></a>Tokens
A implementação de v 2.0 de OAuth 2.0 e o OpenID Connect tornar utilizar muita tokens de portador, incluindo os tokens de portador representados como JWTs. Um token de portador é um token de segurança simples que concede acesso a "portador" para um recurso protegido. Este sentido, "portador" é que podem apresentar o token de terceiros. Embora um terceiros devem primeiro autenticar com o Azure AD para receber o token de portador, se não são tidas nos passos necessários para proteger o token na transmissão e o armazenamento, podem ser intercetado e utilizado por uma entidade indesejada. Enquanto algumas tokens de segurança tem um mecanismo incorporado para impedir que as partes não autorizadas a utilizá-los, os tokens de portador não têm este mecanismo e tem de ser transportados num canal seguro, como de segurança de camada de transporte (HTTPS). Se um token de portador é transmitido na limpar, um ataques man-in média ataque pode ser utilizado por uma entidade maliciosa para adquirir o token e utilizá-lo para um acesso não autorizado a um recurso protegido. Os princípios de segurança mesmo se aplica quando armazenar ou colocação em cache os tokens de portador para utilização posterior. Certifique-se sempre de que a aplicação transmite e armazena os tokens de portador de forma segura. Para mais considerações de segurança em tokens de portador, consulte [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Obter mais detalhes dos diferentes tipos de tokens utilizados no ponto final v 2.0 está disponível no [a referência de token de ponto final v 2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protocolos
Se estiver pronto para ver alguns pedidos de exemplo, começar a utilizar um do abaixo tutoriais.  Cada um deles corresponde a um cenário de autenticação específica.  Se precisar de ajuda para determinar qual é o fluxo correto para si, consulte [os tipos de aplicações, pode criar com a v 2.0](active-directory-v2-flows.md).

* [Criar Mobile e aplicação nativa com OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
* [Criar Web aplicações com abrir ID Connect](active-directory-v2-protocols-oidc.md)
* [Criar aplicações de página única com o fluxo implícito de OAuth 2.0](active-directory-v2-protocols-implicit.md)
* [Aplicações Daemons de compilação ou lado os processos do servidor com as credenciais de cliente OAuth 2.0 fluxo](active-directory-v2-protocols-oauth-client-creds.md)
* [Obter os tokens de uma API Web com o fluxo do OAuth 2.0 no nome de](active-directory-v2-protocols-oauth-on-behalf-of.md)
