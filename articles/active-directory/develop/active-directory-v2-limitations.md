---
title: "Restrições e limitações de ponto final de v 2.0 do Azure Active Directory | Microsoft Docs"
description: "Uma lista de limitações e restrições para o ponto de final de v 2.0 do Azure AD."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 5a9d455203e50da47208ef1494d38a950161bee1
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="should-i-use-the-v20-endpoint"></a>Deve utilizar o ponto final v 2.0?
Quando criar aplicações que se integram com o Azure Active Directory, terá de decidir se os protocolos de autenticação e de ponto final v 2.0 as suas necessidades. Ponto final do Azure do Active Directory original é ainda totalmente suportado e, em alguns aspetos, é mais avançada funcionalidade de v 2.0. No entanto, o ponto final v 2.0 [apresenta as vantagens significativas](active-directory-v2-compare.md) para programadores.

Segue-se a nossa recomendação simplificada para programadores neste ponto no tempo:

* Caso seja necessário suportar contas Microsoft pessoais na sua aplicação, utilize o ponto final v 2.0. Mas antes de efetuar, lembre-se de que compreende as limitações que discutimos neste artigo.
* Se a aplicação só tem de suportar o trabalho do Microsoft contas escolares ou profissionais, não utilize o ponto final v 2.0. Em vez disso, consulte a nossa [guia para programadores do Azure AD](active-directory-developers-guide.md).

Ao longo do tempo, irá aumentar o ponto final v 2.0 para eliminar as restrições listadas aqui, para que irá apenas necessitar de utilizar o ponto final v 2.0. Entretanto, este artigo destina-se para o ajudar a determinar se o ponto final v 2.0 é adequado para si. Vamos continuar a atualização deste artigo para refletir o estado atual do ponto final v 2.0. Certifique-se novamente reevaluate os requisitos de capacidades de v 2.0.

Se tiver uma aplicação do Azure AD existente que não utiliza o ponto final v 2.0, não é necessário para iniciar a partir do zero. No futuro, iremos irá fornecer uma forma para que possa utilizar as suas aplicações do Azure AD existentes com o ponto final v 2.0.

