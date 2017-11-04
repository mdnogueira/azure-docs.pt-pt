---
title: "Exemplo de Script do PowerShell do Azure - instantâneo de cópia (mover) de um disco gerido à subscrição idêntica ou diferente | Microsoft Docs"
description: "Exemplo de Script do PowerShell do Azure - instantâneo de cópia (mover) de um disco gerido à subscrição idêntica ou diferente"
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 064b5355da10fe683563fa078cfafc65080f7ea2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Instantâneo de cópia de um disco gerido na mesma subscrição ou outra subscrição com o PowerShell

Este script cria uma cópia de um instantâneo na mesma subscrição mesma ou uma subscrição diferente. Utilize este script para mover um instantâneo para uma subscrição diferente para a retenção de dados. Armazenar instantâneos numa subscrição diferente protegê-lo contra eliminação acidental de instantâneos na sua subscrição principal. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um instantâneo na subscrição de destino com o Id do instantâneo de origem. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | Cria a configuração de instantâneos que é utilizada para criação de instantâneos. Inclui o Id do instantâneo de principal e a localização que seja a mesma que o instantâneo de principal do recurso.  |
| [Novo AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | Cria um instantâneo, utilizando a configuração de instantâneos, o nome de instantâneo e o nome do grupo de recursos transmitido como parâmetros. |


## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um instantâneo](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de máquina virtual adicional que podem ser encontrados no [documentação do Azure Windows VM](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).