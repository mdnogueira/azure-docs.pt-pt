---
title: "Desenvolver aplicações para o Azure AD | Microsoft Docs"
description: "Escrito para profissionais de TI, este artigo fornece diretrizes para a integração de aplicações do Azure com o Active Directory."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: dd69f2bc-37c5-457c-857d-27acb84267fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76bb8d239b54b5236d077b98a5908c230cdc31a1
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Desenvolver aplicações de linha de negócio para o Azure Active Directory
Este guia fornece uma descrição geral do desenvolvimento de aplicações do linha de negócio (LoB) para o Azure Active Directory (AD). O público-alvo é administradores globais do Active Directory/Office 365.

## <a name="overview"></a>Descrição geral
Criar aplicações integradas com o Azure AD fornece aos utilizadores na sua organização-início de sessão único com o Office 365. Com a aplicação no fornecem do Azure AD que tem controlo sobre a política de autenticação para a aplicação. Para saber mais sobre o acesso condicional e de como proteger aplicações com autenticação multifator (MFA) consulte [configurar regras de acesso](active-directory-conditional-access-azure-portal-get-started.md).

Registe a sua aplicação para utilizar o Azure Active Directory. Registar a aplicação significa que os programadores podem utilizar o Azure AD para autenticar os utilizadores e solicitar acesso a recursos de utilizador, tais como e-mail, calendário e os documentos.

Qualquer membro do seu diretório (não convidados) pode registar uma aplicação, caso contrário, conhecida como *criar um objeto de aplicação*.

Registar uma aplicação permite que qualquer utilizador fazer o seguinte:

* Obter uma identidade para a respetiva aplicação que reconhece do Azure AD
* Obter um ou mais segredos/chaves que pode utilizar a aplicação para se autenticar perante AD
* Marca a aplicação no portal do Azure com um nome personalizado, o logótipo, etc.
* Aplicar funcionalidades de autorização do Azure AD para a sua aplicação, incluindo:

  * Controlo de Acesso Baseado em Funções (RBAC)
  * Azure Active Directory como servidor de autorização do oAuth (proteger uma API exposta pela aplicação)
* Declare as permissões necessárias necessário para a aplicação funcione conforme esperado, incluindo:

      - Permissões de aplicações (os administradores globais apenas). Por exemplo: membro da função na outra do Azure AD aplicação ou função de associação relativa um recurso de Azure, grupo de recursos ou subscrição
      - Permissões delegadas (qualquer utilizador). Por exemplo: do Azure AD, início de sessão e perfil de leitura

> [!NOTE]
> Por predefinição, qualquer membro pode registar uma aplicação. Para saber como restringir as permissões para registar aplicações aos membros específicos, consulte o artigo [como as aplicações são adicionadas para o Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Eis o que, o administrador global, precisa de fazer para ajudar os programadores tornar pronta para produção a respetiva aplicação:

* Configurar regras de acesso (política de acesso/MFA)
* Configurar a aplicação necessita de atribuição de utilizadores e atribuir utilizadores
* Suprimir a experiência de consentimento do utilizador predefinido

## <a name="configure-access-rules"></a>Configurar regras de acesso
Configure regras de acesso por aplicação às suas aplicações SaaS. Por exemplo, pode exigir a MFA ou permitir apenas o acesso a utilizadores em redes fidedignas. Os detalhes para este estão disponíveis no documento [configurar regras de acesso](active-directory-conditional-access-azure-portal-get-started.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurar a aplicação necessita de atribuição de utilizadores e atribuir utilizadores
Por predefinição, os utilizadores podem aceder a aplicações sem a ser atribuída. No entanto, se a aplicação expõe funções, ou se pretender que a aplicação a aparecer no painel de acesso de um utilizador, deve solicitar a atribuição de utilizadores.

[Necessidade de atribuição de utilizadores](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Se tiver um subscritor do Azure AD Premium ou Enterprise Mobility Suite (EMS), recomendamos vivamente a utilização de grupos. Atribuição de grupos para a aplicação permite-lhe delegar a gestão de acesso em curso para o proprietário do grupo. Pode criar o grupo ou peça a entidade responsável na sua organização ao criar o grupo utilizando a função de gestão de grupo.

[Atribuir utilizadores a uma aplicação](active-directory-applications-guiding-developers-assigning-users.md)  
[Atribuir grupos a uma aplicação](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Suprimir o consentimento do utilizador
Por predefinição, cada utilizador passa através de uma experiência de consentimento para iniciar sessão. A experiência de consentimento, pedir aos utilizadores para conceder permissões para uma aplicação, pode ser disconcerting para os utilizadores que não estão familiarizados com essas decisões.

Para aplicações que considera fidedignos, pode simplificar a experiência de utilizador por consenting para a aplicação em nome da sua organização.

Para obter mais informações sobre o consentimento do utilizador e o consentimento experiência no Azure, consulte [integrar aplicações com o Azure Active Directory](active-directory-integrating-applications.md).

## <a name="related-articles"></a>Artigos Relacionados
* [Ativar o acesso remoto seguro a aplicações no local com o Proxy de aplicações do Azure AD](active-directory-application-proxy-get-started.md)
* [Pré-visualização do acesso condicional do Azure para aplicações SaaS](active-directory-conditional-access-azure-portal-get-started.md)
* [Gerir o acesso a aplicações com o Azure AD](active-directory-managing-access-to-apps.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
