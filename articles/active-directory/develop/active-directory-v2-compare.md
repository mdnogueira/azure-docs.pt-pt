---
title: "O que é diferente no ponto final v 2.0 do Azure AD? | Microsoft Docs"
description: "Uma comparação entre o Azure AD original e os pontos finais de v 2.0."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 81de65b0e825dec64383f52b02c5ee56c9434807
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="whats-different-about-the-v20-endpoint"></a>O que é diferente sobre o ponto final v 2.0?
Se estiver familiarizado com o Azure Active Directory ou ter integrado a aplicações com o Azure AD no passado, poderão existir algumas diferenças no ponto final v 2.0 não esperados.  Este documento chama dessas diferenças pela sua compreensão.

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Contas Microsoft e contas do Azure AD
O ponto final v 2.0 permitem aos programadores escrever as aplicações que aceitam início de sessão das contas Microsoft Accounts e o Azure AD, utilizando um ponto final de autenticação única.  Isto proporciona a capacidade de escrever a sua aplicação completamente agnóstico conta; pode ser ignorant do tipo de conta que o utilizador inicia sessão com.  Obviamente, que *pode* tornar a sua aplicação com suporte para o tipo de conta que está a ser utilizada numa sessão específica, mas não tem.

Por exemplo, se a sua aplicação chama o [Microsoft Graph](https://graph.microsoft.io), algumas funcionalidades adicionais e os dados que estarão disponíveis para os utilizadores empresariais, tais como os respetivos SharePoint sites ou dados de diretório.  Mas para várias ações, tal como [ler correio eletrónico de um utilizador](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), é possível escrever o código exatamente da mesma para contas Microsoft Accounts e o Azure AD.  

Integrar a sua aplicação com Accounts Microsoft e contas do Azure AD é agora um processo simples.  Pode utilizar um único conjunto de pontos finais, uma biblioteca único e um registo de aplicação único para obterem acesso às universos de consumidor e empresariais.  Para saber mais sobre o ponto final v 2.0, consulte [descrição geral](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Novo portal de registo de aplicação
Para registar uma aplicação que funciona com o ponto final v 2.0, tem de utilizar um novo portal de registo de aplicação: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Este é o portal onde pode obter uma ID da aplicação, personalizar o aspeto da página de início de sessão da sua aplicação e muito mais.  Tudo o que precisa para aceder ao portal é uma conta Microsoft ligada - conta pessoal ou trabalho/escola.

## <a name="one-app-id-for-all-platforms"></a>ID de uma aplicação para todas as plataformas
Se utilizou o Azure Active Directory, provavelmente, já registado várias aplicações diferentes para um único projeto.  Por exemplo, se criou um Web site e uma aplicação iOS, era necessário registá-los separadamente, com dois Ids de aplicação diferente. O portal de registo de aplicações do Azure AD forçado a fazer esta distinção durante o registo:

![Registo de aplicação antigo da IU](../../media/active-directory-v2-flows/old_app_registration.PNG)

Da mesma forma, se tiver um Web site e um web api de back-end, que poderá registou cada como uma aplicação individual no Azure AD.  Se tiver uma aplicação iOS e uma aplicação Android, também poderá ter registado ou duas aplicações diferentes.  Registar cada componente de uma aplicação gerou alguns comportamentos inesperados para os programadores e os respetivos clientes:

* Cada componente apareceu como uma aplicação no inquilino do Azure Active Directory de cada cliente separada.
* Quando um administrador inquilino foi efetuada uma tentativa aplicar a política para gerir o acesso ao ou eliminar uma aplicação, seria têm de fazer para cada componente da aplicação.
* Quando os clientes autorizado a uma aplicação, cada componente deveria aparecer no ecrã de consentimento como uma aplicação diferente.

Com o ponto final v 2.0, pode agora registar todos os componentes do seu projeto como um registo de aplicação único e utilizar um único Id de aplicação para o projeto todo.  Pode adicionar várias "plataformas" para um cada projeto e fornecer os dados adequados para cada plataforma de que adicionar.  Obviamente, pode criar como muitas aplicações, como é que gosta dependendo dos requisitos, mas para a maioria dos casos, apenas um Id da aplicação deve ser necessário.

Nosso objetivo é isto irá originar uma experiência de desenvolvimento e gestão de aplicações mais simplificada e criar uma vista consolidada mais de um único projeto que poderá estar a trabalhar num.

## <a name="scopes-not-resources"></a>Âmbitos de recursos não
No Azure Active Directory, uma aplicação pode ter um comportamento como um **recursos**, ou um destinatário de tokens.  Um recurso pode definir um número de **âmbitos** ou **oAuth2Permissions** que compreende, permitindo aplicações pedir tokens para esse recurso para um determinado conjunto de âmbitos de cliente.  Considere a AD Graph API do Azure como um exemplo de um recurso:

* Identificador de recurso, ou `AppID URI`:`https://graph.windows.net/`
* Âmbitos, ou `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, etc.  

Tudo isto se aplica para o ponto final v 2.0.  Uma aplicação ainda pode ter um comportamento como recurso, definir âmbitos e ser identificado por um URI.  Aplicações de cliente ainda podem pedir acesso a esses âmbitos.  No entanto, foi alterada a forma no qual um cliente solicita essas permissões.  No passado, autorizar um OAuth 2.0 pedido para o Azure AD poderão ter comparados como:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

onde o **recursos** parâmetro indicado o recurso a aplicação de cliente está a solicitar a autorização para.  Azure AD calculada as permissões necessárias pela aplicação com base na configuração estática no Portal do Azure e tokens emitidos em conformidade.  Agora, o mesmo OAuth 2.0 autorizar parece do pedido:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

onde o **âmbito** parâmetro indica qual o recurso e permissões a aplicação está a solicitar a autorização para. O recurso pretendido ainda está muito presente no pedido - é simplesmente incluído em cada um dos valores de parâmetro de âmbito.  Utilizando o parâmetro de âmbito desta forma permite que o ponto final v 2.0 ser mais compatível com a especificação de OAuth 2.0 e mais detalhadamente alinha com comuns práticas da indústria.  Também permite que as aplicações efetuar [consentimento incremental](#incremental-and-dynamic-consent), que é descrita na secção seguinte.

## <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico
Aplicações registado no Azure AD anteriormente necessário para especificar as respetivas permissões de OAuth 2.0 necessárias no Portal do Azure, no momento de criação de aplicações:

![Permissões de registo da IU](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

Permissões de uma aplicação necessária foram configuradas **estaticamente**.  Apesar de isto permitido configuração da aplicação existir no Portal do Azure e manter o código nice e simples, apresenta alguns problemas para os programadores:

* Uma aplicação que tiveram de saber de todas as permissões que seriam necessitar no momento de criação de aplicações.  A adição de permissões ao longo do tempo era um processo difícil.
* Uma aplicação que tiveram de conhecer todos os recursos de alguma vez acederia antecedência.  Foi difícil criar aplicações que podem aceder a um número aleatório de recursos.
* Uma aplicação que tiveram de todas as permissões que seriam necessitar após primeiro início de sessão o utilizador do pedido.  Em alguns casos, isto guiado para uma muito longa lista de permissões, o que não recomendado os utilizadores finais de aprovação de acesso da aplicação no início de sessão inicial.

Com o ponto final v 2.0, pode especificar as permissões às suas necessidades de aplicação **dinamicamente**, durante a execução, durante a utilização normal da sua aplicação.  Para tal, pode especificar os âmbitos que a aplicação necessita do dado no tempo, incluindo-las no `scope` parâmetro de um pedido de autorização:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

A permissão de pedidos acima para a aplicação leia os dados de diretório de um utilizador do Azure AD, bem como escrever dados ao seu diretório.  Se o utilizador consentiu essas permissões no passado para aplicações em particular, estes serão bastará introduzir as respetivas credenciais e a aplicação ser iniciadas.  Se o utilizador não se consentiu para qualquer uma destas permissões, o ponto final v 2.0 pedirá ao utilizador consentimento para essas permissões.  Para obter mais informações, pode ler sobre [permissões, consentimento e dos âmbitos](active-directory-v2-scopes.md).

Permitir que uma aplicação para pedir permissões dinamicamente através de `scope` parâmetro dá-lhe controlo total sobre a experiência do utilizador.  Se assim o desejar, pode optar por frontload seu consentimento experiência e peça para todas as permissões num pedido autorização inicial.  Ou, se a sua aplicação requer um grande número de permissões, pode optar por reunir essas permissões de utilizador de forma incremental, como tentam utilizar funcionalidades específicas da sua aplicação ao longo do tempo.

## <a name="well-known-scopes"></a>Âmbitos well-known
#### <a name="offline-access"></a>Acesso offline
Aplicações com o ponto final v 2.0 podem exigir a utilização de uma nova permissão conhecida para aplicações - o `offline_access` âmbito.  Todas as aplicações terá de pedir esta permissão, se precisar de aceder a recursos em nome de um utilizador durante um período prolongado de tempo, mesmo quando o utilizador poderá não estar ativamente a utilizar a aplicação.  O `offline_access` âmbito será apresentada ao utilizador nas caixas de diálogo de consentimento como "Acesso os dados offline", que o utilizador tem de aceitar.  A pedir a `offline_access` permissão irá permitir que a aplicação web receber o OAuth 2.0 refresh_tokens do ponto final v 2.0.  Refresh_tokens são longa duração e podem ser trocadas para novo OAuth 2.0 access_tokens por períodos prolongados de acesso.  

Se a aplicação pediu a `offline_access` âmbito, não receberá refresh_tokens.  Isto significa que quando resgatar uma authorization_code o fluxo de código de autorização do OAuth 2.0, apenas receber novamente um access_token do `/token` ponto final.  Esse access_token continuará a ser válido durante um curto período de tempo (normalmente, uma hora), mas, eventualmente, irá expirar.  AT ponto no tempo, a aplicação será necessário redirecionar o utilizador novamente para o `/authorize` ponto final para obter um novo authorization_code.  Durante este redirecionamento, o utilizador pode ou não pode ter de introduzir as respetivas credenciais novamente ou consentimento novamente as permissões, consoante o tipo de aplicação.

Para saber mais sobre o OAuth 2.0, refresh_tokens e access_tokens, veja o [referência do protocolo de v 2.0](active-directory-v2-protocols.md).

#### <a name="openid-profile-and-email"></a>OpenID e o perfil de e-mail
Historicamente, o mais básico início de sessão no fluxo de OpenID Connect no Azure Active Directory iria fornecer uma variedade de informações sobre o utilizador no id_token resultante.  As afirmações numa id_token podem incluir o utilizador nome, nome de utilizador preferencial, endereço de correio eletrónico, ID de objeto e muito mais.

Iremos agora são restringir as informações que o `openid` âmbito affords o acesso à aplicação ao.  O âmbito 'openid' só irá permitir que a sua aplicação iniciar sessão do utilizador no e receber um identificador de aplicação específicos para o utilizador.  Se pretender obter informações de identificação pessoal (PII) sobre o utilizador na sua aplicação, a aplicação será necessário solicitar permissões adicionais do utilizador.  Iremos introduzir dois âmbitos novos – o `email` e `profile` âmbitos – que permitem-lhe fazê-lo.

O `email` âmbito é muito simples – permite que o acesso de aplicação para o endereço de correio eletrónico principal do utilizador através de `email` o id_token de afirmação.  O `profile` âmbito affords o acesso de aplicação para todas as outras informações básicas sobre o utilizador – o respetivo nome, nome de utilizador preferencial, ID de objeto e assim sucessivamente.

Isto permite-lhe código a aplicação de forma mínima divulgação – pode apenas pedir ao utilizador para o conjunto de informações que requer a sua aplicação para efetuar a tarefa.  Para obter mais informações sobre estes âmbitos, consulte [a referência de âmbito de v 2.0](active-directory-v2-scopes.md).

## <a name="token-claims"></a>Afirmações do token
As afirmações no tokens emitidos pelo ponto final v 2.0 não irão ser idênticas ao tokens emitidos pelo geralmente disponível pontos finais do Azure AD - migrar para o novo serviço de aplicações devem não partem do princípio de uma afirmação específica existem na id_tokens ou access_tokens. Para saber mais sobre as afirmações específicas emitidas em tokens de v 2.0, consulte o [referência de token de v 2.0](active-directory-v2-tokens.md).

## <a name="limitations"></a>Limitações
Existem algumas restrições a ter em consideração ao utilizar o ponto de v 2.0.  Consulte o [documento limitações de v 2.0](active-directory-v2-limitations.md) para ver se qualquer uma destas restrições aplicam-se ao seu cenário específico.
