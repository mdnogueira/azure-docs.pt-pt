---
title: "Compreender o OAuth2 implícita conceder fluxo no Azure AD | Microsoft Docs"
description: "Saiba mais sobre a implementação do Azure Active Directory de OAuth2 implícita conceder fluxo, e se é adequada para a sua aplicação."
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 241c744737515ee0c8d5d833a51121808877e559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Compreender o fluxo de concessão implícita de OAuth2 no Azure Active Directory (AD)
A concessão implícita OAuth2 é notorious para ser a concessão com a lista mais longo das preocupações de segurança na especificação do OAuth2. E, ainda, que é a abordagem implementada por ADAL JS e aquele que recomendamos quando SPA aplicações de escrita. O que lhe dá? É todos os um fim de fala: e como fica, a concessão implícita é a abordagem das melhores pode pursue para aplicações que consumam uma API Web através de JavaScript a partir de um browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>O que é a concessão implícita OAuth2?
O quintessential [concessão do código de autorização de OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) é a concessão de autorização que utiliza dois pontos finais separados. O ponto final de autorização é utilizado para a fase de interação do utilizador, que resulta num código de autorização. O ponto final de tokens, em seguida, é utilizado pelo cliente para trocar o código para um token de acesso e, muitas vezes, bem como um token de atualização. As aplicações Web são necessárias para apresentar as suas próprias credenciais de aplicação para o ponto final de tokens, para que o servidor de autorização pode autenticar o cliente.

O [concessão implícita OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) é uma variante de outros concede autorização. Permite que um cliente obter um token de acesso (e id_token, ao utilizar [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html)) diretamente a partir do ponto final de autorização, sem contactar o ponto final do token nem autenticar o cliente. Este variant foi tenham sido concebida especificamente para aplicações em execução num browser de baseada em JavaScript: na especificação de OAuth2 original, os tokens são devolvidos um fragmento URI. Que disponibiliza os bits de token para o código JavaScript no cliente, mas garante que não irá ser incluídos na redireciona para o servidor. Devolver tokens através do browser redireciona diretamente a partir do ponto final de autorização. Também tem a vantagem de eliminando quaisquer requisitos para entre chamadas de origem, que são necessárias se a aplicação do JavaScript é necessário contactar o ponto final do token.

Uma característica importante do que a concessão implícita OAuth2 é o facto como flui de retorno nunca de atualização de tokens para o cliente. Como veremos aparecer na secção seguinte, que não está realmente necessário e na realidade seria um problema de segurança.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a concessão implícita OAuth2
Como a especificação do OAuth2 declara, a concessão implícita tem sido pensada para ativar aplicações de agente do utilizador – que diga, as aplicações de JavaScript em execução dentro de um browser. A definição característica dessas aplicações é que o código JavaScript é utilizado para aceder a recursos de servidor (normalmente, uma API Web) e para atualizar a aplicação UX em conformidade. Considere aplicações como o Gmail ou o Outlook Web Access: Quando seleciona uma mensagem da sua pasta a receber, apenas o painel de visualização da mensagem é alterado para apresentar a nova seleção, enquanto o resto da página permanece inalterado. Trata-se em contraste com tradicionais baseada em redireccionamento de aplicações Web, onde cada interação do utilizador resulta numa nova colocação página completa e uma composição de página completa da resposta de servidor novo.

As aplicações que adotar a abordagem de com base em JavaScript para a respetiva Alpine são denominadas aplicações de página única ou SPAs: a ideia é que essas aplicações servem apenas uma página HTML inicial e associada JavaScript, com todas as interações subsequentes que está a ser controladas através da Web API as chamadas efetuadas através de JavaScript. No entanto, abordagens híbrida, onde a aplicação é principalmente condicionada por nova colocação, mas efetua ocasionais JS chamadas, não são incomuns – o debate sobre a utilização de fluxo implícito é relevante para quem bem.

Aplicações baseadas no redirecionamento normalmente Proteja os seus pedidos através de cookies, no entanto, o que abordagem não funciona bem para aplicações JavaScript. Os cookies funcionam apenas contra o domínio que ter sido gerados, enquanto chamadas JavaScript podem ser direcionadas para outros domínios. Na verdade, que frequentemente será o caso: considere aplicações invocar Microsoft Graph API, API do Office, Azure API – todas as que residem fora do domínio de onde a aplicação é servida. Uma tendência crescente para aplicações JavaScript é não ter nenhum back-end, entidade confiadora 100% no terceiros 3rd APIs da Web para implementar a sua função de negócio.

Atualmente, o método preferencial de proteção de chamadas para uma API Web é utilizar a abordagem token de portador o OAuth2, onde cada chamada é acompanhada por um token de acesso do OAuth2. A API Web examina o token de acesso de entrada e, se encontrar na mesma os âmbitos necessários, concede acesso para a operação pedida. O fluxo implícito fornece um mecanismo conveniente para aplicações JavaScript obter os tokens de acesso para uma API Web, oferece várias vantagens no que respeita à cookies:

