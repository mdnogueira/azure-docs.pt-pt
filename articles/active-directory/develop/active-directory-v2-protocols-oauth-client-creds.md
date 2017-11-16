---
title: "Utilizar o Azure AD v 2.0 para aceder a recursos segurados sem interação do utilizador | Microsoft Docs"
description: "Crie aplicações web, utilizando a implementação do Azure AD do protocolo de autenticação OAuth 2.0."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 93b54c3fc4397573f77b2e157c6f1866786690da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v 2.0 e o fluxo de credenciais de cliente OAuth 2.0
Pode utilizar o [conceder credenciais de cliente OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), por vezes chamado *legged de dois OAuth*, para aceder a recursos alojados em web utilizando a identidade de uma aplicação. Este tipo de conceder normalmente é utilizado para o servidor para servidor interações que devem ser executada em segundo plano, sem interação imediata com um utilizador. Estes tipos de aplicações, muitas vezes, são denominados *daemons* ou *às contas de serviço*.

> [!NOTE]
> O ponto final v 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades. Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
>
>

O mais comum na *legged de três OAuth*, uma aplicação de cliente é concedida permissão para aceder a um recurso em nome de um utilizador específico. A permissão é delegada ao utilizador para a aplicação, normalmente durante a [consentimento](active-directory-v2-scopes.md) processo. No entanto, no fluxo de credenciais de cliente, são concedidas permissões diretamente para a própria aplicação. Quando apresenta a aplicação que impõe um token a um recurso, o recurso a que a aplicação em si tem autorização para efetuar uma ação e não que o utilizador tem autorização.

## <a name="protocol-diagram"></a>Diagrama de protocolo
O fluxo de credenciais de cliente completa semelhante ao seguinte diagrama. Iremos descrevem cada um dos passos neste artigo.

