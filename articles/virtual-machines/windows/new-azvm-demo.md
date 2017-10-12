---
title: Criar uma VM do Windows com o cmdlet New-AzVM no Azure Cloud Shell| Microsoft Docs
description: "Aprenda rapidamente a criar máquinas virtuais do Windows com o cmdlet New-AzVM no Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3be46c8c02ad136edb1936fbb39560d479b27277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Criar uma máquina virtual do Windows com o cmdlet New-AzVM (Pré-visualização) no Cloud Shell 

O cmdlet New-AzVM (Pré-visualização) é uma forma simplificada de criar uma nova VM com o PowerShell. Este guia fornece detalhes sobre como utilizar o PowerShell no Azure Cloud Shell, com o cmdlet New-AzVM pré-instalado, para criar uma nova máquina virtual do Azure com o Windows Server 2016. Depois de concluída a implementação, é feita a ligação ao servidor via RDP.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>Crie a VM

Pode utilizar o cmdlet **New-AzVM** para criar uma VM com predefinições inteligentes que incluem a utilização da imagem do Windows Server 2016 Datacenter do Azure Marketplace. Pode utilizar o cmdlet New-AzVM individualmente; este utilizará os valores predefinidos para os nomes dos recursos. Neste exemplo, vamos definir o parâmetro **-Name** como *myVM*. O cmdlet cria todos os recursos necessários utilizando *myVM* como prefixo para o nome do recurso. 

Certifique-se de que o **PowerShell** está selecionado no Cloud Shell e escreva:

```azurepowershell-interactive
New-AzVm -Name myVM
```

É-lhe pedido para criar um nome de utilizador e uma palavra-passe para a VM, que serão utilizados quando ligar à VM mais adiante neste tópico. A palavra-passe tem de ter entre 12 e 123 carateres e cumprir três dos quatro requisitos de complexidade que se seguem: um caráter em letra minúscula, um caráter em letra maiúscula, um número e um caráter especial.

Basta um minuto para criar a VM e os recursos associados. Quando tudo estiver terminado, poderá ver todos os recursos criados com o cmdlet [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Ligar à VM

Depois de a implementação estar concluída, crie uma ligação de ambiente de trabalho remoto à máquina virtual.

Utilize o comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver o endereço IP público da máquina virtual. Tome nota deste Endereço IP.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

No computador local, abra uma linha de comandos e utilize o comando **mstsc** para iniciar uma sessão de ambiente de trabalho remoto com a nova VM. Substitua &lt;publicIPAddress&gt; pelo endereço IP da máquina virtual. Quando lhe for pedido, introduza o nome de utilizador e a palavra-passe que atribuiu à VM quando esta foi criada.

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tópico, implementou uma máquina virtual simples com o cmdlet New-AzVM e, em seguida, ligou-a via RDP. Para saber mais sobre as máquinas virtuais do Azure, continue com o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](./tutorial-manage-vm.md)
