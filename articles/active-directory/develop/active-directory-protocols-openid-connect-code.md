---
title: "Compreender o fluxo de código de autenticação OpenID Connect no Azure AD | Microsoft Docs"
description: "Este artigo descreve como utilizar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino utilizando o Azure Active Directory e o OpenID Connect."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1bb944997caa0c43354e82bf9b1a70e3e104a476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# Autorizar o acesso a aplicações web com OpenID Connect e o Azure Active Directory
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) é uma camada de identidade simples desenvolvida com o protocolo OAuth 2.0. OAuth 2.0 define os mecanismos de obter e utilizar **tokens de acesso** para aceder a recursos protegidos, mas não definir métodos padrão para fornecer informações de identidade. OpenID Connect implementa a autenticação como uma extensão para o processo de autorização do OAuth 2.0. Fornece informações sobre o utilizador final sob a forma de um `id_token` que verifica a identidade do utilizador e fornece informações de perfil básicas sobre o utilizador.

OpenID Connect é a nossa recomendação se está a criar uma aplicação web que está alojada num servidor e acessível através de um browser.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## Fluxo de autenticação com OpenID Connect
O fluxo de início de sessão mais básico contém os seguintes passos - cada um deles é descrita detalhadamente abaixo.

![OpenId Connect fluxo de autenticação](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## Documento de metadados OpenID Connect

OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para uma aplicação efetuar o início de sessão. Isto inclui informações como os URL a utilizar e a localização de chaves públicas de assinatura do serviço. O documento de metadados OpenID Connect pode ser encontrado em:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Os metadados são um documento de JavaScript Object Notation (JSON) simples. Consulte o fragmento seguinte para obter um exemplo. Conteúdo do fragmento é totalmente descrito no [OpenID Connect especificação](https://openid.net).

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## Enviar o pedido de início de sessão
Quando a aplicação web tem de autenticar o utilizador, tem a direcionar o utilizador a `/authorize` ponto final. Este pedido é semelhante para a primeira fase do [fluxo de código do OAuth 2.0 autorização](active-directory-protocols-oauth-code.md), com algumas distinctions importantes:

* O pedido tem de incluir o âmbito `openid` no `scope` parâmetro.
* O `response_type` parâmetro tem de incluir `id_token`.
* O pedido tem de incluir o `nonce` parâmetro.

Para que um pedido de exemplo seria ter este aspeto:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de inquilino independentes |
| client_id |Necessário |O Id de aplicação atribuída à aplicação quando é registado com o Azure AD. Pode encontrá-lo no Portal do Azure. Clique em **do Azure Active Directory**, clique em **registos de aplicação**, escolha a aplicação e localize o Id da aplicação na página da aplicação. |
| response_type |Necessário |Tem de incluir `id_token` para OpenID Connect início de sessão.  Poderão incluir outras response_types, tais como `code`. |
| Âmbito |Necessário |Uma lista separada por espaço de âmbitos.  Para OpenID Connect, tem de incluir o âmbito `openid`, que traduz-se a permissão "Iniciar sessão" no consentimento IU.  Também pode incluir outros âmbitos neste pedido para pedir consentimento. |
| nonce |Necessário |Um valor incluído no pedido de gerado pela aplicação, o que está incluída na resultante `id_token` como uma afirmação.  A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição de token.  O valor é normalmente uma cadeia de aleatório, exclusiva ou o GUID que pode ser utilizado para identificar a origem do pedido. |
| redirect_uri |recomendado |Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação.  Este deve corresponder exatamente um dos redirect_uris registado no portal, exceto tem de ser codificados de url. |
| response_mode |recomendado |Especifica o método que deve ser utilizado para enviar o authorization_code resultante para a sua aplicação.  Os valores suportados são `form_post` para *HTTP post de formulário* ou `fragment` para *fragmento de URL*.  Para aplicações web, recomendamos que utilize `response_mode=form_post` para garantir que a transferência mais segura de tokens para a aplicação. |
| state |recomendado |Um valor incluído no pedido de que é devolvido na resposta token.  Pode ser uma cadeia de todos os conteúdos que pretende.  Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de pedidos entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |
| linha de comandos |Opcional |Indica o tipo de interação do utilizador que é necessário.  Atualmente, os únicos valores válidos são 'início de sessão', 'none' e 'consentimento'.  `prompt=login`força o utilizador introduza as suas credenciais nesse pedido, a negação início de sessão único.  `prompt=none`é o oposto - assegura que o utilizador não é apresentado com qualquer linha de comandos interativa contratutal.  Se o pedido não é possível concluir automaticamente através de início de sessão único, o ponto final devolve um erro.  `prompt=consent`Depois do utilizador inicia sessão, solicitando ao utilizador para conceder permissões para a aplicação de acionadores a OAuth consentimento caixa de diálogo. |
| login_hint |Opcional |Pode ser utilizado para a pré-preencher o campo de endereço de e-mail/nome de utilizador da página de início de sessão do utilizador, se souber o nome de utilizador antecedência.  Aplicações, muitas vezes, utilizam este parâmetro durante a reautenticação rápida, já ter extraiu o nome de utilizador de uma anterior início de sessão utilizando o `preferred_username` de afirmação. |

Neste momento, é pedido ao utilizador para introduzir as suas credenciais e concluir a autenticação.

### Resposta de amostra
Uma resposta de exemplo, depois do utilizador foi autenticado, pode ter o seguinte aspeto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que a aplicação pedida. Pode utilizar o `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| state |Um valor incluído no pedido de que também é devolvido na resposta token. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de pedidos entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |

### Resposta de erro
As respostas de erro também podem ser enviadas para o `redirect_uri` para a aplicação pode processar corretamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros ocorridos e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |

#### Códigos de erro para erros de ponto final de autorização
A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro.

| Código de erro | Descrição | Ação de cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário em falta. |Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento e, normalmente é detetada durante o teste inicial. |
| unauthorized_client |A aplicação de cliente não é permitida para pedir um código de autorização. |Normalmente, isto ocorre quando a aplicação de cliente não está registada no Azure AD ou não é adicionada ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo para o Azure AD. |
| ACCESS_DENIED |Proprietário do recurso negado consentimento |A aplicação cliente pode notificar o utilizador que não é possível continuar a menos que o utilizador permitir. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento e, normalmente é detetada durante o teste inicial. |
| server_error |O servidor encontrou um erro inesperado. |Repita o pedido. Estes erros poderá resultar de condições temporárias. A aplicação cliente pode explicar ao utilizador que a respetiva resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor estiver temporariamente demasiado ocupado para processar o pedido. |Repita o pedido. A aplicação cliente pode explicar ao utilizador que a respetiva resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo para o Azure AD. |

## Validar o id_token
Receber apenas um `id_token` não é suficiente para autenticar o utilizador; tem de validar a assinatura e certifique-se as afirmações no `id_token` por requisitos da sua aplicação. O ponto final do Azure AD utiliza JSON Web Tokens (JWTs) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Pode optar por validar o `id_token` cliente código, mas uma prática comum consiste em enviar o `id_token` para um servidor de back-end e efetuar a validação não existe. Depois de ter confirmado a assinatura do `id_token`, existem alguns afirmações são necessárias para verificar.

Também pode pretender validar afirmações adicionais dependendo do seu cenário. Alguns validações comuns incluem:

* Garantir que a organização/utilizador foi inscrito para a aplicação.
* Garantir que o utilizador não tem privilégios/autorização adequados
* Garantir que um determinada força de autenticação tiver ocorrido, tais como a autenticação multifator.

Depois de validar o `id_token`, pode iniciar uma sessão com o utilizador e utilizar as afirmações no `id_token` para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para apresentar, registos, autorizações, etc. Para obter mais informações sobre os tipos de tokens e afirmações, leia o artigo [suportado Token e tipos de afirmação](active-directory-token-and-claims.md).

## Enviar um pedido de início de sessão
Quando pretender assinar o utilizador fora da aplicação, não é suficiente limpar os cookies da sua aplicação ou fim; caso contrário, a sessão com o utilizador.  Também tem de redirecionar o utilizador a `end_session_endpoint` para início de sessão.  Se conseguir fazê-lo, o utilizador será capaz de voltar à sua aplicação, sem introduzir as respetivas credenciais novamente, porque terá uma único início de sessão sessão válido com o ponto final do Azure AD.

Pode simplesmente redirecionar o utilizador a `end_session_endpoint` listados no documento de metadados de OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| post_logout_redirect_uri |recomendado |O URL que o utilizador deve ser redirecionado para depois de terminar com êxito.  Se não incluída, o utilizador é apresentado uma mensagem genérica. |

## Fim de sessão único
Quando lhe redireciona o utilizador para o `end_session_endpoint`, do Azure AD limpa a sessão do utilizador do browser. No entanto, o utilizador ainda pode ser iniciado outras aplicações que utilizam o Azure AD para autenticação. Para ativar as aplicações para o utilizador terminar sessão em simultâneo, o Azure AD envia um pedido de HTTP GET para o registado `LogoutUrl` de todas as aplicações que o utilizador tem atualmente sessão iniciado para. As aplicações devem responder para este pedido por qualquer sessão que identifica o utilizador de limpeza e devolver um `200` resposta.  Se pretender suportar terminar o início de sessão único na sua aplicação, tem de implementar como uma `LogoutUrl` no código da aplicação.  Pode definir o `LogoutUrl` do portal do Azure:

1. Navegue para o [Portal do Azure](https://portal.azure.com).
2. Escolha o Active Directory, clicando na sua conta no canto superior direito da página.
3. No painel de navegação esquerda, escolha **do Azure Active Directory**, em seguida, escolha **registos de aplicação** e selecione a aplicação.
4. Clique em **propriedades** e localize o **URL de fim de sessão** caixa de texto. 

## Aquisição do token
Muitas aplicações web necessitam não só a inicie a sessão do utilizador no, mas também aceder a um serviço web em nome de utilizador com OAuth. Este cenário combina OpenID Connect para a autenticação de utilizador ao adquirir em simultâneo um `authorization_code` que podem ser utilizados para obter `access_tokens` utilizando o fluxo de código de autorização do OAuth.

## Obter os Tokens de acesso
Para adquirir tokens de acesso, terá de modificar o pedido de início de sessão do acima:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Incluindo âmbitos de permissões no pedido e utilizando `response_type=code+id_token`, a `authorize` endpoint assegura que o utilizador consentiu as permissões indicadas a `scope` parâmetro de consulta e devolver um código de autorização para exchange para a sua aplicação um token de acesso.

### Resposta com êxito
Uma resposta com êxito utilizando `response_mode=form_post` parece ser:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que a aplicação pedida. Pode utilizar o `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| código |Authorization_code que a aplicação pedida. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino. Authorization_codes são curto lived e normalmente expiram após cerca de 10 minutos. |
| state |Se um parâmetro de estado está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deverá certificar-se de que os valores de estado no pedido e resposta são idênticos. |

### Resposta de erro
As respostas de erro também podem ser enviadas para o `redirect_uri` para a aplicação pode processar corretamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros ocorridos e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |

Para obter uma descrição dos códigos de erro possíveis e a ação de cliente recomendada, consulte [códigos de erro para erros de ponto final de autorização](#error-codes-for-authorization-endpoint-errors).

Assim que tiver foi obtido uma autorização `code` e um `id_token`, pode sessão do utilizador e obter tokens de acesso em nome daqueles.  Para iniciar a sessão do utilizador no, é necessário validar o `id_token` exatamente conforme descrito acima. Para obter os tokens de acesso, pode seguir os passos descritos na secção "Utilizar o código de autorização para pedir um token de acesso" do nosso [documentação do protocolo de OAuth](active-directory-protocols-oauth-code.md).
