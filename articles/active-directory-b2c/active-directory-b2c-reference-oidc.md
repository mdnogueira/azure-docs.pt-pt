---
title: "Web de início de sessão com OpenID Connect - Azure AD B2C | Microsoft Docs"
description: "A criação de aplicações web, utilizando a implementação do Azure Active Directory do protocolo de autenticação OpenID Connect"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 21d420c8-3c10-4319-b681-adf2e89e7ede
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: b0c33a47dd0cae79eab32ac578448fae8bf59be5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>O Azure Active Directory B2C: Web início de sessão com OpenID Connect
OpenID Connect é um protocolo de autenticação, desenvolvido com OAuth 2.0, que podem ser utilizados de forma segura iniciar sessão utilizadores em aplicações web. Ao utilizar o Azure Active Directory B2C (Azure AD B2C) a implementação do OpenID Connect, pode outsource inscrição, início de sessão, e as experiências de outra gestão de identidades nas aplicações web no Azure Active Directory (Azure AD). Este guia mostra-lhe como fazê-lo de forma independente de idioma. Descreve como enviar e receber mensagens HTTP sem utilizar qualquer uma das nossas bibliotecas de open source.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) expande o OAuth 2.0 *autorização* protocolo para utilização como um *autenticação* protocolo. Isto permite-lhe executar o início de sessão único através do OAuth. Introduz o conceito de uma *ID token*, que é um token de segurança que permite que o cliente verificar a identidade do utilizador e obter informações de perfil básicas sobre o utilizador.

