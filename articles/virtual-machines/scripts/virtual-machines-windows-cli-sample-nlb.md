---
title: CLI do Azure Script de exemplo - criar uma VM do Windows Server 2016 com NLB | Microsoft Docs
description: CLI do Azure Script de exemplo - criar uma VM do Windows Server 2016 com NLB
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: 9662e9a2ea128b609b76edfc4ec7b57016909761
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Balanceamento de carga do tráfego entre máquinas virtuais altamente disponíveis

Este script de exemplo cria tudo necessárias para executar várias máquinas de virtuais Ubuntu configuradas numa elevada disponibilidade e a configuração com balanceamento de carga. Depois de executar o script, terá três máquinas virtuais, associadas a um Azure do conjunto de disponibilidade e é acessível através de um balanceador de carga do Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-windows-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, máquina virtual, conjunto de disponibilidade, o Balanceador de carga e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ rede vnet](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede virtual do Azure e a sub-rede. |
| [Criar AZ público-ip da rede](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [Criar AZ lb de rede](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Cria um balanceador de carga de rede do Azure (NLB). |
| [criar a sonda de lb AZ rede](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Cria uma pesquisa NLB. Uma pesquisa NLB é utilizada para monitorizar cada VM no conjunto de NLB. Se qualquer VM torna-se inacessível, o tráfego não é encaminhado para a VM. |
| [Criar regra de lb AZ de rede](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria uma regra NLB. Neste exemplo, é criada uma regra para a porta 80. Como o tráfego HTTP chega o NLB, é encaminhado para a porta 80 uma das VMs no conjunto de NLB. |
| [Criar AZ rede lb-nat-regras de entrada](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Cria uma regra de tradução de endereços de rede (NAT) do NLB.  As regras NAT de mapeiam uma porta do NLB para uma porta numa VM. Neste exemplo, é criada uma regra NAT para o tráfego SSH para cada VM no conjunto de NLB.  |
| [Criar AZ rede nsg](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a internet e a máquina virtual. |
| [criar regras de nsg AZ rede](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Cria uma regra NSG para permitir tráfego de entrada. Neste exemplo, a porta 22 é aberta para tráfego SSH. |
| [Criar AZ nic da rede](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Cria uma placa de rede virtual e anexa-lo para a rede virtual, uma sub-rede e um NSG. |
| [criar a vm AZ conjunto de disponibilidade](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria um conjunto de disponibilidade. Conjuntos de disponibilidade garantir a disponibilidade de aplicações, propagando-se as máquinas virtuais em recursos físicos, de modo a que o se ocorrer uma falha, o conjunto completo não é afetado. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Cria a máquina virtual e liga-o para a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser credenciais administrativas e utilizadas.  |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação do Azure Windows VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
