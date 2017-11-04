---
title: Script do PowerShell do Azure de exemplo - criar uma VM ao anexar um disco como disco de SO gerido | Microsoft Docs
description: Script do PowerShell do Azure de exemplo - criar uma VM ao anexar um disco gerido como disco do SO
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ec532811e94647c8a04b9faf9474f6749969f83e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Criar uma máquina virtual utilizando um disco de SO existente gerido com o PowerShell

Este script cria uma máquina virtual, anexar um disco existente gerido como disco do SO. Utilize este script no precedente cenários:
* Criar uma VM a partir de um disco de SO gerido existente que foi copiado a partir de um disco gerido numa subscrição diferente
* Criar uma VM a partir de um disco gerido existente criada a partir do ficheiro VHD especializado 
* Criar uma VM a partir de um disco de SO gerido existente que foi criado a partir de um instantâneo 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para obter propriedades de disco gerido, anexar um disco gerido para uma nova VM e criar uma VM. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmDisk](/powershell/module/azurerm.compute/Get-AzureRmDisk) | Obtém o objeto de disco com base no nome e o grupo de recursos de um disco. Propriedade de ID do objeto do disco devolvido é utilizada para anexar o disco para uma nova VM |
| [Novo AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Cria uma configuração de VM. Esta configuração inclui informações como o nome VM, sistema operativo e as credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [Conjunto AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Anexa um disco gerido utilizando a propriedade de Id do disco como disco de SO para uma nova máquina virtual |
| [Novo AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Cria um endereço IP público. |
| [Novo AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Cria uma interface de rede. |
| [Novo-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Crie uma máquina virtual. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Remove um grupo de recursos e todos os recursos contidos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de máquina virtual adicional que podem ser encontrados no [documentação do Azure Windows VM](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).