Porque expande o OAuth 2.0, também permite que aplicações de forma segura adquirir *tokens de acesso*. Pode utilizar access_tokens para aceder a recursos que estão protegidos por um [servidor autorização](active-directory-b2c-reference-protocols.md#the-basics). Recomendamos que OpenID Connect se estiver a criar uma aplicação web que está alojada num servidor e acessível através de um browser. Se pretender adicionar gestão de identidades às suas aplicações móveis ou de ambiente de trabalho utilizando o Azure AD B2C, deve utilizar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) em vez de OpenID Connect.

O Azure AD B2C expande o protocolo OpenID Connect padrão fazer mais do que simples autenticação e autorização. Introduz o [parâmetro política](active-directory-b2c-reference-policies.md), que permite-lhe utilizar o OpenID Connect para adicionar como experiências de utilizador - inscrição, início de sessão e gestão de perfis – à sua aplicação. Aqui, vamos mostrar-lhe como utilizar o OpenID Connect e políticas para implementar cada um destes experiências nas suas aplicações web. Iremos irá também mostram como obter os tokens de acesso para aceder a web APIs.

Utilizam os pedidos HTTP de exemplo na secção seguinte diretório do B2C nosso exemplo, fabrikamb2c.onmicrosoft.com, bem como nossa aplicação de exemplo, https://aadb2cplayground.azurewebsites.net e políticas. Estiver livre para experimentar os pedidos de si próprio ao utilizar estes valores, ou pode substituí-los com os seus próprios.
Saiba como [obter o seu próprio inquilino do B2C, a aplicação e políticas](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação
Quando a aplicação web precisa de uma política de execução e autenticar o utilizador, pode direcionar o utilizador para o `/authorize` ponto final. Esta é a parte interativa de fluxo, onde o utilizador executa a ação, consoante a política.

Este pedido, o cliente indica as permissões que necessita de adquirir do utilizador no `scope` parâmetro e a política para ser executado no `p` parâmetro. Exemplos de três são fornecidos nas secções seguintes (com quebras de linha para legibilidade), cada através de uma política diferente. Para obter uma funcionalidade para como funciona a cada pedido, tente colar o pedido para um browser e executá-lo.

#### <a name="use-a-sign-in-policy"></a>Utilizar uma política de início de sessão
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilizar uma política de inscrição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilizar uma política de perfil de edição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| client_id |Necessário |ID de aplicação que o [portal do Azure](https://portal.azure.com/) atribuída à aplicação. |
| response_type |Necessário |O tipo de resposta, tem de incluir um token de ID de OpenID Connect. Se a sua aplicação web tem também de tokens para chamar uma API web, pode utilizar `code+id_token`, tal como estamos tiver terminado aqui. |
| redirect_uri |Recomendado |O `redirect_uri` parâmetro da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação. Tem de corresponder exatamente um do `redirect_uri` parâmetros que registou no portal, exceto que tem de ser codificado de URL. |
| Âmbito |Necessário |Uma lista separada por espaço de âmbitos. Um valor único âmbito indica, para o Azure AD, ambas as permissões que estão a ser solicitadas. O `openid` âmbito indica uma permissão para iniciar o utilizador e obter dados sobre o utilizador no formato de tokens de identidade (mais fique neste mais tarde no artigo). O `offline_access` âmbito é opcional para aplicações web. Indica que a sua aplicação terá um *token de atualização* de longa duração acesso a recursos. |
| response_mode |Recomendado |O método que deve ser utilizado para enviar o código de autorização resultante de volta à aplicação. Pode ser um `query`, `form_post`, ou `fragment`.  O `form_post` modo de resposta é recomendado para melhor segurança. |
| state |Recomendado |Um valor incluído no pedido de que também é devolvido na resposta token. Pode ser uma cadeia de qualquer conteúdo que pretende. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para impedir ataques de falsificação de pedidos entre sites. O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página estivessem nas suas. |
| nonce |Necessário |Um valor incluído no pedido de (gerado pela aplicação) que será incluído no token resultante ID como uma afirmação. A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição de token. O valor é, geralmente, uma cadeia exclusiva aleatório que pode ser utilizada para identificar a origem do pedido. |
| P |Necessário |A política que será executada. É o nome de uma política que é criado no seu inquilino do B2C. O valor de nome de política deve iniciar com `b2c\_1\_`. Saiba mais sobre as políticas e o [estrutura de política extensível](active-directory-b2c-reference-policies.md). |
| linha de comandos |Opcional |O tipo de interação do utilizador que é necessário. O valor só é válido neste momento, é `login`, que força o utilizador introduza as suas credenciais desse pedido. O início de sessão único não entrarão em vigor. |

Neste momento, é pedido ao utilizador para concluir o fluxo de trabalho da política. Isto poderá envolver o utilizador introduzir o respetivo nome de utilizador e palavra-passe, início de sessão com uma identidade de redes social, inscrever-se para o diretório ou qualquer outro número de passos, dependendo de como a política está definida.

Depois do utilizador conclui a política, do Azure AD devolve uma resposta a sua aplicação no indicados `redirect_uri` parâmetro, utilizando o método especificado no `response_mode` parâmetro. A resposta é o mesmo para cada um dos casos anteriores, independentemente da política que é executada.

Uma resposta com êxito utilizando `response_mode=fragment` teria aspeto:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que a aplicação pedida. Pode utilizar o token de ID para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Obter mais detalhes sobre os tokens de ID e os respetivos conteúdos estão incluídos no [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| código |O código de autorização que a aplicação pedida, se tiver utilizado `response_type=code+id_token`. A aplicação pode utilizar o código de autorização para pedir um token de acesso para um recurso de destino. Códigos de autorização são muito curta duração. Normalmente, estas expiram após cerca de 10 minutos. |
| state |Se um `state` parâmetro está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que o `state` valores no pedido e resposta são idênticos. |

As respostas de erro também podem ser enviadas para o `redirect_uri` parâmetro para que a aplicação pode processar corretamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros que ocorrem e que podem ser utilizados para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |
| state |Consulte a descrição completa na primeira tabela nesta secção. Se um `state` parâmetro está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que o `state` valores no pedido e resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o token de ID
Apenas receber um token de ID não é suficiente para autenticar o utilizador. Tem de validar a assinatura do token de ID e certifique-se as afirmações no token por requisitos da sua aplicação. Azure AD B2C utiliza [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Existem muitos bibliotecas de open source que estão disponíveis para validar JWTs, consoante o idioma de preferência. Recomendamos a explorar a essas opções, em vez de implementar a sua própria lógica de validação. As informações aqui será útil perceber como corretamente utilizar as bibliotecas.

O Azure AD B2C tem um OpenID Connect ponto final de metadados, que permite a uma aplicação obter informações sobre o Azure AD B2C no tempo de execução. Estas informações incluem pontos finais, conteúdo de token e chaves de assinatura de tokens. Não há um documento de metadados JSON para cada política no seu inquilino do B2C. Por exemplo, o documento de metadados para o `b2c_1_sign_in` política no `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades deste documento de configuração é `jwks_uri`, cujo valor para a mesma política seria:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Para determinar qual a política foi utilizada um ID de assinatura do token (e a partir de onde obter os metadados), tem duas opções. Em primeiro lugar, o nome da política está incluído no `acr` no token de ID de afirmação. Para obter informações sobre como analisar as afirmações a partir de um token de ID, consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção consiste em codificar a política no valor do `state` parâmetro quando emitir o pedido e, em seguida, descodificar para determinar qual a política foi utilizada. O método é válido.

Depois de ter adquirido o documento de metadados a partir do ponto final de metadados OpenID Connect, pode utilizar as chaves públicas RSA 256 (que estão localizadas neste ponto final) para validar a assinatura do ID token. Podem existir várias chaves listadas neste ponto final em qualquer momento de determinado momento, cada identificado por um `kid` de afirmação. O cabeçalho do ID token também contém um `kid` de afirmação, que indica que estas chaves foi utilizado para assinar o token de ID. Para obter mais informações, consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md) (a secção em [tokens de validação](active-directory-b2c-reference-tokens.md#token-validation), em particular).
<!--TODO: Improve the information on this-->

Depois de ter confirmado a assinatura do token de ID, existem várias afirmações que precisa para verificar. Por exemplo:

* Deve validar o `nonce` impedir ataques de repetição de token de afirmação. O valor deve ser especificado no pedido de início de sessão.
* Deve validar o `aud` de afirmação para se certificar de que o token de ID foi emitido para a sua aplicação. O valor deve ser o ID da sua aplicação.
* Deve validar o `iat` e `exp` afirmações para se certificar de que o token de ID não expirou.

Também existem vários validações mais que deverá efetuar. Estas são descritas em detalhe no [OpenID Connect Core Spec](http://openid.net/specs/openid-connect-core-1_0.html).  Pode também querer validar afirmações adicionais, dependendo do seu cenário. Alguns validações comuns incluem:

* Garantir que o utilizador/organização assinou cópias de segurança para a aplicação.
* Garantir que o utilizador possui privilégios/autorização adequados.
* Garantir que um determinada força de autenticação ocorreu, tais como o Azure multi-factor Authentication.

Para obter mais informações sobre as afirmações num ID token, consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Depois de validar o token de ID, pode iniciar uma sessão com o utilizador. Pode utilizar as afirmações no ID token para obter informações sobre o utilizador na sua aplicação. Para obter estas informações utilizações autorização, regista e apresentar.

## <a name="get-a-token"></a>Obter um token
Se precisar da aplicação web para executar apenas políticas, pode ignorar as secções seguintes alguns. Estas secções são aplicáveis apenas a web aplicações que precisam para fazer chamadas a uma API web autenticadas e também estão protegidas pelo Azure AD B2C.

Pode resgatar o código de autorização que obteve (utilizando `response_type=code+id_token`) para obter um token para o recurso pretendido, enviando um `POST` pedido para o `/token` ponto final. Atualmente, o recurso só pode pedir um token para está a web de back-end da aplicação API. A Convenção de pedir um token a próprio consiste em utilizar o ID de cliente da sua aplicação como o âmbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| P |Necessário |A política que utilizou para adquirir o código de autorização. Não é possível utilizar uma política diferente neste pedido. Tenha em atenção que poderá adiciona este parâmetro para a cadeia de consulta, não ao `POST` corpo. |
| client_id |Necessário |ID de aplicação que o [portal do Azure](https://portal.azure.com/) atribuída à aplicação. |
| grant_type |Necessário |O tipo de concessão, que têm de ser `authorization_code` para que o fluxo de código de autorização. |
| Âmbito |Recomendado |Uma lista separada por espaço de âmbitos. Um valor único âmbito indica, para o Azure AD, ambas as permissões que estão a ser solicitadas. O `openid` âmbito indica uma permissão para iniciar o utilizador e obter dados sobre o utilizador no formato id_token parâmetros. Pode ser utilizado para obter os tokens para a web de back-end da aplicação API, que é representada pelo mesmo ID de aplicação que o cliente. O `offline_access` âmbito indica que a sua aplicação terá um token de atualização de longa duração acesso a recursos. |
| código |Necessário |O código de autorização que obteve na primeira parte do fluxo. |
| redirect_uri |Necessário |O `redirect_uri` parâmetro da aplicação em que foi recebido o código de autorização. |
| client_secret |Necessário |O segredo de aplicação gerados pelo [portal do Azure](https://portal.azure.com/). Este segredo de aplicação é um artefacto de segurança importantes. Deve armazená-lo em segurança no seu servidor. Também deve rodar Este segredo de cliente numa base periódica. |

Uma resposta de token com êxito se pareça com:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| --- | --- |
| not_before |A hora em que o token é considerado válido, no tempo de época. |
| token_type |O valor de tipo de token. O único tipo que suporta do Azure AD é `Bearer`. |
| access_token |O token JWT assinado que pediu. |
| Âmbito |Os âmbitos para a qual o token é válido. Estes podem ser utilizados para colocar em cache de tokens para utilização posterior. |
| expires_in |O período de tempo que o token de acesso é válido (em segundos). |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do token atual expira. Atualizar tokens são longa duração e pode ser utilizados para manter o acesso a recursos para períodos de tempo prolongados. Para obter mais detalhes, consulte o [referência de token de B2C](active-directory-b2c-reference-tokens.md). Tenha em atenção que tem de ter utilizou o âmbito `offline_access` na autorização e pedidos de token para receber um token de atualização. |

As respostas de erro ter o seguinte aspeto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros que ocorrem e que podem ser utilizados para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |

## <a name="use-the-token"></a>Utilizar o token
Agora que já obteve com êxito um token de acesso, pode utilizar o token de pedidos para o back-end APIs web, incluindo-na `Authorization` cabeçalho:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token
Os tokens de ID são de curta duração. Deve atualizá-las após expirarem para continuar a poder aceder aos recursos. Pode fazê-lo ao submeter outro `POST` pedido para o `/token` ponto final. Neste momento, forneça o `refresh_token` parâmetro em vez do `code` parâmetro:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Necessário | Descrição |
| --- | --- | --- |
| P |Necessário |A política que utilizou para adquirir o token de atualização original. Não é possível utilizar uma política diferente neste pedido. Tenha em atenção que o adicione este parâmetro para a cadeia de consulta, não para o corpo da mensagem. |
| client_id |Necessário |ID de aplicação que o [portal do Azure](https://portal.azure.com/) atribuída à aplicação. |
| grant_type |Necessário |O tipo de concessão, que têm de ser um token de atualização para esta parte do fluxo de código de autorização. |
| Âmbito |Recomendado |Uma lista separada por espaço de âmbitos. Um valor único âmbito indica, para o Azure AD, ambas as permissões que estão a ser solicitadas. O `openid` âmbito indica uma permissão para iniciar o utilizador e obter dados sobre o utilizador no formato de tokens de identidade. Pode ser utilizado para obter os tokens para a web de back-end da aplicação API, que é representada pelo mesmo ID de aplicação que o cliente. O `offline_access` âmbito indica que a sua aplicação terá um token de atualização de longa duração acesso a recursos. |
| redirect_uri |Recomendado |O `redirect_uri` parâmetro da aplicação em que foi recebido o código de autorização. |
| refresh_token |Necessário |O token de atualização original que obteve na segunda parte do fluxo. Tenha em atenção que tem de ter utilizou o âmbito `offline_access` na autorização e pedidos de token para receber um token de atualização. |
| client_secret |Necessário |O segredo de aplicação gerados pelo [portal do Azure](https://portal.azure.com/). Este segredo de aplicação é um artefacto de segurança importantes. Deve armazená-lo em segurança no seu servidor. Também deve rodar Este segredo de cliente numa base periódica. |

Uma resposta de token com êxito se pareça com:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| --- | --- |
| not_before |A hora em que o token é considerado válido, no tempo de época. |
| token_type |O valor de tipo de token. O único tipo que suporta do Azure AD é `Bearer`. |
| access_token |O token JWT assinado que pediu. |
| Âmbito |O âmbito do que o token é válido, que pode ser utilizado para colocar em cache de tokens para utilização posterior. |
| expires_in |O período de tempo que o token de acesso é válido (em segundos). |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do token atual expira.  Atualizar tokens são longa duração e pode ser utilizados para manter o acesso a recursos para períodos de tempo prolongados. Para obter mais detalhes, consulte o [referência de token de B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro ter o seguinte aspeto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros que ocorrem e que podem ser utilizados para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |

## <a name="send-a-sign-out-request"></a>Enviar um pedido de início de sessão
Quando pretender assinar o utilizador fora da aplicação, não é suficiente limpar os cookies da sua aplicação ou fim; caso contrário, a sessão com o utilizador. Também tem de redirecionar o utilizador para o Azure AD para terminar sessão. Se conseguir fazê-lo, o utilizador poderá voltar à sua aplicação, sem introduzir as respetivas credenciais novamente. Isto acontece porque terá uma único início de sessão sessão válido com o Azure AD.

Pode simplesmente redirecionar o utilizador a `end_session` ponto final que está listado no documento de metadados de OpenID Connect descrito anteriormente no "validar o token de ID" secção:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| P |Necessário |A política que pretende utilizar para assinar o utilizador fora da sua aplicação. |
| post_logout_redirect_uri |Recomendado |O URL que o utilizador deve ser redirecionado para após êxito fim de sessão. Se não estiver incluída, o Azure AD B2C mostra ao utilizador uma mensagem genérica. |

> [!NOTE]
> Embora instruir o utilizador a `end_session` endpoint limpará algumas do Estado do utilizador único início de sessão com o Azure AD B2C, não irá iniciar-o utilizador fora a sessão de fornecedor (IDP) de identidade de redes sociais. Se o utilizador seleciona o mesmo IDP durante um subsequente início de sessão, estes serão possível reautenticar, sem introduzir as respetivas credenciais. Se um utilizador pretende terminar a sua aplicação B2C, este não significam necessariamente que pretende terminar a respetiva conta do Facebook. No entanto, no caso de contas locais, a sessão do utilizador será terminada corretamente.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Utilizar o seu inquilino do B2C
Se pretender tente estes pedidos para si, tem de efetuar primeiro a estes três passos e, em seguida, substitua os valores de exemplo descritos anteriormente com os seus próprios:

1. [Criar um inquilino do B2C](active-directory-b2c-get-started.md)e utilize o nome do seu inquilino nos pedidos.
2. [Criar uma aplicação](active-directory-b2c-app-registration.md) para obter uma ID de aplicação. Inclua uma web app/web API na sua aplicação. Opcionalmente, crie um segredo da aplicação.
3. [Criar as políticas](active-directory-b2c-reference-policies.md) para obter os nomes de política.

