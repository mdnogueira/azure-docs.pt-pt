---
title: "Protocolos de autenticação do Azure Active Directory | Microsoft Docs"
description: "Uma descrição geral de protocolos de autenticação suportados pelo Azure Active Directory (AD)"
documentationcenter: dev-center-name
author: priyamohanram
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: e106a9bdf28243dd829b6a014b73c148809c1bde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# Protocolos de autenticação do Azure Active Directory
Azure Active Directory (Azure AD) suporta vários os protocolos de autenticação e autorização mais amplamente utilizados. Os tópicos nesta secção descrevem os protocolos suportados e a respetiva implementação no Azure AD. Os tópicos incluídos uma revisão dos tipos de afirmação suportados, uma introdução à utilização de metadados de Federação, detalhada OAuth 2.0. função de segurança e documentação de referência do protocolo SAML 2.0 e uma secção de resolução de problemas.

## Protocolos de autenticação artigos e de referência
* [Importante informações sobre como assinar Rollover de chave no Azure AD](active-directory-signing-key-rollover.md) – Saiba mais sobre a assinatura do Azure AD cadência da chave, as alterações que pode efetuar para atualizar automaticamente a chave e debate sobre como atualizar os cenários de aplicações mais comuns.
* [Token e tipos de afirmação suportados](active-directory-token-and-claims.md) -Saiba mais sobre as afirmações nos tokens de que o Azure AD emite.
* [Os metadados de Federação](active-directory-federation-metadata.md) -aprender a localizar e interpretar os documentos de metadados que gera do Azure AD.
* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md) -Saiba mais sobre a implementação de OAuth 2.0 no Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) -aprender a utilizar um protocolo de autorização do OAuth 2.0 para autenticação.
* [Chamadas de serviços com credenciais do cliente](active-directory-protocols-oauth-service-to-service.md) -aprender a utilizar o fluxo de concessão de credenciais de cliente OAuth 2.0 para chamadas de serviço ao serviço.
* [Chamadas de serviços com o fluxo de On-Behalf-Of](active-directory-protocols-oauth-on-behalf-of.md) -aprender a utilizar o fluxo de OAuth 2.0 On-Behalf-Of para chamadas de serviço ao serviço.
* [Referência do protocolo SAML](active-directory-saml-protocol-reference.md) -Saiba mais sobre os perfis de Single Sign-On e única Sign-out SAML do Azure AD.

## Veja Também
[Guia para programadores do Azure Active Directory](active-directory-developers-guide.md)

[Exemplos de código do Active Directory](active-directory-code-samples.md)
