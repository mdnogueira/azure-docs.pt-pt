---
title: "Azure início de sessão único protocolo SAML | Microsoft Docs"
description: "Este artigo descreve o protocolo de início de sessão único no SAML no Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: f41402fc2cb282975b93071d998365fdb0a21941
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="single-sign-on-saml-protocol"></a>Protocolo de SAML do início de sessão único
Este artigo abrange os pedidos de autenticação SAML 2.0 e as respostas que suporta o Azure Active Directory (Azure AD) para Single Sign-On.

O diagrama de protocolo abaixo descreve a sequência de início de sessão único. O serviço de nuvem (o fornecedor de serviços) utiliza um enlace de redirecionamento de HTTP para passar uma `AuthnRequest` elemento (pedido de autenticação) para o Azure AD (fornecedor de identidade). Azure AD, em seguida, utiliza um HTTP post enlace publicar um `Response` elemento para que o serviço em nuvem.

![Fluxo de trabalho de início de sessão único](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest
Para pedir uma autenticação de utilizador, cloud services enviar um `AuthnRequest` elemento com o Azure AD. Exemplo de SAML 2.0 `AuthnRequest` foi este aspeto:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Parâmetro |  | Descrição |
| --- | --- | --- |
| ID |Necessário |Azure AD utiliza este atributo para preencher o `InResponseTo` atributo da resposta devolvida. ID não tem de começar com um número, pelo que é uma estratégia de comuns preceder uma cadeia como "id" para a representação de cadeia de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é um ID válido. |
| Versão |Necessário |Deve ser **2.0**. |
| IssueInstant |Necessário |Esta é uma cadeia de DateTime com um valor de UTC e [formato reportadas round-trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD espera um valor de DateTime deste tipo, mas não avaliar ou utilize o valor. |
| AssertionConsumerServiceUrl |Opcional |Se for indicado, este tem de corresponder à `RedirectUri` do serviço de nuvem no Azure AD. |
| ForceAuthn |Opcional | Este é um valor booleano. Se for VERDADEIRO, isto significa que o utilizador ser forçado a autenticar novamente, mesmo que tenha uma sessão válida com o Azure AD. |
| IsPassive |Opcional |Este é um valor booleano que especifica se do Azure AD deve autenticar o utilizador silenciosamente, sem interação do utilizador, utilizando o cookie de sessão, se existir. Se for VERDADEIRO, o Azure AD irá tentar autenticar o utilizador utilizando o cookie de sessão. |

Todos os outros `AuthnRequest` atributos, tais como o consentimento, destino, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName são **ignoradas**.

Azure AD também ignora a `Conditions` elemento `AuthnRequest`.

### <a name="issuer"></a>emissor
O `Issuer` elemento um `AuthnRequest` deve corresponder exatamente um do **ServicePrincipalNames** no serviço em nuvem no Azure AD. Normalmente, isto estiver definido como o **URI de ID de aplicação** que é especificado durante o registo de aplicação.

Um exemplo SAML excerpt que contém o `Issuer` elemento tem o seguinte aspeto:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy
Este elemento solicita um formato de ID de nome específico na resposta e é opcional no `AuthnRequest` elementos enviados para o Azure AD.

Um exemplo `NameIdPolicy` elemento tem o seguinte aspeto:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` é fornecida, pode incluir o opcional `Format` atributo. O `Format` atributo só pode ter um dos seguintes valores; qualquer outra valor resulta num erro.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: O azure Active Directory emite afirmações de NameID como um identificador pairwise.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: O azure Active Directory emite afirmações de NameID no formato de endereço de correio eletrónico.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Este valor permite ao Azure Active Directory para selecionar o formato de afirmação. Azure Active Directory emite o NameID como um identificador pairwise.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: O azure Active Directory emite afirmações de NameID como um valor gerado aleatoriamente, que é exclusivo para a operação atual do SSO. Isto significa que o valor é temporário e não pode ser utilizado para identificar o utilizador a autenticar.

Azure AD ignora a `AllowCreate` atributo.

### <a name="requestauthncontext"></a>RequestAuthnContext
O `RequestedAuthnContext` elemento Especifica os métodos de autenticação pretendido. É opcional no `AuthnRequest` elementos enviados para o Azure AD. AD do Azure suporta apenas um `AuthnContextClassRef` valor: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Controlo de âmbito
O `Scoping` elemento, que inclui uma lista de fornecedores de identidade, é opcional no `AuthnRequest` elementos enviados para o Azure AD.

Se for indicado, não inclua o `ProxyCount` atributo, `IDPListOption` ou `RequesterID` elemento, como não são suportados.

### <a name="signature"></a>Assinatura
Não incluir um `Signature` elemento `AuthnRequest` elementos, do Azure AD não suporta assinados pedidos de autenticação.

### <a name="subject"></a>Assunto
Azure AD ignora a `Subject` elemento do `AuthnRequest` elementos.

## <a name="response"></a>Resposta
Quando um pedido início de sessão for concluída com êxito, o Azure AD mensagens uma resposta para o serviço em nuvem. Uma resposta de exemplo para uma tentativa de início de sessão com êxito tem o seguinte aspeto:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Resposta
O `Response` elemento inclui o resultado do pedido de autorização. Conjuntos do Azure AD a `ID`, `Version` e `IssueInstant` valores no `Response` elemento. Também define os seguintes atributos:

* `Destination`: Quando o início de sessão for concluída com êxito, é definida o `RedirectUri` do fornecedor de serviço (serviço de nuvem).
* `InResponseTo`: Este é definido como o `ID` atributo do `AuthnRequest` elemento que iniciou a resposta.

### <a name="issuer"></a>emissor
Conjuntos do Azure AD a `Issuer` elemento `https://login.microsoftonline.com/<TenantIDGUID>/` onde <TenantIDGUID> é o ID de inquilino do inquilino do Azure AD.

Por exemplo, uma resposta de exemplo com o elemento de emissor foi este aspeto:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Estado
O `Status` elemento transmite o êxito ou falha de início de sessão. Inclui o `StatusCode` elemento, que contém um código ou um conjunto de códigos aninhados que representa o estado do pedido. Também inclui o `StatusMessage` elemento, que contém mensagens de erro personalizadas que são geradas durante o processo de início de sessão.

<!-- TODO: Add a authentication protocol error reference -->

Segue-se uma resposta SAML para uma tentativa de início de sessão sem êxito.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Asserção
Para além de `ID`, `IssueInstant` e `Version`, do Azure AD define os elementos seguintes `Assertion` elemento da resposta.

#### <a name="issuer"></a>emissor
Isto está definido como `https://sts.windows.net/<TenantIDGUID>/`onde <TenantIDGUID> é o ID de inquilino do inquilino do Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Assinatura
Azure AD inicia a asserção em resposta a um início de sessão-com êxito. O `Signature` elemento contém uma assinatura digital que pode utilizar o serviço em nuvem para autenticar a origem para verificar a integridade da asserção.

Para gerar este assinatura digital, do Azure AD utiliza a chave de assinatura no `IDPSSODescriptor` elemento do seu documento de metadados.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Assunto
Esta ação Especifica o principal que é o assunto das instruções na asserção. Contém um `NameID` elemento, que representa o utilizador autenticado. O `NameID` valor é um identificador de destino que é direcionado apenas para o fornecedor de serviço que é o público-alvo para o token. É persistente - podem ser revogado, mas nunca é reatribuído. Também é opaco, em que este não expõe nada sobre o utilizador e não pode ser utilizado como um identificador para consultas de atributo.

O `Method` atributo o `SubjectConfirmation` elemento é sempre definido como `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condições
Este elemento Especifica as condições que definem o uso aceitável de asserções de SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

O `NotBefore` e `NotOnOrAfter` atributos especifique o intervalo durante os quais a asserção é válida.

* O valor da `NotBefore` atributo é igual a ou ligeiramente (menos de um segundo) posterior ao valor de `IssueInstant` atributo o `Assertion` elemento. Azure AD não tem em conta qualquer diferença entre si próprio e o serviço de nuvem (fornecedor de serviços) e não adiciona quaisquer da memória intermédia para este período de tempo.
* O valor da `NotOnOrAfter` atributo é posterior ao valor de 70 minutos a `NotBefore` atributo.

#### <a name="audience"></a>Audiência
Contém um URI que identifique um público-alvo. Azure AD define o valor deste elemento com o valor do `Issuer` elemento do `AuthnRequest` que iniciou o início de sessão. Para avaliar o `Audience` valor, utilize o valor do `App ID URI` que foi especificado durante o registo de aplicação.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Como o `Issuer` valor, o `Audience` valor deve corresponder exatamente um dos nomes de principal serviço que representa o serviço em nuvem no Azure AD. No entanto, se o valor da `Issuer` elemento não é um valor URI, o `Audience` valor na resposta é o `Issuer` valor o prefixo `spn:`.

#### <a name="attributestatement"></a>AttributeStatement
Contém afirmações sobre o assunto ou o utilizador. A seguinte excerpt contém uma amostra `AttributeStatement` elemento. O botão de reticências indica que o elemento pode incluir vários atributos e valores de atributo.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Nome de afirmação** : O valor da `Name` atributo (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) é o nome principal de utilizador do utilizador autenticado, tal como `testuser@managedtenant.com`.
* **ObjectIdentifier afirmação** : O valor da `ObjectIdentifier` atributo (`http://schemas.microsoft.com/identity/claims/objectidentifier`) é o `ObjectId` do objeto de diretório que representa o utilizador autenticado no Azure AD. `ObjectId`é um imutável globalmente exclusivo e utilizar novamente o identificador de segurança do utilizador autenticado.

#### <a name="authnstatement"></a>AuthnStatement
Este elemento asserções que o assunto de asserção foi autenticado por um meio específico num determinado momento.

* O `AuthnInstant` atributo especifica o tempo no qual o utilizador autenticado com o Azure AD.
* O `AuthnContext` elemento Especifica o contexto de autenticação utilizado para autenticar o utilizador.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```