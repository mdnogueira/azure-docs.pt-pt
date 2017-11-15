---
title: "Referência do protocolo SAML do Azure AD | Microsoft Docs"
description: "Este artigo fornece uma descrição geral dos perfis de Single Sign-On e única Sign-Out SAML no Azure Active Directory."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.openlocfilehash: 7361d05850cf3ae997c0c186bf9a674c139f1f9e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Como o Azure Active Directory utiliza o protocolo SAML
Azure Active Directory (Azure AD) utiliza SAML 2.0 que o protocolo permitem às aplicações fornecer uma experiência de início de sessão única aos respetivos utilizadores. O [Single Sign-On](active-directory-single-sign-on-protocol-reference.md) e [fim de sessão único](active-directory-single-sign-out-protocol-reference.md) perfis SAML do Azure AD explicam como SAML asserções, protocolos e enlaces são utilizados no serviço do fornecedor de identidade.

Protocolo SAML requer que o fornecedor de identidade (Azure AD) e o fornecedor de serviços (aplicação) para trocar informações sobre si próprios.

Quando uma aplicação for registada com o Azure AD, o programador da aplicação regista informações relacionadas com a Federação com o Azure AD. Isto inclui o **URI de redirecionamento** da aplicação.

Azure Active Directory expõe específicas do inquilino e comuns (independente de inquilino) único início de sessão e única fim de sessão pontos finais. Estes URLs representam localizações endereçáveis – não são apenas um identificadores –, pelo que pode ir para o ponto final para ler os metadados.

* O ponto final de inquilino específico está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  O <TenantDomainName> marcador de posição representa um nome de domínio registado ou o GUID de TenantID de um inquilino do Azure AD. Por exemplo, os metadados de Federação do inquilino contoso.com são: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto final independente de inquilino está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Este endereço de ponto final, **comuns** for apresentada, em vez de um nome de domínio do inquilino ou um ID.

Para obter informações sobre os documentos de metadados de Federação publica do Azure AD, consulte [metadados da Federação](active-directory-federation-metadata.md).
