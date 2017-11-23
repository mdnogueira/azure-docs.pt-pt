---
title: Como configurar MSI numa VM do Azure com o PowerShell
description: "Passo pelo passo as instruções para configurar uma identidade de serviço geridas (MSI) na VM do Azure, utilizando o PowerShell."
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
ms.openlocfilehash: c166d5d4e0ae054e89eb3a5728f1d86f4e008c12
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurar uma VM geridos serviço de identidade (MSI) com o PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identidade de serviço geridas fornece serviços do Azure com uma identidade gerido automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter de credenciais no seu código. 

Neste artigo, irá aprender a ativar e remover MSI para uma VM do Azure, utilizando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Além disso, instalar [Azure PowerShell versão 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) se ainda não o fez.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Ativar MSI durante a criação de uma VM do Azure

Para criar uma VM ativada de MSI:

1. Consulte um do seguinte VM inícios rápidos do Azure, concluir apenas as secções necessárias ("Iniciar sessão no Azure", "Criar grupo de recursos", "Criar grupo de rede", "Criar a VM"). 

   > [!IMPORTANT] 
   > Quando chegar à secção "Criar a VM", efetuar uma pequena modificação para a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) sintaxe de cmdlet. Certifique-se de adicionar um `-IdentityType "SystemAssigned"` parâmetro para aprovisionar a VM com um MSI, por exemplo:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Criar uma máquina virtual do Windows com o PowerShell](../virtual-machines/windows/quick-create-powershell.md)
   - [Criar uma máquina virtual de Linux através do PowerShell](../virtual-machines/linux/quick-create-powershell.md)



2. Adicionar a extensão de VM de MSI com o `-Type` parâmetro no [conjunto AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nome-la utilizando o `-Name` parâmetro. O `-Settings` parâmetro especifica a porta utilizada pelo ponto final token OAuth de aquisição de token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Ativar MSI numa VM do Azure existente

Se precisar de ativar MSI numa máquina Virtual existente:

1. Inicie sessão no Azure com `Login-AzureRmAccount`. Utilize uma conta que está associada à subscrição do Azure que contém a VM. Certifique-se a sua conta pertencer a uma função que dá-lhe também permissões de escrita na VM, tais como "Contribuinte de Máquina Virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Obtenha primeiro as propriedades da VM utilizando o `Get-AzureRmVM` cmdlet. Em seguida, para ativar o MSI, utilize o `-IdentityType` comutador no [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Adicionar a extensão de VM de MSI com o `-Type` parâmetro no [conjunto AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nome-la utilizando o `-Name` parâmetro. O `-Settings` parâmetro especifica a porta utilizada pelo ponto final token OAuth para aquisição do token. É necessário especificar o correto `-Location` parâmetro correspondente a localização da VM existente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Remover MSI a partir de uma VM do Azure

Se tiver uma Máquina Virtual que já não necessita de um MSI, pode utilizar o `RemoveAzureRmVMExtension` cmdlet para remover o MSI da VM:

1. Inicie sessão no Azure com `Login-AzureRmAccount`. Utilize uma conta que está associada à subscrição do Azure que contém a VM. Certifique-se a sua conta pertencer a uma função que dá-lhe também permissões de escrita na VM, tais como "Contribuinte de Máquina Virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Utilize o `-Name` mudar com o [remover AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet, especificando o mesmo nome que utilizou quando adicionou a extensão:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Conteúdo relacionado

- [Descrição geral de identidade de serviço gerida](msi-overview.md)
- Para a criação de VM do Azure completa inícios rápidos, consulte:
  
  - [Criar uma máquina virtual do Windows com o PowerShell](../virtual-machines/windows/quick-create-powershell.md) 
  - [Criar uma máquina virtual Linux com o PowerShell](../virtual-machines/linux/quick-create-powershell.md) 

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.
















