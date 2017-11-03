---
title: "Referência de tokens do Azure Active Directory v 2.0 | Microsoft Docs"
description: "Os tipos de tokens e afirmações emitidas pelo ponto final v 2.0 do Azure AD"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ec25d4375647a2c8983d7573b9912e544fc3e7b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Referência de tokens de v 2.0 do Azure Active Directory
O ponto de final de v 2.0 do Azure Active Directory (Azure AD) emite vários tipos de tokens de segurança em cada [fluxo de autenticação](active-directory-v2-flows.md). Esta referência descreve o formato, características de segurança e conteúdo de cada tipo de token.

> [!NOTE]
> O ponto final v 2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades. Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Tipos de tokens
O ponto final v 2.0 suporta o [protocolo de autorização do OAuth 2.0](active-directory-v2-protocols.md), que utiliza os tokens de acesso e tokens de atualização. O ponto final v 2.0 também suporta a autenticação e início de sessão através de [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect introduz um tipo de terceiro do token, o token de ID. Cada um destes tokens é representada como uma *portador* token.

Um token de portador é um token de segurança simples que concede acesso de portador para um recurso protegido. O portador é que podem apresentar o token de terceiros. Embora uma entidade tem de autenticar com o Azure AD para receber o token de portador, se os passos não são tidas para proteger o token durante a transmissão e o armazenamento, podem ser intercetado e utilizado por uma entidade indesejada. Alguns tokens de segurança tem um mecanismo incorporado para impedir que as partes não autorizadas a utilizá-los, mas não os tokens de portador. Os tokens de portador tem transportados num canal seguro, como de segurança de camada de transporte (HTTPS). Se um token de portador é transmitido sem este tipo de segurança, uma entidade maliciosa poderia utilizar um ataque"man-in-the-middle" para adquirir o token e utilizá-lo para o acesso não autorizado a um recurso protegido. Os princípios de segurança mesmo se aplica quando armazenar ou colocação em cache os tokens de portador para utilização posterior. Certifique-se sempre de que a aplicação e em segurança transmite armazena os tokens de portador. Para mais considerações de segurança para os tokens de portador, consulte [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo ponto final v 2.0 são implementados como JSON Web Tokens (JWTs). Um JWT é uma forma compact, segura de URL para transferir informações entre duas partes. As informações num JWT são chamadas um *afirmação*. É uma asserção de informações sobre o assunto do token e portador. As afirmações num JWT são objetos de JavaScript Object Notation (JSON) que são codificados e serializados para transmissão. Porque JWTs emitidos pelo ponto final v 2.0 estiver assinados, mas não encriptados, pode facilmente inspecionar os conteúdos de um JWT para fins de depuração. Para mais informações sobre JWTs, consulte o [especificação JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de ID
Um token de ID é um formulário de início de sessão de token de segurança que a aplicação recebe quando efetua a autenticação utilizando [OpenID Connect](active-directory-v2-protocols.md). Tokens de ID são representados como [JWTs](#types-of-tokens), e contêm afirmações que pode utilizar para a sessão do utilizador para a sua aplicação. Pode utilizar as afirmações num token ID de várias formas. Normalmente, admins Utilize tokens de identidade para apresentar informações de conta ou para tomar decisões de controlo de acesso numa aplicação. O ponto final v 2.0 emite apenas um tipo de token de ID, que tem um conjunto de afirmações, independentemente do tipo de utilizador que tem sessão iniciada consistente. O formato e o conteúdo dos tokens de ID são as mesmas para os utilizadores da conta Microsoft pessoais e para contas profissionais ou escolares.

Atualmente, os tokens de ID são assinados, mas não encriptados. Quando a aplicação recebe um token de ID, tem [validar a assinatura](#validating-tokens) para provar a autenticidade do token e validar alguns afirmações no token para provar a respetiva validade. As afirmações validadas por uma aplicação variam dependendo dos requisitos de cenário, mas a aplicação tem de efetuar alguns [validações de afirmação comuns](#validating-tokens) cada cenário.

Vamos dar-lhe os detalhes completos sobre afirmações em tokens de ID nas secções seguintes, para além de um token de ID de exemplo. Tenha em atenção que as afirmações no ID não são devolvidas por uma ordem específica. Além disso, podem ser introduzidas novas afirmações para os tokens de ID em qualquer altura. Não deverá interromper a sua aplicação quando são introduzidas novas afirmações. A lista seguinte inclui as afirmações que a aplicação atualmente fiável pode interpretar. Pode encontrar mais detalhes no [OpenID Connect especificação](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Token de ID de exemplo
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Para prática, para inspecionar as afirmações no token de ID de exemplo, cole o token de ID de exemplo para [calebb.net](http://calebb.net/).
>
>

#### <a name="claims-in-id-tokens"></a>Afirmações no ID
| Nome | Afirmação | Valor de exemplo | Descrição |
| --- | --- | --- | --- |
| público-alvo |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifica o destinatário do token. Tokens de ID, o público-alvo é ID da aplicação da sua aplicação, atribuído à sua aplicação no Portal de registo de aplicações da Microsoft. A aplicação deve validar este valor e rejeitar o token, se o valor não corresponde. |
| emissor |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifica o serviço de token de segurança (STS) que constrói e devolve o token e de inquilino do Azure AD em que o utilizador foi autenticado. A aplicação deve validar a afirmação de emissor para se certificar de que o token provém do ponto final v 2.0. Também deverá utilizar a parte do GUID de afirmação para restringir o conjunto de inquilinos que pode iniciar sessão aplicação. O GUID que indica que o utilizador é um utilizador de consumidor de uma conta Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| emitido no |`iat` |`1452285331` |A hora em que o token foi emitido, representado na hora de época. |
| hora de expiração |`exp` |`1452289231` |A hora em que o token passa a ser inválido, representado na hora de época. A aplicação deve utilizar esta afirmação para verificar a validade da duração do token. |
| não antes |`nbf` |`1452285331` |A hora em que o token passa a ser válido, representado na hora de época. Normalmente, é o mesmo que o tempo de emissão. A aplicação deve utilizar esta afirmação para verificar a validade da duração do token. |
| Versão |`ver` |`2.0` |A versão do token de ID, conforme definido pelo Azure AD. Para o ponto final v 2.0, o valor é `2.0`. |
| ID do inquilino |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Um GUID que representa o inquilino do Azure AD que o utilizador. Para contas profissionais e escolares, o GUID é o ID de inquilino imutável da organização que o utilizador pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O `profile` âmbito é necessário para receber esta afirmação. |
| hash de código |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |O hash de código está incluído em tokens de ID apenas quando o token de ID é emitido com um código de autorização do OAuth 2.0. Pode ser utilizado para validar a autenticidade de um código de autorização. Para obter mais informações sobre como efetuar esta validação, consulte o [OpenID Connect especificação](http://openid.net/specs/openid-connect-core-1_0.html). |
| hash de token de acesso |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |O hash do token está incluído no ID de acesso tokens apenas quando o token de ID é emitido com um token de acesso de OAuth 2.0. Pode ser utilizado para validar a autenticidade de um token de acesso. Para obter mais informações sobre como efetuar esta validação, consulte o [OpenID Connect especificação](http://openid.net/specs/openid-connect-core-1_0.html). |
| nonce |`nonce` |`12345` |O nonce é uma estratégia para mitigar ataques de repetição de token. A aplicação pode especificar um nonce num pedido de autorização utilizando a `nonce` parâmetro de consulta. O valor terá de fornecer no pedido é emitido no token de ID `nonce` afirmação, não modificada. A aplicação pode verificar o valor em relação ao valor que esta especificada no pedido, o que associa sessão da aplicação um token de ID específico. A aplicação deverá efetuar esta validação durante o processo de validação de token de ID. |
| nome |`name` |`Babe Ruth` |A afirmação de nome fornece um valor legível por humanos que identifica o assunto do token. O valor não é garantido ser exclusivo, é mutável e foi concebido para ser utilizado apenas para efeitos de apresentação. O `profile` âmbito é necessário para receber esta afirmação. |
| Correio eletrónico |`email` |`thegreatbambino@nyy.onmicrosoft.com` |O endereço de correio eletrónico principal associado com a conta de utilizador, se existir. O valor é mutável e pode ser alterados ao longo do tempo. O `email` âmbito é necessário para receber esta afirmação. |
| nome de utilizador preferencial |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |O nome de utilizador primário que representa o utilizador no ponto final v 2.0. Poderia ser um endereço de e-mail, número de telefone ou um nome de utilizador genérico sem um formato especificado. O valor é mutável e pode ser alterados ao longo do tempo. Uma vez que é mutável, este valor não deve ser utilizado para tomar decisões de autorização. O `profile` âmbito é necessário para receber esta afirmação. |
| Requerente |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | O principal sobre os quais o token de asserções informações, tais como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizada. Este pode ser utilizado para efetuar verificações de autorização com segurança, tal como quando o token é utilizado para aceder a um recurso e pode ser utilizado como uma chave nas tabelas de base de dados. Porque o requerente é sempre apresente os tokens que problemas do Azure AD, recomendamos que utilize este valor num sistema de autorização para fins gerais. O requerente é, no entanto, um identificador pairwise - é exclusivo num ID de aplicação em particular.  Por conseguinte, se um único utilizador inicia sessão duas aplicações diferentes com dois IDs de cliente diferente, essas aplicações receberá dois valores diferentes para a afirmação de assunto.  Esta pode ou não pode ser pretendida dependendo dos requisitos de privacidade e arquitetura. |
| ID de objeto |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | O identificador imutável para um objeto de sistema de identidade Microsoft, neste caso, uma conta de utilizador.  Também pode ser utilizado para efetuar verificações de autorização segura e como uma chave nas tabelas de base de dados. Este ID identifica exclusivamente o utilizador em todas as aplicações - duas aplicações diferentes assinatura no mesmo utilizador irão receber o mesmo valor no `oid` de afirmação.  Isto significa que pode ser utilizado quando efetuar consultas para o Microsoft online services, como o Microsoft Graph.  O Microsoft Graph devolverá este ID de como o `id` propriedade para uma determinada conta de utilizador.  Porque o `oid` permite que várias aplicações correlacionar os utilizadores, a `profile` âmbito é necessário para receber esta afirmação. Tenha em atenção que, se existir um único utilizador na vários inquilinos, o utilizador irá conter um ID de objeto diferentes cada inquilino - que são consideradas diferentes contas, apesar do utilizador iniciar sessão cada conta com as mesmas credenciais. |

### <a name="access-tokens"></a>Tokens de acesso
Atualmente, os tokens de acesso emitidos pelo ponto final v 2.0 podem ser utilizados apenas por Microsoft Services. As suas aplicações não devem necessitar de efetuar qualquer validação ou inspeção de tokens de acesso para qualquer um dos cenários atualmente suportados. Pode processar os tokens de acesso como completamente opaco. São apenas cadeias que a aplicação pode passar para a Microsoft em pedidos de HTTP.

Num futuro próximo, o ponto final v 2.0 está a introduzir a capacidade para a sua aplicação receber tokens de acesso a partir de outros clientes. Nessa altura, serão possível atualizar as informações deste tópico de referência com as informações necessárias para a sua aplicação efetuar a validação de token de acesso e outras tarefas semelhantes.

Quando solicita um token de acesso do ponto final v 2.0, o ponto final v 2.0 também devolve os metadados sobre o token de acesso para a sua aplicação utilizar. Estas informações incluem a hora de expiração do token de acesso e âmbitos para a qual é válido. A aplicação utiliza estes metadados para efectuar inteligente colocação em cache de tokens de acesso sem ter de analisar a abrir o token de acesso si próprio.

### <a name="refresh-tokens"></a>Tokens de atualização
Atualizar tokens são tokens de segurança que pode utilizar a aplicação para obter novos tokens de acesso de um fluxo de OAuth 2.0. A aplicação pode utilizar a atualização de tokens para alcançar a longo prazo acesso a recursos em nome de um utilizador sem necessidade de interação com o utilizador.

Os tokens de atualização são vários recursos. Um token de atualização recebido durante um pedido de token para um recurso pode ser resgatado para tokens de acesso a um recurso completamente diferente.

Para receber uma atualização numa resposta token, a aplicação tem de pedir e receber o `offline_acesss` âmbito. Para saber mais sobre o `offline_access` âmbito, consulte o [consentimento e âmbitos](active-directory-v2-scopes.md) artigo.

Tokens de atualização são e sempre serão, completamente opaco à sua aplicação. Estes são emitidos pelo ponto final v 2.0 do Azure AD e podem apenas ser inspecionadas e interpretados pelo ponto final v 2.0. Estão longa duração, mas a aplicação não deve ser escrita esperar que um token de atualização duram qualquer período de tempo. Atualização de tokens pode ser inválida em qualquer momento por vários motivos. É a única forma da sua aplicação para saber se um token de atualização é válido para tentar resgatar-ao efetuar um pedido de token para o ponto final v 2.0.

Quando resgatar a um token de atualização para um novo token de acesso (e se a aplicação tivesse sido concedida a `offline_access` âmbito), receberá um novo token de atualização na resposta token. Guarde o token de atualização emitidos recentemente, para substituir a utilizado no pedido. Esta ação garante que os tokens de atualização mantêm-se válidas para, desde que possível.

## <a name="validating-tokens"></a>Tokens de validação
Atualmente, a validação de token apenas que as aplicações devem necessitar de efetuar está a ser validada tokens de identidade. Para validar um token de ID, a aplicação deve validar a assinatura do token de ID e as afirmações no token de ID.

<!-- TODO: Link -->
A Microsoft fornece bibliotecas e exemplos de código mostram-lhe como processar facilmente a validação de token. Nas secções seguintes, vamos descrevem o processo de subjacente. Vários bibliotecas de open source de terceiros também estão disponíveis para a validação de JWT. Há a opção de pelo menos uma biblioteca para quase todas as plataforma e de idioma.

### <a name="validate-the-signature"></a>Validar a assinatura
Um JWT contém três segmentos, separados pelo `.` caráter. O primeiro segmento é conhecido como o *cabeçalho*, é o segmento de segundo a *corpo*, e é o terceiro segmento o *assinatura*. O segmento de assinatura pode ser utilizado para validar a autenticidade do token de ID de modo a que pode ser fidedigno pela sua aplicação.

Tokens de ID são assinados com os algoritmos de encriptação assimétrico de norma da indústria, tal como RSA 256. O cabeçalho do ID token apresenta informações sobre o método de encriptação e a chave utilizada para assinar o token. Por exemplo:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

O `alg` afirmação indica o algoritmo que foi utilizado para assinar o token. O `kid` afirmação indica que foi utilizado para assinar o token de chave pública.

Em qualquer altura, o ponto final v 2.0 pode assinar um token de ID com qualquer uma de um conjunto específico de pares de chaves públicas-privadas. O ponto final v 2.0 roda periodicamente o conjunto possíveis de chaves, pelo que a sua aplicação deve ser escrita para lidar com essas alterações chaves automaticamente. Uma frequência razoável para procurar atualizações para as chaves públicas utilizadas pelo ponto final v 2.0 é a cada 24 horas.

Pode obter os dados de chave de assinatura que é necessário validar a assinatura utilizando o documento de metadados OpenID Connect, localizado em:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Repita o URL num browser!
>
>

Este documento de metadados é um objeto JSON que tem várias partes útil de informações, tais como a localização dos vários pontos finais necessários para a autenticação OpenID Connect.  O documento também inclui um *jwks_uri*, que lhe oferece a localização do conjunto de chaves públicas utilizado para assinar os tokens. O documento JSON localizado no jwks_uri tem todas as informações de chaves públicas está atualmente em utilização. A aplicação pode utilizar o `kid` no cabeçalho de JWT para selecionar qual a chave pública neste documento foi utilizada para assinar um token de afirmação. Em seguida, efetua validação da assinatura utilizando a chave pública correta e o algoritmo indicado.

Efetuar a validação da assinatura está fora do âmbito deste documento. Bibliotecas de open source muitos estão disponíveis para ajudá-lo com este.

### <a name="validate-the-claims"></a>Validar as afirmações
Quando a aplicação recebe um token de ID após o início de sessão do utilizador,-lo também deve efetuar algumas verificações contra as afirmações no token de ID. Estes incluem, mas não estão limitados a:

* **público-alvo** de afirmação, para verificar que o token de ID se destinar a ser indicado para a sua aplicação
* **não antes** e **hora de expiração** afirmações, certifique-se de que o token de ID não expirou
* **emissor** de afirmação, para verificar se o token foi emitido para a sua aplicação pelo ponto final v 2.0
* **nonce**, como um token de mitigação de ataques de repetição.

Para obter uma lista completa de validações de afirmação que deve executar a sua aplicação, consulte o [OpenID Connect especificação](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Detalhes dos valores esperados para estas afirmações incluídos no [ID tokens](# ID tokens) secção.

## <a name="token-lifetimes"></a>Durações de token
Podemos fornecer as durações de token seguintes apenas para sua informação. As informações podem ajudá-lo como desenvolver e depurar aplicações. As suas aplicações não devem ser escritas esperar qualquer um destes durações permaneça constante. Pode durações do token e mudará em qualquer altura.

| Token | Duração | Descrição |
| --- | --- | --- |
| ID de tokens (contas profissionais ou escolares) |1 hora |Tokens de ID normalmente são válidos para 1 hora. A aplicação web pode utilizar esta duração mesma para manter a sua própria sessão com o utilizador (recomendado) ou, pode escolher uma duração de sessão totalmente diferente. Se a aplicação tem de obter um novo ID de token, é necessário tornar um novo início de sessão pedido para a v 2.0 autorizar o ponto final. Se o utilizador tem uma sessão de browser válido com o ponto final v 2.0, o utilizador não poderá ser necessário para introduzir as respetivas credenciais novamente. |
| ID de tokens (contas pessoais) |24 horas |Tokens de ID para contas pessoais normalmente são válidos durante 24 horas. A aplicação web pode utilizar esta duração mesma para manter a sua própria sessão com o utilizador (recomendado) ou, pode escolher uma duração de sessão totalmente diferente. Se a aplicação tem de obter um novo ID de token, é necessário tornar um novo início de sessão pedido para a v 2.0 autorizar o ponto final. Se o utilizador tem uma sessão de browser válido com o ponto final v 2.0, o utilizador não poderá ser necessário para introduzir as respetivas credenciais novamente. |
| Tokens de acesso (contas profissionais ou escolares) |1 hora |Indicados em respostas token como parte dos metadados do token. |
| Tokens de acesso (contas pessoais) |1 hora |Indicados em respostas token como parte dos metadados do token. Os tokens de acesso que são emitidos em nome de contas pessoais podem ser configurados para uma duração diferentes, mas 1 hora é normal. |
| Atualizar tokens (conta escolar ou profissional) |Até 14 dias |Um token de atualização é válido para um máximo de 14 dias. No entanto, o token de atualização pode tornar-se inválida em qualquer altura por vários motivos, pelo que a aplicação deve continuar a tentar utilizar um token de atualização até que este falhar, ou até que a sua aplicação substitui-os com um novo token de atualização. Um token de atualização também passa a ser inválido se foi 90 dias, uma vez que o utilizador ter introduzido as respetivas credenciais. |
| Atualizar tokens (contas pessoais) |Até 1 ano |Um token de atualização é válido para um máximo de 1 ano. No entanto, o token de atualização pode tornar-se inválida em qualquer altura por vários motivos, pelo que a aplicação deve continuar a tentar utilizar um token de atualização até falhar. |
| Códigos de autorização (contas profissionais ou escolares) |10 minutos |Códigos de autorização são propositadamente curta duração e devem ser resgatados imediatamente para tokens de acesso e atualização de tokens quando os tokens são recebidos. |
| Códigos de autorização (contas pessoais) |5 minutos |Códigos de autorização são propositadamente curta duração e devem ser resgatados imediatamente para tokens de acesso e atualização de tokens quando os tokens são recebidos. Códigos de autorização que são emitidos em nome de contas pessoais são para utilização única. |
