---
title: "Autenticação de serviços do Azure AD, utilizando OAuth2.0 especificação em-nome-de rascunho | Microsoft Docs"
description: "Este artigo descreve como utilizar mensagens HTTP para implementar a autenticação de serviços utilizando a OAuth2.0 fluxo em-nome-de."
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0bb74816f216f0965c3ec780c4895cf7e488c3cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Serviço para chamadas de serviço com o delegado de fluxo em-nome-de identidade do utilizador
On-Behalf-Of de 2.0 OAuth fluxo funciona o caso de utilização onde uma aplicação invoca uma serviço/API web, que por sua vez tem de chamar outro serviço/API web. A ideia é para propagar a identidade do delegado de utilizador e as permissões através da cadeia de pedidos. Para o serviço de camada média fazer pedidos autenticados para o serviço a jusante, tem de proteger um token de acesso do Azure Active Directory (Azure AD), em nome do utilizador.

## <a name="on-behalf-of-flow-diagram"></a>Diagrama de fluxo em-nome-de
Partem do princípio de que o utilizador ser autenticado utilizando uma aplicação a [fluxo de concessão do código de autorização do OAuth 2.0](active-directory-protocols-oauth-code.md). Neste momento, a aplicação tem um token de acesso (token A) com afirmações do utilizador e do consentimento para aceder à camada média web API (API A). Agora, API A necessita fazer um pedido autenticado ao web a jusante API (API B).

Os passos que se seguem constituem o fluxo em-nome-de e são explicados com a ajuda do diagrama a seguir.

