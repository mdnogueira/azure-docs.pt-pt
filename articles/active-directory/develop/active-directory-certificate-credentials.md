---
title: As credenciais de certificado no Azure AD | Microsoft Docs
description: "Este artigo descreve o registo e a utilização de credenciais de certificado para autenticação de aplicação"
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 08bb5140bb35bbd120aaa506afeab8ad247f81e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="certificate-credentials-for-application-authentication"></a>Credenciais do certificado para autenticação de aplicação

Azure Active Directory permite uma aplicação a utilizar as suas próprias credenciais para autenticação, por exemplo, o fluxo de concessão de credenciais do OAuth 2.0 cliente e o fluxo em-nome-de.
Uma forma de credenciais que podem ser utilizados é uma asserção de JSON Web Token(JWT) assinada com um certificado que é proprietário da aplicação.

## <a name="format-of-the-assertion"></a>Formato da asserção
Para calcular a asserção, provavelmente pretende utilizar um dos muitos [JSON Web Token](https://jwt.io/) bibliotecas num idioma à sua escolha. As informações transportadas pelo token são:

#### <a name="header"></a>Cabeçalho

| Parâmetro |  Remark |
| --- | --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | Deve ser o thumbprint do certificado de x. 509 SHA-1 |

#### <a name="claims-payload"></a>Afirmações (Payload)

| Parâmetro |  Remark |
| --- | --- | --- |
| `aud` | : Público-alvo  **https://login.microsoftonline.com/*tenant_Id*  /oauth2/token * * |
| `exp` | Data de expiração: a data em que o token expira. A hora é representada como o número de segundos de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até que a hora a validade do token expira.|
| `iss` | Emissor: deve ser o client_id (Id de aplicação do serviço de cliente) |
| `jti` | GUID: o ID de JWT |
| `nbf` | Não antes: a data antes do qual o token não pode ser utilizado. A hora é representada como o número de segundos de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até à vez que o token foi emitido. |
| `sub` | Requerente: como para `iss`, deve ser o client_id (Id de aplicação do serviço de cliente) |

#### <a name="signature"></a>Assinatura
A assinatura é calculada ao aplicar o certificado, conforme descrito no [especificação de JSON Web Token RFC7519](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma asserção JWT descodificado
```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

### <a name="example-of-an-encoded-jwt-assertion"></a>Exemplo de uma asserção JWT codificado
A cadeia seguinte é um exemplo de asserção codificado. Se procurar cuidadosamente, repare três secções separadas por pontos (.).
A primeira secção codifica o cabeçalho, o segundo o payload e do último é a assinatura calculada com os certificados do conteúdo das duas primeiras secções.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Registar o certificado com o Azure AD
Para associar a credencial de certificado com a aplicação de cliente no Azure AD, terá de editar o manifesto da aplicação.
Ter espera de um certificado, terá de computação:
- `$base64Thumbprint`, que é o base64 codificação do Hash de certificado
- `$base64Value`, que é o base64 codificação de dados brutos do certificado

terá também de fornecer um GUID para identificar a chave no manifesto da aplicação (`$keyId`)

No registo de aplicações do Azure para a aplicação de cliente, abra o manifesto da aplicação e substitua o *keyCredentials* propriedade com as novas informações de certificado utilizando o esquema seguinte:
```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
]
```

Guardar as edições que efectuou o manifesto da aplicação e carregar para o Azure AD. A propriedade de keyCredentials é com múltiplos valor, pelo que pode carregar vários certificados mais rico gestão de chaves.
