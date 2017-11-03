---
title: "Token de referência - Azure AD B2C | Microsoft Docs"
description: Os tipos de tokens emitidos no Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: parakhj
ms.openlocfilehash: 92087e4553580a5fe14e647d014e493bc7e47b67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-token-reference"></a>Do Azure AD B2C: Referência de Token

O Azure Active Directory B2C (Azure AD B2C) emite vários tipos de tokens de segurança como processa cada [fluxo de autenticação](active-directory-b2c-apps.md). Este documento descreve o formato, características de segurança e conteúdo de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens
Azure AD B2C suporta o [protocolo de autorização do OAuth 2.0](active-directory-b2c-reference-protocols.md), que utiliza ambos os tokens de acesso e tokens de atualização. Também suporta a autenticação e início de sessão através de [OpenID Connect](active-directory-b2c-reference-protocols.md), que apresenta um tipo de token terceiro: o token de ID. Cada um destes tokens é representada como um token de portador.

Um token de portador é um token de segurança simples que concede acesso a "portador" para um recurso protegido. O portador é que podem apresentar o token de terceiros. O Azure AD B2C devem primeiro autenticar uma parte antes de pode receber um token de portador. Mas, se não são tidas em conta os passos necessários para proteger o token na transmissão e o armazenamento, pode ser intercetado e utilizado por uma entidade indesejada. Alguns tokens de segurança tem um mecanismo incorporado para impedir que as partes não autorizadas a utilizá-los, mas os tokens de portador não têm este mecanismo. Estes têm de ser transportados num canal seguro, tais como a segurança de camada de transporte (HTTPS).

Se um token de portador é transmitido fora de um canal seguro, uma parte maliciosa pode utilizar um ataque man-in-the-middle para adquirir o token e utilizá-la para obter acesso não autorizado a um recurso protegido. Os princípios de segurança mesmo se aplica quando os tokens de portador são armazenados ou colocada em cache para utilização posterior. Certifique-se sempre de que a aplicação transmite e armazena os tokens de portador de forma segura.

