---
title: Como atribuir acesso MSI para um recurso do Azure, utilizando o PowerShell
description: "Passo a passo instruções para atribuir um MSI um recurso, aceder a outro recurso, utilizando o PowerShell."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 4ac10621330a671d279f15f3f100a68a57365825
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>Atribuir acesso uma identidade de serviço geridas (MSI) a um recurso com o PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso do Azure com um MSI, é possível conceder o acesso do MSI para outro recurso, tal como qualquer principal de segurança. Este exemplo mostra como conceder acesso MSI de uma máquina virtual do Azure para uma conta de armazenamento do Azure, utilizando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Além disso, instalar [Azure PowerShell versão 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) se ainda não o fez.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Utilizar o RBAC para atribuir o acesso MSI a outro recurso

Depois de ativar MSI um recurso do Azure, [como uma VM do Azure](msi-qs-configure-powershell-windows-vm.md):

1. Inicie sessão no Azure com o `Login-AzureRmAccount` cmdlet. Utilize uma conta que está associada à subscrição do Azure na qual configurou o MSI:

   ```powershell
   Login-AzureRmAccount
   ```
2. Neste exemplo, vamos dá ao acesso VM do Azure para uma conta de armazenamento. Primeiro vamos utilizar [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) para obter o principal de serviço para a VM com o nome "myVM", que foi criada quando é ativada MSI. Em seguida, utilizamos [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) para dar a VM acesso de "Leitor" a uma conta de armazenamento denominado "myStorageAcct":

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>Resolução de problemas

Se o MSI para o recurso não aparecer na lista de identidades disponíveis, certifique-se de que o MSI foi ativado corretamente. No nosso caso, iremos pode voltar para a VM do Azure a [portal do Azure](https://portal.azure.com) e:

- Observe a página "Configuration" e certifique-se MSI ativado = "Yes".
- Observe a página "Extensões" e certifique-se a extensão MSI implementada com êxito.

Se o estiver incorreto, terá de voltar a implementar o MSI no seu recurso ou resolver a falha de implementação.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](msi-overview.md).
- Para ativar o MSI numa VM do Azure, consulte [configurar um Azure VM geridos serviço de identidade (MSI) com o PowerShell](msi-qs-configure-powershell-windows-vm.md).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.

