---
title: "Fluxo de código de autorização - Azure AD B2C | Microsoft Docs"
description: "Saiba como compilar aplicações web através do protocolo de autenticação do Azure AD B2C e o OpenID Connect."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c371aaab-813a-4317-97df-b62e2f53d865
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: dfc4f2e84704307ccbea6141c0dbc8d089733b22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Do Azure Active Directory B2C: Fluxo de código de autorização de OAuth 2.0
Pode utilizar a concessão de código de autorização do OAuth 2.0 aplicações instaladas no dispositivo para obter acesso a recursos protegidos, como as APIs web. Ao utilizar o Azure Active Directory B2C (Azure AD B2C) a implementação de OAuth 2.0, pode adicionar inscrição, início de sessão e tarefas de outra gestão de identidades às suas aplicações de ambiente de trabalho e móveis. Este artigo é independente de idioma. O artigo, vamos descrevem como enviar e receber mensagens HTTP sem utilizar quaisquer bibliotecas de open source.

<!-- TODO: Need link to libraries -->

O fluxo de código de autorização do OAuth 2.0 está descrito em [secção 4.1 da especificação de OAuth 2.0](http://tools.ietf.org/html/rfc6749). Pode utilizá-lo para autenticação e autorização na maioria dos tipos de aplicação, incluindo [aplicações web](active-directory-b2c-apps.md#web-apps) e [instalado nativamente aplicações](active-directory-b2c-apps.md#mobile-and-native-apps). Pode utilizar o fluxo de código de autorização do OAuth 2.0 para adquirir em segurança *tokens de acesso* para as suas aplicações, que podem ser utilizadas para aceder a recursos que estão protegidos por um [servidor autorização](active-directory-b2c-reference-protocols.md#the-basics).

Este artigo incida no **clientes públicos** fluxo de código de autorização do OAuth 2.0. Um cliente público é qualquer aplicação cliente que não pode ser fidedigna de forma segura a manter a integridade de uma palavra-passe secreta. Isto inclui as aplicações móveis, aplicações de ambiente de trabalho e essencialmente qualquer aplicação que é executado num dispositivo e tem de obter os tokens de acesso. 

> [!NOTE]
> Para adicionar a gestão de identidade para uma aplicação web utilizando o Azure AD B2C, utilize [OpenID Connect](active-directory-b2c-reference-oidc.md) em vez de OAuth 2.0.

O Azure AD B2C expande o padrão de que OAuth 2.0 flui fazer mais do que simples autenticação e autorização. Introduz o [parâmetro política](active-directory-b2c-reference-policies.md). Com as políticas incorporadas, pode utilizar o OAuth 2.0 para adicionar experiências de utilizador para a sua aplicação, tais como inscrição, início de sessão e gestão de perfis. Neste artigo, vamos mostrar-lhe como utilizar políticas e OAuth 2.0 para implementar cada um destes experiências nas suas aplicações nativas. Podemos também mostram como obter os tokens de acesso para aceder a web APIs.

Os pedidos HTTP de exemplo neste artigo, utilizamos diretório do Azure AD B2C nosso exemplo, **fabrikamb2c.onmicrosoft.com**. Também utilizamos a nossa aplicação de exemplo e as políticas. Pode tentar os pedidos utilizando estes valores, ou pode substituí-los com os seus próprios valores.
Saiba como [obter os seus próprios diretório do Azure AD B2C, aplicações e políticas](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Obtenha um código de autorização
O fluxo de código de autorização começa com o cliente instruir o utilizador a `/authorize` ponto final. Esta é a parte interativa de fluxo, onde o utilizador executa a ação. Este pedido, o cliente indica no `scope` parâmetro as permissões que necessita de adquirir do utilizador. No `p` parâmetro, ele indica que a política de execução. Os seguintes três exemplos (com quebras de linha para legibilidade) cada utilizam uma política diferente.

### <a name="use-a-sign-in-policy"></a>Utilizar uma política de início de sessão
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Utilizar uma política de inscrição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Utilizar uma política de perfil de edição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| response_type |Necessário |O tipo de resposta, tem de incluir `code` para que o fluxo de código de autorização. |
| redirect_uri |Necessário |O URI de redirecionamento da sua aplicação, onde as respostas de autenticação são enviadas e recebidas pela sua aplicação. -Deve corresponder exatamente um do redirecionamento de URIs registados no portal, exceto que tem de ser codificados de URL. |
| Âmbito |Necessário |Uma lista separada por espaço de âmbitos. Um valor único âmbito indica ao Azure Active Directory (Azure AD) ambas as permissões que estão a ser solicitada. O ID de cliente a utilizar como âmbito indica que a aplicação tem um token de acesso que pode ser utilizado contra o seu próprio serviço ou a web API, representada pelo mesmo ID de cliente.  O `offline_access` âmbito indica que a aplicação tem um token de atualização de longa duração acesso a recursos. Também pode utilizar o `openid` âmbito para pedir um token de ID do Azure AD B2C. |
| response_mode |Recomendado |O método que utilizar para enviar o código de autorização resultante de volta à aplicação. Pode ser `query`, `form_post`, ou `fragment`. |
| state |Recomendado |Um valor incluído no pedido de que é devolvido na resposta token. Pode ser uma cadeia de todos os conteúdos que pretende utilizar. Normalmente, um valor exclusivo gerado aleatoriamente é utilizado, para impedir ataques de falsificação de pedidos entre sites. O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu. Por exemplo, a página que do utilizador estava no, ou a política que estava a ser executada. |
| P |Necessário |A política que é executada. É o nome de uma política que é criado no diretório do Azure AD B2C. O valor de nome de política deve iniciar com **b2c\_1\_**. Para saber mais sobre as políticas, consulte [políticas incorporadas do Azure AD B2C](active-directory-b2c-reference-policies.md). |
| linha de comandos |Opcional |O tipo de interação do utilizador que é necessário. Atualmente, o único valor válido é `login`, que força o utilizador introduza as suas credenciais desse pedido. O início de sessão único não entrarão em vigor. |

Neste momento, é pedido ao utilizador para concluir o fluxo de trabalho da política. Isto poderá envolver o utilizador introduzir o respetivo nome de utilizador e palavra-passe, início de sessão com uma identidade de redes social, inscrever-se para o diretório ou qualquer outro número de passos. Ações de utilizador dependem como a política está definida.

Depois do utilizador conclui a política, o Azure AD devolve uma resposta à sua aplicação no valor utilizado para `redirect_uri`. Utiliza o método especificado no `response_mode` parâmetro. A resposta é exatamente o mesmo para todos os cenários de ação do utilizador, independentemente da política que foi executada.

Uma resposta com êxito que utiliza `response_mode=query` se parece com isto:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| --- | --- |
| código |O código de autorização que a aplicação pedida. A aplicação pode utilizar o código de autorização para pedir um token de acesso para um recurso de destino. Códigos de autorização são muito curta duração. Normalmente, estas expiram após cerca de 10 minutos. |
| state |Consulte a descrição completa da tabela na secção anterior. Se um `state` parâmetro está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que o `state` valores no pedido e resposta são idênticos. |

As respostas de erro também podem ser enviadas para o URI de redirecionamento, para que a aplicação pode processar corretamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros que ocorrem. Também pode utilizar a cadeia de reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |
| state |Consulte a descrição completa na tabela anterior. Se um `state` parâmetro está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que o `state` valores no pedido e resposta são idênticos. |

## <a name="2-get-a-token"></a>2. Obter um token
Agora que já obteve um código de autorização, pode resgatar a `code` de um token para o recurso pretendido, enviando um pedido POST para o `/token` ponto final. No Azure AD B2C, o recurso só pode pedir um token para está a web de back-end da aplicação API. A Convenção de que é utilizada para pedir um token a próprio consiste em utilizar o ID de cliente da sua aplicação como o âmbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| P |Necessário |A política que utilizou para adquirir o código de autorização. Não é possível utilizar uma política diferente neste pedido. Tenha em atenção que o adicione este parâmetro para o *cadeia de consulta*, não no corpo da mensagem. |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| grant_type |Necessário |O tipo de concessão. Para que o fluxo de código de autorização, o tipo de concessão deve ser `authorization_code`. |
| Âmbito |Recomendado |Uma lista separada por espaço de âmbitos. Indica um valor único âmbito para o Azure AD ambas as permissões que estão a ser solicitada. O ID de cliente a utilizar como âmbito indica que a aplicação tem um token de acesso que pode ser utilizado contra o seu próprio serviço ou a web API, representada pelo mesmo ID de cliente.  O `offline_access` âmbito indica que a aplicação tem um token de atualização de longa duração acesso a recursos.  Também pode utilizar o `openid` âmbito para pedir um token de ID do Azure AD B2C. |
| código |Necessário |O código de autorização que obteve na primeira parte do fluxo. |
| redirect_uri |Necessário |O URI da aplicação em que foi recebido o código de autorização de redirecionamento. |

Uma resposta com êxito de token tem o seguinte aspeto:

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
| token_type |O valor de tipo de token. O único tipo que suporte do Azure AD é portador. |
| access_token |O assinado JSON Web tokens (JWT) que pediu. |
| Âmbito |Os âmbitos que o token é válido para. Também pode utilizar âmbitos para tokens de cache para utilização posterior. |
| expires_in |O período de tempo que o token é válido (em segundos). |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do token atual expira. Os tokens de atualização são longa duração. Pode utilizá-los para manter o acesso a recursos para períodos de tempo prolongados. Para obter mais informações, consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro tem o seguinte aspeto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros que ocorrem. Também pode utilizar a cadeia de reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. Utilizar o token
Agora que já obteve com êxito um token de acesso, pode utilizar o token de pedidos para o back-end APIs web, incluindo-na `Authorization` cabeçalho:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Atualizar o token
Os tokens de acesso e tokens de ID são curta duração. Depois de expirarem, deve atualizá-los para continuar a aceder aos recursos. Para tal, Submeter outro pedido POST para o `/token` ponto final. Neste momento, forneça o `refresh_token` em vez do `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| P |Necessário |A política que utilizou para adquirir o token de atualização original. Não é possível utilizar uma política diferente neste pedido. Tenha em atenção que o adicione este parâmetro para o *cadeia de consulta*, não no corpo da mensagem. |
| client_id |Recomendado |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| grant_type |Necessário |O tipo de concessão. Para esta parte do fluxo de código de autorização, o tipo de concessão deve ser `refresh_token`. |
| Âmbito |Recomendado |Uma lista separada por espaço de âmbitos. Indica um valor único âmbito para o Azure AD ambas as permissões que estão a ser solicitada. O ID de cliente a utilizar como âmbito indica que a aplicação tem um token de acesso que pode ser utilizado contra o seu próprio serviço ou a web API, representada pelo mesmo ID de cliente.  O `offline_access` âmbito indica que a sua aplicação terá um token de atualização de longa duração acesso a recursos.  Também pode utilizar o `openid` âmbito para pedir um token de ID do Azure AD B2C. |
| redirect_uri |Opcional |O URI da aplicação em que foi recebido o código de autorização de redirecionamento. |
| refresh_token |Necessário |O token de atualização original que obteve na segunda parte do fluxo. |

Uma resposta com êxito de token tem o seguinte aspeto:

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
| token_type |O valor de tipo de token. O único tipo que suporte do Azure AD é portador. |
| access_token |O JWT assinado que pediu. |
| Âmbito |Os âmbitos que o token é válido para. Também pode utilizar os âmbitos para tokens de cache para utilização posterior. |
| expires_in |O período de tempo que o token é válido (em segundos). |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do token atual expira. Atualizar são os tokens e pode ser utilizados para manter o acesso a recursos para períodos de tempo prolongados. Para obter mais informações, consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro tem o seguinte aspeto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros que ocorrem. Também pode utilizar a cadeia de reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Utilizar os seus próprios diretórios do Azure AD B2C
Para experimentar estes pedidos por si, conclua os passos seguintes. Substitua os valores de exemplo que são utilizadas neste artigo com os seus próprios valores.

1. [Criar um diretório do Azure AD B2C](active-directory-b2c-get-started.md). Utilize o nome do seu diretório nos pedidos.
2. [Criar uma aplicação](active-directory-b2c-app-registration.md) para obter uma ID da aplicação e um URI de redirecionamento. Incluem um cliente nativo na sua aplicação.
3. [Criar as políticas](active-directory-b2c-reference-policies.md) para obter os nomes de política.

