---
title: "Script do PowerShell do Azure de exemplo - criar uma rede para aplicações de várias camadas | Microsoft Docs"
description: "Script do PowerShell do Azure de exemplo - criar uma rede virtual para aplicações de várias camadas."
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: ab49e78ef17b093d2bbe4e3276a1ece3a4247f91
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-network-for-multi-tier-applications"></a>Criar uma rede para aplicações de várias camadas

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end é limitado para HTTP e SSH, enquanto o tráfego para a sub-rede de back-end está limitado a MySQL, porta 3306. Depois de executar o script, tem duas máquinas virtuais, um em cada sub-rede que pode implementar MySQL software e do servidor web.

Se necessário, instale o Azure PowerShell com a instrução foi encontrada no [Guia do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)e, em seguida, execute `Login-AzureRmAccount` para criar uma ligação com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-powershell[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e os grupos de segurança de rede. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Novo-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Cria uma rede virtual do Azure e a sub-rede do front-end. |
| [Novo AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Cria uma sub-rede de back-end. |
| [Novo AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Cria um endereço IP público para aceder a VM a partir da Internet. |
| [Novo AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Cria interfaces de rede virtual e anexa-las a sub-redes de front-end e back-end à rede virtual. |
| [Novo AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Cria grupos de segurança (NSG) que estão associados às sub-redes de front-end e back-end de rede. |
| [Novo AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) |Cria regras do NSG que permitem ou bloquear portas específicas para sub-redes específicas. |
| [Novo-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Cria máquinas virtuais e anexa um NIC para cada VM. Este comando também especifica a imagem de máquina virtual para utilizar e credenciais administrativas. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina um grupo de recursos e todos os recursos que nele contidos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Exemplos de script do PowerShell redes adicionais podem ser encontrados no [documentação de descrição geral de funcionamento em rede do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).