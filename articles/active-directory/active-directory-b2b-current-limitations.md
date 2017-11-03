---
title: "Limitações de colaboração B2B do Azure Active Directory do | Microsoft Docs"
description: "Limitações atuais para colaboração B2B do Azure Active Directory do"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 581e5d1fb5fb08d0dc89ed2c85edcb5f0005650b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitações de colaboração B2B do Azure AD
Colaboração B2B do Active Directory (Azure AD) Azure está atualmente sujeitos as limitações descritas neste artigo.

## <a name="possible-double-multi-factor-authentication"></a>Autenticação multifator dupla possíveis
Com o Azure AD B2B, pode impor autenticação multifator na organização de recursos (a organização convidando). As razões para esta abordagem passo são detalhadas no [acesso condicional para os utilizadores de colaboração do B2B](active-directory-b2b-mfa-instructions.md). Se um parceiro já tem a autenticação multifator, configurar e imposto, os seus utilizadores poderão ter de efetuar a autenticação de uma vez na respetiva organização inicial e, em seguida, novamente no seu.

## <a name="instant-on"></a>No instantâneas
Os fluxos de colaboração B2B, iremos adicionar utilizadores ao diretório e atualizar dinamicamente durante resgate convite, atribuição de aplicação e assim sucessivamente. As atualizações e escritas normalmente acontecer na instância de um diretório e têm de ser replicadas em todas as instâncias. Conclusão da replicação depois de todas as instâncias são atualizadas. Por vezes, quando o objeto é escrito ou atualizado numa instância e a chamada para obter este objeto é outra instância, podem ocorrer latências de replicação. Se isto acontecer, atualize ou volte a tentar para o ajudar. Se estiver a escrever uma aplicação utilizando a nossa API, tentativas com algumas término é uma prática bom e defesas para reduzir este problema.

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de utilizador de colaboração B2B](active-directory-b2b-user-properties.md)
* [A adição de um utilizador de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar B2bB convites de colaboração](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e de colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração do B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicações SaaS colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de utilizador de colaboração B2B](active-directory-b2b-user-token.md)
* [Afirmações de utilizador de colaboração B2B mapeamento](active-directory-b2b-claims-mapping.md)
* [Partilha externa do Office 365](active-directory-b2b-o365-external-user.md)