![OAuth2.0 em-nome-de fluxo](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. A aplicação de cliente faz um pedido para API A com o token A.
2. API A autentica para o ponto final de emissão de tokens de AD do Azure e os pedidos de um token para aceder à API b.
3. O ponto final de emissão de tokens de AD do Azure valida as credenciais de API A com um token e emite o token de acesso para a API B (token B).
4. O token B está definido no cabeçalho de autorização do pedido para o b de API.
5. Dados a partir de recursos protegidos são devolvidos pelo b de API.

## <a name="register-the-application-and-service-in-azure-ad"></a>Registar a aplicação e do serviço no Azure AD
Registe a aplicação de cliente e o serviço de camada média no Azure AD.
### <a name="register-the-middle-tier-service"></a>Registar o serviço de camada média
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta e, no **diretório** lista, escolha o inquilino do Active Directory onde pretende registar a sua aplicação.
3. Clique em **mais serviços** na navegação esquerda e escolha **do Azure Active Directory**.
4. Clique em **registos de aplicação** e escolha **novo registo de aplicação**.
5. Introduza um nome amigável para a aplicação e selecione o tipo de aplicação. Com base num conjunto de tipo de aplicação de início de sessão no URL de redirecionamento URL ou para o URL de base. Clique em **criar** ao criar a aplicação.
6. Enquanto ainda no portal do Azure, escolha a sua aplicação e clique em **definições**. No menu de definições, escolha **chaves** e adicionar uma chave – Selecione uma duração de chave do ano 1 ou 2 anos. Quando guardar esta página, o valor da chave será apresentado, copie e guarde o valor numa localização segura, terá - esta chave mais tarde para configurar as definições da aplicação na sua implementação - este valor de chave não serão apresentadas novamente, nem recuperável por quaisquer outros meios , para registar assim que está visível a partir do Portal do Azure.

### <a name="register-the-client-application"></a>Registar a aplicação de cliente
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta e, no **diretório** lista, escolha o inquilino do Active Directory onde pretende registar a sua aplicação.
3. Clique em **mais serviços** na navegação esquerda e escolha **do Azure Active Directory**.
4. Clique em **registos de aplicação** e escolha **novo registo de aplicação**.
5. Introduza um nome amigável para a aplicação e selecione o tipo de aplicação. Com base num conjunto de tipo de aplicação de início de sessão no URL de redirecionamento URL ou para o URL de base. Clique em **criar** ao criar a aplicação.
6. Configurar permissões para a sua aplicação - no menu definições, escolha o **as permissões necessárias** secção, clique em **adicionar**, em seguida, **selecionar um API**e escreva o nome de o serviço de camada média na caixa de texto. Em seguida, clique em **selecionar permissões** e selecione ' acesso *nome do serviço*'.

### <a name="configure-known-client-applications"></a>Configurar aplicações cliente conhecidos
Neste cenário, o serviço de camada média tem sem interação do utilizador para obter consentimento do utilizador para aceder à API da jusante. Por conseguinte, a opção para conceder acesso à API a jusante deve ser apresentada compromisso como parte de consentimento passo durante a autenticação.
Para tal, siga os passos abaixo para criar o registo da aplicação de cliente no Azure AD com o registo do serviço de camada média, intercala o consentimento necessário para o cliente e a camada média para uma única caixa de diálogo de enlace explicitamente.
1. Navegue para o registo do serviço de camada média e clique em **manifesto** para abrir o editor de manifesto.
2. No manifesto, localize o `knownClientApplications` propriedade de matriz e adicione o ID de cliente da aplicação cliente como um elemento.
3. Guardar o manifesto clicando a guardar no botão.

## <a name="service-to-service-access-token-request"></a>Serviço para o pedido de token de acesso de serviço
Para pedir um token de acesso, se um HTTP POST para o inquilino específico do Azure AD ponto final com os seguintes parâmetros.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Existem dois cenários, dependendo se a aplicação cliente escolhe estar protegido por um segredo partilhado ou um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro maiúsculas e minúsculas: pedido de token de acesso com um segredo partilhado
Quando utilizar um segredo partilhado, um pedido de token de acesso de serviço a serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |Necessário | O tipo de pedido de token. Para um pedido utilizando um JWT, o valor tem de ser **urn: ietf:params:oauth:grant-tipo: jwt-portador**. |
| Asserção |Necessário | O valor do token utilizado no pedido. |
| client_id |Necessário | O ID da aplicação atribuída para este serviço de chamada durante o registo com o Azure AD. Para localizar o ID da aplicação no Portal de gestão do Azure, clique em **do Active Directory**, clique no diretório e, em seguida, clique no nome de aplicação. |
| client_secret |Necessário | A chave registada para o serviço de chamada no Azure AD. Este valor deve ter foi indicado o momento de registo. |
| Recursos |Necessário | O URI de ID de aplicação do serviço de receção (recursos protegidos). Para obter o URI de ID de aplicação no Portal de gestão do Azure, clique em **do Active Directory**, clique no diretório, clique no nome de aplicação, clique em **todas as definições** e, em seguida, clique em **propriedades**. |
| requested_token_use |Necessário | Especifica a forma como o pedido deve ser processado. No fluxo em-nome-de, o valor tem de ser **on_behalf_of**. |
| Âmbito |Necessário | Lista de âmbitos para o pedido de token valores separados por um espaço. Para OpenID Connect, o âmbito **openid** tem de ser especificado.|

#### <a name="example"></a>Exemplo
O HTTP POST seguintes pedidos de um token de acesso para a API web de https://graph.windows.net. O `client_id` identifica o serviço que os pedidos de token de acesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segunda caso: pedido de token de acesso com um certificado
Um pedido de token de acesso de serviços com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |Necessário | O tipo de pedido de token. Para um pedido utilizando um JWT, o valor tem de ser **urn: ietf:params:oauth:grant-tipo: jwt-portador**. |
| Asserção |Necessário | O valor do token utilizado no pedido. |
| client_id |Necessário | O ID da aplicação atribuída para este serviço de chamada durante o registo com o Azure AD. Para localizar o ID da aplicação no Portal de gestão do Azure, clique em **do Active Directory**, clique no diretório e, em seguida, clique no nome de aplicação. |
| client_assertion_type |Necessário |O valor tem de ser`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Necessário | Uma asserção (um JSON Web Token) que precisa para criar e assinar com o certificado é registado como as credenciais para a sua aplicação.  Leia sobre [credenciais de certificado](active-directory-certificate-credentials.md) para aprender a registar o certificado e o formato da asserção.|
| Recursos |Necessário | O URI de ID de aplicação do serviço de receção (recursos protegidos). Para obter o URI de ID de aplicação no Portal de gestão do Azure, clique em **do Active Directory**, clique no diretório, clique no nome de aplicação, clique em **todas as definições** e, em seguida, clique em **propriedades**. |
| requested_token_use |Necessário | Especifica a forma como o pedido deve ser processado. No fluxo em-nome-de, o valor tem de ser **on_behalf_of**. |
| Âmbito |Necessário | Lista de âmbitos para o pedido de token valores separados por um espaço. Para OpenID Connect, o âmbito **openid** tem de ser especificado.|

Tenha em atenção que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado com a exceção que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

#### <a name="example"></a>Exemplo
O HTTP POST seguintes pedidos de um token de acesso para a API de web https://graph.windows.net com um certificado. O `client_id` identifica o serviço que os pedidos de token de acesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Serviço de resposta de token de acesso de serviço
Uma resposta de êxito for uma resposta de JSON OAuth 2.0 com os seguintes parâmetros.

| Parâmetro | Descrição |
| --- | --- |
| token_type |Indica o valor de tipo de token. O único tipo que suporta do Azure AD é **portador**. Para obter mais informações sobre os tokens de portador, consulte o [Framework de autorização do OAuth 2.0: utilização de tokens de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| Âmbito |O âmbito de acesso concedido no token. |
| expires_in |O período de tempo do token de acesso é válido (em segundos). |
| expires_on |O tempo que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até que a hora de expiração. Este valor é utilizado para determinar a duração de tokens em cache. |
| Recursos |O URI de ID de aplicação do serviço de receção (recursos protegidos). |
| access_token |O token de pedido de acesso. O serviço de chamada pode utilizar este token para se autenticar o serviço de receção. |
| id_token |O token de id pedido. O serviço de chamada pode utilizar este para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| refresh_token |O token de atualização para o token de acesso solicitado. O serviço de chamada pode utilizar este token para pedir outro token de acesso após o atual token de acesso expira. |

### <a name="success-response-example"></a>Exemplo de resposta de êxito
O exemplo seguinte mostra a resposta de êxito a um pedido de um token de acesso para a API web de https://graph.windows.net.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Exemplo de resposta de erro
Uma resposta de erro é devolvida pelo ponto final de tokens do AD do Azure ao tentar adquirir um token de acesso para a API a jusante, se a API a jusante tem uma política de acesso condicional, tais como a autenticação multifator definido nela. O serviço de camada média deve superfície este erro para a aplicação de cliente para que a aplicação de cliente pode fornecer a interação do utilizador para satisfazer a política de acesso condicional.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Utilizar o token de acesso para aceder o recursos protegidos
Agora que o serviço de camada média, pode utilizar o token adquirido acima para fazer pedidos autenticados para o web a jusante API, definindo o token no `Authorization` cabeçalho.

### <a name="example"></a>Exemplo
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o protocolo OAuth 2.0 e outra forma de efetuar a autenticação de serviço a serviço utilizando credenciais do cliente.
* [Serviço de autenticação de serviço utilizando a concessão de credenciais de cliente OAuth 2.0 no Azure AD](active-directory-protocols-oauth-service-to-service.md)
* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md)
