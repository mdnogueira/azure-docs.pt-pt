---
title: "Alterações ao ponto final v 2.0 do Azure AD | Microsoft Docs"
description: "Uma descrição das alterações que estão a ser efetuadas para os protocolos de pré-visualização pública de v 2.0 de modelo de aplicação."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e6c5b891-0b5d-47dc-8184-5d0ef6a1a006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ae73833a68db14804dc40eaf07ff7d3effaa9052
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="important-updates-to-the-v20-authentication-protocols"></a>Atualizações importantes para os protocolos de autenticação de v 2.0
Programadores de atenção! Através de duas próximas semanas, iremos irá ser dynamicupdateservices alguns nosso protocolos de autenticação de v 2.0 que podem significar que interrompendo as alterações para quaisquer aplicações que ter escrito durante a nossa período de pré-visualização.  

## <a name="who-does-this-affect"></a>Que Isto afeta?
Todas as aplicações que tenham sido escritas para utilizar a v 2.0 convergido ponto final de autenticação

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Podem encontrar mais informações sobre o ponto final v 2.0 [aqui](active-directory-appmodel-v2-overview.md).

Se criou uma aplicação utilizando o ponto final v 2.0 pela codificação diretamente para o protocolo de v 2.0, utilizando qualquer um dos nossos middlewares de web OpenID Connect ou OAuth, ou outras bibliotecas de terceiros 3rd para efetuar a autenticação, deve ser preparado para testar os seus projetos e fazer alterações Se necessário.

## <a name="who-doesnt-this-affect"></a>Que não Isto afeta?
Todas as aplicações que foram escritas relativamente ao ponto de final para a autenticação de produção do Azure AD,

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Este protocolo está definido num stone e não irá ocorrer quaisquer alterações.

Além disso, se a aplicação **apenas** utiliza a nossa biblioteca da ADAL para efetuar a autenticação, não terá de alterar nada.  ADAL tem blindada a sua aplicação das alterações.  

## <a name="what-are-the-changes"></a>Quais são as alterações?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Remover o valor de x5t dos cabeçalhos JWT
O ponto final v 2.0 utiliza os tokens JWT extensivamente, que contenha uma secção de parâmetros de cabeçalho com metadados relevantes sobre o token.  Se descodificar o cabeçalho de um dos nossos JWTs atuais, poderia localizar algo semelhante ao seguinte:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Em que as propriedades de "x5t" e "kid" identificam a chave pública que deve ser utilizada para validar a assinatura do token, como obtidos a partir do ponto final de metadados OpenID Connect.

É a alteração que estamos aqui remover a propriedade "x5t".  Pode continuar a utilizar os mesmos mecanismos para validar os tokens, mas deverá confiar apenas a propriedade "kid" para obter a chave pública correta, conforme especificado no protocolo OpenID Connect. 

> [!IMPORTANT]
> **A tarefa: Certifique-se de que a aplicação não depende da existência do valor x5t.**
> 
> 

### <a name="removing-profileinfo"></a>Remover profile_info
Anteriormente, o ponto final v 2.0 tem sido devolver um objeto JSON codificado em base64 nas respostas token chamadas `profile_info`.  Quando pedir um token de acesso do ponto final v 2.0 ao enviar um pedido para:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

A resposta teria aspeto do seguinte objeto JSON:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

O `profile_info` valor contido obter informações sobre o utilizador que iniciou a sessão na aplicação – respetivo nome a apresentar, nome próprio, apelido, endereço de correio eletrónico, identificador e assim sucessivamente.  Principalmente, os `profile_info` foi utilizado para a colocação em cache token e apresentar fins.

Iremos agora está a remover o `profile_info` valor – mas não se preocupe, vamos ainda está a fornecer estas informações para os programadores num local ligeiramente diferente.  Em vez de `profile_info`, o ponto final v 2.0 agora irá devolver um `id_token` em cada resposta token:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Pode descodificar e analisar o id_token para obter as mesmas informações que recebeu profile_info.  O id_token é um Token Web JSON (JWT), com conteúdo, tal como especificado pelo OpenID Connect.  O código para fazê-lo, por isso, deve ser muito semelhante – apenas terá de extrair o segmento média (o corpo) do id_token e base64 descodificá-lo para aceder ao objeto JSON no.

Através de duas próximas semanas, deve por código a aplicação para obter as informações de utilizador a partir de `id_token` ou `profile_info`; que está presente.  Dessa forma, quando é efetuada a alteração, a aplicação totalmente pode processar a transição do `profile_info` para `id_token` sem interrupção.

> [!IMPORTANT]
> **A tarefa: Certifique-se de que a aplicação não depende da existência do `profile_info` valor.**
> 
> 

### <a name="removing-idtokenexpiresin"></a>Remover id_token_expires_in
Semelhante ao `profile_info`, estamos também a remover o `id_token_expires_in` parâmetro de respostas.  Anteriormente, o ponto final v 2.0 iria devolver um valor para `id_token_expires_in` juntamente com cada resposta id_token, para a instância numa resposta autorizar:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Ou numa resposta token:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

O `id_token_expires_in` valor seria indicar o número de segundos a id_token permanecerá válido para.  Agora, estamos a remover o `id_token_expires_in` valor completamente.  Em vez disso, pode utilizar o padrão de OpenID Connect `nbf` e `exp` afirmações para examinar a validade de uma id_token.  Consulte o [referência de token de v 2.0](active-directory-v2-tokens.md) para obter mais informações sobre estas afirmações.

> [!IMPORTANT]
> **A tarefa: Certifique-se de que a aplicação não depende da existência do `id_token_expires_in` valor.**
> 
> 