* Os tokens que podem ser obtidos fiável sem ser necessário para entre chamadas de origem – registo obrigatório de redirecionamento URI para o qual os tokens são retorno garante que os tokens não são displaced
* Aplicações de JavaScript podem obter os tokens de acesso tantos conforme necessário, para tantos APIs da Web de destino que – sem restrição de domínios
* Funcionalidades HTML5 como sessão ou armazenamento local conceder controlo total sobre a colocação em cache token e gestão de duração, enquanto que a gestão de cookies é opaco para a aplicação
* Os tokens de acesso não são suscetíveis a ataques de falsificação (CSRF) do pedido de sites

O fluxo de concessão implícita não emitir tokens de atualização, principalmente por motivos de segurança. Um token de atualização como narrowly não se encontra no âmbito como tokens de acesso, até que ponto maior potência de inflicting, por conseguinte, até que ponto mais danos no caso de Ocorreu uma fuga da de concessão de permissões. O fluxo implícito, tokens são fornecidos no URL, pelo que o risco de interception num nível superior que a concessão do código de autorização.

No entanto, tenha em atenção que uma aplicação do JavaScript tem outro mecanismo no respetivo disposal para renovar tokens de acesso sem repetidamente pedir ao utilizador as credenciais. A aplicação pode utilizar um iframe oculta para efetuar a novos pedidos de token relativamente ao ponto final de autorização do Azure AD:, desde que o browser ainda tem uma sessão ativa (ler: tem um cookie de sessão) contra o domínio do Azure AD, o pedido de autenticação pode ocorre com êxito sem ser necessário para interação do utilizador.

Este modelo concede a aplicação do JavaScript a capacidade de forma independente renovar tokens de acesso e mesmo adquirir novos para uma nova API (desde que o utilizador autorizado anteriormente para os mesmos. Isto evita o fardo adicionado de aquisição, manutenção e proteger um artefacto de valor elevado, tais como um token de atualização. O artefacto que possibilita a renovação automática, o cookie de sessão do Azure AD, é gerido fora da aplicação. Outra vantagem desta abordagem é que um utilizador pode terminar sessão do Azure AD, utilizando qualquer uma das aplicações assinadas com o Azure AD, em execução em qualquer um dos separadores de browser. Como resultado, a eliminação do cookie de sessão do Azure AD e a aplicação do JavaScript automaticamente perderá a capacidade de renovar tokens para o utilizador assinado enviados.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A concessão implícita é adequado para a minha aplicação?
A concessão implícita apresenta mais riscos que outros concede e as áreas que terá prestar atenção à estão também documentadas. Por exemplo, [utilização indevida do acesso Token para representar o proprietário de recursos no fluxo implícito] [ OAuth2-Spec-Implicit-Misuse] e [modelo de ameaça do OAuth 2.0 e considerações de segurança] [ OAuth2-Threat-Model-And-Security-Implications]). No entanto, o perfil de risco superior é amplamente porque de facto que o principal objetivo é permitir que aplicações executar código de Active Directory, servido por um recurso remoto para o browser. Se estiver a planear uma arquitetura SPA não ter nenhum componentes de back-end ou pretende invocar uma API Web através de JavaScript, recomenda-se a utilização do fluxo implícito para a aquisição do token.

Se a aplicação for um cliente nativo, o fluxo implícito de não é uma excelente opção. A ausência de cookie de sessão do Azure AD no contexto de um cliente nativo deprives aplicação a partir do meio de uma sessão lived período de tempo de manutenção dos. Significa que a aplicação repetidamente pedirá ao utilizador quando obter tokens de acesso para recursos novos.

Se estiver a desenvolver uma aplicação Web que inclui um back-end e consumir uma API a partir do respetivo código de back-end, o fluxo implícito também não é uma boa opção. Outros concede-lhe muito maior potência. Por exemplo, a concessão de credenciais do cliente de OAuth2 fornece a capacidade para obter os tokens refletem as permissões atribuídas à aplicação, por oposição as delegações do utilizador. Isto significa que o cliente tem a capacidade de manter o acesso programático a recursos mesmo quando um utilizador não está ativamente envolvido numa sessão e assim sucessivamente. Não só que, mas essas concede dar mais elevada de segurança de garantias. Por exemplo, tokens de acesso nunca trânsito através do browser do utilizador, não sejam guardados no histórico do browser de risco e assim sucessivamente. A aplicação de cliente também pode efetuar a autenticação forte quando pedir um token.

## <a name="next-steps"></a>Passos seguintes
* Para obter uma lista completa dos recursos de programador, incluindo informações de referência para os protocolos e a autorização de OAuth2 conceder fluxos suporte pelo Azure AD, consulte o [Guia do programador do Azure AD][AAD-Developers-Guide]
* Consulte [como integrar uma aplicação com o Azure AD] [ ACOM-How-To-Integrate] para profundidade adicional do processo de integração de aplicações.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
