---
title: "Autenticação de serviços do Azure AD, utilizando OAuth2.0 | Microsoft Docs"
description: "Este artigo descreve como utilizar mensagens HTTP para implementar a autenticação de serviço a serviço utilizando o fluxo de concessão de credenciais de cliente OAuth2.0."
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: cc30a54cd56c0cb03a67f86e4552398baa764e58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# Serviço para chamadas de serviço utilizando credenciais de cliente (segredo partilhado ou certificado)
O OAuth 2.0 cliente credenciais conceder fluxo permite um serviço web (*cliente confidencial*) para utilizar as suas próprias credenciais em vez de representar um utilizador, para autenticação quando chama outro serviço da web. Neste cenário, o cliente é normalmente um serviço web de camada média, um serviço do daemon ou o web site. Um nível mais elevado de garantia, o Azure AD permite também que o serviço de chamada utilizar um certificado (em vez de um segredo partilhado) como uma credencial.

## Diagrama de fluxo de conceder de credenciais do cliente
O diagrama seguinte explica como as credenciais do cliente conceder fluxo funciona no Azure Active Directory (Azure AD).

![Fluxo de concessão de credenciais de cliente OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. A aplicação cliente autentica para o ponto final de emissão de tokens de AD do Azure e os pedidos de um token de acesso.
2. O ponto final de emissão de tokens do Azure AD emite o token de acesso.
3. O token de acesso é utilizado para autenticar para o recurso protegido.
4. Dados a partir de recursos protegidos, são devolvidos para a aplicação web.

## Registar os serviços no Azure AD
Registe o serviço de chamada e o serviço de receção no Azure Active Directory (Azure AD). Para obter instruções detalhadas, consulte [integrar aplicações com o Azure Active Directory](active-directory-integrating-applications.md).

## Pedir um Token de acesso
Para pedir um token de acesso, utilize um HTTP POST para o inquilino específico ponto final do Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## Pedido de token de acesso de serviços
Existem dois cenários, dependendo se a aplicação cliente escolhe estar protegido por um segredo partilhado ou um certificado.

### Primeiro maiúsculas e minúsculas: pedido de token de acesso com um segredo partilhado
Quando utilizar um segredo partilhado, um pedido de token de acesso de serviço a serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |Necessário |Especifica o tipo de pedido de concessão. Um fluxo de concessão de credenciais do cliente, o valor tem de ser **client_credentials**. |
| client_id |Necessário |Especifica o id de cliente do Azure AD do serviço web chamada. Para localizar ID de cliente a aplicação de chamada, além de [portal do Azure](https://portal.azure.com), clique em **do Active Directory**, mude de diretório, clique na aplicação. O client_id é o *ID da aplicação* |
| client_secret |Necessário |Introduza uma chave registada para a chamada serviço ou o daemon de aplicação web no Azure AD. Para criar uma chave no portal do Azure, clique em **do Active Directory**, mude de diretório, clique na aplicação, clique em **definições**, clique em **chaves**, e adicionar uma chave.|
| Recursos |Necessário |Introduza o URI de ID de aplicação do serviço web do recetor. Para obter o URI de ID de aplicação no portal do Azure, clique em **do Active Directory**, mude de diretório, clique na aplicação de serviço e, em seguida, clique em **definições** e **propriedades** |

#### Exemplo
O HTTP POST seguintes pedidos de um token de acesso para o serviço web de https://service.contoso.com/. O `client_id` identifica o serviço web que os pedidos de token de acesso.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### Segunda caso: pedido de token de acesso com um certificado
Um pedido de token de acesso de serviços com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |Necessário |Especifica o tipo de resposta pedida. Um fluxo de concessão de credenciais do cliente, o valor tem de ser **client_credentials**. |
| client_id |Necessário |Especifica o id de cliente do Azure AD do serviço web chamada. Para localizar ID de cliente a aplicação de chamada, além de [portal do Azure](https://portal.azure.com), clique em **do Active Directory**, mude de diretório, clique na aplicação. O client_id é o *ID da aplicação* |
| client_assertion_type |Necessário |O valor tem de ser`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Necessário | Uma asserção (um JSON Web Token) que precisa para criar e assinar com o certificado é registado como as credenciais para a sua aplicação. Leia sobre [credenciais de certificado](active-directory-certificate-credentials.md) para aprender a registar o certificado e o formato da asserção.|
| Recursos | Necessário |Introduza o URI de ID de aplicação do serviço web do recetor. Para obter o URI de ID de aplicação no portal do Azure, clique em **do Active Directory**, clique no diretório, clique na aplicação e, em seguida, clique em **configurar**. |

Tenha em atenção que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado com a exceção que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

#### Exemplo
O HTTP POST seguintes pedidos de um token de acesso para o serviço web de https://service.contoso.com/ com um certificado. O `client_id` identifica o serviço web que os pedidos de token de acesso.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### Resposta de Token de acesso de serviços

Uma resposta de êxito contém uma resposta JSON OAuth 2.0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de pedido de acesso. O serviço web de chamada pode utilizar este token para autenticar para o serviço web de receção. |
| token_type |Indica o valor de tipo de token. O único tipo que suporta do Azure AD é **portador**. Para obter mais informações sobre os tokens de portador, consulte o [Framework de autorização do OAuth 2.0: utilização de tokens de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Quanto o token de acesso é válido (em segundos). |
| expires_on |O tempo que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até que a hora de expiração. Este valor é utilizado para determinar a duração de tokens em cache. |
| not_before |O tempo do que o token de acesso fica utilizável. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até que o tempo de validade para o token.|
| Recursos |O URI de ID de aplicação do serviço web do recetor. |

#### Exemplo de resposta
O exemplo seguinte mostra a resposta de êxito a um pedido de um token de acesso a um serviço web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## Consultar também
* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md)
* [Exemplo em c# da chamada de serviços com um segredo partilhado](https://github.com/Azure-Samples/active-directory-dotnet-daemon) e [exemplo em c# da chamada de serviços com um certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
