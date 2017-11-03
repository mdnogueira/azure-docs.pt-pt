---
title: "Comparar a colaboração B2B e B2C no Azure Active Directory | Microsoft Docs"
description: "O que é a diferença entre a colaboração B2B do Active Directory do Azure e o Azure AD B2C?"
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
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 44cbbc149787a2d6cf2e0e8750b98d33b52f6136
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparar a colaboração B2B e B2C no Azure Active Directory

Colaboração B2B do Azure Active Directory (Azure AD) e o Azure AD B2C permitem-lhe trabalhar com utilizadores externos no Azure AD. Mas a sua comparação?


Funcionalidades de colaboração do B2B |     Oferta de autónoma AD B2C do Azure
-------- | --------
Se destina a: as organizações que pretendem conseguir autenticar os utilizadores da organização de parceiro, independentemente do fornecedor de identidade. | Se destina a: Inviting se os clientes do seu dispositivo móvel e as aplicações web, pessoas, os clientes institutional ou organizacionais no seu Azure AD.
Identidades suportadas: os funcionários com o trabalho ou escola contas, parceiros com contas profissionais ou escolares ou qualquer endereço de e-mail. Em breve para suportar a Federação direta.  | Identidades suportadas: os utilizadores com contas de aplicação local (qualquer utilizador ou endereço de nome de e-mail) ou qualquer suportado social identidade com a Federação direta.
O diretório de utilizadores de parceiros estão em: os utilizadores de parceiro da organização externo são geridos no mesmo diretório que os funcionários, mas anotados especial. Estes podem ser geridos da mesma forma que os funcionários, podem ser adicionados para os mesmos grupos e assim sucessivamente  | O diretório as entidades de utilizador do cliente estão em: no diretório de aplicação. Gerido separadamente a partir do diretório da organização dos funcionários e parceiros (se aplicável.
Início de sessão único (SSO) para todas as aplicações do Azure AD-ligado é suportada. Por exemplo, pode fornecer acesso ao Office 365 ou aplicações no local e para outras aplicações de SaaS, como o Salesforce ou Workday.  |  SSO ao cliente pertencentes à empresa de aplicações dentro de inquilinos do Azure AD B2C é suportada. Não é suportado o SSO para Office 365 ou para outras aplicações SaaS não Microsoft e da Microsoft.
Ciclo de vida do parceiro: geridos através do anfitrião/inviting organização.  | Ciclo de vida do cliente: gestão personalizada ou geridos pela aplicação.
Política de segurança e conformidade: geridos através do anfitrião/inviting organização.  | Política de segurança e conformidade: gerida pela aplicação.
Imagem corporativa: É utilizada a marca do anfitrião/inviting da organização.  |    Imagem corporativa: Gerida pela aplicação. Normalmente, tende produto imagem corporativa, com o fading organização em segundo plano.
Obter mais informações: [blogue](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentação](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Obter mais informações: [página de produto](https://azure.microsoft.com/en-us/services/active-directory-b2c/), [documentação](https://docs.microsoft.com/en-us/azure/active-directory-b2c/)


### <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de utilizador de colaboração B2B](active-directory-b2b-user-properties.md)
* [A adição de um utilizador de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites para colaboração do B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e de colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Configurar aplicações SaaS colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de utilizador de colaboração B2B](active-directory-b2b-user-token.md)
* [Afirmações de utilizador de colaboração B2B mapeamento](active-directory-b2b-claims-mapping.md)
* [Partilha externa do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais de colaboração B2B](active-directory-b2b-current-limitations.md)
* [Obter o suporte para colaboração B2B](active-directory-b2b-support.md)
