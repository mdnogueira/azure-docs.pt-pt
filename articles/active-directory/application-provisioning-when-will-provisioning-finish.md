---
title: "Aprovisionamento de utilizadores a uma aplicação de galeria do Azure AD é colocar horas ou mais | Microsoft Docs"
description: "Como saber razão pela qual o aprovisionamento para a aplicação pode estar a demorar mais do que esperado"
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
ms.openlocfilehash: 183d60cbbbc8d02f7cd3cacc160453c45717ef0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Aprovisionamento de utilizadores a uma aplicação de galeria do Azure AD é colocar horas ou mais

Quando ativar primeiro o aprovisionamento automático de uma aplicação, a sincronização inicial pode tomar entre 20 minutos ou várias horas, consoante o tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. 

As sincronizações posteriores após a sincronização inicial ser mais rápida, como o serviço de aprovisionamento armazena as marcas d'água que representam o estado de ambos os sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes.

## <a name="how-to-improve-provisioning-performance"></a>Como melhorar o desempenho de aprovisionamento

Se a sincronização inicial estiver a demorar mais do que algumas horas, há única coisa que pode fazer para melhorar o desempenho:

-   **Filtros de âmbito do utilizador.** Filtros de âmbito permitem-lhe que os dados que o serviço de aprovisionamento extrai do Azure AD por filtrar os utilizadores com base nos valores de atributo específico. Para obter mais informações sobre filtros de âmbito, consulte [aprovisionamento de aplicações baseadas em atributos com filtros de âmbito](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Passos seguintes
[Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)

