---
title: "Início de sessão único do Azure o protocolo SAML | Microsoft Docs"
description: "Este artigo descreve o protocolo de SAML Sign-Out único no Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: 45e4705f53d80b5fe852c484b5e64d18a8e24f09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# Protocolo SAML fim de sessão único
Perfil de início de sessão único de browser web do Azure suporta do Active Directory (Azure AD) SAML 2.0. Para único fim de sessão funcione corretamente, o **LogoutURL** para a aplicação tem de ser explicitamente registada com o Azure AD durante o registo de aplicação. AD do Azure utiliza o LogoutURL para redirecionar os utilizadores depois de terminada.

Este diagrama apresenta o fluxo de trabalho do processo de início de sessão único do Azure AD.

![Início de sessão único o fluxo de trabalho](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## LogoutRequest
Envia de serviço em nuvem um `LogoutRequest` mensagem para o Azure AD para indicar que uma sessão foi terminada. O excerpt seguinte mostra um exemplo `LogoutRequest` elemento.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### LogoutRequest
O `LogoutRequest` elemento enviado para o Azure AD requer os seguintes atributos:

* `ID`: Este identifica o pedido de início de sessão. O valor de `ID` não devem começar com um número. A prática típica é a acrescentar **id** para a representação de cadeia de um GUID.
* `Version`: Definir o valor deste elemento para **2.0**. Este valor é preciso.
* `IssueInstant`: Este é um `DateTime` cadeia com um valor de hora Universal Coordenada (UTC) e [formato reportadas round-trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD espera um valor deste tipo, mas não impõe.

### emissor
O `Issuer` elemento um `LogoutRequest` deve corresponder exatamente um do **ServicePrincipalNames** no serviço em nuvem no Azure AD. Normalmente, isto estiver definido como o **URI de ID de aplicação** que é especificado durante o registo de aplicação.

### NameID
O valor da `NameID` elemento deve corresponder exatamente a `NameID` do utilizador que está a ser terminado.

## LogoutResponse
Azure AD envia um `LogoutResponse` em resposta a um `LogoutRequest` elemento. O excerpt seguinte mostra um exemplo `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### LogoutResponse
Conjuntos do Azure AD a `ID`, `Version` e `IssueInstant` valores no `LogoutResponse` elemento. Define também o `InResponseTo` elemento para que o valor da `ID` atributo do `LogoutRequest` elicited que a resposta.

### emissor
Azure AD define este valor como `https://login.microsoftonline.com/<TenantIdGUID>/` onde <TenantIdGUID> é o ID de inquilino do inquilino do Azure AD.

Para avaliar o valor da `Issuer` elemento, utilize o valor do **URI de ID de aplicação** fornecido durante o registo de aplicação.

### Estado
Azure AD utiliza o `StatusCode` elemento o `Status` elemento para indicar o êxito ou falha de início de sessão. Quando a tentativa de início de sessão falha, o `StatusCode` elemento também pode conter mensagens de erro personalizadas.