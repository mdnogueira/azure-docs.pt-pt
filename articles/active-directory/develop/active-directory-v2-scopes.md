---
title: "Âmbitos de v 2.0 do Active Directory do Azure, permissões e consentimento | Microsoft Docs"
description: "Uma descrição da autorização no Azure AD ponto final v 2.0, incluindo âmbitos, permissões e consentimento."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 04869a7627ecb3e6a0d11733fae7da2ecb04ed51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Âmbitos, permissões e consentimento no ponto final v 2.0 do Azure Active Directory
As aplicações que se integram com o Azure Active Directory (Azure AD), siga um modelo de autorização que proporciona aos utilizadores controlo sobre a forma como uma aplicação pode aceder aos respetivos dados. A implementação de v 2.0 do modelo de autorização foi atualizada e as alterações como uma aplicação tem de interagir com o Azure AD. Este artigo aborda os conceitos básicos neste modelo de autorização, incluindo âmbitos, permissões e consentimento.

> [!NOTE]
> O ponto final v 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades. Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Âmbitos e permissões
Azure AD implementa o [OAuth 2.0](active-directory-v2-protocols.md) protocolo de autorização. OAuth 2.0 é um método através do qual uma aplicação de terceiros pode aceder a recursos alojados em web em nome de um utilizador. Quaisquer recursos alojados em web que se integra com o Azure AD tem um identificador de recurso, ou *URI de ID de aplicação*. Por exemplo, alguns dos recursos de alojada em web da Microsoft incluem:

* O Office 365 Unified correio API:`https://outlook.office.com`
* A API do Azure AD Graph:`https://graph.windows.net`
* Microsoft Graph:`https://graph.microsoft.com`

