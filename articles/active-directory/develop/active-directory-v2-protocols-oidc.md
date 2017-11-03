---
title: Azure Active Directory v 2.0 e o protocolo OpenID Connect | Microsoft Docs
description: "Crie aplicações web, utilizando a implementação de v 2.0 do Azure AD do protocolo de autenticação OpenID Connect."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: d63692f02b3dec50a1e7df034b8915bb450b4cfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# Azure Active Directory v 2.0 e o protocolo OpenID Connect
OpenID Connect é um protocolo de autenticação incorporado no OAuth 2.0, que pode utilizar a sessão em segurança um utilizador a uma aplicação web. Quando utilizar a implementação do ponto final v 2.0 do OpenID Connect, pode adicionar início de sessão e acesso à API às suas aplicações baseadas na web. Neste artigo, vamos mostrar-lhe como efetuar esta independente de idioma. Iremos descrevem como enviar e receber mensagens HTTP sem utilizar quaisquer bibliotecas de open source de Microsoft.

> [!NOTE]
> O ponto final v 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades. Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) expande o OAuth 2.0 *autorização* protocolo a utilizar como um *autenticação* protocolo, para que possam executar único início de sessão OAuth a utilizar. OpenID Connect apresenta o conceito de uma *ID token*, que é um token de segurança que permite que o cliente verificar a identidade do utilizador. O token de ID também obtém informações de perfil básicas sobre o utilizador. Porque o OpenID Connect expande o OAuth 2.0, aplicações em segurança podem adquirir *tokens de acesso*, que podem ser utilizadas para aceder a recursos que estão protegidos por um [servidor autorização](active-directory-v2-protocols.md#the-basics). Recomendamos que utilize OpenID Connect, se está a criar um [aplicação web](active-directory-v2-flows.md#web-apps) que está alojada num servidor e acessível através de um browser.

## Diagrama de protocolo: início de sessão
O fluxo de início de sessão mais básico tem passos apresentados no diagrama seguinte. Iremos descrevem cada passo em pormenor neste artigo.

![Protocolo OpenID Connect: início de sessão](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Obter documento de metadados OpenID Connect
OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para uma aplicação efetuar o início de sessão. Isto inclui informações como os URL a utilizar e a localização de chaves públicas de assinatura do serviço. Para o ponto final v 2.0, este é o documento de metadados OpenID Connect que deve utilizar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

O `{tenant}` pode efetuar uma das quatro valores:

| Valor | Descrição |
| --- | --- |
| `common` |Os utilizadores com uma conta Microsoft pessoal e uma conta escolar ou profissional do Azure Active Directory (Azure AD) podem iniciar sessão aplicação. |
| `organizations` |Apenas os utilizadores com escolar ou profissional as contas do Azure AD podem iniciar sessão na aplicação. |
| `consumers` |Apenas os utilizadores com uma conta Microsoft pessoal podem iniciar sessão para a aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` |Apenas os utilizadores com uma conta escolar ou profissional de um específico do Azure AD inquilino pode iniciar sessão para a aplicação. O nome de domínio amigável dos inquilino do Azure AD ou identificador GUID do inquilino pode ser utilizado. |

Os metadados são um documento de JavaScript Object Notation (JSON) simples. Consulte o fragmento seguinte para obter um exemplo. Conteúdo do fragmento é totalmente descrito no [OpenID Connect especificação](https://openid.net).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Normalmente, utilizaria este documento de metadados para configurar um SDK; ou biblioteca de OpenID Connect a biblioteca teria de utilizar os metadados para realizar o seu trabalho. No entanto, se não estiver a utilizar uma biblioteca de pré-compilação de OpenID Connect, pode seguir os passos no resto deste artigo para efetuar início de sessão numa aplicação web utilizando o ponto final v 2.0.

## Enviar o pedido de início de sessão
Quando a aplicação web tem de autenticar o utilizador, pode direcionar o utilizador para o `/authorize` ponto final. Este pedido é semelhante para a primeira fase do [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md), com estes distinctions importantes:

* O pedido tem de incluir o `openid` âmbito no `scope` parâmetro.
* O `response_type` parâmetro tem de incluir `id_token`.
* O pedido tem de incluir o `nonce` parâmetro.

Por exemplo:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Clique na hiperligação seguinte para executar este pedido. Depois de iniciar sessão, o seu browser será redirecionado para https://localhost/myapp/, com um token de ID na barra de endereço. Tenha em atenção que este pedido utiliza `response_mode=query` (para fins de demonstração apenas). Recomendamos que utilize `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>
> 
> 

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |Pode utilizar o `{tenant}` valor no caminho do pedido para controlar quem pode iniciar sessão para a aplicação. Os valores permitidos são `common`, `organizations`, `consumers`e inquilinos identificadores. Para obter mais informações, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |Necessário |ID de aplicação que o [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuída à aplicação. |
| response_type |Necessário |Tem de incluir `id_token` para OpenID Connect início de sessão. Também pode incluir outros `response_types` valores, tais como `code`. |
| redirect_uri |Recomendado |O URI de redirecionamento da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação. Este deve corresponder exatamente um do redirecionamento de URIs registados no portal, exceto que tem de ser codificado de URL. |
| Âmbito |Necessário |Uma lista separada por espaço de âmbitos. Para OpenID Connect, tem de incluir o âmbito `openid`, que traduz-se a permissão "Iniciar sessão" no consentimento IU. Também pode incluir outros âmbitos neste pedido para pedir consentimento. |
| nonce |Necessário |Um valor incluído no pedido de gerado pela aplicação, que será incluída no valor de id_token resultante como uma afirmação. A aplicação pode verificar este valor para mitigar ataques de repetição de token. O valor é normalmente uma cadeia de aleatório, exclusiva que pode ser utilizada para identificar a origem do pedido. |
| response_mode |Recomendado |Especifica o método que deve ser utilizado para enviar o código de autorização resultante para a sua aplicação. Pode ser um dos `query`, `form_post`, ou `fragment`. Para aplicações web, recomendamos que utilize `response_mode=form_post`, para garantir que a transferência mais segura de tokens para a aplicação. |
| state |Recomendado |Um valor incluído no pedido de que também vai ser devolvido na resposta token. Pode ser uma cadeia de qualquer conteúdo que pretende. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de pedidos entre sites](http://tools.ietf.org/html/rfc6749#section-10.12). O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista que do utilizador estava no. |
| linha de comandos |Opcional |Indica o tipo de interação do utilizador que é necessário. São os únicos valores válidos neste momento `login`, `none`, e `consent`. O `prompt=login` afirmação força o utilizador introduza as suas credenciais nesse pedido, o qual negates o início de sessão único. O `prompt=none` afirmação é o oposto. Esta afirmação assegura que o utilizador não é apresentado com qualquer linha de comandos interativa contratutal. Se o pedido não é possível concluir automaticamente através de um início de sessão único, o ponto final v 2.0 devolve um erro. O `prompt=consent` afirmação aciona a caixa de diálogo de consentimento do OAuth depois do utilizador inicia sessão. A caixa de diálogo pede ao utilizador para conceder permissões para a aplicação. |
| login_hint |Opcional |Pode utilizar este parâmetro para pré-preencher o campo de endereço de e-mail e nome de utilizador da página de início de sessão do utilizador, se souber o nome de utilizador antecedência. Muitas vezes, as aplicações utilizam este parâmetro durante a reautenticação, após já extrair o nome de utilizador de um anterior início de sessão utilizando o `preferred_username` de afirmação. |
| domain_hint |Opcional |Este valor pode ser `consumers` ou `organizations`. Se incluído, ignora o processo de deteção baseada em e-mail que o utilizador atravessa a v 2.0-na página sessão, para uma experiência de utilizador ligeiramente mais simplificada. Muitas vezes, as aplicações utilizam este parâmetro durante a reautenticação, a extrair o `tid` afirmações do ID token. Se o `tid` é o valor de afirmação `9188040d-6c67-4c5b-b112-36a304b66dad`, utilize `domain_hint=consumers`. Caso contrário, utilize `domain_hint=organizations`. |

Neste momento, é pedido ao utilizador para introduzir as suas credenciais e concluir a autenticação. O ponto final v 2.0 verifica que o utilizador consentiu as permissões indicadas a `scope` parâmetro de consulta. Se o utilizador não se consentiu para qualquer um dessas permissões, o ponto final v 2.0 pede ao utilizador consentimento para as permissões necessárias. Pode ler mais sobre [permissões, consentimento e aplicações multi-inquilino](active-directory-v2-scopes.md).

Depois do utilizador efetua a autenticação e atribui o consentimento, o ponto final v 2.0 devolve uma resposta para a sua aplicação do indicado no URI de redirecionamento, utilizando o método especificado no `response_mode` parâmetro.

### Resposta com êxito
Uma resposta com êxito ao utilizar `response_mode=form_post` se parece com isto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que a aplicação pedida. Pode utilizar o `id_token` parâmetro para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais detalhes sobre os tokens de ID e os respetivos conteúdos, consulte o [ponto final v 2.0 tokens referência](active-directory-v2-tokens.md). |
| state |Se um `state` parâmetro está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deverá certificar-se de que os valores de estado no pedido e resposta são idênticos. |

### Resposta de erro
As respostas de erro também poderão ser enviadas para o URI de redirecionamento, para que a aplicação pode processá-los. Uma resposta de erro tem o seguinte aspeto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros ocorridos e para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

### Códigos de erro para erros de ponto final de autorização
A tabela seguinte descreve os códigos de erro que podem ser devolvidos no `error` parâmetro da resposta ao erro:

| Código de erro | Descrição | Ação de cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um em falta, necessário parâmetro. |Corrija e submeta novamente o pedido. Este é um erro de programação que normalmente é detetado durante o teste inicial. |
| unauthorized_client |A aplicação de cliente não é possível pedir um código de autorização. |Normalmente, isto ocorre quando a aplicação de cliente não está registada no Azure AD ou não é adicionada ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com as instruções para instalar a aplicação e adicione-o para o Azure AD. |
| ACCESS_DENIED |O proprietário do recurso negado consentimento. |A aplicação cliente pode notificar o utilizador que não é possível continuar a menos que o utilizador permitir. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e submeta novamente o pedido. Este é um erro de programação que normalmente é detetado durante o teste inicial. |
| server_error |O servidor encontrou um erro inesperado. |Repita o pedido. Estes erros poderá resultar de condições temporárias. A aplicação cliente pode explicar ao utilizador que a respetiva resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor estiver temporariamente demasiado ocupado para processar o pedido. |Repita o pedido. A aplicação cliente pode explicar ao utilizador que a respetiva resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo, ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com as instruções para instalar a aplicação e adicioná-lo para o Azure AD. |

## Validar o token de ID
Não é suficiente para autenticar o utilizador recebe um token de ID. Também tem de validar a assinatura do token de ID e certifique-se as afirmações no token por requisitos da sua aplicação. Utiliza o ponto final v 2.0 [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Pode optar por validar o token do ID no código de cliente, mas é uma prática comum para enviar o token de ID para um servidor de back-end e efetuar a validação não existe. Depois de ter confirmado a assinatura do token de ID, terá de verificar alguns afirmações. Para obter mais informações, incluindo informações sobre [tokens de validação](active-directory-v2-tokens.md#validating-tokens) e [informações importantes sobre o rollover da chave de assinatura](active-directory-v2-tokens.md#validating-tokens), consulte o [v 2.0 tokens referência](active-directory-v2-tokens.md). Recomendamos que utilize uma biblioteca para analisar e validar os tokens. Não há, pelo menos, um destas bibliotecas disponíveis para a maioria dos idiomas e plataformas.
<!--TODO: Improve the information on this-->

Pode também querer validar afirmações adicionais, dependendo do seu cenário. Alguns validações comuns incluem:

* Certifique-se de que o utilizador ou a organização tiver inscrito para a aplicação.
* Certifique-se de que foi pedido ao utilizador privilégios ou de autorização.
* Certifique-se de que um determinada força de autenticação ocorreu, tais como a autenticação multifator.

Para obter mais informações sobre as afirmações num ID token, consulte o [ponto final v 2.0 tokens referência](active-directory-v2-tokens.md).

Depois de ter completamente validar o token de ID, pode iniciar uma sessão com o utilizador. Utilize as afirmações no ID token para obter informações sobre o utilizador na sua aplicação. Pode utilizar estas informações para apresentar, registos, autorizações e assim sucessivamente.

## Enviar um pedido de início de sessão
Quando se pretende terminar sessão de utilizador da sua aplicação, esta não é suficiente para limpar os cookies da sua aplicação ou caso contrário terminar a sessão do utilizador. Também tem de redirecionar o utilizador para o ponto final v 2.0 para terminar sessão. Se não fizer isto, o utilizador novamente autentica para a sua aplicação sem introduzir as respetivas credenciais novamente, porque terá uma válido único início de sessão com o ponto final v 2.0.

Pode redirecionar o utilizador a `end_session_endpoint` listados no documento de metadados de OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parâmetro | Condição | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Recomendado | O URL que o utilizador é redirecionado para depois de terminar com êxito. Se o parâmetro não for incluído, o utilizador é apresentado uma mensagem genérica que é gerada pelo ponto final v 2.0. Este URL tem de corresponder a um redirecionamento que URIs registados para a sua aplicação no portal de registo de aplicação.  |

## Fim de sessão único
Quando lhe redireciona o utilizador para o `end_session_endpoint`, o ponto final v 2.0 limpa a sessão do utilizador do browser. No entanto, o utilizador ainda pode ser iniciado outras aplicações que utilizem contas Microsoft para autenticação. Para ativar as aplicações iniciar o utilizador terminar em simultâneo, a v 2.0 o ponto final envia um pedido de HTTP GET para o registado `LogoutUrl` de todas as aplicações que o utilizador tem atualmente sessão iniciado para. As aplicações devem responder para este pedido por qualquer sessão que identifica o utilizador de limpeza e devolver um `200` resposta.  Se pretender suportar terminar o início de sessão único na sua aplicação, tem de implementar como uma `LogoutUrl` no código da aplicação.  Pode definir o `LogoutUrl` partir do portal de registo da aplicação.

## Diagrama de protocolo: aquisição do Token
Muitas aplicações web tem de assinar não só o utilizador no, mas também para aceder a um serviço web em nome do utilizador através da utilização de OAuth. Este cenário combina OpenID Connect para a autenticação de utilizador ao obter simultaneamente um código de autorização que pode utilizar para obter os tokens de acesso, se estiver a utilizar o fluxo de código de autorização do OAuth.

O fluxo de aquisição de início de sessão e token OpenID Connect completo semelhante ao seguinte diagrama. Iremos descrevem cada passo em detalhe nas secções seguintes do artigo.

![Protocolo OpenID Connect: aquisição do Token](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## Obter os tokens de acesso
Para adquirir tokens de acesso, modifique o pedido de início de sessão:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Clique na hiperligação seguinte para executar este pedido. Depois de iniciar sessão, o browser é redirecionado para https://localhost/myapp/, com um token de ID e um código na barra de endereço. Tenha em atenção que este pedido utiliza `response_mode=query` (para fins de demonstração apenas). Recomendamos que utilize `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>
> 
> 

Incluindo os âmbitos de permissões no pedido e utilizando `response_type=id_token code`, o ponto final v 2.0 assegura que o utilizador consentiu as permissões indicadas a `scope` parâmetro de consulta. Devolve um código de autorização para a sua aplicação para um token de acesso do Exchange.

### Resposta com êxito
Uma resposta com êxito de utilizar `response_mode=form_post` se parece com isto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que a aplicação pedida. Pode utilizar o token de ID para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Irá encontrar mais detalhes sobre os tokens de ID e os respetivos conteúdos no [ponto final v 2.0 tokens referência](active-directory-v2-tokens.md). |
| código |O código de autorização que a aplicação pedida. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino. Um código de autorização é muito curta duração. Normalmente, um código de autorização expira dentro de 10 minutos. |
| state |Se um parâmetro de estado está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deverá certificar-se de que os valores de estado no pedido e resposta são idênticos. |

### Resposta de erro
As respostas de erro também poderão ser enviadas para o URI de redirecionamento, para que a aplicação pode processar corretamente. Uma resposta de erro tem o seguinte aspeto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros ocorridos e para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

Para obter uma descrição de códigos de erro possíveis e respostas de cliente recomendada, consulte [códigos de erro para erros de ponto final de autorização](#error-codes-for-authorization-endpoint-errors).

Quando tiver um código de autorização e um token de ID, pode sessão do utilizador e obter os tokens de acesso em nome daqueles. Para iniciar a sessão do utilizador no, é necessário validar o token de ID [exatamente conforme descrito](#validate-the-id-token). Para obter os tokens de acesso, siga os passos descritos na nossa [documentação do protocolo de OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).