## <a name="restrictions-on-app-types"></a>Restrições em tipos de aplicação
Atualmente, os seguintes tipos de aplicações não são suportados pelo ponto final v 2.0. Para obter uma descrição dos tipos de aplicação suportados, consulte [tipos de aplicação para o ponto de final de v 2.0 do Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>APIs da Web autónoma
Pode utilizar o ponto final v 2.0 para [criar uma API Web que está protegida com OAuth 2.0](active-directory-v2-flows.md#web-apis). No entanto, essa API Web pode receber tokens apenas a partir de uma aplicação que tenha o mesmo ID de aplicação Não é possível aceder a uma API Web de um cliente que tenha um ID de aplicação diferente. O cliente não conseguirá obter permissões para a API Web ou um pedido.

Para ver como criar uma API Web que aceita tokens a partir de um cliente que tem o mesmo ID de aplicação, consulte os exemplos de Web API de ponto final v 2.0 no nosso [introdução](active-directory-appmodel-v2-overview.md#getting-started) secção.

## <a name="restrictions-on-app-registrations"></a>Restrições de registos de aplicação
Atualmente, para cada aplicação que pretende integrar com o ponto final v 2.0, tem de criar um registo de aplicação no novo [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Existente do Azure AD ou aplicações da conta Microsoft não são compatíveis com o ponto final v 2.0. As aplicações que estão registadas em qualquer portal que não seja o Portal de registo de aplicação não são compatíveis com o ponto final v 2.0. No futuro, planeamos fornecer uma forma de utilizar uma aplicação existente como uma aplicação v 2.0. Atualmente, no entanto, não há nenhum caminho de migração para uma aplicação existente para trabalhar com o ponto final v 2.0.

Além disso, os registos de aplicações que criar no [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tem os seguintes avisos:

* Apenas dois segredos de aplicação são permitidos por ID de aplicação.
* Um registo de aplicação, registado por um utilizador com uma conta Microsoft pessoal, pode ser visualizado e gerido apenas por uma conta de programador único. Não é possível partilhar entre vários programadores.  Se gostaria de partilhar o seu registo de aplicação entre vários programadores, pode criar a aplicação ao iniciar sessão o portal de registo com uma conta do Azure AD.
* Existem várias restrições sobre o formato do redirecionamento URI que é permitida. Para obter mais informações sobre redirecionamento URIs, consulte a secção seguinte.

## <a name="restrictions-on-redirect-uris"></a>Restrições no URI de redirecionamento
Atualmente, as aplicações que estão registadas no Portal de registo de aplicação estão limitadas a um conjunto limitado de valores URI de redirecionamento. O URI para serviços e aplicações web têm de começar com o esquema de redirecionamento `https`, e todos os valores URI de redirecionamento têm de partilhar um único domínio DNS. Por exemplo, não é possível registar uma aplicação web que tem um destes URI de redirecionamento:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

O sistema de registo compara o nome DNS todo o URI de redirecionamento existente com o nome DNS do redirecionamento URI que estiver a adicionar. O pedido para adicionar o nome DNS irá falhar, se uma das seguintes condições for verdadeira:  

* O nome DNS todo o URI de redirecionamento novo não corresponde ao nome DNS do URI de redirecionamento existente.
* O nome DNS todo o URI de redirecionamento novo não é um subdomínio o URI de redirecionamento existente.

Por exemplo, se a aplicação tiver este URI de redirecionamento:

`https://login.contoso.com`

Pode adicioná-lo, da seguinte forma:

`https://login.contoso.com/new`

Neste caso, o nome DNS corresponde exatamente. Ou pode fazer o seguinte:

`https://new.login.contoso.com`

Neste caso, está a referir-se a um subdomínio de DNS de login.contoso.com. Se pretender ter uma aplicação que tenha início de sessão east.contoso.com e west.contoso.com início de sessão como os URIs de redirecionamento, tem de adicionar que os URIs de redirecionamento pela seguinte ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Pode adicionar as duas última porque são subdomínios do redirecionamento primeiro URI, contoso.com. Esta limitação será removida de uma versão futura.

Tenha também em atenção, pode ter apenas 20 URLs de resposta para uma aplicação específica.

Para saber como registar uma aplicação no Portal de registo de aplicação, consulte [como registar uma aplicação com o ponto final v 2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services-and-apis"></a>Restrições sobre serviços e APIs
Atualmente, o ponto final v 2.0 suporta início de sessão para qualquer aplicação que está registado no Portal de registo de aplicação e que está na lista de [suportada autenticação fluxos](active-directory-v2-flows.md). No entanto, estas aplicações podem obter tokens de acesso de OAuth 2.0 para um conjunto muito limitado de recursos. Os problemas de ponto final v 2.0 aceder tokens apenas para:

* A aplicação que pediu o token. Uma aplicação pode adquirir um token de acesso para si, se a aplicação lógica é composta por vários componentes diferentes ou camadas. Para ver este cenário na ação, consulte a nossa [introdução](active-directory-appmodel-v2-overview.md#getting-started) tutoriais.
* A capacidade de correio do Outlook, calendário e contactos APIs REST, todos os que estão localizados em https://outlook.office.com. Para saber mais sobre como escrever uma aplicação que acede a estas APIs, consulte o [Office introdução](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) tutoriais.
* APIs de Microsoft Graph. Pode saber mais sobre [Microsoft Graph](https://graph.microsoft.io) e os dados que estão disponíveis para si.

Outros serviços não são suportados neste momento. Mais Microsoft Online Services será adicionado no futuro, além de suporte para as suas próprias APIs da Web e serviços personalizada.

## <a name="restrictions-on-libraries-and-sdks"></a>Restrições na SDKs e de bibliotecas
Atualmente, o suporte de biblioteca para o ponto final v 2.0 é limitado. Se pretender utilizar o ponto final v 2.0 numa aplicação de produção, tem estas opções:

* Se está a criar uma aplicação web, pode utilizar com segurança Microsoft geralmente disponível middleware de lado do servidor para efetuar a validação do início de sessão e o token. Estes incluem o middleware OWIN abrir ID Connect para ASP.NET e o Node.js Passport Plug-in. Para exemplos de código que utilizam o middleware da Microsoft, consulte a nossa [introdução](active-directory-appmodel-v2-overview.md#getting-started) secção.
* Se está a criar uma aplicação de ambiente de trabalho ou de dispositivo móvel, pode utilizar uma das nossa bibliotecas de autenticação da Microsoft (MSAL) de pré-visualização.  Estas bibliotecas estão na pré-visualização suportado de produção, pelo que é seguro para utilizá-los em aplicações de produção. Pode ler mais sobre os termos de pré-visualização e as bibliotecas disponíveis no nosso [referência de bibliotecas de autenticação](active-directory-v2-libraries.md).
* Para plataformas não abrangidas por bibliotecas Microsoft, pode integrar com o ponto final v 2.0 por diretamente enviar e receber mensagens de protocolo no código da aplicação. Os protocolos de OpenID Connect e OAuth v 2.0 [explicitamente estão documentados](active-directory-v2-protocols.md) para ajudar a efetuar essa uma integração.
* Por fim, pode utilizar bibliotecas de open source abrir ID Connect e OAuth para integrar com o ponto final v 2.0. O protocolo de v 2.0 deve ser compatível com muitos bibliotecas de open source protocolo sem alterações principais. A disponibilidade destes tipos de bibliotecas varia consoante a plataforma e de idioma. O [abrir ID Connect](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) sites manter uma lista de implementações populares. Para obter mais informações, consulte [bibliotecas de v 2.0 e a autenticação do Azure Active Directory](active-directory-v2-libraries.md)e a lista de bibliotecas de cliente de open source e exemplos de que foi testados com o ponto final v 2.0.

## <a name="restrictions-on-protocols"></a>Restrições de protocolos
O ponto final v 2.0 não suporta SAML ou WS-Federation; só suporta abrir ID Connect e OAuth 2.0.  Nem todas as funcionalidades e capacidades de protocolos de OAuth tenham sido incorporadas para o ponto final v 2.0. Estas capacidades e funcionalidades do protocolo atualmente são *não está disponível* no ponto final v 2.0:

* Tokens de ID, que são emitidos pelo ponto final v 2.0 não contêm um `email` de afirmação para o utilizador, mesmo se adquirir a permissão do utilizador para ver o respetivo e-mail.
* O ponto final OpenID Connect UserInfo não está implementado no ponto final v 2.0. No entanto, todos os dados de perfil de utilizador que potencialmente seria receberá neste ponto final está disponível a partir do Microsoft Graph `/me` ponto final.
* O ponto final v 2.0 não suporta afirmações emissoras de função ou grupo em tokens de ID.
* O [concessão de credenciais de palavra-passe do OAuth 2.0 recursos proprietário](https://tools.ietf.org/html/rfc6749#section-4.3) não é suportado pelo ponto final v 2.0.

Addtion, o ponto final v 2.0 não suporta qualquer outra forma dos protocolos SAML ou WS-Federation.

Para melhor compreender o âmbito da funcionalidade de protocolo suportado no ponto final v 2.0, leia a nossa [referência do protocolo OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restrições para contas profissional e escolar
Se utilizou o Active Directory Authentication Library (ADAL) em aplicações do Windows, poderá ter demorado partido da autenticação integrada do Windows, que utiliza a concessão de asserção de Security Assertion Markup Language (SAML). Com esta concessão, os utilizadores de federado do Azure AD os inquilinos podem autenticar silenciosamente com as respetivas instâncias de Active Directory no local sem introduzir as credenciais. Atualmente, a concessão de asserção SAML não é suportada no ponto final v 2.0.