### <a name="changing-the-claims-returned-by-scopeopenid"></a>Alterar as afirmações devolvidas pelo âmbito = openid
Esta alteração será mais significativas – na realidade, afetará quase todas as aplicações que utiliza o ponto final v 2.0.  Muitas aplicações enviam pedidos para o ponto final v 2.0, utilizando o `openid` , como o âmbito:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Hoje, quando o utilizador concede consentimento para o `openid` âmbito, a aplicação recebe uma variedade de informações sobre o utilizador no id_token resultante.  Estas afirmações podem incluir o respetivo nome, nome de utilizador preferencial, endereço de correio eletrónico, ID de objeto e muito mais.

Esta atualização, está a alterar as informações que o `openid` âmbito affords o acesso de aplicação, para melhor comform com a especificação de OpenID Connect.  O `openid` âmbito só irá permitir que a sua aplicação iniciar sessão do utilizador no e receber um identificador de aplicação específicos para o utilizador no `sub` de afirmações da id_token.  As afirmações numa id_token com apenas o `openid` âmbito concedido será devoid of quaisquer informações pessoais identificáveis.  Exemplo id_token afirmações são:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Se pretender obter informações de identificação pessoal (PII) sobre o utilizador na sua aplicação, a aplicação será necessário solicitar permissões adicionais do utilizador.  Iremos introduzir suporte para dois âmbitos novos da especificação OpenID Connect – o `email` e `profile` âmbitos – que permitem-lhe fazê-lo.

* O `email` âmbito é muito simples – permite que o acesso de aplicação para o endereço de correio eletrónico principal do utilizador através de `email` o id_token de afirmação.  Tenha em atenção que o `email` afirmação não será sempre presente no id_tokens – apenas será incluído se disponíveis no perfil de utilizador.
* O `profile` âmbito affords o acesso de aplicação para todas as outras informações básicas sobre o utilizador – o respetivo nome, nome de utilizador preferencial, ID de objeto e assim sucessivamente.

Isto permite-lhe código a aplicação de forma mínima divulgação – pode pedir ao utilizador para apenas o conjunto de informações que requer a sua aplicação para efetuar a tarefa.  Se pretender continuar a obter o conjunto completo de informações de utilizador que a aplicação está atualmente a receber, deve incluir todos os âmbitos de três nos seus pedidos de autorização:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

A aplicação pode começar a enviar o `email` e `profile` âmbitos imediatamente e o ponto final v 2.0 irão aceitar estes dois âmbitos e começar a pedir as permissões dos utilizadores conforme necessário.  No entanto, a alteração na interpretação do `openid` âmbito não entrarão em vigor para de algumas semanas.

> [!IMPORTANT]
> **A tarefa: adicionar o `profile` e `email` âmbitos se a sua aplicação precisar de informações sobre o utilizador.**  Tenha em atenção que ADAL incluirá ambas estas permissões nos pedidos por predefinição. 
> 
> 

### <a name="removing-the-issuer-trailing-slash"></a>Remover o emissor à direita barra.
Anteriormente, o valor de emissor que aparece em tokens do ponto final v 2.0 demorou o formulário

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Em que o guid foi o tenantId do inquilino do Azure AD que emitiu o token.  Com estas alterações, o valor de emissor torna-se

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

em ambos os tokens e no documento de identificação OpenID Connect.

> [!IMPORTANT]
> **A tarefa: Certifique-se a sua aplicação aceita o valor do emissor com e sem uma barra durante a validação do emissor.**
> 
> 

## <a name="why-change"></a>Por que razão a alteração?
É a motivação primária para a introduzir estas alterações para estar em conformidade com a especificação de padrão OpenID Connect.  Sendo OpenID Connect em conformidade, Esperamos minimizar as diferenças entre a integração com os serviços de identidade da Microsoft e com outros serviços de identidade da indústria.  Queremos permitir que os programadores a utilizar as bibliotecas de autenticação de open source para favorito sem ter de alterar as bibliotecas para acomodar as diferenças de Microsoft.

## <a name="what-can-you-do"></a>O que fazer?
A partir de hoje, pode começar efetuar todas as alterações descritas acima.  Deve imediatamente:

1. **Remova quaisquer dependências no `x5t` parâmetro de cabeçalho.**
2. **Lidar graciosamente com a transição do `profile_info` para `id_token` nas respostas token.**
3. **Remova quaisquer dependências no `id_token_expires_in` parâmetro de resposta.**
4. **Adicionar o `profile` e `email` âmbitos para a sua aplicação, se a sua aplicação precisar de informações de utilizador básico.**
5. **Aceite os valores de emissor em tokens com e sem uma barra.**

A nossa [documentação do protocolo de v 2.0](active-directory-v2-protocols.md) já foi atualizado para refletir estas alterações, pelo que pode utilizá-lo como referência no ajudar a atualizar o seu código.

Se tiver alguma questão adicional no âmbito das alterações, hesite aceder à-no Twitter em @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Frequência ocorrerá alterações de protocolo?
Iremos não previr qualquer interrompendo mais alterações para os protocolos de autenticação.  Iremos intencionalmente são agrupamento estas alterações para uma versão, para que não terá de seguir este tipo de processo de atualização novamente sempre em breve.  Obviamente, vamos continuar a adicionar as funcionalidades para o serviço de autenticação de v 2.0 convergida que podem tirar partido da, mas essas alterações devem ser cumulativas e não quebra existente código.

Por último, gostaríamos de dizer Agradecemos-lhe por experimentar coisas durante o período de pré-visualização.  As experiências do nosso adotantes iniciais e insights tem sido valiosas, por conseguinte, até que ponto e esperamos que irá continuar para partilhar as suas opinions e ideias.

Codificação satisfeito!

A divisão de identidade da Microsoft

