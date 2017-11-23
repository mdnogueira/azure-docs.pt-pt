---
title: Como configurar MSI numa VM do Azure utilizando a CLI do Azure
description: "Passo pelo passo as instruções para configurar uma identidade de serviço geridas (MSI) na VM do Azure, utilizando a CLI do Azure."
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
ms.openlocfilehash: a8050383a03344e41c90af06e00dbca3885aa566
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Configurar uma VM geridos serviço de identidade (MSI) utilizando a CLI do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identidade de serviço geridas fornece serviços do Azure com uma identidade gerido automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter de credenciais no seu código. 

Neste artigo, irá aprender como ativar e remover MSI para uma VM do Azure, utilizando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Para executar os exemplos de script do CLI, tem três opções:

- Utilize [Shell de nuvem do Azure](../cloud-shell/overview.md) do portal do Azure (consulte a secção seguinte).
- Utilize a Shell de nuvem do Azure incorporados através de "Tente-" botão, localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente do CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir utilizar uma consola local do CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Ativar MSI durante a criação de uma VM do Azure

Para criar uma VM ativada de MSI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [início de sessão az](/cli/azure/#login). Utilize uma conta que está associada à subscrição do Azure na qual gostaria de implementar a VM:

   ```azurecli-interactive
   az login
   ```

2. Criar um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#terminology) para a implementação da VM e os respetivos recursos relacionados, utilizar e contenção [criar grupo az](/cli/azure/group/#create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Criar uma VM utilizando [az vm criar](/cli/azure/vm/#create). O exemplo seguinte cria uma VM chamada *myVM* com um MSI, conforme solicitado pelo `--assign-identity` parâmetro. O `--admin-username` e `--admin-password` parâmetros especificam a conta de nome e palavra-passe de utilizador administrativo para início de sessão na máquina virtual. Atualize estes valores conforme adequado para o seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Ativar MSI numa VM do Azure existente

Se precisar de ativar MSI numa máquina Virtual existente:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [início de sessão az](/cli/azure/#login). Utilize uma conta que está associada à subscrição do Azure que contém a VM. Certifique-se a sua conta pertencer a uma função que dá-lhe também permissões de escrita na VM, tais como "Contribuinte de Máquina Virtual":

   ```azurecli-interactive
   az login
   ```

2. Utilize [az vm atribuir-identity](/cli/azure/vm/#az_vm_assign_identity) com o `--assign-identity` parâmetro para adicionar um MSI para uma VM existente:

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Remover MSI a partir de uma VM do Azure

Se tiver uma Máquina Virtual que já não necessita de um MSI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [início de sessão az](/cli/azure/#login). Utilize uma conta que está associada à subscrição do Azure que contém a VM. Certifique-se a sua conta pertencer a uma função que dá-lhe também permissões de escrita na VM, tais como "Contribuinte de Máquina Virtual":

   ```azurecli-interactive
   az login
   ```

2. Utilize o `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` mudar (dependendo do tipo de VM) com [delete de extensão de vm az](https://docs.microsoft.com/cli/azure/vm/#assign-identity) para remover o MSI:

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Conteúdo relacionado

- [Descrição geral de identidade de serviço gerida](msi-overview.md)
- Para a criação de VM do Azure completa inícios rápidos, consulte: 

  - [Criar uma máquina virtual do Windows com a CLI](../virtual-machines/windows/quick-create-cli.md)  
  - [Criar uma máquina virtual Linux com a CLI](../virtual-machines/linux/quick-create-cli.md) 

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.
















