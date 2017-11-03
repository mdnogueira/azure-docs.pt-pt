---
title: "Compreender o fluxo de código de autorização do OAuth 2.0 no Azure AD | Microsoft Docs"
description: "Este artigo descreve como utilizar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino utilizando o Azure Active Directory e OAuth 2.0."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: de3412cb-5fde-4eca-903a-4e9c74db68f2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 916652f2d6336da625be91431c3771a730204a73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# Authorize access to web applications using OAuth 2.0 and Azure Active Directory (Autorizar o acesso a aplicações Web com OAuth 2.0 e o Azure Active Directory)
Azure Active Directory (Azure AD) utiliza OAuth 2.0 para que possa autorizar o acesso a aplicações web e APIs web no inquilino do Azure AD. Este guia é independente de idioma e descreve como enviar e receber mensagens HTTP sem utilizar qualquer uma das nossas bibliotecas de open source.

O fluxo de código de autorização do OAuth 2.0 está descrito em [secção 4.1 da especificação de OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). É utilizado para autenticação e autorização na maioria dos tipos de aplicação, incluindo as web apps e as aplicações instaladas nativamente.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## Fluxo de autorização do OAuth 2.0
Um nível elevado, o fluxo de autorização completa para uma aplicação um pouco este aspeto:

![Fluxo de código de autenticação do OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## Pedir um código de autorização
O fluxo de código de autorização começa com o cliente instruir o utilizador a `/authorize` ponto final. Este pedido, o cliente indica as permissões que necessita de adquirir do utilizador. Pode obter os pontos finais de OAuth 2.0 da página da sua aplicação no Portal clássico do Azure, além de **ver pontos finais** botão na gaveta de parte inferior do.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de inquilino independentes |
| client_id |Necessário |O Id de aplicação atribuída à aplicação quando é registado com o Azure AD. Pode encontrá-lo no Portal do Azure. Clique em **do Active Directory**, clique no diretório, escolha a aplicação e clique em **configurar** |
| response_type |Necessário |Tem de incluir `code` para que o fluxo de código de autorização. |
| redirect_uri |recomendado |Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação.  Este deve corresponder exatamente um dos redirect_uris registado no portal, exceto tem de ser codificados de url.  Para aplicações móveis & nativas, deve utilizar o valor predefinido de `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |recomendado |Especifica o método que deve ser utilizado para enviar a cópia de segurança de token resultante para a sua aplicação.  Pode ser `query` ou `form_post`. |
| state |recomendado |Um valor incluído no pedido de que também é devolvido na resposta token. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de pedidos entre sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes de ocorrer o pedido de autenticação, tais como a página ou a vista estivessem nas suas. |
| Recursos |Opcional |O URI de ID de aplicação da web API (recursos protegidos). Para obter o URI de ID de aplicação de API, web no Portal do Azure, clique em **do Active Directory**, clique no diretório, clique na aplicação e, em seguida, clique em **configurar**. |
| linha de comandos |Opcional |Indica o tipo de interação do utilizador que é necessário.<p> Os valores válidos são: <p> *início de sessão*: O utilizador deve ser pedido para voltar. <p> *consentimento*: consentimento do utilizador foram concedido, mas tem de ser atualizado. Deve ser pedido ao utilizador consentimento. <p> *admin_consent*: um administrador deve ser-lhe pedido que consentimento em nome de todos os utilizadores da organização |
| login_hint |Opcional |Pode ser utilizado para a pré-preencher o campo de endereço de e-mail/nome de utilizador da página de início de sessão do utilizador, se souber o nome de utilizador antecedência.  Aplicações, muitas vezes, utilizam este parâmetro durante a reautenticação rápida, já ter extraiu o nome de utilizador de uma anterior início de sessão utilizando o `preferred_username` de afirmação. |
| domain_hint |Opcional |Fornece uma sugestão sobre o domínio que o utilizador deve utilizar para iniciar sessão ou inquilino. O valor da domain_hint é um domínio registado para o inquilino. Se o inquilino está federado para um diretório no local, AAD redireciona para o servidor de Federação do inquilino especificado. |

> [!NOTE]
> Se o utilizador faz parte de uma organização, um administrador da organização pode consentimento ou recusar em nome do utilizador ou permitir que o utilizador consentimento. O utilizador é dada a opção de consentimento apenas quando o administrador permite-lo.
>
>

Neste momento, é pedido ao utilizador para introduzir as suas credenciais e autorizar as permissões indicadas a `scope` parâmetro de consulta. Depois do utilizador efetua a autenticação e atribui o consentimento, do Azure AD envia uma resposta a sua aplicação no `redirect_uri` endereço do seu pedido.

### Resposta com êxito
Uma resposta com êxito pode ter o seguinte aspeto:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parâmetro | Descrição |
| --- | --- |
| admin_consent |O valor é verdadeiro se um administrador autorizado uma linha de comandos do pedido de consentimento. |
| código |O código de autorização que a aplicação pedida. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino. |
| session_state |Um valor exclusivo que identifica a sessão do utilizador atual. Este valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem serem examinados. |
| state |Se um parâmetro de estado está incluído no pedido, o mesmo valor deve aparecer na resposta. É uma boa prática para a aplicação verificar se os valores de estado no pedido e resposta são idênticos antes de utilizar a resposta. Isto ajuda a detetar [ataques de falsificação de pedidos entre sites (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) contra o cliente. |

### Resposta de erro
As respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Um valor de código de erro definido na secção 5.2 do [Framework de autorização do OAuth 2.0](http://tools.ietf.org/html/rfc6749). A tabela seguinte descreve os códigos de erro do Azure AD devolve. |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável de utilizador final. |
| state |O valor de estado é um valor de não reutilizado gerado aleatoriamente enviar no pedido e devolvido na resposta impedir ataques de falsificação (CSRF) de pedidos entre sites. |

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

## Utilizar o código de autorização para pedir um token de acesso
Agora que já obteve um código de autorização e tenha sido concedida permissão pelo utilizador, pode resgatar o código para um token de acesso para o recurso pretendido, enviando um pedido POST para o `/token` ponto final:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| Inquilino |Necessário |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de inquilino independentes |
| client_id |Necessário |O Id de aplicação atribuída à aplicação quando é registado com o Azure AD. Pode encontrá-lo no Portal clássico do Azure. Clique em **do Active Directory**, clique no diretório, escolha a aplicação e clique em **configurar** |
| grant_type |Necessário |Tem de ser `authorization_code` para que o fluxo de código de autorização. |
| código |Necessário |O `authorization_code` que obteve na secção anterior |
| redirect_uri |Necessário |O mesmo `redirect_uri` valor que utilizou para adquirir o `authorization_code`. |
| client_secret |necessário para aplicações web |O segredo de aplicação que criou no portal de registo de aplicação para a sua aplicação.  Não deve ser utilizado numa aplicação nativa, porque client_secrets não pode ser fiável armazenado nos dispositivos.  É necessário para as aplicações web e web APIs, que têm a capacidade de armazenar o `client_secret` em segurança no lado do servidor. |
| Recursos |necessário se especificado num pedido de código de autorização, caso contrário, opcional |O URI de ID de aplicação da web API (recursos protegidos). |

Para obter o URI de ID de aplicação no Portal de gestão do Azure, clique em **do Active Directory**, clique no diretório, clique na aplicação e, em seguida, clique em **configurar**.

### Resposta com êxito
Azure AD devolve um token de acesso após uma resposta com êxito. Para minimizar as chamadas de rede da aplicação de cliente e os respetivos latência associada, a aplicação cliente deve colocar em cache tokens de acesso para a duração do token que é especificada na resposta OAuth 2.0. Para determinar a duração do token, utilize o `expires_in` ou `expires_on` valores de parâmetros.

Se um recurso de API de web devolve um `invalid_token` código de erro, isto pode indicar que o recurso determinou que o token expirou. Se as horas de relógio do cliente e de recursos diferente (conhecido como uma "Hora dissimetrias"), o recurso poderá considerar o token para ser expirou antes do token é eliminado da cache do cliente. Se isto ocorrer, limpe o token da cache, mesmo se for ainda dentro da respetiva duração calculada.

Uma resposta com êxito pode ter o seguinte aspeto:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de pedido de acesso. A aplicação pode utilizar este token para autenticar para o recurso seguro, tais como uma API web. |
| token_type |Indica o valor de tipo de token. O único tipo que suporte do Azure AD é portador. Para mais informações sobre os tokens de portador, consulte [OAuth2.0 autorização Framework: utilização de tokens de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Quanto o token de acesso é válido (em segundos). |
| expires_on |O tempo que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até que a hora de expiração. Este valor é utilizado para determinar a duração de tokens em cache. |
| Recursos |O URI de ID de aplicação da web API (recursos protegidos). |
| Âmbito |Representação, as permissões concedidas à aplicação de cliente. A permissão predefinida é `user_impersonation`. O proprietário do recurso protegido pode registar valores adicionais no Azure AD. |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens de acesso adicionais depois do token de acesso atual expira.  Atualizar são os tokens e pode ser utilizados para manter o acesso a recursos para períodos de tempo prolongados. |
| id_token |Um não assinados JSON Web tokens (JWT). Base64Url de pode aplicação descodificar os segmentos do token pedido informações sobre o utilizador que tem sessão iniciada. A aplicação pode colocar em cache os valores e apresentá-los, mas não deverá confiar nos mesmos para qualquer autorização ou limites de segurança. |

### Afirmações de Token JWT
O token JWT no valor do `id_token` parâmetro pode ser descodificar para as afirmações seguintes:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Para obter mais informações sobre os JSON web tokens, consulte o [especificação de rascunho JWT IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Para obter mais informações sobre os tipos de tokens e afirmações, leia o artigo [suportado Token e tipos de afirmação](active-directory-token-and-claims.md)

O `id_token` parâmetro inclui os seguintes tipos de afirmação:

| Tipo de afirmação | Descrição |
| --- | --- |
| aud |Público-alvo do token. Quando o token emitido a uma aplicação de cliente, o público-alvo é o `client_id` do cliente. |
| EXP |Hora de expiração. A hora quando o token expira. Para o token ser válida, a data/hora atual tem de ser menor ou igual ao `exp` valor. A hora é representada como o número de segundos de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até que a hora a validade do token expira.|
| family_name |Do utilizador último nome ou apelido. A aplicação pode apresentar este valor. |
| given_name |Nome do próprio do utilizador. A aplicação pode apresentar este valor. |
| iat |Emitido no tempo. A hora quando o JWT foi emitido. A hora é representada como o número de segundos de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até à vez que o token foi emitido. |
| ISS |Identifica o emissor de token |
| NBF |Não antes da hora. A hora quando o token de entrar em vigor. Para o token ser válida, a data/hora atual tem de ser maior que ou igual ao valor Nbf. A hora é representada como o número de segundos de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até à vez que o token foi emitido. |
| OID |Identificador de objeto (ID) do objeto de utilizador no Azure AD. |
| sub |Identificador do requerente token. Este é um identificador persistente e imutável para o utilizador que descreve o token. Utilize este valor na lógica de colocação em cache. |
| TID |O identificador (ID) do inquilino do Azure AD que emitiu o token de inquilino. |
| unique_name |Um identificador exclusivo para o que pode ser apresentado ao utilizador. Isto é normalmente um nome principal de utilizador (UPN). |
| UPN |Nome principal de utilizador do utilizador. |
| ver |Versão. A versão do token JWT, normalmente 1.0. |

### Resposta de erro
Os erros de ponto final de emissão de tokens são códigos de erro HTTP, uma vez que o cliente chama o ponto final de emissão de tokens diretamente. Para além do código de estado HTTP, o ponto final de emissão de tokens de AD do Azure também devolve um documento JSON com objetos que descrevem o erro.

Uma resposta de erro de exemplo foi ter este aspeto:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros ocorridos e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro de STS específicas que podem ajudar no diagnóstico. |
| carimbo de data/hora |A hora em que ocorreu o erro. |
| trace_id |Um identificador exclusivo para o pedido que pode ajudar no diagnóstico. |
| correlation_id |Um identificador exclusivo para o pedido que pode ajudar no diagnóstico componentes. |

#### Códigos de estado HTTP
A tabela seguinte lista os códigos de estado HTTP que devolve o ponto final de emissão de tokens. Em alguns casos, o código de erro é suficiente para descrever a resposta, mas se não existirem erros, tem de analisar o documento JSON associado e examinar o código de erro.

| Código de HTTP | Descrição |
| --- | --- |
| 400 |Código HTTP predefinido. Utilizado na maioria dos casos e é normalmente devido a um pedido com formato incorreto. Corrija e submeta novamente o pedido. |
| 401 |Falha na autenticação. Por exemplo, o pedido está em falta o parâmetro client_secret. |
| 403 |Falha de autorização. Por exemplo, o utilizador não tem permissão para aceder ao recurso. |
| 500 |Erro interno do serviço. Repita o pedido. |

#### Códigos de erro para erros de ponto final de tokens
| Código de erro | Descrição | Ação de cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário em falta. |Corrija e submeta novamente o pedido |
| invalid_grant |O código de autorização é inválido ou expirou. |Tente um novo pedido para o `/authorize` ponto final |
| unauthorized_client |O cliente autenticado não está autorizado a utilizar este tipo de conceder de autorização. |Normalmente, isto ocorre quando a aplicação de cliente não está registada no Azure AD ou não é adicionada ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo para o Azure AD. |
| invalid_client |Falha na autenticação de cliente. |As credenciais do cliente não são válidas. Para corrigir, o administrador da aplicação atualiza as credenciais. |
| unsupported_grant_type |O servidor de autorização não suporta o tipo de conceder de autorização. |Altere o tipo de concessão no pedido. Este tipo de erro deve ocorrem apenas durante o desenvolvimento e ser detetado durante o teste inicial. |
| invalid_resource |O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo para o Azure AD. |
| interaction_required |O pedido requer interação do utilizador. Por exemplo, um passo de autenticação adicional é necessário. | Em vez de um pedido não interativa, tente novamente com um pedido de autorização interativa para o mesmo recurso. |
| temporarily_unavailable |O servidor estiver temporariamente demasiado ocupado para processar o pedido. |Repita o pedido. A aplicação cliente pode explicar ao utilizador que a respetiva resposta está atrasada devido a uma condição temporária. |

## Utilizar o token de acesso para aceder ao recurso
Agora que já obteve com êxito um `access_token`, pode utilizar o token de pedidos a APIs da Web, incluindo-na `Authorization` cabeçalho. O [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) especificação explica como utilizar os tokens de portador em pedidos de HTTP para aceder a recursos protegidos.

### Exemplo de pedido
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### Resposta de erro
Recursos protegidos que implementam os códigos de estado HTTP do problema 6750 de RFC. Se o pedido não inclui as credenciais de autenticação ou está em falta o token, a resposta inclui um `WWW-Authenticate` cabeçalho. Quando um pedido falha, o servidor de recursos responde com o código de estado HTTP e um código de erro.

Segue-se um exemplo de uma resposta sem êxito quando o pedido de cliente não inclui o token de portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### Parâmetros de erro
| Parâmetro | Descrição |
| --- | --- |
| authorization_uri |O URI (ponto final físico) do servidor de autorização. Este valor é também utilizado como uma chave de pesquisa para obter mais informações sobre o servidor a partir de um ponto final de deteção. <p><p> O cliente tem de verificar que o servidor de autorização é fidedigno. Quando o recurso está protegido pelo Azure AD, é suficiente verificar que o URL começa com https://login.microsoftonline.com ou outro nome de anfitrião que suporte do Azure AD. Um recurso de inquilino específico deve sempre devolver um URI de autorização de inquilino específico. |
| erro |Um valor de código de erro definido na secção 5.2 do [Framework de autorização do OAuth 2.0](http://tools.ietf.org/html/rfc6749). |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável de utilizador final. |
| resource_id |Devolve o identificador exclusivo do recurso. A aplicação de cliente pode utilizar este identificador como o valor de `resource` parâmetro quando que solicita um token para o recurso. <p><p> É importante para a aplicação de cliente verificar este valor, caso contrário, um serviço mal intencionado poderá induce um **elevação de privilégios** ataque <p><p> A estratégia recomendada para impedir que um ataque é para verificar se o `resource_id` corresponde à base de URL de API web que está a ser acedido. Por exemplo, se estiver a ser acedido https://service.contoso.com/data, o `resource_id` pode ser htttps://service.contoso.com/. A aplicação cliente deve rejeitar um `resource_id` que não começa com o URL de base a menos que exista uma maneira alternativa fiável para verificar o id. |

#### Códigos de erro do esquema de portador
A especificação de RFC 6750 define os seguintes erros para os recursos que utilizam o cabeçalho WWW-Authenticate e o esquema de portador na resposta.

| Código de estado de HTTP | Código de erro | Descrição | Ação de cliente |
| --- | --- | --- | --- |
| 400 |invalid_request |O pedido não é bem formado. Por exemplo, pode ser um parâmetro em falta ou utilizando o mesmo parâmetro duas vezes. |Corrija o erro e repita o pedido. Este tipo de erro deve ocorrem apenas durante o desenvolvimento e detetado nos testes iniciais. |
| 401 |invalid_token |O token de acesso está em falta, inválida ou foi revogado. O valor do parâmetro error_description fornece detalhes adicionais. |Pedir um novo token do servidor de autorização. Se o novo token falhar, Ocorreu um erro inesperado. Envie uma mensagem de erro do utilizador e tente novamente após atrasos aleatórios. |
| 403 |insufficient_scope |O token de acesso não contém as permissões de representação necessárias para aceder ao recurso. |Envie um novo pedido de autorização para o ponto final de autorização. Se a resposta contém o parâmetro de âmbito, utilize o valor do âmbito no pedido para o recurso. |
| 403 |insufficient_access |O requerente do token não tem as permissões que são necessárias para aceder ao recurso. |Pedir ao utilizador para utilizar uma conta diferente ou para solicitar permissões para o recurso especificado. |

## Atualizar os tokens de acesso
Tokens de acesso são de curta duração e têm de ser atualizados depois expirarem para continuar a aceder a recursos. Pode atualizar o `access_token` submetendo outro `POST` pedido para o `/token` ponto final, mas este tempo, fornecendo o `refresh_token` em vez do `code`.

Atualizar tokens não dispõe de durações especificadas. Normalmente, as durações da atualização de tokens são relativamente longos. No entanto, em alguns casos, atualização de tokens expira, é revogada ou não têm privilégios suficientes para a acção pretendida. A aplicação tem de esperar e processar erros devolvidos pelo ponto final de emissão de tokens corretamente.

Quando recebem uma resposta com um erro de token de atualização, rejeitar o token de atualização atual e um novo código de autorização ou um pedido de token de acesso. Em particular, quando utilizar uma atualização do token no fluxo de concessão do código de autorização, se receber uma resposta com o `interaction_required` ou `invalid_grant` códigos de erro, rejeitar o token de atualização e peça um novo código de autorização.

Um pedido de exemplo para o **inquilino específico** ponto final (também pode utilizar o **comuns** endpoint) para obter acesso um novo token utilizando um token de atualização tem este aspeto:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### Resposta com êxito
Uma resposta de token com êxito irá ter o seguinte aspeto:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parâmetro | Descrição |
| --- | --- |
| token_type |O tipo de token. O único valor suportado é **portador**. |
| expires_in |A duração restantes do token em segundos. Um valor típico é 3600 (uma hora). |
| expires_on |A data e hora em que o token expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até que a hora de expiração. |
| Recursos |Identifica o recurso seguro que o token de acesso pode ser utilizado para acesso. |
| Âmbito |Representação, as permissões concedidas à aplicação cliente nativa. A permissão predefinida é **user_impersonation**. O proprietário do recurso de destino pode registar valores alternativos no Azure AD. |
| access_token |O novo token de acesso que foi pedido. |
| refresh_token |Refresh_token OAuth 2.0 novo que pode ser utilizado para pedir tokens de acesso de novo quando expira desta resposta. |

### Resposta de erro
Uma resposta de erro de exemplo foi ter este aspeto:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros ocorridos e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar um programador identificar a causa de raiz de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro de STS específicas que podem ajudar no diagnóstico. |
| carimbo de data/hora |A hora em que ocorreu o erro. |
| trace_id |Um identificador exclusivo para o pedido que pode ajudar no diagnóstico. |
| correlation_id |Um identificador exclusivo para o pedido que pode ajudar no diagnóstico componentes. |

Para obter uma descrição dos códigos de erro e a ação de cliente recomendada, consulte [códigos de erro para erros de ponto final de tokens](#error-codes-for-token-endpoint-errors).
