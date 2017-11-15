---
title: Utilizar grupos para gerir o acesso aos recursos no Azure Active Directory | Microsoft Docs
description: "Como utilizar grupos no Azure Active Directory para gerir o acesso de utilizador no local e de aplicações em nuvem e de recursos."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 714120d0-cdf9-465d-afee-39bef591c6b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 6f43b722b0c9a31c8c7da4361e415673f3721b31
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="manage-access-to-resources-with-azure-active-directory-groups"></a>Gerir o acesso a recursos com grupos do Active Directory do Azure
Azure Active Directory (Azure AD) é uma solução de gestão identidades e acessos abrangente que fornece um conjunto robusto de funcionalidades para gerir o acesso a recursos, incluindo os serviços online da Microsoft como o Office 365 e aplicações em nuvem e no local e um mundo de aplicações SaaS não Microsoft. Este artigo fornece uma descrição geral, mas se pretender começar a utilizar o Azure AD grupos neste momento, siga as instruções em [gerir grupos de segurança no Azure AD](active-directory-groups-create-azure-portal.md). Se quiser ver como pode utilizar o PowerShell para gerir grupos no Azure Active directory pode ler mais em [cmdlets do Azure Active Directory para gestão de grupo](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

> [!NOTE]
> Para utilizar o Azure Active Directory, terá de uma conta do Azure. Se não tiver uma conta, pode [inscrever-se numa conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).
>
>

Dentro do Azure AD, uma das principais funcionalidades é a capacidade para gerir o acesso aos recursos. Estes recursos podem fazer parte do diretório, como no caso de permissões para gerir objetos através de funções no diretório ou recursos que são externos ao diretório, tais como aplicações SaaS, serviços do Azure e sites do SharePoint ou recursos no local. Existem quatro formas que um utilizador pode ser atribuído direitos de acesso a um recurso:

1. Atribuição direta

    Os utilizadores podem ser atribuídos diretamente a um recurso pelo proprietário desse recurso.
2. Associação ao grupo

    Um grupo pode ser atribuído a um recurso pelo proprietário do recurso e, deste modo, conceder os membros desse grupo acesso ao recurso. A associação do grupo, em seguida, pode ser gerida pelo proprietário do grupo. Efetivamente, o proprietário do recurso delega a permissão para atribuir utilizadores ao seu recurso para o proprietário do grupo.
3. Baseado em regras

    O proprietário do recurso pode utilizar uma regra para expressar que devem ser atribuído aos utilizadores acesso a um recurso. O resultado da regra depende os atributos utilizados em dessa regra e os respetivos valores para utilizadores específicos e ao fazê-lo, o proprietário do recurso delega eficazmente o direito de gerir o acesso ao seu recurso para a origem autoritativa para os atributos que são utilizados em a regra. O proprietário do recurso ainda gere a regra de si próprio e determina quais os atributos e valores de fornecem acesso ao seu recurso.
4. Autoridade externa

    O acesso a um recurso é derivado de uma origem externa; Por exemplo, um grupo que é sincronizado a partir de uma origem autoritária, tais como um diretório no local ou uma aplicação de SaaS, como do WorkDay. O proprietário do recurso atribui o grupo para fornecer acesso ao recurso e a origem externa gere os membros do grupo.

   ![Descrição geral do diagrama de gestão de acesso](./media/active-directory-access-management-groups/access-management-overview.png)

## <a name="watch-a-video-that-explains-access-management"></a>Ver um vídeo que explica a gestão de acesso
Pode ver um breve vídeo, que explica mais sobre esta:

**Do Azure AD: Introdução a filiação dinâmica para grupos**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]
>
>

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Como aceder à gestão de trabalho do Azure Active Directory?
O centro do Azure AD a solução de gestão de acesso é o grupo de segurança. Utilização de um grupo de segurança para gerir o acesso aos recursos é uma paradigma bem conhecida, que permite uma forma flexível e facilmente compreendida fornecer acesso a um recurso para o grupo de utilizadores pretendido. O proprietário do recurso (ou o administrador do diretório), pode atribuir um grupo para fornecer um determinado direitos de acesso aos recursos que possuem. Os membros do grupo serão fornecidos o acesso e o proprietário do recurso pode delegar o direito de gerir a lista de membros de um grupo para outra pessoa, por exemplo, um Gestor de departamento ou um administrador de suporte técnico.

![Diagrama de gestão de acesso do Azure Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

O proprietário de um grupo pode também efetuar esse grupo disponíveis para pedidos de self-service. Ao fazê-lo, um utilizador final pode procurar e encontrar o grupo e efetuar um pedido para associar, pesquisa eficazmente permissão para aceder aos recursos que são geridos através do grupo de. O proprietário do grupo pode configurar o grupo para que os pedidos de associação são aprovados automaticamente ou exigem a aprovação pelo proprietário do grupo. Quando um utilizador faz um pedido para aderir a um grupo, o pedido de associação seja reencaminhado para os proprietários do grupo. Se uma dos proprietários aprova o pedido, o utilizador requerente é notificado e o utilizador está associado ao grupo. Se uma dos proprietários nega o pedido, o utilizador requerente é notificado mas não associado ao grupo.

## <a name="getting-started-with-access-management"></a>Introdução à gestão de acesso
Pronto para começar? Deve tentar terminar algumas das tarefas básicas que pode fazer com grupos do Azure AD. Utilize estas capacidades para fornecer acesso especializado para diferentes grupos de pessoas para diferentes recursos na sua organização. Uma lista dos passos básicos de primeiro estão listados abaixo.

* [Criar uma regra simples para configurar a filiação dinâmica para um grupo](active-directory-groups-create-azure-portal.md)
* [Utilização de um grupo para gerir o acesso a aplicações SaaS](active-directory-accessmanagement-group-saasapps.md)
* [Disponibilizar um grupo para o final utilizador self-service](active-directory-accessmanagement-self-service-group-management.md)
* [A sincronizar a um grupo no local para o Azure com o Azure AD Connect](active-directory-aadconnect.md)
* [Gerir proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md)

## <a name="next-steps"></a>Passos seguintes
Agora que tem compreendido as noções básicas de gestão de acesso, aqui estão algumas funcionalidades avançadas adicionais disponíveis no Azure Active Directory para gerir o acesso aos seus recursos e aplicações.

* [Utilizar atributos para criar regras avançadas](active-directory-groups-dynamic-membership-azure-portal.md)
* [Gerir grupos de segurança no Azure AD](active-directory-groups-create-azure-portal.md)
* [Configurar grupos dedicados no Azure AD](active-directory-accessmanagement-dedicated-groups.md)
* [Referência da Graph API para grupos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
