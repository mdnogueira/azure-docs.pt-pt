---
title: Gerir o acesso aos recursos do Azure com o Azure Active Directory
description: "Saiba mais sobre as formas de gerir o acesso a recursos do Azure através de diferentes funcionalidades do Azure Active Directory."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: e42bbed0a9e37a6632b2a71d2b3546fcbc38cdc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Gerir o acesso aos recursos do Azure com o Azure Active Directory

Gestão de identidades e acesso para recursos de nuvem é uma função crítica para qualquer organização que está a utilizar a nuvem. Azure Active Directory (Azure AD) é o sistema de identidades e acessos do Microsoft Azure.  

Antes de explorar a suportar a funcionalidade áreas do Azure AD, veja o vídeo seguinte: "Bloquear acesso para a nuvem do Azure, utilizando SSO, controlo de acesso com base de funções e condicional". Aqui, pode obter informações sobre:

- Melhores práticas para configurar um único início de sessão no portal do Azure, com o Active Directory no local.
- A utilizar o RBAC do Azure para o controlo de acesso detalhada para recursos nas subscrições.
- Impor regras de autenticação forte, utilizando o acesso condicional do Azure AD.
- O conceito de geridos identidade de serviço, onde os recursos do Azure podem autenticar automaticamente aos serviços do Azure e os programadores não precisam de processar API chaves ou segredos.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Áreas de funcionalidade
O Azure AD fornece as seguintes funcionalidades para gerir o acesso aos recursos do Azure:

|||
|---|---|
| [Relação entre inquilinos do Azure AD e subscrições](active-directory-understanding-resource-access.md) | Saiba mais sobre como do Azure AD é a origem de fidedigna dos utilizadores e grupos para uma subscrição do Azure. |
| [Controlo de acesso baseado em funções (RBAC)](role-based-access-control-what-is.md) | Oferece gestão de acesso detalhada através de funções atribuídas a utilizadores, grupos ou principais de serviço. |
| [Gestão de identidades privilegiadas com RBAC](pim-azure-resource.md) | Elevada disponibilidade, controlo de acesso privilegiado através da atribuição de funções com privilégios just-in-time. |
| [Acesso condicional para gestão do Azure](conditional-access-azure-management.md) | Configure políticas de acesso condicional para permitir ou bloquear o acesso a pontos finais de gestão do Azure. |
| [Identidade de serviço geridas (MSI)](msi-overview.md) | Fornecer recursos do Azure como máquinas virtuais às suas próprias identidades, para que não tem de colocar as credenciais no seu código. |

 