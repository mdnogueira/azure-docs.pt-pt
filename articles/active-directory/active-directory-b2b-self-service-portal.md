---
title: "Portal de inscrição self-service para colaboração B2B do Azure Active Directory do | Microsoft Docs"
description: "A colaboração do B2B Azure Active Directory suporta as relações entre empresas, permitindo a parceiros de negócios acederem, seletivamente, às suas aplicações empresariais"
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
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal self-service para inscrição de colaboração B2B do Azure AD

Os clientes podem fazer muito com as funcionalidades incorporadas que estão expostas através do nosso administrador de TI [portal do Azure](https://portal.azure.com) e no nosso [painel de acesso de aplicação](https://myapps.microsoft.com) para os utilizadores finais. Mas estamos também em atenção de que as empresas necessitam de personalizar o fluxo de trabalho de integração para os utilizadores de B2B para se ajustarem às necessidades da sua organização. Podem fazê-lo com [nossa API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Em discussões com os nossos clientes, vemos um comuns tem de aumentar a segurança acima de todos os outros. A organização convidando não saiba antecedência quem são os colaboradores externos individuais que necessitam de aceder aos respetivos recursos. Pretendessem uma forma para os utilizadores de empresas, para se inscrever-se com um conjunto de políticas que controla a organização convidando. Este cenário é possível através do nosso APIs, pelo que iremos publicado um projeto no Github que criou apenas que: [projeto do Github de exemplo](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Nosso projeto Github demonstra como as organizações podem utilizar os APIs e fornecer uma capacidade de inscrição baseadas em políticas, self-service para os respetivos parceiros confiadores, com regras que determinam as aplicações que possam aceder ao. Os utilizadores do parceiro aceder aos recursos quando precisam, de forma segura, sem exigindo que a organização convidando para carregar manualmente. Pode facilmente implementar o projeto para uma subscrição do Azure à sua escolha.

## <a name="as-is-code"></a>Como-é código

Lembre-se de que este código é disponibilizado como um exemplo para demonstrar a utilização do convite do Azure Active Directory B2B API. Deve ser personalizada pela sua equipa de desenvolvimento ou de um parceiro e deve ser revisto antes de ser implementado num cenário de produção.

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:
* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como é que os administradores de Azure Active Directory adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento e colaboração do Azure AD B2B](active-directory-b2b-licensing.md)
* [Resolução de problemas de colaboração B2B do Azure Active Directory do](active-directory-b2b-troubleshooting.md)
* [Colaboração do Azure Active Directory B2B perguntas mais frequentes (FAQ)](active-directory-b2b-faq.md)
* [Autenticação multifator para os utilizadores da colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar utilizadores de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)