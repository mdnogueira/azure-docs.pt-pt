---
title: "Proteger aplicações de página única utilizando o fluxo implícito de v 2.0 do Azure AD | Microsoft Docs"
description: "Criar aplicações web utilizando a implementação de v 2.0 do Azure AD do fluxo implícito para aplicações de página única."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3bd8256814036a357b30b69286da6bb7c974162f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# v 2.0 protocolos - SPAs utilizando o fluxo implícito
Com o ponto final v 2.0, pode iniciar a sessão de utilizadores para as aplicações de página única com contas pessoais e trabalho/profissional da Microsoft.  Página única e outras aplicações de JavaScript que execute principalmente na enfrentam browser algumas interessantes desafia quando se trata de autenticação:

* As características de segurança destas aplicações forem significativamente diferentes de aplicações do servidor tradicional baseado em web.
* Muitos servidores de autorização & fornecedores de identidade não suportam pedidos CORS.
* Browser de página completa redireciona na direção oposta à aplicação ficar particularmente INVASIVO à experiência de utilizador.

Para estas aplicações (pensar: AngularJS, Ember.js, React.js, etc.) do Azure AD suporta o fluxo de concessão implícita do OAuth 2.0.  O fluxo implícito é descrito no [especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2).  A principal vantagem é que permite que a aplicação obter os tokens do AD do Azure, sem executar um servidor de back-end credencial exchange.  Isto permite que a aplicação para o utilizador iniciar sessão, manter a sessão e obter tokens para outras APIs web tudo isto no cliente código JavaScript.  Existem algumas considerações de segurança importante a ter em consideração quando utilizar o fluxo implícito - especificamente cerca [cliente](http://tools.ietf.org/html/rfc6749#section-10.3) e [representação de utilizadores](http://tools.ietf.org/html/rfc6749#section-10.3).

Se pretender utilizar o fluxo implícito e o Azure AD para adicionar autenticação à sua aplicação JavaScript, recomendamos que utilize a nossa biblioteca de JavaScript de código aberto, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Existem alguns AngularJS tutoriais disponíveis [aqui](active-directory-appmodel-v2-overview.md#getting-started) para ajudar a começar.  

No entanto, se preferir não utilizarem uma biblioteca na sua aplicação de página única e enviar mensagens de protocolo por si, siga os passos gerais abaixo.

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

## Diagrama de protocolo
Fluxo de início de sessão implícito todo aspeto semelhante ao seguinte isto - todos os passos descritos detalhadamente abaixo.

![OpenId Connect pistas de diagrama](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## Enviar o pedido de início de sessão
Para assinar inicialmente o utilizador na sua aplicação, pode enviar um [OpenID Connect](active-directory-v2-protocols-oidc.md) pedido de autorização e obter um `id_token` do ponto final v 2.0:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Clique na hiperligação abaixo para executar este pedido! Depois de iniciarem sessão, o browser deve ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereço.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>
> 
> 

| Parâmetro |  | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são `common`, `organizations`, `consumers`e inquilinos identificadores.  Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |Necessário |O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuído a sua aplicação. |
| response_type |Necessário |Tem de incluir `id_token` para OpenID Connect início de sessão.  Poderão incluir o response_type `token`. Utilizar `token` aqui irá permitir que a aplicação receba imediatamente um token de acesso do ponto final autorizar sem ter de efetuar um pedido de segundo para o ponto final de autorização.  Se utilizar o `token` response_type, o `scope` parâmetro tem de conter um âmbito que indica que o recurso a emitir o token para. |
| redirect_uri |recomendado |Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação.  Este deve corresponder exatamente um dos redirect_uris registado no portal, exceto tem de ser codificados de url. |
| Âmbito |Necessário |Uma lista separada por espaço de âmbitos.  Para OpenID Connect, tem de incluir o âmbito `openid`, que traduz-se a permissão "Iniciar sessão" no consentimento IU.  Opcionalmente, também poderá incluir a `email` ou `profile` [âmbitos](active-directory-v2-scopes.md) para obter acesso a dados de utilizador adicionais.  Também pode incluir outros âmbitos neste pedido para pedir consentimento a vários recursos. |
| response_mode |recomendado |Especifica o método que deve ser utilizado para enviar a cópia de segurança de token resultante para a sua aplicação.  Deve ser `fragment` para que o fluxo implícito. |
| state |recomendado |Um valor incluído no pedido de que também vai ser devolvido na resposta token.  Pode ser uma cadeia de todos os conteúdos que pretende.  Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de pedidos entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |
| nonce |Necessário |Um valor incluído no pedido de gerado pela aplicação, que será incluída no id_token resultante como uma afirmação.  A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição de token.  O valor é, geralmente, uma cadeia exclusiva, aleatório que pode ser utilizada para identificar a origem do pedido. |
| linha de comandos |Opcional |Indica o tipo de interação do utilizador que é necessário.  Os únicos valores válidos neste momento, são 'início de sessão', 'none' e 'consentimento'.  `prompt=login`irá forçar o utilizador introduza as suas credenciais nesse pedido, a negação início de sessão único.  `prompt=none`é o oposto --irá garantir que o utilizador não é apresentado com qualquer linha de comandos interativa contratutal.  Se o pedido não é possível concluir automaticamente através de início de sessão único, o ponto final v 2.0 devolverá um erro.  `prompt=consent`aciona a caixa de diálogo de consentimento do OAuth depois do utilizador inicia sessão, solicitando ao utilizador para conceder permissões para a aplicação. |
| login_hint |Opcional |Pode ser utilizado para a pré-preencher o campo de endereço de e-mail/nome de utilizador de início de sessão na página para o utilizador, se souber o nome de utilizador antecedência.  Muitas vezes, as aplicações irão utilizar este parâmetro durante a reautenticação, já ter extraiu o nome de utilizador de uma anterior início de sessão utilizando o `preferred_username` de afirmação. |
| domain_hint |Opcional |Pode ser um dos `consumers` ou `organizations`.  Caso, irá ignorar o processo de deteção baseada em e-mail esse utilizador passa no sinal v 2.0 na página, originando uma experiência de utilizador ligeiramente mais simplificada.  Aplicações, muitas vezes, irão utilizar este parâmetro durante a reautenticação, extraindo o `tid` o id_token de afirmação.  Se o `tid` é o valor de afirmação `9188040d-6c67-4c5b-b112-36a304b66dad`, deve utilizar `domain_hint=consumers`.  Caso contrário, utilize `domain_hint=organizations`. |

Neste momento, o utilizador será ser-lhe pedido para introduzir as suas credenciais e concluir a autenticação.  O ponto final v 2.0 também vai assegurar que o utilizador consentiu as permissões indicadas a `scope` parâmetro de consulta.  Se o utilizador não se consentiu para qualquer um dessas permissões, pedirá ao utilizador consentimento para as permissões necessárias.  Detalhes de [permissões, consentimento e aplicações de multi-inquilinos são fornecidas aqui](active-directory-v2-scopes.md).

Depois do utilizador efetua a autenticação e atribui o consentimento, o ponto final v 2.0 irá devolver uma resposta a sua aplicação no indicados `redirect_uri`, utilizando o método especificado no `response_mode` parâmetro.

#### Resposta com êxito
Uma resposta com êxito utilizando `response_mode=fragment` e `response_type=id_token+token` se parece com o seguinte, com as quebras de linha para melhorar a legibilidade:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |Se incluídos `response_type` inclui `token`. O token de acesso que a aplicação pedida, neste caso, para o Microsoft Graph.  O token de acesso não deve estar descodificar ou inspecionado caso contrário, pode ser tratado como uma cadeia opaco. |
| token_type |Se incluídos `response_type` inclui `token`.  Será sempre `Bearer`. |
| expires_in |Se incluídos `response_type` inclui `token`.  Indica o número de segundos que o token é válido, para efeitos de colocação em cache. |
| Âmbito |Se incluídos `response_type` inclui `token`.  Indica os âmbitos que se para o qual o access_token será válido. |
| id_token |Id_token que a aplicação pedida. Pode utilizar o id_token para verificar a identidade do utilizador e iniciar uma sessão com o utilizador.  Obter mais detalhes sobre id_tokens e os respetivos conteúdos está incluído no [referência de token de ponto final v 2.0](active-directory-v2-tokens.md). |
| state |Se um parâmetro de estado está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deverá certificar-se de que os valores de estado no pedido e resposta são idênticos. |

#### Resposta de erro
As respostas de erro também podem ser enviadas para o `redirect_uri` para a aplicação pode processar corretamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros ocorridos e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |

## Validar o id_token
Não é suficiente para autenticar o utilizador; apenas receber uma id_token tem de validar a assinatura do id_token e certifique-se as afirmações no token por requisitos da sua aplicação.  Utiliza o ponto final v 2.0 [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Pode optar por validar o `id_token` cliente código, mas uma prática comum consiste em enviar o `id_token` para um servidor de back-end e efetuar a validação não existe.  Depois de ter confirmado a assinatura do id_token, existem alguns afirmações, que será necessário para verificar.  Consulte o [referência de token de v 2.0](active-directory-v2-tokens.md) para obter mais informações, incluindo [validar os Tokens](active-directory-v2-tokens.md#validating-tokens) e [importantes informações sobre como assinar Rollover de chave](active-directory-v2-tokens.md#validating-tokens).  Recomendamos que efetuar utilização de uma biblioteca para analisar e a validar a tokens - há, pelo menos, um disponível para a maioria dos idiomas e plataformas.
<!--TODO: Improve the information on this-->

Também pode pretender validar afirmações adicionais dependendo do seu cenário.  Alguns validações comuns incluem:

* Garantir que a organização/utilizador foi inscrito para a aplicação.
* Garantir que o utilizador não tem privilégios/autorização adequados
* Garantir que um determinada força de autenticação tiver ocorrido, tais como a autenticação multifator.

Para obter mais informações sobre as afirmações numa id_token, consulte o [referência de token de ponto final v 2.0](active-directory-v2-tokens.md).

Depois de validar o id_token completamente, pode iniciar uma sessão com o utilizador e utilize as afirmações a id_token para obter informações sobre o utilizador na sua aplicação.  Estas informações podem ser utilizadas para apresentar, registos, autorizações, etc.

## Obter os tokens de acesso
Agora que já iniciou a sessão do utilizador na sua aplicação de página única, pode obter os tokens de acesso para chamar web APIs protegidas pelo Azure AD, como o [Microsoft Graph](https://graph.microsoft.io).  Mesmo que já recebido um token utilizando o `token` response_type, pode utilizar este método para adquirir tokens para recursos adicionais sem ter de redirecionar o utilizador iniciar sessão novamente.

O fluxo de OpenID Connect/OAuth normal, isto seria feito ao efetuar um pedido para a v 2.0 `/token` ponto final.  No entanto, o ponto final v 2.0 não suporta pedidos CORS, pelo que efetuar chamadas AJAX para obter e tokens de atualização está fora da pergunta.  Em vez disso, pode utilizar o fluxo implícito um iframe oculta para obter os tokens de novo para outras APIs web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [!TIP]
> Tente copiar e colar o abaixo pedido para um separador do browser! (Não se esqueça de substituir o `domain_hint` e `login_hint` valores com os valores corretos para os seus utilizadores)
> 
> 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são `common`, `organizations`, `consumers`e inquilinos identificadores.  Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |Necessário |O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuído a sua aplicação. |
| response_type |Necessário |Tem de incluir `id_token` para OpenID Connect início de sessão.  Poderão incluir outras response_types, tais como `code`. |
| redirect_uri |recomendado |Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação.  Este deve corresponder exatamente um dos redirect_uris registado no portal, exceto tem de ser codificados de url. |
| Âmbito |Necessário |Uma lista separada por espaço de âmbitos.  Para obter os tokens, incluir todos os [âmbitos](active-directory-v2-scopes.md) precisa para o recurso de interesse. |
| response_mode |recomendado |Especifica o método que deve ser utilizado para enviar a cópia de segurança de token resultante para a sua aplicação.  Pode ser um dos `query`, `form_post`, ou `fragment`. |
| state |recomendado |Um valor incluído no pedido de que também vai ser devolvido na resposta token.  Pode ser uma cadeia de todos os conteúdos que pretende.  Um valor exclusivo gerado aleatoriamente é normalmente utilizado para impedir ataques de falsificação de pedidos entre sites.  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |
| nonce |Necessário |Um valor incluído no pedido de gerado pela aplicação, que será incluída no id_token resultante como uma afirmação.  A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição de token.  O valor é, geralmente, uma cadeia exclusiva, aleatório que pode ser utilizada para identificar a origem do pedido. |
| linha de comandos |Necessário |Para atualizar e obter os tokens um iframe oculta, deve utilizar `prompt=none` para garantir que o iframe bloquear não na página de início de sessão de v 2.0 e devolve imediatamente. |
| login_hint |Necessário |Para atualizar e obter os tokens um iframe oculta, tem de incluir o nome de utilizador do utilizador desta sugestão para distinguir entre várias sessões que de utilizador pode ter num determinado ponto no tempo. Pode extrair o nome de utilizador a partir de uma anterior início de sessão utilizando o `preferred_username` de afirmação. |
| domain_hint |Necessário |Pode ser um dos `consumers` ou `organizations`.  Para atualizar e obter os tokens um iframe oculta, tem de incluir o domain_hint no pedido.  Deve extrair a `tid` id_token de um anterior início de sessão para determinar qual a utilizar o valor de afirmação.  Se o `tid` é o valor de afirmação `9188040d-6c67-4c5b-b112-36a304b66dad`, deve utilizar `domain_hint=consumers`.  Caso contrário, utilize `domain_hint=organizations`. |

Thanks para o `prompt=none` parâmetro, este pedido é concluída com êxito ou falharão imediatamente e regressar à sua aplicação.  Será enviada uma resposta com êxito a sua aplicação do indicado no `redirect_uri`, utilizando o método especificado no `response_mode` parâmetro.

#### Resposta com êxito
Uma resposta com êxito utilizando `response_mode=fragment` parece ser:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de que a aplicação pedida. |
| token_type |Será sempre `Bearer`. |
| state |Se um parâmetro de estado está incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deverá certificar-se de que os valores de estado no pedido e resposta são idênticos. |
| expires_in |Quanto o token de acesso é válido (em segundos). |
| Âmbito |Os âmbitos que o token de acesso é válido para. |

#### Resposta de erro
As respostas de erro também podem ser enviadas para o `redirect_uri` para a aplicação pode processar corretamente.  Em case de `prompt=none`, será um erro esperado:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros ocorridos e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |

Se receber este erro no pedido iframe, o utilizador tem interativamente iniciar sessão novamente para obter um novo token.  Pode escolher processar este caso no forma que achar mais faz sentido para a sua aplicação.

## Atualizar tokens
Ambos `id_token`s e `access_token`s irá expirar após um curto período de tempo, pelo que a aplicação tem de ser preparada para atualizar estes tokens periodicamente.  Para atualizar qualquer tipo de token, pode efetuar o pedido de iframe oculta mesmo from above utilizando o `prompt=none` parâmetro para controlar o comportamento do Azure AD.  Se pretender receber uma nova `id_token`, certifique-se de utilizar `response_type=id_token` e `scope=openid`, bem como um `nonce` parâmetro.

## Enviar um pedido de terminar
O OpenIdConnect `end_session_endpoint` permite à aplicação enviar um pedido para o ponto final v 2.0 para terminar uma sessão de utilizador e desmarque cookies definidos pelo ponto final v 2.0.  Para iniciar completamente um utilizador fora de uma aplicação web, a aplicação deve terminar a sua própria sessão com o utilizador (normalmente, ao limpar a cache de tokens ou remover cookies) e redirecionamento, em seguida, o browser:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são `common`, `organizations`, `consumers`e inquilinos identificadores.  Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| post_logout_redirect_uri | recomendado | O URL que o utilizador deve ser reencaminhado para depois de concluída a fim de sessão. Este valor tem de corresponder a um redirecionamento que URIs registados para a aplicação. Se não incluída, o utilizador será apresentado uma mensagem genérica pelo ponto final v 2.0. |
