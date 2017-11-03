---
title: "Saiba mais sobre o token diferente e afirmação tipos suportados pelo Azure AD | Microsoft Docs"
description: "Um guia para compreender e avaliar as afirmações no SAML 2.0 e JSON Web Tokens (JWT) emitidos pelo Azure Active Directory (AAD)"
documentationcenter: na
author: dstrockis
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: be28230b9c56dcbca4ba8f70e44741f65a447f73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-token-reference"></a>Referência de token do Azure AD
Azure Active Directory (Azure AD) emite vários tipos de tokens de segurança no processamento do cada fluxo de autenticação. Este documento descreve o formato, características de segurança e conteúdo de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens
Azure AD suporta o [protocolo de autorização do OAuth 2.0](active-directory-protocols-oauth-code.md), que utiliza access_tokens e refresh_tokens.  Também suporta a autenticação e início de sessão através de [OpenID Connect](active-directory-protocols-openid-connect-code.md), que apresenta um tipo de token, o id_token terceiro.  Cada um destes tokens é representada como um "token de portador".

Um token de portador é um token de segurança simples que concede acesso a "portador" para um recurso protegido. Este sentido, "portador" é que podem apresentar o token de terceiros. Apesar da autenticação com o Azure AD é necessário para receber um token de portador, passos devem ser colocados para proteger o token, para evitar interception por uma entidade indesejada. Porque os tokens de portador não dispõe de um mecanismo incorporado para impedir que as partes não autorizadas a utilizá-los, tem de ser transportados num canal seguro, como de segurança de camada de transporte (HTTPS). Se um token de portador é transmitido na limpar, um ataques man-in média ataque pode ser utilizado para adquirir o token e obter acesso não autorizado a um recurso protegido. Os princípios de segurança mesmo se aplica quando armazenar ou colocação em cache os tokens de portador para utilização posterior. Certifique-se sempre de que a aplicação transmite e armazena os tokens de portador de forma segura. Para mais considerações de segurança em tokens de portador, consulte [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo Azure AD são implementados como JSON Web Tokens ou JWTs.  Um JWT é um meio compact, segura de URL de transferência de informações entre duas partes.  As informações contidas no JWTs são conhecidos como "afirmações" ou asserções de informações sobre o assunto do token e portador.  As afirmações no JWTs são objetos JSON codificado e serializada para transmissão.  Uma vez que o JWTs emitidos pelo Azure AD são assinados, mas não encriptados, pode facilmente inspecionar os conteúdos de um JWT para fins de depuração.  Existem várias ferramentas disponíveis para fazê-lo, tal como [jwt.ms](https://jwt.ms/). Para obter mais informações sobre JWTs, pode consultar o [especificação JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens são um formulário de início de sessão de token de segurança que a aplicação recebe quando efetuar a autenticação utilizando [OpenID Connect](active-directory-protocols-openid-connect-code.md).  Estes são representados como [JWTs](#types-of-tokens)e contêm afirmações que pode utilizar para o utilizador a iniciar sessão na sua aplicação.  Pode utilizar as afirmações uma id_token como julgar - normalmente são utilizadas para apresentar informações de conta ou tomar decisões de controlo de acesso numa aplicação.

Id_tokens estiver assinado, mas não encriptada neste momento.  Quando a aplicação recebe uma id_token, tem [validar a assinatura](#validating-tokens) para provar a autenticidade do token e validar alguns afirmações no token para provar a respetiva validade.  As afirmações validadas por uma aplicação variam dependendo dos requisitos de cenário, mas existem algumas [validações de afirmação comuns](#validating-tokens) que a aplicação tem de efetuar cada cenário.

Consulte a secção seguinte para obter informações sobre afirmações id_tokens, bem como uma id_token de exemplo.  Tenha em atenção que as afirmações no id_tokens não são devolvidas por qualquer ordem específica.  Além disso, novas afirmações podem ser introduzidas na id_tokens em qualquer ponto no tempo - não deve interromper a sua aplicação à nova afirmações que são introduzidas.  A lista seguinte inclui as afirmações que a aplicação pode interpretar fiável no momento desta redação.  Se necessário, ainda mais detalhes podem ser encontrado no [OpenID Connect especificação](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Id_token de exemplo
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Para prática, tente inspecionar as afirmações no id_token de exemplo através da colagem para [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Afirmações no id_tokens
> [!div class="mx-codeBreakAll"]
| JWT afirmação | Nome | Descrição |
| --- | --- | --- |
| `appid` |ID da aplicação |Identifica a aplicação que está a utilizar o token para aceder a um recurso. A aplicação pode agir como o próprio ou em nome de um utilizador. O ID da aplicação normalmente representa um objeto de aplicação, mas também pode representar um objeto principal do serviço no Azure AD. <br><br> **Valor de JWT exemplo**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud` |Audiência |O destinatário do token. A aplicação que recebe o token tem de verificar que o valor de audiência está correto e rejeitar quaisquer tokens se destina a um público-alvo diferentes. <br><br> **Valor de SAML do exemplo**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Valor de JWT exemplo**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Referência de classe de contexto de autenticação de aplicação |Indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se estiver a ID de cliente e o segredo do cliente, o valor é 1. <br><br> **Valor de JWT exemplo**: <br> `"appidacr": "0"` |
| `acr` |Referência de classe de contexto de autenticação |Indica como o requerente foi autenticado, em vez do cliente na afirmação de referência de classe de contexto de autenticação de aplicação. Um valor de "0" indica que a autenticação de utilizador final não cumpria os requisitos da norma ISO/IEC 29115. <br><br> **Valor de JWT exemplo**: <br> `"acr": "0"` |
| Autenticação instantânea |Regista a data e hora quando ocorreu a autenticação. <br><br> **Valor de SAML do exemplo**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Método de Autenticação |Identifica como o assunto do token foi autenticado. <br><br> **Valor de SAML do exemplo**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Valor de JWT exemplo**:`“amr”: ["pwd"]` |
| `given_name` |Nome Próprio |Fornece o primeiro ou "fornecido" nome do utilizador, conforme definido no objeto de utilizador do Azure AD. <br><br> **Valor de SAML do exemplo**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Valor de JWT exemplo**: <br> `"given_name": "Frank"` |
| `groups` |Grupos |Fornece os IDs de objeto que representam as associações de grupo do requerente. Estes valores são exclusivo (consulte o ID de objeto) e podem ser utilizados em segurança para gerir o acesso, tais como impor autorização para aceder a um recurso. Os grupos incluídos na afirmação grupos são configurados numa base por aplicação, através da propriedade de "groupMembershipClaims" de manifesto da aplicação. Um valor nulo excluir todos os grupos, um valor de "SecurityGroup" incluirá apenas associações a grupos de segurança do Active Directory e um valor de "All" irá incluem grupos de segurança e listas de distribuição do Office 365. <br><br> **Valor de SAML do exemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Valor de JWT exemplo**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` |Fornecedor de Identidade |Regista o fornecedor de identidade autenticado o assunto do token. Este valor é idêntico para o valor da afirmação de Issuer, a menos que a conta de utilizador de um inquilino diferente que o emissor. <br><br> **Valor de SAML do exemplo**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Valor de JWT exemplo**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Armazena o tempo no qual o token foi emitido. Muitas vezes, é utilizado para medir reatualização token. <br><br> **Valor de SAML do exemplo**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Valor de JWT exemplo**: <br> `"iat": 1390234181` |
| `iss` |emissor |Identifica o serviço de token de segurança (STS) que constrói e devolve o token. Os tokens que devolve do Azure AD, o emissor é sts.windows.net. O GUID no valor de afirmação de emissor é o ID de inquilino do diretório do Azure AD. O ID do inquilino é um identificador imutável e fiável do diretório. <br><br> **Valor de SAML do exemplo**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Valor de JWT exemplo**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Apelido |Fornece o último nome, apelido ou nome de família do utilizador, tal como definido no objeto de utilizador do Azure AD. <br><br> **Valor de SAML do exemplo**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Valor de JWT exemplo**: <br> `"family_name": "Miller"` |
| `unique_name` |Nome |Fornece um valor de legível humano que identifica o assunto do token. Este valor não é garantida ser exclusivo dentro de um inquilino e foi concebido para ser utilizado apenas para efeitos de apresentação. <br><br> **Valor de SAML do exemplo**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Valor de JWT exemplo**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |ID de objeto |Contém um identificador exclusivo de um objeto no Azure AD. Este valor é imutável e não pode ser reatribuído ou reutilizada. Utilize o ID de objeto para identificar um objeto nas consultas para o Azure AD. <br><br> **Valor de SAML do exemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Valor de JWT exemplo**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Funções |Representa todas as funções de aplicação que o requerente foi concedido diretamente e indiretamente através da associação de grupo e pode ser utilizado para impor controlo de acesso baseado em funções. Funções da aplicação são definidas numa base por aplicação, através de `appRoles` propriedade do manifesto da aplicação. O `value` propriedade de cada função de aplicação é o valor que aparece na afirmação de funções. <br><br> **Valor de SAML do exemplo**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Valor de JWT exemplo**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Âmbito |Indica as permissões de representação concedidas para a aplicação de cliente. A permissão predefinida é `user_impersonation`. O proprietário do recurso protegido pode registar valores adicionais no Azure AD. <br><br> **Valor de JWT exemplo**: <br> `"scp": "user_impersonation"` |
| `sub` |Assunto |Identifica o principal sobre os quais o token de asserções informações, tais como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado, pelo que pode ser utilizado para efetuar verificações de autorização de forma segura. Porque o requerente encontra-se sempre em tokens de problemas do Azure AD, recomendamos este valor a utilizar um sistema de autorização de objetivo geral. <br> `SubjectConfirmation`Não é uma afirmação. Descreve como o assunto do token é verificado. `Bearer`indica que o requerente é confirmado ao respetivo posse do token. <br><br> **Valor de SAML do exemplo**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Valor de JWT exemplo**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |ID do inquilino |Um identificador de imutável e não reutilizáveis que identifica o inquilino de diretório que emitiu o token. Pode utilizar este valor para aceder a recursos de inquilino específico diretório numa aplicação multi-inquilino. Por exemplo, pode utilizar este valor para identificar o inquilino numa chamada para a Graph API. <br><br> **Valor de SAML do exemplo**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Valor de JWT exemplo**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Duração do token |Define o intervalo de tempo no qual um token é válido. O serviço que valida o token deve verificar que a data atual está dentro da duração do token, pessoa que deve rejeitar o token. O serviço poderá permitir até cinco minutos ultrapassou o intervalo de duração do token para uma conta para todas as diferenças no tempo de relógio ("desfasamento de tempo") entre o Azure AD e o serviço. <br><br> **Valor de SAML do exemplo**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Valor de JWT exemplo**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Nome Principal de utilizador |Armazena o nome de utilizador de principal de utilizador.<br><br> **Valor de JWT exemplo**: <br> `"upn": frankm@contoso.com` |
| `ver` |Versão |Armazena o número de versão do token. <br><br> **Valor de JWT exemplo**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Tokens de acesso
Após a autenticação com êxito, o Azure AD devolve um token de acesso, o que pode ser utilizado para aceder a recursos protegidos. O token de acesso é uma base 64 codificado JSON Web tokens (JWT) e o respetivo conteúdo pode ser inspecionado pelo executá-lo através de um descodificador.

Se a aplicação apenas *utiliza* tokens de acesso para obter acesso a APIs, pode (e deve) tratar tokens de acesso como completamente opaco - são apenas cadeias que a aplicação pode passar para recursos em pedidos de HTTP.

Quando pedir um token de acesso, o Azure AD devolve também alguns metadados sobre o token de acesso para consumo da sua aplicação.  Estas informações incluem a hora de expiração do token de acesso e âmbitos para a qual é válido.  Isto permite à aplicação para efetuar a colocação em cache inteligente de tokens de acesso sem ter de analisar a abrir o token de acesso si próprio.

Se a aplicação é uma API protegida com o Azure AD que espera tokens de acesso em pedidos de HTTP, em seguida, deverá efetuar inspeção dos tokens de que receber e a validação. A aplicação deve efetuar a validação do token de acesso antes de a utilizar para aceder aos recursos. Para obter mais informações sobre a validação, consulte [validar os Tokens](#validating-tokens).  
Para obter detalhes sobre como fazê-lo com o .NET, consulte [proteger uma API Web utilizando tokens de portador do Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

## <a name="refresh-tokens"></a>Tokens de atualização

Atualize os tokens são tokens de segurança, a aplicação pode utilizar para adquirir novos tokens de acesso de um fluxo de OAuth 2.0.  Permite que a aplicação alcançar a longo prazo acesso a recursos em nome de um utilizador sem necessidade de interação pelo utilizador.

Os tokens de atualização são vários recursos.  Que seja diga a que um token de atualização recebido durante um pedido de token para um recurso pode ser resgatada para tokens de acesso a um recurso completamente diferente. Para tal, defina o `resource` parâmetro no pedido para o recurso de destino.

Os tokens de atualização são completamente opaco à sua aplicação. Estão longa duração, mas a aplicação não deve ser escrita esperar que um token de atualização duram qualquer período de tempo.  Atualização de tokens pode ser inválida em qualquer momento para diversos motivos.  É a única forma da sua aplicação para saber se um token de atualização é válido para tentar resgatar-ao efetuar um pedido de token para o ponto final de token do Azure AD.

Quando resgatar um token de atualização para um novo token de acesso, irá receber um novo token de atualização na resposta token.  Deve guardar o token de atualização emitidos recentemente, substituindo que utilizou no pedido.  Isto irá garantir que os tokens de atualização mantêm-se válidas para, desde que possível.

## <a name="validating-tokens"></a>Tokens de validação

Para validar uma id_token ou um access_token, a aplicação deve validar a assinatura do token e as afirmações. A aplicação também deve validar para validar os tokens de acesso, o emissor, o público-alvo e os tokens de assinatura. Estes têm de ser validada face aos valores no documento de identificação de OpenID. Por exemplo, a versão de independente de inquilino do documento está localizada em [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration). Do Azure AD middleware possui capacidades incorporadas para validar os tokens de acesso e pode procurar através do nosso [amostras](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-code-samples) para localizar um idioma da sua preferência. Para obter mais informações sobre como validar explicitamente um token JWT, consulte o [exemplo de validação do manual JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).  

Fornecemos bibliotecas e exemplos de código que mostram como processar facilmente a validação de token - o abaixo informações simplesmente é fornecida para quem pretende compreender o processo de subjacente.  Também existem vários bibliotecas de open source para terceiros disponíveis para a validação de JWT - há, pelo menos, uma opção para quase todas as plataforma e de idioma horizontalmente. Para obter mais informações sobre bibliotecas de autenticação do Azure AD e exemplos de código, consulte [bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>A validar a assinatura

Um JWT contém três segmentos, separados pelo `.` caráter.  O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo**e a terceira como o **assinatura**.  O segmento de assinatura pode ser utilizado para validar a autenticidade do token de modo a que pode ser fidedigno pela sua aplicação.

Tokens emitidos pelo Azure AD tem sessão iniciados utilizando os algoritmos de encriptação assimétrico padrão de indústria, como RSA 256. O cabeçalho do JWT contém informações sobre o método de encriptação e a chave utilizada para assinar o token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

O `alg` afirmação indica o algoritmo que foi utilizado para assinar o token, tempo de `x5t` afirmação indica a chave pública específica que foi utilizada para assinar o token.

Em qualquer determinada altura, do Azure AD pode iniciar sessão uma id_token utilizando qualquer um de um determinado conjunto de pares de chaves públicas-privadas. Azure AD roda o conjunto de chaves periodicamente, possíveis para a sua aplicação deve ser escrita para lidar com essas alterações chaves automaticamente.  Uma frequência razoável para procurar atualizações para as chaves públicas utilizadas pelo Azure AD é a cada 24 horas.

Pode obter os dados assinatura de chave necessários validar a assinatura utilizando o documento de metadados OpenID Connect, localizado em:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Repita este URL num browser!
> 
> 

Este documento de metadados é um objeto JSON que contém várias partes útil de informação, como a localização dos vários pontos finais necessários para efetuar a autenticação OpenID Connect.  

Também inclui um `jwks_uri`, que lhe oferece a localização do conjunto de chaves públicas utilizado para assinar os tokens.  O documento JSON localizado no `jwks_uri` contém todas as informações de chaves públicas em utilização nesse momento específico.  A aplicação pode utilizar o `kid` no cabeçalho de JWT para selecionar qual a chave pública neste documento foi utilizada para assinar um determinado token de afirmação.  Em seguida, pode efetuar a validação da assinatura utilizando a chave pública correta e o algoritmo indicado.

Efetuar a validação da assinatura está fora do âmbito deste documento - existem bibliotecas de open source para muitos disponíveis para ajudar a fazê-lo se for necessário.

#### <a name="validating-the-claims"></a>A validar as afirmações

Quando a aplicação recebe um token (uma id_token após o início de sessão do utilizador, ou um token de acesso como um token de portador no pedido de HTTP)-também deve efetuar algumas verificações contra as afirmações no token.  Estes incluem, mas não estão limitados a:

* O **público-alvo** afirmação - para verificar que o token se destinar a ser indicado para a sua aplicação.
* O **não antes** e **hora de expiração** afirmações - Certifique-se de que o token não expirou.
* O **emissor** afirmação - para verificar se o token foi emitido, de facto, a sua aplicação pelo Azure AD.
* O **Nonce** - para mitigar um ataque de reprodução de tokens.
* e muito mais...

Para obter uma lista completa das validações de afirmação a aplicação deverá efetuar para Tokens de identidade, consulte o [OpenID Connect especificação](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Detalhes dos valores esperados para estas afirmações incluídos no precedente [id_token secção](#id-tokens) secção.

## <a name="sample-tokens"></a>Tokens de exemplo

Esta secção apresenta exemplos de tokens de SAML e JWT devolve do Azure AD. Estes exemplos permitem-lhe ver as afirmações no contexto.

### <a name="saml-token"></a>SAML Token

Este é um exemplo de um token SAML típico.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Token JWT - representação de utilizadores
Este é um exemplo de um típico token de web do JSON (JWT) utilizado num fluxo de concessão do código de autorização.
Para além de afirmações, o token inclui um número de versão no **vidor** e **appidacr**, a referência de classe de contexto de autenticação, que indica como o cliente foi autenticado. Para um cliente público, o valor é 0. Se foi utilizado um ID de cliente ou o segredo do cliente, o valor é 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Conteúdo relacionado
* Consulte o Azure AD Graph [operações de política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e [entidade política](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), para saber mais sobre a gestão de política de duração do token através da API do Azure AD Graph.
* Para obter mais informações e exemplos em gestão de políticas através de cmdlets do PowerShell, incluindo exemplos, consulte [configuráveis durações token no Azure AD](../active-directory-configurable-token-lifetimes.md). 
