---
title: "Como configurar o aprovisionamento de utilizadores a uma aplicação de galeria do Azure AD | Microsoft Docs"
description: "Como pode rapidamente configurar conta de utilizador Rica de aprovisionamento e desaprovisionamento para aplicações já listadas na Galeria de aplicações do Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 2e38fcb30ea7632339a3ba8815a536872cfcc69e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Como configurar o aprovisionamento de utilizadores a uma aplicação de galeria do Azure AD

*Aprovisionamento da conta de utilizador* é o ato de criar, atualizar e/ou desativar os registos da conta de utilizador no arquivo de perfil de utilizador local de uma aplicação. A maioria das nuvem e de aplicações SaaS armazenam as permissões e a função de utilizadores no seu próprio arquivo de perfil de utilizador local, não sendo presença deste tipo um registo de utilizador no arquivo local *necessário* para o início de sessão único e acesso ao trabalho.

No portal do Azure, o **aprovisionamento** separador no painel de navegação esquerdo para uma aplicação empresarial apresenta os modos de aprovisionamento são suportados para essa aplicação. Isto pode ser um de dois valores:

## <a name="configuring-an-application-for-manual-provisioning"></a>Configuração de uma aplicação de aprovisionamento Manual

*Manual* aprovisionamento significa que as contas de utilizador tem de ser criadas manualmente utilizando os métodos fornecidos por essa aplicação. Isto pode significar registo para um portal de administração para essa aplicação e adicionar utilizadores que utilizam uma interface de utilizador baseadas na web. Ou foi possível carregar uma folha de cálculo com detalhes de conta de utilizador, utilizando um mecanismo fornecido por essa aplicação. Consulte a documentação fornecida pela aplicação, ou contacte o programador da aplicação para determinar a wat mecanismos estão disponíveis.

Se Manual é o único modo mostrado para uma aplicação específica, significa que nenhum automático do Azure AD aprovisionamento conector foi criado para a aplicação ainda. Ou, significa que a aplicação não suporta a API de gestão de pré-requisitos do utilizador após a qual pretende criar um conector de aprovisionamento automatizado.

Se gostaria de pedido de suporte para o aprovisionamento automático para uma determinada aplicação, pode preencher um pedido no <http://aka.ms/aadapprequest>.

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configurar uma aplicação para o aprovisionamento automático

*Automática* significa que um Azure AD que o conector de aprovisionamento tem foi desenvolvido para esta aplicação. Para obter mais informações sobre o Azure AD de aprovisionamento de serviço e como funciona, consulte [automatizar utilizador aprovisionamento e desaprovisionamento para aplicações de SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Para obter mais informações sobre como aprovisionar utilizadores e grupos para uma aplicação específicos, consulte [gerir aprovisionamento da conta de utilizador para aplicações da empresa](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Os passos reais necessários para ativar e configurar o aprovisionamento automático variam, dependendo da aplicação.

>[!NOTE]
>Deve começar por ao localizar o tutorial do programa de configuração específicas para configurar o aprovisionamento para a sua aplicação e segue os passos para configurar a aplicação e o Azure AD para criar a ligação de aprovisionamento. 
>
>

Tutoriais de aplicação podem ser encontrados em [lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Um aspeto importante a considerar ao configurar o aprovisionamento ser para rever e configurar os mapeamentos de atributos e os fluxos de trabalho que definem o que utilizador (ou grupo) propriedades fluxo do Azure AD para a aplicação. Isto inclui a definição "da propriedade correspondente" que ser utilizado para identificar e correspondam aos utilizadores/grupos entre os dois sistemas de forma exclusiva. Para mais informações sobre este processo importante.

## <a name="next-steps"></a>Passos seguintes
[Personalização de utilizador aprovisionamento mapeamentos de atributos para aplicações de SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

