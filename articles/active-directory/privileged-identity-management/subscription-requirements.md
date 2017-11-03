---
title: "Privilegiado subscrições de gestão de identidades - Azure | Microsoft Docs"
description: "Explica a subscrição e os requisitos para gestão e a utilização do Azure AD Privileged Identity Management no seu inquilino de licenciamento"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 62d8f80fa1bec3a1b75e316f0b0ee7be8cbefbff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Requisitos da subscrição do Azure Active Directory Privileged Identity Management

O Azure AD Privileged Identity Management está disponível como parte da edição Premium P2 do Azure AD. Para obter mais informações sobre outras funcionalidades de P2 e como compara para Premium P1, consulte [edições do Azure Active Directory](../active-directory-editions.md).

>[!NOTE]
Quando o Azure Active Directory (Azure AD) Privileged Identity Management foi na pré-visualização, não existiam verifica nenhuma licença para um inquilino experimentarem o serviço.  Agora que o Azure AD Privileged Identity Management atingiu disponibilidade geral, uma subscrição de avaliação ou paga tem de estar presente para o inquilino continuar a utilizar o Privileged Identity Management após Dezembro de 2016.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Confirme a sua subscrição de avaliação ou paga

Se não tiver a certeza de se a sua organização tem uma versão de avaliação ou adquirido a subscrição, em seguida, pode verificar se há uma subscrição no seu inquilino, utilizando os comandos incluídos no Azure Active Directory módulo para o Windows PowerShell V1. 
1. Abra uma janela do PowerShell.
2. Introduza `Connect-MsolService` para autenticar como um utilizador no seu inquilino.
3. Introduza `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Este comando obtém uma lista de subscrições no seu inquilino. Se não houver nenhuma linhas devolvidas, terá de obter uma avaliação, compra de Azure AD Premium P2 uma subscrição do Azure AD Premium P2 ou EMS E5 subscrição para utilizar o Azure AD Privileged Identity Management.  Para obter uma versão de avaliação e começar a utilizar o Azure AD Privileged Identity Management, leia [introdução ao Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

Se este comando devolve uma linha na qual SkuPartNumber é "AAD_PREMIUM_P2" ou "EMSPREMIUM" e IsTrial for "True", isto indica que uma versão de avaliação do Azure AD Premium P2 está presente no inquilino.  Se o estado da subscrição não está ativado e não tiver uma subscrição do Azure AD Premium P2 ou EMS E5 comprar, em seguida, tem de comprar uma subscrição do Azure AD Premium P2 ou EMS E5 subscrição para continuar a utilizar o Azure AD Privileged Identity Management.

Do Azure AD Premium P2 está disponível através de um [Enterprise Agreement do Microsoft](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), a [programa de licenciamento em Volume abrir](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)e o [programa de fornecedores de soluções de nuvem](https://partner.microsoft.com/en-US/cloud-solution-provider). Os subscritores do Azure e o Office 365 também podem comprar o Azure AD Premium P2 online.  Podem encontrar mais informações sobre preços do Azure AD Premium e como ordenar online em [preços do Azure Active Directory](https://azure.microsoft.com/en-us/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Não está disponível no inquilino do Azure AD Privileged Identity Management

Já não estará disponível no seu inquilino do Azure AD Privileged Identity Management se:
- Organização estava a utilizar Azure AD Privileged Identity Management quando estava em pré-visualização e não comprar a subscrição do Azure AD Premium P2 ou EMS E5 subscrição.
- Organização tinha um P2 do Azure AD Premium ou EMS E5 avaliação expirado.
- Organização tinha uma subscrição adquirida que expirou.

Quando uma subscrição do Azure AD Premium P2 ou EMS E5 subscrição expira, ou de uma organização que estava a utilizar o Azure AD Privileged Identity Management pré-visualização não obter a subscrição do Azure AD Premium P2 ou do EMS E5:

- Atribuições de função permanente para funções do Azure AD será afetadas.
- A extensão do Azure AD Privileged Identity Management no portal do Azure, bem como os cmdlets de Graph API e PowerShell interfaces do Azure AD Privileged Identity Management, deixará de estar disponível para os utilizadores ativarem funções com privilégios, gerir o acesso privilegiado ou efetuar revisões de acesso de funções com privilégios.
- Atribuições de função elegível das funções do Azure AD serão removidas, como os utilizadores já não será possível ativar funções com privilégios.
- Quaisquer revisões de acesso em curso das funções do Azure AD irão terminar e as definições de configuração do Azure AD Privileged Identity Management serão removidas.
- O Azure AD Privileged Identity Management já não irá enviar mensagens de correio eletrónico no alterações à atribuição de função.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md)
- [Funções no Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-roles.md)