O mesmo se aplica a quaisquer recursos de terceiros que tenha integrado com o Azure AD. Qualquer um destes recursos também pode definir um conjunto de permissões que pode ser utilizado para dividir a funcionalidade desse recurso em segmentos mais pequenos. Por exemplo, [Microsoft Graph](https://graph.microsoft.io) tiver definido permissões para efetuar as seguintes tarefas, entre outras pessoas:

* Calendário de um utilizador de leitura
* Escrever calendário de um utilizador
* Enviar e-mails como se fosse um utilizador

Ao definir estes tipos de permissões, o recurso tem controlo detalhado sobre os dados e como os dados são expostos. Uma aplicação de terceiros pode pedir as permissões de um utilizador de aplicação. O utilizador da aplicação têm de aprovar as permissões para a aplicação pode agir em nome do utilizador. Através da funcionalidade do recurso para conjuntos mais pequenos de permissão de agrupamento, aplicações de terceiros podem ser criadas para pedir apenas as permissões específicas que precisam para realizar o seu funcionamento. Utilizadores de aplicações podem saber exatamente como uma aplicação utilizará os seus dados e podem ser mais a certeza de que a aplicação não está a comportar com intenções maliciosas.

No Azure AD e OAuth, estes tipos de permissões são denominados *âmbitos*. Eles também por vezes, são referidos como *oAuth2Permissions*. Um âmbito é representado no Azure AD como um valor de cadeia. Continuar com o exemplo de Microsoft Graph, o valor do âmbito para cada permissão é:

* Leia o calendário de um utilizador através da utilização`Calendar.Read`
* Escrever calendário do utilizador através da utilização`Mail.ReadWrite`
* Enviar correio eletrónico como um utilizador a utilizar pelo`Mail.Send`

Uma aplicação pode solicitar estas permissões, especificando os âmbitos nos pedidos para o ponto final v 2.0.

## <a name="openid-connect-scopes"></a>Âmbitos de OpenID Connect
A implementação de v 2.0 do OpenID Connect tem alguns âmbitos bem definidos que não se aplicam a um recurso específico: `openid`, `email`, `profile`, e `offline_access`.

### <a name="openid"></a>openid
Se uma aplicação executa início de sessão utilizando [OpenID Connect](active-directory-v2-protocols.md), tem de pedir a `openid` âmbito. O `openid` âmbito mostra a página de consentimento de conta de trabalho como a permissão "Iniciar sessão" e a página de consentimento de conta Microsoft pessoal como a permissão "Ver o seu perfil e ligar a aplicações e serviços utilizando a sua conta Microsoft". Com esta permissão, uma aplicação pode receber um identificador exclusivo para o utilizador sob a forma do `sub` de afirmação. Também permite o acesso de aplicação para o ponto final UserInfo. O `openid` âmbito pode ser utilizado o ponto de final de token de v 2.0 para adquirir tokens de ID, o que podem ser utilizados para proteger chamadas HTTP entre os diferentes componentes de uma aplicação.

### <a name="email"></a>Correio eletrónico
O `email` âmbito pode ser utilizado com o `openid` âmbito e quaisquer outros. Fornece o acesso de aplicação para o endereço de e-mail principal do utilizador no formato de `email` de afirmação. O `email` afirmação está incluída num token apenas se um endereço de e-mail está associado à conta de utilizador, que não é sempre o caso. Se utiliza o `email` âmbito, a aplicação deve estar preparada para processar um caso em que o `email` afirmação não existe no token.

### <a name="profile"></a>Perfil
O `profile` âmbito pode ser utilizado com o `openid` âmbito e quaisquer outros. Fornece o acesso de aplicação para uma quantidade substancial de informações sobre o utilizador. As informações que possa aceder incluem, mas não está limitadas a, o nome de utilizador nome próprio, apelido, preferencial e ID de objeto. Para obter uma lista completa das afirmações perfil disponíveis no parâmetro id_tokens para um utilizador específico, consulte o [v 2.0 tokens referência](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
O [ `offline_access` âmbito](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) fornece o acesso de aplicação aos recursos em nome do utilizador para um período de tempo alargado. Na página de consentimento de conta de trabalho, este âmbito é apresentado como a permissão "Aceda aos seus dados em qualquer altura". Na página de pessoal Microsoft conta consentimento, é apresentada como a permissão "Aceder às suas informações em qualquer altura". Quando um utilizador aprova o `offline_access` âmbito, a aplicação pode receber tokens de atualização do ponto de final v 2.0 token. Os tokens de atualização são longa duração. A aplicação pode obter novos tokens de acesso, conforme os antigos.

Se a aplicação pediu a `offline_access` âmbito, não irão receber tokens de atualização. Isto significa que, quando resgatar um código de autorização no [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols.md), irá receber um token de acesso do `/token` ponto final. O token de acesso é válido para um curto período de tempo. O token de acesso normalmente expira dentro de uma hora. AT que ponto, a aplicação tem de redirecionar o utilizador novamente para o `/authorize` ponto final para obter um novo código de autorização. Durante este redirecionamento, dependendo do tipo de aplicação, o utilizador poderá ter de introduzir as respetivas credenciais novamente ou consentimento novamente as permissões.

Para mais informações sobre como obter e utilizar a atualização de tokens, consulte o [referência do protocolo de v 2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Pedir consentimento do utilizador individuais
Num [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md) autorização do pedido, uma aplicação pode pedir as permissões que necessita, utilizando o `scope` parâmetro de consulta. Por exemplo, quando um utilizador inicia sessão numa aplicação, a aplicação envia um pedido, como no seguinte exemplo (com quebras de linha adicionadas para melhorar a legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

O `scope` parâmetro é uma lista separada por espaço dos âmbitos que a aplicação está a pedir. Cada âmbito indicado, acrescentando o valor do âmbito para o identificador do recurso (o URI de ID de aplicação). O exemplo de pedido, a aplicação tem permissão para ler o calendário do utilizador e enviar correio eletrónico como o utilizador.

Depois do utilizador introduz as respetivas credenciais, o ponto final v 2.0 verifica a existência de um registo correspondente *consentimento do utilizador*. Se o utilizador não se consentiu para qualquer uma das permissões de pedido no passado, o ponto final v 2.0 pede ao utilizador para conceder as permissões de pedido.

![Consentimento de conta de trabalho](../../media/active-directory-v2-flows/work_account_consent.png)

Quando o utilizador aprova a permissão, o consentimento é registado para que o utilizador não é necessário consentimento novamente em inícios de sessão subsequentes de conta.

## <a name="requesting-consent-for-an-entire-tenant"></a>Pedir consentimento para um inquilino todo
Muitas vezes, quando uma organização comprar uma licença ou uma subscrição para uma aplicação, a organização pretende aprovisionar completamente a aplicação para os respetivos empregados. Como parte deste processo, um administrador pode conceder consentimento para a aplicação para agir em nome de qualquer empregado. Se o administrador atribui consentimento para o inquilino de todo, os funcionários da organização não serão apresentada uma página de consentimento para a aplicação.

Para pedir consentimento para todos os utilizadores de um inquilino, a aplicação pode utilizar o ponto final de consentimento de administração.

## <a name="admin-restricted-scopes"></a>Âmbitos de administração restrito
Alguns permissões de privilégio elevado no ecossistema da Microsoft podem ser definidas como *admin restrito*. Os exemplos destes tipos de âmbitos incluem as seguintes permissões:

* Ler dados do diretório da organização utilizando`Directory.Read`
* Escrever dados para o diretório da organização utilizando`Directory.ReadWrite`
* Grupos de segurança de leitura no diretório da organização utilizando`Groups.Read.All`

Apesar de um utilizador de consumidor pode conceder um acesso de aplicação para este tipo de dados, os utilizadores organizacionais são impedidos de conceder acesso para o mesmo conjunto de dados confidenciais da empresa. Se a aplicação solicitar acesso a uma destas permissões de um utilizador organizacional, o utilizador recebe uma mensagem de erro que indica que não estão autorizados a autorizar permissões da sua aplicação.

Se a aplicação necessita de acesso a âmbitos de administração restrito para organizações, deve solicitá-los também diretamente a partir de um administrador de empresa, utilizando o consentimento do ponto final de administração, descrito a seguir.

Quando um administrador atribui estas permissões através do ponto final de consentimento de administração, é concedido consentimento para todos os utilizadores no inquilino.

## <a name="using-the-admin-consent-endpoint"></a>Utilizar o ponto final de consentimento de administração
Se seguir estes passos, a aplicação pode recolher as permissões para todos os utilizadores de um inquilino, incluindo os âmbitos de administração restrito. Para ver um exemplo de código que implementa os passos, consulte o [exemplo de âmbitos de administração restrito](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Pedir as permissões no portal de registo de aplicação
1. Aceda à sua aplicação no [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou [criar uma aplicação](active-directory-v2-app-registration.md) se ainda não o fez.
2. Localize o **Microsoft Graph permissões** secção e, em seguida, adicione as permissões que requer a sua aplicação.
3. Certifique-se de **guardar** o registo de aplicação.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Recomendado: Sessão do utilizador para a sua aplicação
Normalmente, quando criar uma aplicação que utiliza o ponto final de consentimento de administração, a aplicação necessita de uma vista em que o administrador pode aprovar permissões de uma aplicação ou página. Nesta página pode fazer parte do fluxo de inscrição da aplicação, parte das definições da aplicação, ou pode ser um fluxo de "estabelecer a ligação" dedicado. Em muitos casos, faz sentido para a aplicação mostrar isto "ligar" vista apenas depois de um utilizador tem sessão iniciada com uma empresa ou escola conta Microsoft.

Quando a sessão do utilizador para a sua aplicação, pode identificar a organização a que o administrador pertence antes de pedir para aprovar as permissões necessárias. Apesar de não estritamente necessários, pode ajudar a criar uma experiência mais intuitiva para os seus utilizadores organizacionais. Para iniciar a sessão do utilizador no, siga a nossa [tutoriais de protocolo de v 2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Pedir as permissões de um administrador de diretório
Quando estiver pronto para solicitar permissões de administrador da sua organização, pode redirecionar o utilizador para a v 2.0 *ponto final de consentimento de administração*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |O inquilino de diretório que pretende pedir permissão a partir da. Pode ser fornecido no formato de nome amigável ou de GUID. |
| client_id |Necessário |ID de aplicação que o [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuída à aplicação. |
| redirect_uri |Necessário |O URI de redirecionamento do onde pretende que a resposta sejam enviados para a sua aplicação processar. Este deve corresponder exatamente um do redirecionamento de URIs registados no portal de registo de aplicação. |
| state |Recomendado |Um valor incluído no pedido de que também vai ser devolvido na resposta token. Pode ser uma cadeia de qualquer conteúdo que pretende. Utilize o estado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |

Neste momento, o Azure AD requer um administrador inquilino para iniciar sessão para concluir o pedido. O administrador é-lhe pedido para aprovar todas as permissões que pediu para a sua aplicação no portal de registo de aplicação.

#### <a name="successful-response"></a>Resposta com êxito
Se o administrador aprova as permissões para a sua aplicação, a resposta com êxito este aspeto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- | --- |
| Inquilino |O inquilino de diretório concedidas as permissões de aplicação pediu, no formato GUID. |
| state |Um valor incluído no pedido de que também vai ser devolvido na resposta token. Pode ser uma cadeia de qualquer conteúdo que pretende. O estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |
| admin_consent |Será definida para **verdadeiro**. |

#### <a name="error-response"></a>Resposta de erro
Se o administrador não aprovar as permissões para a sua aplicação, a resposta de falha este aspeto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros ocorridos e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro. |

Depois de ter recebeu uma resposta com êxito o ponto final de consentimento de administração, a aplicação tem adquiridos as permissões que solicitada. Em seguida, pode pedir um token para o recurso que pretende.

## <a name="using-permissions"></a>Utilizando as permissões
Depois do utilizador permitir às permissões para a sua aplicação, a aplicação pode adquirir tokens de acesso que representam a permissão da aplicação para aceder a um recurso de algumas capacidade. Um token de acesso pode ser utilizado apenas para um único recurso, mas codificado dentro do token de acesso é todas as permissões que tiver sido concedida a sua aplicação para esse recurso. Para obter um token de acesso, a aplicação pode efetuar um pedido para o token ponto final v 2.0, como esta:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Pode utilizar o token de acesso resultante nos pedidos HTTP para o recurso. Fiável indica ao recurso de que a aplicação tiver as permissões adequadas para executar uma tarefa específica.  

Para obter mais informações sobre o protocolo OAuth 2.0 e como obter os tokens de acesso, consulte o [referência de protocolo de ponto final v 2.0](active-directory-v2-protocols.md).