Para considerações adicionais de segurança em tokens de portador, consulte [RFC 6750 secção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens de problemas do Azure AD B2C são implementados como tokens de web JSON (JWTs). Um JWT é um meio compact, segura de URL de transferência de informações entre duas partes. JWTs contêm informações conhecidas como afirmações. Estas são asserções de informações sobre o portador e o assunto do token. As afirmações no JWTs são objetos JSON que são codificados e serializados para transmissão. Porque JWTs emitidos pelo Azure AD B2C estiver assinados, mas não encriptados, pode facilmente inspecionar os conteúdos de um JWT para depurá-lo. Estão disponíveis várias ferramentas que pode fazer isto, incluindo [jwt.ms](https://jwt.ms). Para mais informações sobre JWTs, consulte [especificações JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de ID

Um token de ID é um formulário de token de segurança que a aplicação recebe do Azure AD B2C `/authorize` e `/token` pontos finais. Tokens de ID são representados como [JWTs](#types-of-tokens), e contêm afirmações que pode utilizar para identificar os utilizadores na sua aplicação. Quando os tokens de ID adquiridos junto do `/authorize` ponto final, são efetuadas utilizando o [fluxo implícito](active-directory-b2c-reference-spa.md), que é frequentemente utilizado para iniciar sessão na javascript de utilizadores com base em aplicações web. Quando os tokens de ID adquiridos junto do `/token` ponto final, são efetuadas utilizando o [fluxo de código confidencial](active-directory-b2c-reference-oidc.md), que mantém o token ocultado a partir do browser. Isto permite que o token para ser enviadas de forma segura em pedidos de HTTP para a comunicação entre dois componentes da mesma aplicação ou serviço. Pode utilizar as afirmações num ID token como julgar. São frequentemente utilizadas para apresentar informações de conta ou para tomar decisões de controlo de acesso numa aplicação.  

Tokens de ID tem sessão iniciados, mas não são atualmente encriptados. Quando a sua aplicação ou a API recebe um token de ID, tem [validar a assinatura](#token-validation) para provar que é autêntico o token. A aplicação ou a API também deve validar alguns afirmações no token para provar que é válido. Consoante os requisitos de cenário, as afirmações validadas por uma aplicação podem variar, mas a aplicação tem de efetuar alguns [validações de afirmação comuns](#token-validation) cada cenário.

#### <a name="sample-id-token"></a>Token de ID de exemplo
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Tokens de acesso

Um token de acesso também é um formulário de token de segurança que a aplicação recebe do Azure AD B2C `/authorize` e `/token` pontos finais. Os tokens de acesso também são representados como [JWTs](#types-of-tokens), e contêm afirmações que pode utilizar para identificar as permissões concedidas para as suas APIs. Os tokens de acesso tem sessão iniciados, mas não estão atualmente encriptados. Os tokens de acesso devem ser utilizados para fornecer acesso a APIs e recursos de servidores. Saiba mais sobre como [Utilize tokens de acesso](active-directory-b2c-access-tokens.md). 

Quando a API recebe um token de acesso, tem [validar a assinatura](#token-validation) para provar que é autêntico o token. A API também deve validar alguns afirmações no token para provar que é válido. Consoante os requisitos de cenário, as afirmações validadas por uma aplicação podem variar, mas a aplicação tem de efetuar alguns [validações de afirmação comuns](#token-validation) cada cenário.

### <a name="claims-in-id-and-access-tokens"></a>Afirmações no ID e acesso

Quando utiliza o Azure AD B2C, tem controlo detalhado sobre o conteúdo dos seus tokens. Pode configurar [políticas](active-directory-b2c-reference-policies.md) enviar determinados conjuntos de dados do utilizador em afirmações que requer a sua aplicação para as suas operações. Estas afirmações podem incluir propriedades padrão, tais como o utilizador `displayName` e `emailAddress`. Também pode incluir [atributos de utilizador personalizada](active-directory-b2c-reference-custom-attr.md) que pode definir no diretório do B2C. Cada acesso e o ID de token que recebe contém um determinado conjunto de afirmações relacionadas com segurança. As suas aplicações podem utilizar estas afirmações para autenticar em segurança a utilizadores e pedidos.

Tenha em atenção que as afirmações no ID não são devolvidas por qualquer ordem específica. Além disso, as novas afirmações podem ser introduzidas no tokens de identidade em qualquer altura. Não deverá interromper a sua aplicação à nova afirmações que são introduzidas. Seguem-se as afirmações que pretende existe no ID e acesso tokens emitidos pelo Azure AD B2C. Qualquer afirmações adicionais são determinadas por políticas. Para prática, tente inspecionar as afirmações no token de ID de exemplo através da colagem para [jwt.ms](https://jwt.ms). Podem encontrar mais detalhes no [OpenID Connect especificação](http://openid.net/specs/openid-connect-core-1_0.html).

| Nome | Afirmação | Valor de exemplo | Descrição |
| --- | --- | --- | --- |
| Audiência |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Uma afirmação de audiência identifica o destinatário do token. Para o Azure AD B2C, o público-alvo é o ID da aplicação da sua aplicação, consoante atribuído à sua aplicação no portal de registo de aplicação. A aplicação deve validar este valor e rejeitar o token, se não corresponde. |
| emissor |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |Esta afirmação identifica o serviço de token de segurança (STS) que constrói e devolve o token. Também identifica o diretório do Azure AD em que o utilizador foi autenticado. A aplicação deve validar a afirmação de emissor para se certificar de que o token provém de ponto final v 2.0 do Azure Active Directory. |
| emitido no |`iat` |`1438535543` |Esta afirmação é o tempo no qual o token foi emitido, representado na hora de época. |
| hora de expiração |`exp` |`1438539443` |Hora de expiração da afirmação é a hora em que o token passa a ser inválido, representado na hora de época. A aplicação deve utilizar esta afirmação para verificar a validade da duração do token. |
| não antes |`nbf` |`1438535543` |Esta afirmação é a hora em que o token passa a ser representado na hora de época, válido. Isto é normalmente o mesmo que o tempo que o token foi emitido. A aplicação deve utilizar esta afirmação para verificar a validade da duração do token. |
| Versão |`ver` |`1.0` |Esta é a versão do ID token, conforme definido pelo Azure AD. |
| hash de código |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Um hash de código está incluído num ID token apenas quando o token emitido juntamente com um código de autorização do OAuth 2.0. Um hash de código pode ser utilizado para validar a autenticidade de um código de autorização. Para obter mais detalhes sobre como efetuar esta validação, consulte o [OpenID Connect especificação](http://openid.net/specs/openid-connect-core-1_0.html).  |
| hash de token de acesso |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Um hash de token de acesso está incluído num ID token apenas quando o token emitido juntamente com um token de acesso de OAuth 2.0. Um hash de token de acesso pode ser utilizado para validar a autenticidade de um token de acesso. Para obter mais detalhes sobre como efetuar esta validação, consulte o [especificação OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)  |
| nonce |`nonce` |`12345` |Um nonce é uma estratégia utilizada para mitigar ataques de repetição de token. A aplicação pode especificar um nonce num pedido de autorização utilizando a `nonce` parâmetro de consulta. O valor terá de fornecer no pedido irá ser emitido unmodified no `nonce` de afirmações de um token de ID apenas. Isto permite à aplicação verificar o valor em relação ao valor que esta especificada no pedido, o que associa sessão da aplicação um token de ID especificado. A aplicação deverá efetuar esta validação durante o processo de validação de token de ID. |
| Assunto |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Este é o principal sobre os quais o token de asserções informações, tais como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizada. Pode ser utilizado para efetuar verificações de autorização com segurança, tal como quando o token é utilizado para aceder a um recurso. Por predefinição, a afirmação de requerente é preenchida com o ID de objeto do utilizador no diretório. Para obter mais informações, consulte [Azure Active Directory B2C: Token, a sessão e a configuração de início de sessão único](active-directory-b2c-token-session-sso.md). |
| Referência de classe de contexto de autenticação |`acr` |Não aplicável |Não utilizado atualmente, exceto no caso de políticas mais antigas. Para obter mais informações, consulte [Azure Active Directory B2C: Token, a sessão e a configuração de início de sessão único](active-directory-b2c-token-session-sso.md). |
| Política de estrutura de confiança |`tfp` |`b2c_1_sign_in` |Este é o nome da política que utilizou para adquirir o token de ID. |
| Tempo de autenticação |`auth_time` |`1438535543` |Esta afirmação é o tempo que um último introduzir as credenciais de utilizador, representado na hora de época. |

### <a name="refresh-tokens"></a>Tokens de atualização
Atualizar tokens são tokens de segurança que pode utilizar a aplicação para adquirir novos tokens de ID e aceder a tokens de um fluxo de OAuth 2.0. Fornecem a aplicação com acesso de longo prazo para recursos em nome dos utilizadores sem necessidade de interação com os utilizadores.

Para receber uma atualização token numa resposta token, a aplicação tem de pedir a `offline_acesss` âmbito. Para saber mais sobre o `offline_access` âmbito, consulte o [referência do protocolo do Azure AD B2C](active-directory-b2c-reference-protocols.md).

Tokens de atualização são e será sempre, completamente opaco à sua aplicação. São emitidos pelo Azure AD e podem ser inspecionadas e interpretados apenas pelo Azure AD. Estão longa duração, mas a aplicação não deve ser escrita com a expectativa que um token de atualização duram um período de tempo específico. Atualização de tokens pode ser inválida em qualquer momento para diversos motivos. É a única forma da sua aplicação para saber se um token de atualização é válido para tentar resgatar-ao efetuar um pedido de token para o Azure AD.

Quando resgatar a um token de atualização para um novo token (e se a sua aplicação tiver sido concedida a `offline_access` âmbito), receberá um novo token de atualização na resposta token. Deve guardar o token de atualização emitidos recentemente. Deve de substituir o token de atualização que tiver utilizado anteriormente no pedido. Isto ajuda a garantir que os tokens de atualização mantêm-se válidas para, desde que possível.

## <a name="token-validation"></a>Validação de token
Para validar um token, a aplicação deve verificar a assinatura e de afirmações do token.

Bibliotecas de open source para muitos estão disponíveis para validar JWTs, consoante o seu idioma de preferência. Recomendamos que explorar essas opções em vez de implementar a sua própria lógica de validação. As informações neste guia podem ajudar a saber como utilizar corretamente as bibliotecas.

### <a name="validate-the-signature"></a>Validar a assinatura
Um JWT contém três segmentos, separados pelo `.` caráter. É o primeiro segmento o *cabeçalho*, é o segundo o *corpo*, e é o terceiro o *assinatura*. O segmento de assinatura pode ser utilizado para validar a autenticidade do token de modo a que pode ser fidedigno pela sua aplicação.

Azure AD B2C tokens são assinados com os algoritmos de encriptação assimétrico de norma da indústria, tal como RSA 256. O cabeçalho do token contém informações sobre o método de encriptação e a chave utilizada para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O `alg` afirmação indica o algoritmo que foi utilizado para assinar o token. O `kid` afirmação indica a chave pública específica que foi utilizada para assinar o token.

Em qualquer momento, do Azure AD pode assinar um token com qualquer uma de um determinado conjunto de pares de chaves públicas-privadas. Azure AD roda o conjunto de chaves possíveis periodicamente, a aplicação deve ser escrita para lidar com essas alterações chaves automaticamente. Uma frequência razoável para procurar atualizações para as chaves públicas utilizadas pelo Azure AD é a cada 24 horas.

O Azure AD B2C tem um ponto final de metadados OpenID Connect. Isto permite que as aplicações obter informações sobre o Azure AD B2C no tempo de execução. Estas informações incluem pontos finais, conteúdo de token e chaves de assinatura de tokens. Diretório do B2C contém um documento de metadados JSON para cada política. Por exemplo, o documento de metadados para o `b2c_1_sign_in` política no `fabrikamb2c.onmicrosoft.com` está localizado em:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`é o diretório do B2C utilizado para autenticar o utilizador, e `b2c_1_sign_in` é a política utilizada para adquirir o token. Para determinar qual a política foi utilizada para assinar um token (e onde posso para obter os metadados), tem duas opções. Em primeiro lugar, o nome da política está incluído no `acr` no token de afirmação. Pode analisar afirmações fora do corpo do JWT por base-64 descodificar o corpo e a cadeia JSON que resulta de anulação da serialização. O `acr` afirmação será o nome da política que foi utilizado para emitir o token.  A outra opção consiste em codificar a política no valor do `state` parâmetro quando emitir o pedido e, em seguida, descodificar para determinar qual a política foi utilizada. O método é válido.

O documento de metadados é um objeto JSON que contém várias partes útil de informações. Estes incluem a localização dos pontos finais necessários para efetuar a autenticação OpenID Connect. Também incluem `jwks_uri`, que lhe oferece a localização do conjunto de chaves públicas que são utilizados para assinar os tokens. Que a localização é fornecida aqui, mas é melhor obter a localização dinamicamente utilizando o documento de metadados e analisar a saída `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

O documento JSON localizado neste URL contém todas as informações chaves públicas em utilização um momento específico. A aplicação pode utilizar o `kid` no cabeçalho de JWT para selecionar a chave pública no documento JSON que é utilizado para assinar um determinado token de afirmação. Em seguida, pode efetuar a validação da assinatura utilizando a chave pública correta e o algoritmo indicado.

Uma descrição de como efetuar a validação da assinatura está fora do âmbito deste documento. Bibliotecas de open source para muitos estão disponíveis para ajudá-lo com esta opção se o ficheiro necessário.

### <a name="validate-the-claims"></a>Validar as afirmações
Quando a sua aplicação ou a API recebe um token de ID, também, deverá efetuar várias verificações contra as afirmações no token de ID. Estes incluem, mas não estão limitados a:

* O **público-alvo** de afirmação: Isto verifica que o token de ID se destinar a ser indicado para a sua aplicação.
* O **não antes** e **hora de expiração** afirmações: estes Certifique-se de que o token de ID não expirou.
* O **emissor** de afirmação: Isto verifica se o token foi emitido para a sua aplicação pelo Azure AD.
* O **nonce**: Esta é uma estratégia de mitigação de ataque de reprodução de tokens.

Para obter uma lista completa das validações deve executar a sua aplicação, consulte o [OpenID Connect especificação](https://openid.net). Detalhes dos valores esperados para estas afirmações incluídos no precedente [token secção](#types-of-tokens).  

## <a name="token-lifetimes"></a>Durações de token
Durações de token seguintes são fornecidas para aproveitarem os seus dados de conhecimento. Estes podem ajudá-lo quando desenvolver e depurar aplicações. Tenha em atenção que as suas aplicações não devem ser escritas esperar qualquer um destes durações permaneça constante. Podem e será alterado. Leia mais sobre o [personalização do token durações](active-directory-b2c-token-session-sso.md) no Azure AD B2C.

| Token | Duração | Descrição |
| --- | --- | --- |
| Tokens de ID |Uma hora |Os tokens de ID são normalmente válidos para uma hora. A aplicação web pode utilizar esta duração para manter as suas próprias sessões com utilizadores (recomendados). Também pode escolher uma duração de sessão diferente. Se a aplicação tem de obter um novo ID de token, basta tem de efetuar um novo pedido de início de sessão para o Azure AD. Se um utilizador tiver uma sessão de browser válido com o Azure AD, esse utilizador não poderá ser necessário para introduzir as credenciais novamente. |
| Tokens de atualização |Até 14 dias |Um token de atualização é válido para um máximo de 14 dias. No entanto, um token de atualização pode tornar-se inválida em qualquer altura para uma série de razões. A aplicação deve continuar a tentar utilizar um token de atualização até que o pedido falha, ou até que a sua aplicação substitui o token de atualização com um novo. Também pode ficar inválido se 90 dias após ter passado, uma vez que o utilizador introduziu pela última vez credenciais de um token de atualização. |
| Códigos de autorização |Cinco minutos |Códigos de autorização são intencionalmente curta duração. Estes devem ser resgatadas imediatamente para tokens de acesso, tokens de identidade ou tokens de atualização quando serem recebidas. |

