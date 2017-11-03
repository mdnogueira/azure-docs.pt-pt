---
title: "Do Azure Active Directory B2C: Aplicações de página única com o fluxo implícito | Microsoft Docs"
description: "Saiba como criar aplicações de página única diretamente, utilizando o fluxo implícito de OAuth 2.0 com o Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
ms.openlocfilehash: 44ff168599e9078506e1afdd0f1dc4657ef0964d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>O Azure AD B2C: Página única aplicação início de sessão utilizando o fluxo implícito de OAuth 2.0

> [!NOTE]
> Esta funcionalidade está em pré-visualização.
> 

Muitas aplicações modernas têm uma aplicação de página única front-end que é principalmente escrito em JavaScript. Muitas vezes, a aplicação é escrita através da utilização de uma estrutura como AngularJS, Ember.js ou Durandal. Aplicações de página única e outras aplicações de JavaScript que execute principalmente num browser tem alguns desafios de autenticação adicionais:

* As características de segurança destas aplicações forem significativamente diferentes da aplicações web tradicionais e baseadas no servidor.
* Muitos servidores de autorização e fornecedores de identidade não suportam os recursos de várias origens (CORS) pedidos de partilha.
* Full-page browser redirecionamentos longe da aplicação podem ser significativamente INVASIVO à experiência de utilizador.

