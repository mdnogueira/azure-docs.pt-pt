---
title: "Alterar o ID do inquilino do cofre de chaves após a movimentação de uma subscrição | Microsoft Docs"
description: "Saiba como mudar o ID do inquilino para um cofre de chaves depois de mover uma subscrição para um inquilino diferente"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: 2f007dd4f877b48003cddcefa5f4321049853361
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Alterar um ID do inquilino do cofre de chaves após a movimentação de uma subscrição
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>P: A minha subscrição foi movida do inquilino A para o inquilino B. Como altero o ID do inquilino para o meu cofre de chaves existente e defino os ACLs corretos para os principais no inquilino B?
Ao criar um novo cofre de chaves numa subscrição, é automaticamente associado ao ID de inquilino do Azure Active Directory predefinido nessa subscrição. Todas as entradas de política de acesso também estão associadas a este ID de inquilino. Ao mover a sua subscrição do Azure do inquilino A para o inquilino B, os seus cofres de chaves existentes estão inacessíveis pelos principais (utilizadores e aplicações) no inquilino B. Para corrigir este problema, tem de:

* Alterar o ID de inquilino associado a todos os cofres de chaves existentes nesta subscrição para o inquilino B.
* Remover todas as entradas de política de acesso existentes.
* Adicionar novas entradas de política de acesso que estão associadas ao inquilino B.

Por exemplo, se tiver o cofre de chaves "myvault" numa subscrição que tenha sido movida do inquilino A para o B, saiba como alterar o ID de inquilino para este cofre de chaves e remover as políticas de acesso antigas.

<pre>
$Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Uma vez que este cofre estava no inquilino A antes da movimentação, o valor original do **$vault.Properties.TenantId** é o inquilino A, e **(Get-AzureRmContext).Tenant.TenantId** é inquilino B.

Agora que o cofre está associado ao ID de inquilino correto e as entradas de política de acesso antigas foram removidas, defina as novas entradas de política de acesso com [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Passos seguintes
Se tiver perguntas sobre o Cofre de Chaves do Azure, visite os [Fóruns do Cofre de Chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

