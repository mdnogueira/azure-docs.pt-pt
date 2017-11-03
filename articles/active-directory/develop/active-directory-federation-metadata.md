---
title: "Os metadados de Federação do Azure AD | Microsoft Docs"
description: "Este artigo descreve o documento de metadados de Federação do Active Directory do Azure publica para serviços que aceitam tokens do Azure Active Directory."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ecafb02a6ac13d1c3cd1fe77ef710cd8525e32b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="federation-metadata"></a>Metadados de federação
Azure Active Directory (Azure AD) publica um documento de metadados de Federação para serviços que está configurado para aceitar tokens de segurança que o Azure AD emite. O formato de documento de metadados de Federação é descrito no [idioma de Federação de serviços Web (WS-Federation) versão 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que expande [metadados para o v OASIS Security Assertion Markup Language (SAML) 2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Inquilino específico e os pontos finais de metadados do inquilino independentes
Azure AD publica pontos finais de inquilino específico e independente de inquilino.

Pontos finais de inquilino específico foram concebidos para um determinado inquilino. Os metadados de Federação de inquilino específico incluem informações sobre o inquilino, incluindo informações de emissor e o ponto final de inquilino específico. As aplicações que restringem o acesso a um único inquilino utilizar pontos finais de inquilino específico.

Pontos finais de inquilino independente fornecem informações que são comuns aos inquilinos todas as do Azure AD. Estas informações aplicam-se aos inquilinos alojados em *login.microsoftonline.com* e é partilhado entre inquilinos. Pontos finais de independente de inquilino são recomendados para aplicações de multi-inquilinos, uma vez que não estão associados com nenhum inquilino específico.

## <a name="federation-metadata-endpoints"></a>Pontos finais de metadados de Federação
Azure AD publica os metadados de Federação em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Para **pontos finais de inquilino específico**, a `TenantDomainName` pode ser um dos seguintes tipos:

* Um nome de domínio registado de um Azure AD de inquilino, tais como: `contoso.onmicrosoft.com`.
* O imutável inquilino ID do domínio, tal como `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Para **pontos finais de inquilino independente**, a `TenantDomainName` é `common`. Este documento apresenta apenas os elementos de metadados de federação que são comuns a todos os inquilinos do Azure AD que estão alojados em login.microsoftonline.com.

Por exemplo, poderá ser um ponto final de inquilino específico `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. O ponto final independentes do inquilino é [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Pode ver o documento de metadados de Federação, escrevendo este URL num browser.

## <a name="contents-of-federation-metadata"></a>Conteúdo de metadados de Federação
A seguinte secção fornece informações necessárias pela serviços que consumam tokens emitidos pelo Azure AD.

### <a name="entity-id"></a>ID de entidade
O `EntityDescriptor` elemento contém um `EntityID` atributo. O valor da `EntityID` atributo representa o emissor, ou seja, o token serviço de segurança (STS) que emitiu o token. É importante validar o emissor quando receber um token.

Os metadados seguintes mostra um exemplo de inquilino específico `EntityDescriptor` elemento com um `EntityID` elemento.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Pode substituir o ID do inquilino no ponto final independentes do inquilino com o ID de inquilino para criar um inquilino específico `EntityID` valor. O valor resultante será o mesmo que o emissor de token. A estratégia permite uma aplicação multi-inquilino validar o emissor para um determinado inquilino.

Os metadados seguintes mostra um exemplo independentes do inquilino `EntityID` elemento. Tenha em atenção, que o `{tenant}` é um literal, não um marcador de posição.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de assinatura de tokens
Quando um serviço recebe um token que é emitido por um inquilino do Azure AD, a assinatura do token tem de ser validada com uma chave de assinatura que é publicada no documento de metadados de Federação. Os metadados de Federação incluem a parte pública dos certificados que os inquilinos utilizam para assinatura de tokens. Os bytes não processados de certificado são apresentados no `KeyDescriptor` elemento. O token de assinatura de certificado é válido para a assinatura apenas quando o valor da `use` atributo `signing`.

Um documento de metadados de Federação publicado pelo Azure AD pode ter várias chaves de assinatura, por exemplo, quando do Azure AD está a preparar para atualizar o certificado de assinatura. Quando um documento de metadados de Federação inclui mais de um certificado, um serviço que está a validar os tokens deve suportar todos os certificados no documento.

Os metadados seguintes mostra um exemplo `KeyDescriptor` elemento com uma chave de assinatura.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

O `KeyDescriptor` elemento é apresentado em dois locais no documento de metadados de Federação; a secção de específico do WS-Federação e a secção de SAML específicos. Os certificados publicados em ambas as secções será a mesma.

Na secção específico do WS-Federação, um leitor de metadados de WS-Federation seria ler os certificados a partir de um `RoleDescriptor` elemento com o `SecurityTokenServiceType` tipo.

Os metadados seguintes mostra um exemplo `RoleDescriptor` elemento.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

Na secção de SAML específico, um leitor de metadados de WS-Federation seria ler os certificados a partir de um `IDPSSODescriptor` elemento.

Os metadados seguintes mostra um exemplo `IDPSSODescriptor` elemento.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Existem não existem diferenças no formato de certificados específicos de inquilino e independente de inquilino.

### <a name="ws-federation-endpoint-url"></a>URL de ponto final de WS-Federation
Os metadados de Federação incluem o URL que utiliza o Azure AD para o início de sessão único e de fim de sessão no protocolo WS-Federation único. Este ponto final é apresentado no `PassiveRequestorEndpoint` elemento.

Os metadados seguintes mostra um exemplo `PassiveRequestorEndpoint` elemento para um ponto final de inquilino específico.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Para o ponto final independente de inquilino, o URL de WS-Federation aparece no ponto final WS-Federation, conforme mostrado no seguinte exemplo.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL de ponto final de protocolo SAML
Os metadados de Federação incluem o URL que utiliza o Azure AD para o início de sessão único e de fim de sessão no protocolo SAML 2.0 único. Estes pontos finais são apresentados no `IDPSSODescriptor` elemento.

Os URLs de início de sessão e fim de sessão for apresentada a `SingleSignOnService` e `SingleLogoutService` elementos.

Os metadados seguintes mostra um exemplo `PassiveResistorEndpoint` para um ponto final de inquilino específico.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Da mesma forma, os pontos finais para pontos finais de protocolo de SAML 2.0 comuns são publicados nos metadados da Federação independente de inquilino, conforme mostrado no seguinte exemplo.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
