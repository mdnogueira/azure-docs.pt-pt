---
title: "Exemplo de Script do PowerShell do Azure - carga equilibrar o tráfego para VMs de elevada disponibilidade | Microsoft Docs"
description: "Exemplo de Script do PowerShell do Azure - carga equilibrar o tráfego para VMs de elevada disponibilidade"
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: c77def8906b151f2cc6e4bbc4188be8ecbeac732
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Tráfego de balanceamento de carga para as VMs para elevada disponibilidade

Este script de exemplo cria tudo necessárias para executar várias máquinas virtuais do Windows que estão configuradas no altamente disponível e a configuração com balanceamento de carga. Depois de executar o script, terá três máquinas virtuais, associadas a um Azure do conjunto de disponibilidade e é acessível através de um balanceador de carga do Azure.

Se necessário, instale o Azure PowerShell com a instrução foi encontrada no [Guia do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)e, em seguida, execute `Login-AzureRmAccount` para criar uma ligação com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, máquina virtual, conjunto de disponibilidade, o Balanceador de carga e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Novo AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Esta configuração é utilizada com o processo de criação de rede virtual. |
| [Novo-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Cria uma rede virtual do Azure e a sub-rede. |
| [Novo AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)  | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [Novo AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer)  | Cria um balanceador de carga do Azure. |
| [Novo AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Cria uma sonda do Balanceador de carga. Uma sonda do Balanceador de carga é utilizada para monitorizar cada VM no conjunto de Balanceador de carga. Se qualquer VM torna-se inacessível, o tráfego não é encaminhado para a VM. |
| [Novo AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Cria uma regra de Balanceador de carga. Neste exemplo, é criada uma regra para a porta 80. Como o tráfego HTTP chega ao balanceador de carga, é encaminhado para a porta 80 uma das VMs no conjunto de Balanceador de carga. |
| [Novo AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Cria uma regra de tradução de endereços de rede (NAT) do Balanceador de carga.  As regras NAT de mapeiam uma porta de Balanceador de carga para uma porta numa VM. Neste exemplo, é criada uma regra NAT para o tráfego SSH para cada VM no conjunto de Balanceador de carga.  |
| [Novo AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a internet e a máquina virtual. |
| [Novo AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Cria uma regra NSG para permitir tráfego de entrada. Neste exemplo, a porta 22 é aberta para tráfego SSH. |
| [Novo AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Cria uma placa de rede virtual e anexa-lo para a rede virtual, uma sub-rede e um NSG. |
| [Novo AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Cria um conjunto de disponibilidade. Conjuntos de disponibilidade garantir a disponibilidade de aplicações, propagando-se as máquinas virtuais em recursos físicos, de modo a que o se ocorrer uma falha, o conjunto completo não é afetado. |
| [Novo AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Cria uma configuração de VM. Esta configuração inclui informações como o nome VM, sistema operativo e as credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [Novo-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)  | Cria a máquina virtual e liga-o para a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser credenciais administrativas e utilizadas.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Exemplos de script do PowerShell redes adicionais podem ser encontrados no [documentação de descrição geral de funcionamento em rede do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