![Fluxo de credenciais do cliente](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obter autorização direta
Uma aplicação recebe normalmente direta autorização para aceder a um recurso de uma das seguintes formas: através de uma lista de controlo de acesso (ACL) no recurso ou através da atribuição de permissão de aplicação no Azure Active Directory (Azure AD). Estes dois métodos são as mais comuns no Azure AD e recomendamos-las para os clientes e recursos que executam o cliente do fluxo de credenciais. Um recurso pode optar por autorizar os clientes de outras formas, no entanto. Cada servidor de recurso pode escolher o método mais adequada para a sua aplicação.

### <a name="access-control-lists"></a>Lista de controlo de acesso
Um fornecedor de recursos pode impor uma verificação de autorização com base numa lista de IDs da aplicação que sabe e atribui um nível específico de acesso. Quando o recurso recebe um token do ponto final v 2.0, pode descodificar o token e extrair o ID da aplicação do cliente do `appid` e `iss` afirmações. Em seguida, compara a aplicação relativamente a uma ACL que mantém. A ACL granularidade e o método podem variar substancialmente entre os recursos.

Um caso de utilização comum é utilizar uma ACL para executar testes de uma aplicação web ou de uma API Web. A API Web pode conceder apenas um subconjunto de todas as permissões para um cliente específico. Para executar testes de ponto a ponto na API, crie um cliente de teste que adquire tokens a partir do ponto final v 2.0 e, em seguida, envia-as para a API. A API, em seguida, verifica a ACL de ID da aplicação do cliente de teste para acesso completo a funcionalidade de toda a API. Se utilizar este tipo de ACL, é necessário validar não apenas da função invocadora `appid` valor. Também validar que o `iss` o valor do token é fidedigno.

Este tipo de autorização é comum para daemons e contas de serviço que precisem de aceder a dados pertencentes ao consumidor utilizadores com contas Microsoft pessoais. Para os dados pertencentes a organizações, recomendamos que obtém a autorização necessária através de permissões de aplicação.

### <a name="application-permissions"></a>Permissões de aplicação
Em vez de utilizar as ACLs, pode utilizar os APIs para expor um conjunto de permissões de aplicação. Uma permissão de aplicação é concedida a uma aplicação pelo administrador de uma organização e pode ser utilizada apenas para aceder a dados pertencentes a organização e os respetivos empregados. Por exemplo, o Microsoft Graph expõe várias permissões de aplicação para fazer o seguinte:

* Ler correio em todas as caixas de correio
* Ler e escrever correio em todas as caixas de correio
* Enviar e-mails como um utilizador qualquer
* Ler dados de diretório

Para obter mais informações sobre as permissões de aplicação, aceda a [Microsoft Graph](https://graph.microsoft.io).

Para utilizar permissões de aplicação na sua aplicação, efetue os passos, que vamos discutir nas secções seguintes.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Pedir as permissões no portal de registo de aplicação
1. Aceda à sua aplicação no [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou [criar uma aplicação](active-directory-v2-app-registration.md), se ainda não o fez. Terá de utilizar pelo menos um segredo da aplicação ao criar a sua aplicação.
2. Localize o **permissões de aplicação direta** secção e, em seguida, adicione as permissões que requer a sua aplicação.
3. **Guardar** o registo de aplicação.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Recomendado: Sessão do utilizador para a sua aplicação
Normalmente, quando criar uma aplicação que utiliza as permissões de aplicação, a aplicação necessita de uma vista em que o administrador aprova permissões de uma aplicação ou página. Nesta página pode fazer parte início de sessão da aplicação fluxo do, parte das definições da aplicação, ou pode ser um fluxo de "estabelecer a ligação" dedicado. Em muitos casos, faz sentido para a aplicação mostrar isto "ligar" vista apenas depois de um utilizador tem sessão iniciada com uma empresa ou escola conta Microsoft.

Se a sessão do utilizador para a sua aplicação, pode identificar a organização a que o utilizador pertence antes de pedir ao utilizador para aprovar as permissões de aplicação. Apesar de não estritamente necessários, pode ajudar a criar uma experiência mais intuitiva para os seus utilizadores. Para iniciar a sessão do utilizador no, siga a nossa [tutoriais de protocolo de v 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Pedir as permissões de um administrador de diretório
Quando estiver pronto para solicitar permissões de administrador da organização, pode redirecionar o utilizador para a v 2.0 *ponto final de consentimento de administração*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |O inquilino de diretório que pretende pedir permissão a partir da. Isto pode ser no formato de nome amigável ou de GUID. Se não souber qual inquilino, o utilizador pertence a e pretender que a informá-los para iniciar sessão com qualquer inquilino, utilize `common`. |
| client_id |Necessário |ID de aplicação que o [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuída à aplicação. |
| redirect_uri |Necessário |O URI de redirecionamento do onde pretende que a resposta sejam enviados para a sua aplicação processar. Deve corresponder exatamente um do redirecionamento de URIs registados no portal, exceto que tem de ser codificado de URL e pode ter segmentos de caminhos adicionais. |
| state |Recomendado |Um valor que está incluído no pedido que também é devolvido na resposta token. Pode ser uma cadeia de qualquer conteúdo que pretende. O estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |

Neste momento, o Azure AD impõe que apenas um administrador de inquilino pode iniciar sessão para concluir o pedido. O administrador será pedido para aprovar todas as permissões de aplicação direta que pediu para a sua aplicação no portal de registo de aplicação.

##### <a name="successful-response"></a>Resposta com êxito
Se o administrador aprova as permissões para a sua aplicação, a resposta com êxito este aspeto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- | --- |
| Inquilino |O inquilino de diretório que concedidas as permissões que pediu, no formato GUID a sua aplicação. |
| state |Um valor que está incluído no pedido que também é devolvido na resposta token. Pode ser uma cadeia de qualquer conteúdo que pretende. O estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |
| admin_consent |Definido como **verdadeiro**. |

##### <a name="error-response"></a>Resposta de erro
Se o administrador não aprovar as permissões para a sua aplicação, a resposta de falha este aspeto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros, e que pode utilizar para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro. |

Depois de ter recebeu uma resposta com êxito o ponto de final de aprovisionamento de aplicações, a aplicação tem adquiridos as permissões de aplicação direta pedidos. Agora pode pedir um token para o recurso que pretende.

## <a name="get-a-token"></a>Obter um token
Depois de ter adquirido a autorização necessária para a sua aplicação, prossiga com a aquisição de tokens de acesso para APIs. Para obter um token, utilizando o cliente de concessão de credenciais, enviar um pedido POST para o `/token` ponto final v 2.0:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro maiúsculas e minúsculas: pedido de token de acesso com um segredo partilhado

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| client_id |Necessário |ID de aplicação que o [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuída à aplicação. |
| Âmbito |Necessário |O valor transmitido para o `scope` parâmetro neste pedido deve ser o identificador de recurso (URI de ID de aplicação) do recurso que pretende, affixed com o `.default` sufixo. Por exemplo o Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. Este valor informa o ponto final v 2.0 que de todas as aplicações direta permissões que tiver configurado para a sua aplicação, deverá emitir um token para que estão associados o recursos que pretende utilizar. |
| client_secret |Necessário |O segredo de aplicação gerados para a sua aplicação no portal de registo de aplicação. |
| grant_type |Necessário |Tem de ser `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Segunda caso: pedido de token de acesso com um certificado

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| client_id |Necessário |ID de aplicação que o [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuída à aplicação. |
| Âmbito |Necessário |O valor transmitido para o `scope` parâmetro neste pedido deve ser o identificador de recurso (URI de ID de aplicação) do recurso que pretende, affixed com o `.default` sufixo. Por exemplo o Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. Este valor informa o ponto final v 2.0 que de todas as aplicações direta permissões que tiver configurado para a sua aplicação, deverá emitir um token para que estão associados o recursos que pretende utilizar. |
| client_assertion_type |Necessário |O valor tem de ser`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Necessário | Uma asserção (um JSON Web Token) que precisa para criar e assinar com o certificado é registado como as credenciais para a sua aplicação. Leia sobre [credenciais de certificado](active-directory-certificate-credentials.md) para aprender a registar o certificado e o formato da asserção.|
| grant_type |Necessário |Tem de ser `client_credentials`. |

Tenha em atenção que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado com a exceção que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

### <a name="successful-response"></a>Resposta com êxito
Uma resposta com êxito tem o seguinte aspeto:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de pedido de acesso. A aplicação pode utilizar este token para se autenticar o recursos protegidos, tal como para uma API Web. |
| token_type |Indica o valor de tipo de token. O único tipo que suporta do Azure AD é `bearer`. |
| expires_in |Quanto o token de acesso é válido (em segundos). |

### <a name="error-response"></a>Resposta de erro
Uma resposta de erro tem o seguinte aspeto:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros ocorridos e para reagir a erros. |
| error_description |Uma mensagem de erro específicas que poderá ajudar a identificar a causa de raiz de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro de STS específicas que podem ajudar a obter um diagnóstico. |
| carimbo de data/hora |A hora em que ocorreu o erro. |
| trace_id |Um identificador exclusivo para o pedido que pode ajudar a obter um diagnóstico. |
| correlation_id |Um identificador exclusivo para o pedido que pode ajudar a obter um diagnóstico componentes. |

## <a name="use-a-token"></a>Utilizar um token
Agora que já adquirir um token, utilize o token para fazer pedidos para o recurso. Quando o token expira, repita o pedido para o `/token` ponto final para adquirir um token de acesso de raiz.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Exemplo de código
Para ver um exemplo de uma aplicação que implementa as credenciais do cliente conceder utilizando a administração consentimento ponto final, consulte a nossa [exemplo de código do daemon de v 2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