Para suportar estas aplicações, o Azure Active Directory B2C (Azure AD B2C) utiliza o fluxo implícito de OAuth 2.0. O fluxo de concessão implícita de autorização do OAuth 2.0 é descrito no [secção 4.2 da especificação de OAuth 2.0](http://tools.ietf.org/html/rfc6749). No fluxo implícito, a aplicação recebe tokens diretamente a partir do Azure Active Directory (Azure AD) autorizar o ponto final, sem qualquer servidor-servidor exchange. Todos os lógica de autenticação e sessão demora a processar colocar inteiramente no cliente do JavaScript, sem redirecionamentos de página adicionais.

O Azure AD B2C expande o fluxo implícito de OAuth 2.0 padrão em mais do que simples autenticação e autorização. O Azure AD B2C introduz o [parâmetro política](active-directory-b2c-reference-policies.md). Com o parâmetro de política, pode utilizar o OAuth 2.0 para adicionar experiências de utilizador para a sua aplicação, tais como inscrição, início de sessão e gestão de perfis. Neste artigo, vamos mostrar-lhe como utilizar o fluxo implícito e o Azure AD para implementar cada um destes experiências nas suas aplicações de página única. Para ajudá-lo, observe a nossa [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) e [Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) amostras.

Os pedidos HTTP de exemplo neste artigo, utilizamos diretório do Azure AD B2C nosso exemplo, **fabrikamb2c.onmicrosoft.com**. Também utilizamos os nossos própria aplicação de exemplo e as políticas. Pode tentar os pedidos utilizando estes valores, ou pode substituí-los com os seus próprios valores.
Saiba como [obter os seus próprios diretório do Azure AD B2C, aplicações e políticas](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo implícito de início de sessão aspeto semelhante ao seguinte figura seguinte. Cada passo é descrito detalhadamente mais à frente no artigo.

![OpenID Connect pistas de diagrama](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação
Quando a aplicação web precisa de uma política de execução e autenticar o utilizador, direciona o utilizador para o `/authorize` ponto final. Esta é a parte interativa de fluxo, onde o utilizador executa a ação, consoante a política. O utilizador obtém um ID de token do ponto final do Azure AD.

Este pedido, o cliente indica no `scope` parâmetro as permissões que necessita de adquirir do utilizador. No `p` parâmetro, ele indica que a política de execução. Os seguintes três exemplos (com quebras de linha para legibilidade) cada utilizam uma política diferente. Para obter uma funcionalidade para como funciona a cada pedido, tente colar o pedido para um browser e executá-lo.

### <a name="use-a-sign-in-policy"></a>Utilizar uma política de início de sessão
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Utilizar uma política de inscrição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Utilizar uma política de perfil de edição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| response_type |Necessário |Tem de incluir `id_token` para OpenID Connect início de sessão. Também pode incluir o tipo de resposta `token`. Se utilizar `token`, a aplicação imediatamente pode receber um token de acesso de ponto final de autorização, sem o tornar um segundo pedido para o ponto final de autorização.  Se utilizar o `token` tipo de resposta, a `scope` parâmetro tem de conter um âmbito que indica qual para emitir o token para o recurso. |
| redirect_uri |Recomendado |O URI de redirecionamento da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação. -Deve corresponder exatamente um do redirecionamento de URIs registados no portal, exceto que tem de ser codificados de URL. |
| response_mode |Recomendado |Especifica o método a utilizar para enviar a cópia de segurança de token resultante para a sua aplicação.  Para fluxos implícitos, utilize `fragment`. |
| Âmbito |Necessário |Uma lista separada por espaço de âmbitos. Indica um valor único âmbito para o Azure AD ambas as permissões que estão a ser solicitada. O `openid` âmbito indica uma permissão para iniciar o utilizador e obter dados sobre o utilizador no formato de tokens de identidade. (Iremos falar sobre esta mais mais tarde no artigo.) O `offline_access` âmbito é opcional para aplicações web. Indica que a aplicação tem um token de atualização de longa duração acesso a recursos. |
| state |Recomendado |Um valor incluído no pedido de que também é devolvido na resposta token. Pode ser uma cadeia de todos os conteúdos que pretende utilizar. Normalmente, um valor exclusivo, gerado aleatoriamente, é utilizado, para impedir ataques de falsificação de pedidos entre sites. O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, como a página estivessem nas suas. |
| nonce |Necessário |Um valor incluído no pedido de (gerado pela aplicação) que está incluído no token resultante ID como uma afirmação. A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição de token. Normalmente, o valor é uma cadeia de aleatório, exclusiva que pode ser utilizada para identificar a origem do pedido. |
| P |Necessário |A política de execução. É o nome de uma política que é criado no seu inquilino do Azure AD B2C. O valor de nome de política deve iniciar com **b2c\_1\_**. Para obter mais informações, consulte [políticas incorporadas do Azure AD B2C](active-directory-b2c-reference-policies.md). |
| linha de comandos |Opcional |O tipo de interação do utilizador necessárias. Atualmente, o único valor válido é `login`. Isto força o utilizador introduza as suas credenciais desse pedido. O início de sessão único não entrarão em vigor. |

Neste momento, é pedido ao utilizador para concluir o fluxo de trabalho da política. Isto poderá envolver o utilizador introduzir o respetivo nome de utilizador e palavra-passe, início de sessão com uma identidade de redes social, inscrever-se para o diretório ou qualquer outro número de passos. Ações de utilizador dependem como a política está definida.

Depois do utilizador conclui a política, o Azure AD devolve uma resposta à sua aplicação no valor utilizado para `redirect_uri`. Utiliza o método especificado no `response_mode` parâmetro. A resposta é exatamente o mesmo para todos os cenários de ação do utilizador, independentemente da política que foi executada.

### <a name="successful-response"></a>Resposta com êxito
Uma resposta com êxito que utiliza `response_mode=fragment` e `response_type=id_token+token` se parece com o seguinte, com as quebras de linha para melhorar a legibilidade:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de acesso que a aplicação pedida.  O token de acesso não deve estar descodificar ou caso contrário inspecioná-los. Pode ser tratado como uma cadeia opaco. |
| token_type |O valor de tipo de token. O único tipo que suporte do Azure AD é portador. |
| expires_in |O período de tempo que o token de acesso é válido (em segundos). |
| Âmbito |Os âmbitos que o token é válido para. Também pode utilizar âmbitos para tokens de cache para utilização posterior. |
| id_token |O token de ID que a aplicação pedida. Pode utilizar o token de ID para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais informações sobre os tokens de ID e os respetivos conteúdos, consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state |Se um `state` parâmetro está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que o `state` valores no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o URI de redirecionamento, para que a aplicação pode processar corretamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro utilizada para classificar os tipos de erros que ocorrem. Também pode utilizar o código de erro para processamento de erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |
| state |Consulte a descrição completa na tabela anterior. Se um `state` parâmetro está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que o `state` valores no pedido e resposta são idênticos.|

## <a name="validate-the-id-token"></a>Validar o token de ID
Receber um token de ID não é suficiente para autenticar o utilizador. Tem de validar a assinatura do token de ID e certifique-se as afirmações no token por requisitos da sua aplicação. Azure AD B2C utiliza [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Bibliotecas de open source muitos estão disponíveis para validar JWTs, consoante o idioma que preferir para utilizar. Considere a explorar a bibliotecas de open source disponíveis, em vez de implementar a sua própria lógica de validação. Pode utilizar as informações neste artigo para o ajudar a saber como utilizar corretamente as bibliotecas.

O Azure AD B2C tem um ponto final de metadados OpenID Connect. Uma aplicação pode utilizar o ponto final para obter informações sobre o Azure AD B2C no tempo de execução. Estas informações incluem pontos finais, conteúdo de token e chaves de assinatura de tokens. Não há um documento de metadados JSON para cada política no seu inquilino do Azure AD B2C. Por exemplo, o documento de metadados para a política de b2c_1_sign_in no inquilino fabrikamb2c.onmicrosoft.com está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades deste documento de configuração é o `jwks_uri`. O valor para a mesma política seria:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Para determinar qual a política foi utilizada para assinar um token de ID (e onde pode obter os metadados do), tem duas opções. Em primeiro lugar, o nome da política está incluído no `acr` afirmação na `id_token`. Para obter informações sobre como analisar as afirmações a partir de um token de ID, consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção consiste em codificar a política no valor do `state` parâmetro quando emitir o pedido. Em seguida, descodificar o `state` parâmetro para determinar qual a política foi utilizada. O método é válido.

Depois de ter adquirido o documento de metadados a partir do ponto final de metadados OpenID Connect, pode utilizar as chaves públicas RSA-256 (localizadas neste ponto final) para validar a assinatura do ID token. Podem existir várias chaves listadas neste ponto final em qualquer momento, cada identificado por um `kid`. O cabeçalho de `id_token` também contém um `kid` de afirmação. Indica que estas chaves foi utilizado para assinar o token de ID. Para obter mais informações, incluindo saber mais sobre [tokens de validação](active-directory-b2c-reference-tokens.md#token-validation), consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Depois de validar a assinatura do token de ID, várias afirmações requerem verificação. Por exemplo:

* Validar o `nonce` impedir ataques de repetição de token de afirmação. O valor deve ser especificado no pedido de início de sessão.
* Validar o `aud` de afirmação para se certificar de que o token de ID foi emitido para a sua aplicação. O valor deve ser o ID da sua aplicação.
* Validar o `iat` e `exp` afirmações para se certificar de que o token de ID não expirou.

Vários validações mais que deverá efetuar são descritas em detalhe no [OpenID Connect Core Spec](http://openid.net/specs/openid-connect-core-1_0.html). Pode também querer validar afirmações adicionais, dependendo do seu cenário. Alguns validações comuns incluem:

* Garantir que o utilizador ou a organização tiver inscrito para a aplicação.
* Garantir que o utilizador tem autorização adequada e privilégios.
* Garantir que um determinada força de autenticação tiver ocorrido, tal como utilizando o Azure multi-factor Authentication.

Para mais informações sobre as afirmações num ID token, consulte o [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Depois de ter completamente validar o token de ID, pode iniciar uma sessão com o utilizador. Na sua aplicação, utilize as afirmações no ID token para obter informações sobre o utilizador. Estas informações podem ser utilizadas para apresentar, registos de, autorização e assim sucessivamente.

## <a name="get-access-tokens"></a>Obter os tokens de acesso
Se a única coisa que tem de fazer as suas aplicações web está a políticas de execução, pode ignorar as secções seguintes alguns. As informações nas secções seguintes são aplicáveis apenas às aplicações web que precisar de efetuar chamadas autenticadas para uma API web e que estão protegidos pelo Azure AD B2C.

Agora que pode iniciar sessão do utilizador para a sua aplicação de página única, pode obter os tokens de acesso para chamar web APIs que estão protegidas pelo Azure AD. Mesmo se já tiver recebido um token ao utilizar o `token` tipo de resposta, pode utilizar este método para adquirir tokens para obter recursos adicionais sem redirecionar o utilizador iniciar sessão novamente.

Um fluxo de aplicação web típica, isto seria feito ao efetuar um pedido para o `/token` ponto final.  No entanto, o ponto final não suporta pedidos CORS, pelo que efetuar chamadas AJAX para obter e tokens de atualização não é uma opção. Em vez disso, pode utilizar o fluxo implícito num elemento iframe ocultado HTML para obter os tokens de novo para outras APIs web. Eis um exemplo, com as quebras de linha para melhorar a legibilidade:

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| response_type |Necessário |Tem de incluir `id_token` para OpenID Connect início de sessão.  Também poderão incluir o tipo de resposta `token`. Se utilizar `token` aqui, a aplicação pode imediatamente receber um token de acesso de ponto final de autorização, sem o tornar um segundo pedido para o ponto final de autorização. Se utilizar o `token` tipo de resposta, a `scope` parâmetro tem de conter um âmbito que indica qual para emitir o token para o recurso. |
| redirect_uri |Recomendado |O URI de redirecionamento da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação. -Deve corresponder exatamente um do redirecionamento de URIs registados no portal, exceto que tem de ser codificados de URL. |
| Âmbito |Necessário |Uma lista separada por espaço de âmbitos.  Para obter os tokens, inclua todos os âmbitos que necessita para o recurso pretendido. |
| response_mode |Recomendado |Especifica o método que é utilizado para enviar a cópia de segurança de token resultante para a sua aplicação.  Pode ser `query`, `form_post`, ou `fragment`. |
| state |Recomendado |Um valor incluído no pedido de que é devolvido na resposta token.  Pode ser uma cadeia de todos os conteúdos que pretende utilizar.  Normalmente, um valor exclusivo, gerado aleatoriamente, é utilizado, para impedir ataques de falsificação de pedidos entre sites.  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu. Por exemplo, a página ou a vista foi o utilizador em. |
| nonce |Necessário |Um valor incluído no pedido de gerado pela aplicação, o que está incluída no token resultante ID como uma afirmação.  A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição de token. Normalmente, o valor é uma cadeia de aleatório, exclusiva que identifica a origem do pedido. |
| linha de comandos |Necessário |Para atualizar e obter tokens num iframe oculta, utilize `prompt=none` para garantir que o iframe não ficar bloqueado na página de início de sessão e devolve imediatamente. |
| login_hint |Necessário |Para atualizar e obter tokens num iframe oculta, inclua o nome de utilizador do utilizador desta sugestão para distinguir entre várias sessões que de utilizador pode ter um determinado momento. Pode extrair o nome de utilizador a partir de um anterior início de sessão utilizando o `preferred_username` de afirmação. |
| domain_hint |Necessário |Pode ser `consumers` ou `organizations`.  Para atualizar e obter os tokens um iframe oculta, tem de incluir o `domain_hint` valor no pedido.  Extrair o `tid` afirmação a partir do token do ID de um anterior início de sessão para determinar qual o valor a utilizar.  Se o `tid` é o valor de afirmação `9188040d-6c67-4c5b-b112-36a304b66dad`, utilize `domain_hint=consumers`.  Caso contrário, utilize `domain_hint=organizations`. |

Ao definir o `prompt=none` parâmetro, este pedido ou com êxito ou falha imediatamente e devolve a sua aplicação.  É enviada uma resposta com êxito para a sua aplicação, o redirecionamento indicado URI, utilizando o método especificado no `response_mode` parâmetro.

### <a name="successful-response"></a>Resposta com êxito
Uma resposta com êxito utilizando `response_mode=fragment` se parece com isto:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de que a aplicação pedida. |
| token_type |O tipo de token será sempre portador. |
| state |Se um `state` parâmetro está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que o `state` valores no pedido e resposta são idênticos. |
| expires_in |Quanto o token de acesso é válido (em segundos). |
| Âmbito |Os âmbitos que o token de acesso é válido para. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o URI de redirecionamento, para que a aplicação pode processar corretamente.  Para `prompt=none`, um erro esperado tem o seguinte aspeto:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros que ocorrem. Também pode utilizar a cadeia de reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

Se receber este erro no pedido iframe, o utilizador tem interativamente iniciar sessão novamente para obter um novo token. Isto pode processar de forma a que faz sentido para a sua aplicação.

## <a name="refresh-tokens"></a>Tokens de atualização
Tokens de identidade e tokens de acesso expiram após um curto período de tempo. A aplicação tem de ser preparada para atualizar estes tokens periodicamente.  Para atualizar qualquer tipo de token, efetuar o pedido de iframe oculta mesmo utilizámos no exemplo anterior, utilizando o `prompt=none` parâmetro para controlar os passos do Azure AD.  Para receber uma nova `id_token` valor, não se esqueça de utilizar `response_type=id_token` e `scope=openid`e um `nonce` parâmetro.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de início de sessão
Quando pretender assinar o utilizador fora da aplicação, redireciona o utilizador para o Azure AD para terminar sessão. Se não fizer isto, o utilizador poderá voltar à sua aplicação, sem introduzir as respetivas credenciais novamente. Isto acontece porque terá uma único início de sessão sessão válido com o Azure AD.

Pode simplesmente redirecionar o utilizador a `end_session_endpoint` que está listado no OpenID mesmo Connect documento de metadados descrito [validar o token de ID](#validate-the-id-token). Por exemplo:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| P |Necessário |A política a utilizar para assinar o utilizador fora da sua aplicação. |
| post_logout_redirect_uri |Recomendado |O URL que o utilizador deve ser redirecionado para após êxito fim de sessão. Se não estiver incluída, o Azure AD B2C apresenta uma mensagem genérica ao utilizador. |

> [!NOTE]
> Instruir o utilizador a `end_session_endpoint` limpa algumas do Estado do utilizador único início de sessão com o Azure AD B2C. No entanto, este não assinar o utilizador sem sessão de fornecedor de identidade social do utilizador. Se o utilizador seleciona iguais identificam fornecedor durante um subsequente início de sessão, o utilizador é reautenticar, sem introduzir as respetivas credenciais. Se um utilizador pretende terminar a sua aplicação do Azure AD B2C, este não significa necessariamente que pretende que seja completamente terminar a respetiva conta do Facebook, por exemplo. No entanto, para contas locais, a sessão do utilizador será terminada corretamente.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Utilizar o seu inquilino do Azure AD B2C
Para experimentar estes pedidos por si, conclua os seguintes três passos. Substitua os valores de exemplo que iremos utilizar neste artigo com os seus próprios valores:

1. [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md). Utilize o nome do seu inquilino nos pedidos.
2. [Criar uma aplicação](active-directory-b2c-app-registration.md) para obter uma ID da aplicação e um `redirect_uri` valor. Inclua uma aplicação web ou a web API na sua aplicação. Opcionalmente, pode criar um segredo da aplicação.
3. [Criar as políticas](active-directory-b2c-reference-policies.md) para obter os nomes de política.

## <a name="samples"></a>Amostras

* [Criar uma aplicação de página única utilizando Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Criar uma aplicação de página única utilizando o .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

