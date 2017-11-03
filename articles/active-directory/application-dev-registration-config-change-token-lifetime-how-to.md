---
title: "Como alterar a duração do token está predefinida para uma aplicação desenvolvida personalizado | Microsoft Docs"
description: "Como atualizar políticas de duração do Token da aplicação que estiver a desenvolver no Azure AD"
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
ms.openlocfilehash: a28eacd820ed28a6470992ce86b060e886c00bcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar as predefinições de duração do token para uma aplicação desenvolvida personalizada

O Azure AD Premium permite que os programadores de aplicações e administradores de inquilinos configurar a duração de tokens emitidos para clientes não confidencial. Políticas de duração do token estão definidas numa base em todo o inquilino ou os recursos acedidos.

 * Para definir uma política de duração do token, terá de transferir o [módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Execute o **Connect-AzureAD-confirme** comando.

 * Segue-se uma política de exemplo que define o token de atualização de fator único de idade máxima. Crie uma política:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Finalizar a [duração do token configurar](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes) documento para saber como criar outra personalizada.

## <a name="next-steps"></a>Passos seguintes
[Configurar a duração do Token](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Referência de Token do Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)

