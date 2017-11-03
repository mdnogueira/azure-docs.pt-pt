---
title: "O que é a colaboração B2B do Azure Active Directory do? | Microsoft Docs"
description: "Colaboração do Azure Active Directory B2B suporta as relações entre empresas, permitindo a parceiros de negócios seletivamente aceder a aplicações empresariais."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.custom: aaddev
ms.reviewer: sasubram
ms.openlocfilehash: fbc12a52555b190d43b5e953fd4d19923a25b0ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>O que é a colaboração B2B do Azure AD?

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

Azure AD empresa-empresa (B2B) colaboração as capacidades de ativar qualquer organização utilizar o Azure AD a trabalhar com segurança e de forma segura com os utilizadores de qualquer outra organização grande ou pequeno. As organizações podem ser com o Azure AD ou sem, ou mesmo com uma organização de TI ou sem. 

As organizações a utilizar o Azure AD podem fornecer acesso a documentos, recursos e aplicações para os respetivos parceiros, mantendo o controlo total sobre os seus próprios dados empresariais. Os programadores podem utilizar a Azure AD empresa-empresa APIs para escrever as aplicações que reunir as duas organizações no mais segura. Além disso, é pretty fácil para os utilizadores finais navegar.

97% dos nossos clientes disseram-na colaboração B2B do Azure AD é muito importante.

![gráfico circular](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

A partir do precoce de Abril de 2017, iremos tinha cerca de 3 milhões de utilizadores já a utilizar funcionalidades de colaboração B2B do Azure AD. E mais do que 23% organizações do Azure AD que tenham mais de 10 utilizadores já estão benefiting destas capacidades.

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Os principais benefícios da colaboração B2B do Azure AD para a sua organização

### <a name="work-with-any-user-from-any-partner"></a>Trabalhar com qualquer utilizador a partir de qualquer parceiro

* Parceiros de utilizam as suas próprias credenciais

* Sem requisito de parceiros ao utilizar o Azure AD

* Não existem diretórios externos ou complexa configuração necessária

### <a name="simple-and-secure-collaboration"></a>Colaboração segura e Simple

* Fornecer acesso a quaisquer aplicações empresariais ou dados, ao aplicar sofisticadas, Azure AD com tecnologia políticas de autorização de

* Mais fácil para os utilizadores

* Segurança de nível empresarial para aplicações e dados

### <a name="no-management-overhead"></a>Não existem custos de gestão

* Nenhuma gestão de conta ou palavra-passe externo

* Nenhuma gestão de ciclo de vida de conta de sincronização ou manual

* Nenhuma sobrecarga administrativa externa

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>Pode facilmente adicionar utilizadores de colaboração do B2B à sua organização

Administradores podem adicionar utilizadores (convidado) de colaboração B2B do [portal do Azure](https://portal.azure.com).

![Adicionar utilizadores convidados](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Ativar os funcionários colocar as seus próprios identidade

B2B colaboradores podem iniciar sessão com uma identidade da sua eleição. Se o utilizador não tiver uma conta Microsoft ou uma conta do Azure AD – é criada uma para os mesmos totalmente integrada no momento de resgate de oferta.

![opção de início de sessão de identidade](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>Delegar a aplicação e proprietários de grupo 
Aplicação e proprietários de grupo podem adicionar B2B utilizadores diretamente para qualquer aplicação que mais lhe interessam, se se trata de uma aplicação da Microsoft ou não. Administradores de podem delegar permissões para adicionar utilizadores de B2B para não administradores. Administradores não podem utilizar o [painel de acesso de aplicação do Azure AD](https://myapps.microsoft.com) para adicionar utilizadores de colaboração do B2B a aplicações ou grupos.

![Painel de acesso](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![Adicionar membro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Políticas de autorização de protegem os seus conteúdos de empresa

Políticas de acesso condicional, tais como a autenticação multifator, podem ser impostas:
- Ao nível do inquilino
- Ao nível da aplicação
- Para utilizadores específicos proteger aplicações e dados empresariais

![Adicionar membro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Utilize as nossos APIs e código para facilmente criar aplicações para carregar de exemplo
Traga os seus parceiros externos no board formas personalizada para necessidades da sua organização.

Utilizar o [convite de colaboração B2B APIs](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation), pode personalizar as suas experiências de integração, incluindo a criação de portais de inscrição self-service. Podemos fornecer o código de exemplo para um portal self-service [no Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![portal de inscrição](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Com a colaboração B2B do Azure AD, pode obter o poder do Azure AD para proteger as relações com parceiros de forma a que os utilizadores finais encontrar fácil e intuitivo. Por isso, passados, associar as milhares de organizações que já estão a utilizar o Azure AD B2B para as respetivas colaboração externa!

## <a name="next-steps"></a>Passos seguintes

* As experiências de administrador se encontram no [portal do Azure](https://portal.azure.com).

* Informações experiências de trabalho estão disponíveis no [painel de acesso](https://myapps.microsoft.com).

* E, como sempre, estabelecer ligação com a equipa de produto para quaisquer comentários, debates e sugestões através do nosso [Microsoft técnico Comunidade](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [Como é que os administradores de Azure Active Directory adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento e colaboração do Azure AD B2B](active-directory-b2b-licensing.md)
* [Resolução de problemas de colaboração B2B do Azure Active Directory do](active-directory-b2b-troubleshooting.md)
* [Colaboração do Azure Active Directory B2B perguntas mais frequentes (FAQ)](active-directory-b2b-faq.md)
* [Colaboração B2B do Active Directory Azure API e personalização](active-directory-b2b-api.md)
* [Autenticação multifator para os utilizadores da colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar utilizadores de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Utilizador de colaboração do B2B auditoria e de relatórios](active-directory-b2b-auditing-and-reporting.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
