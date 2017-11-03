---
title: Partilha de contas de utilizar o Azure AD | Microsoft Docs
description: "Descreve como o Azure Active Directory permite às organizações partilhar com segurança as contas de aplicações no local e serviços de cloud de consumidor."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e2d77104-d978-46a3-bfea-03ffdf3b61e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: b5a6bad6eca3f0262d5cc2ac01fb3a4eb3676e5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sharing-accounts-with-azure-ad"></a>Partilha de contas com o Azure AD
## <a name="overview"></a>Descrição geral
Por vezes, as organizações precisam de utilizar um único nome de utilizador e palavra-passe de várias pessoas. Esta situação ocorre normalmente em dois cenários:

* Ao aceder a aplicações que requerem um início de sessão único e uma palavra-passe para cada utilizador, se aplicações no local ou o consumidor serviços em nuvem (por exemplo, contas de redes sociais empresariais).
* Ao criar ambientes de vários utilizadores. Pode ter uma conta local, única que tem privilégios elevados e pode ser utilizada para principais atividades de configuração, a administração e a recuperação (por exemplo, "global" conta de administrador local para o Office 365) ou a conta de raiz no Salesforce.

Tradicionalmente, estas contas teriam de ser partilhadas por distribuir as credenciais (nome de utilizador/palavra-passe) para os indivíduos direita ou armazene-os numa localização partilhada onde múltiplos agentes fidedignos podem aceder aos mesmos.

O modelo de partilha tradicional tem vários desvantagens:

* Permitir o acesso a aplicações novas requer a distribuir as credenciais para todas as pessoas que necessita de acesso.
* Cada aplicação partilhada pode exigir que o seu próprio conjunto exclusivo de credenciais partilhadas, que obriga os utilizadores de recordar vários conjuntos de credenciais. Quando os utilizadores tenham de memorizar credenciais muitos, o risco aumenta que irá recorrer ao risco práticas. (por exemplo, escrever para baixo de palavras-passe).
* Não é possível saber quem tem acesso a uma aplicação.
* Não é possível saber quem tem *acedidos* uma aplicação.
* Quando tiver de remover o acesso a uma aplicação, terá de atualizar as credenciais e distribuí-los novamente para todos os utilizadores que necessita de acesso a essa aplicação.

## <a name="azure-active-directory-account-sharing"></a>Partilha e conta do Azure Active Directory
O Azure AD fornece uma nova abordagem à utilização de contas partilhadas, que elimina estes desvantagens.

O administrador do Azure AD configura as aplicações que um utilizador pode aceder ao utilizar o painel de acesso e escolher o tipo de melhor de início de sessão único adequam para essa aplicação. Um desses tipos *baseada em palavra-passe de início de sessão único*, permite ao Azure AD atuar como um tipo de "Mediador" durante o processo de início de sessão para essa aplicação.

Os utilizadores iniciar sessão uma vez com a respetiva conta profissional. Esta é a mesma conta que utilizam regularmente para aceder ao respetivo ambiente de trabalho ou e-mail. Podem detetar e aceder apenas as aplicações que estão atribuídos. Com as contas partilhadas, esta lista de aplicações pode incluir qualquer número de credenciais partilhadas. O utilizador final não tem Lembre-se ou anote as várias contas que podem estar a utilizar.

Contas partilhadas não só aumentam supervisão e melhoram a facilidade de utilização, também melhoram a segurança. Os utilizadores com permissões para utilizar as credenciais não vê a palavra-passe partilhada, mas em vez disso, obter permissões para utilizar a palavra-passe como parte de um fluxo de autenticação orquestradas. Além disso, com algumas aplicações de SSO de palavra-passe, tem a opção para ter o Azure AD periodicamente rollover (atualização) a palavra-passe complexas, grande palavras-passe, aumentar a segurança da conta a utilizar. O administrador pode facilmente conceder ou revogar o acesso a uma aplicação e também saber quem tem acesso à conta e quem acedeu ao mesmo no passado.

AD do Azure suporta a contas partilhadas para qualquer Enterprise Mobility Suite (EMS), Premium ou Basic utilizadores licenciados, em todos os tipos de palavra-passe única, inicie sessão em aplicações. Pode partilhar contas para qualquer uma das milhares de aplicações previamente integradas na Galeria de aplicações e pode adicionar a sua própria aplicação de autenticação de palavra-passe com [aplicações personalizadas de SSO](active-directory-sso-integrate-saas-apps.md).

Funcionalidades do Azure AD que ativar a partilha de conta incluem:

* [Palavra-passe-início de sessão único](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Palavra-passe único início de sessão no agente
* [Atribuição de grupo](active-directory-accessmanagement-self-service-group-management.md)
* Aplicações de palavra-passe personalizada
* [Dashboard/relatórios de utilização da aplicação](active-directory-passwords-get-insights.md)
* Portais de acesso do utilizador final
* [Proxy de aplicações](active-directory-application-proxy-get-started.md)
* [Do Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Uma conta de partilha
Para utilizar o Azure AD para uma conta, terá de partilhar:

* Adicionar uma aplicação [Galeria de aplicações](https://azure.microsoft.com/marketplace/active-directory/) ou [aplicação personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
* Configurar a aplicação para a palavra-passe único Sign-On (SSO)
* Utilize [atribuição baseada em grupo](active-directory-accessmanagement-group-saasapps.md) e selecione a opção para introduzir uma credencial partilhada
* Opcional: em algumas aplicações, tais como o Facebook, Twitter ou LinkedIn, pode ativar a opção para [do Azure AD automatizada palavra-passe roll a ativação pós-falha](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Pode também efetuar a conta partilhada mais segura com o multi-factor Authentication (MFA) (saber mais sobre [proteger aplicações com o Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)) e pode delegar a capacidade de gerir quem tem acesso à aplicação utilizando [ Azure AD Self-Service](active-directory-accessmanagement-self-service-group-management.md) gestão de grupo.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Proteger aplicações com o acesso condicional](active-directory-conditional-access.md)
* [Gestão de grupos self-service/SSAA](active-directory-accessmanagement-self-service-group-management.md)

