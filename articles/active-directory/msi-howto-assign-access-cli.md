---
title: Como atribuir acesso MSI para um recurso do Azure, utilizando a CLI do Azure
description: "Passo a passo instruções para atribuir um MSI um recurso, aceder a outro recurso, utilizando a CLI do Azure."
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
ms.date: 09/25/2017
ms.author: bryanla
ms.openlocfilehash: e77915c1d982ccf6262ffcbc09dc91dfd986dac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Atribuir acesso uma identidade de serviço geridas (MSI) a um recurso com a CLI do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso do Azure com um MSI, é possível conceder o acesso do MSI para outro recurso, tal como qualquer principal de segurança. Este exemplo mostra como conceder acesso MSI de uma máquina virtual do Azure para uma conta de armazenamento do Azure, utilizando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Para executar os exemplos de script do CLI, tem três opções:

- Utilize [Shell de nuvem do Azure](../cloud-shell/overview.md) do portal do Azure (consulte a secção seguinte).
- Utilize a Shell de nuvem do Azure incorporados através de "Tente-" botão, localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente do CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir utilizar uma consola local do CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Utilizar o RBAC para atribuir o acesso MSI a outro recurso

Depois de ativar MSI um recurso do Azure, [como uma VM do Azure](msi-qs-configure-cli-windows-vm.md): 

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [início de sessão az](/cli/azure/#login). Utilize uma conta que está associada à subscrição do Azure na qual gostaria de implementar a VM:

   ```azurecli-interactive
   az login
   ```

2. Neste exemplo, vamos dá ao acesso VM do Azure para uma conta de armazenamento. Primeiro vamos utilizar [lista de recursos de az](/cli/azure/resource/#list) para obter o principal de serviço para a VM com o nome "myVM", que foi criada quando foi ativado o MSI da VM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. Assim que tivermos o ID de principal de serviço, utilizamos [de criação da atribuição de função az](/cli/azure/role/assignment#az_role_assignment_create) para dar a VM acesso de "Leitor" a uma conta de armazenamento denominado "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Resolução de problemas

Se o MSI para o recurso não aparecer na lista de identidades disponíveis, certifique-se de que o MSI foi ativado corretamente. No nosso caso, iremos pode voltar para a VM do Azure a [portal do Azure](https://portal.azure.com) e:

- Observe a página "Configuration" e certifique-se MSI ativado = "Yes".
- Observe a página "Extensões" e certifique-se a extensão MSI implementada com êxito.

Se o estiver incorreto, terá de voltar a implementar o MSI no seu recurso ou resolver a falha de implementação.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](msi-overview.md).
- Para ativar o MSI numa VM do Azure, consulte [configurar um Azure VM geridos serviço de identidade (MSI) utilizando a CLI do Azure](msi-qs-configure-cli-windows-vm.md).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.

