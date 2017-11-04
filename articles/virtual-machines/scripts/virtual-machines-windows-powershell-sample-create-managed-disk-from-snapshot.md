---
title: "Script do PowerShell do Azure de exemplo - criar um disco gerido a partir de um instantâneo | Microsoft Docs"
description: "Script do PowerShell do Azure de exemplo - criar um disco gerido a partir de um instantâneo"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 39ddb49771a55af55949b75623c2d4755b625208
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Criar um disco gerido a partir de um instantâneo com o PowerShell

Este script cria um disco gerido a partir de um instantâneo. Utilizá-la para restaurar uma máquina virtual de instantâneos de discos de dados e SO. Crie o SO e discos geridos dos respetivos instantâneos de dados e, em seguida, criar uma nova máquina virtual ligando discos geridos. Também pode restaurar os discos de dados de uma VM existente ao anexar a criação de instantâneos de discos de dados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial, necessita que é o Azure PowerShell versão 4.0 ou posterior do módulo. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, consulte [instalar o Azure PowerShell](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um instantâneo. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/Get-AzureRmSnapshot) | Obtém as propriedades de instantâneos.  |
| [Novo AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Cria a configuração de disco que é utilizada para a criação do disco. Inclui o Id do instantâneo principal, localização que seja a mesma que a localização dos instantâneos de principal e o tipo de armazenamento do recurso.  |
| [Novo AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Cria um disco com a configuração do disco, o nome do disco e o nome do grupo de recursos transmitido como parâmetros. |


## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de máquina virtual adicional que podem ser encontrados no [documentação do Azure Windows VM](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).