---
title: "Exemplo de Script CLI do Azure - carga equilibrar o tráfego para VMs de elevada disponibilidade | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - carga equilibrar o tráfego para VMs de elevada disponibilidade"
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: b04e9b22fa35f6202bc3fee547519b679f439a3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Tráfego de balanceamento de carga para as VMs para elevada disponibilidade

Este script de exemplo cria tudo necessárias para executar várias máquinas de virtuais Ubuntu configuradas numa elevada disponibilidade e a configuração com balanceamento de carga. Depois de executar o script, terá três máquinas virtuais, associadas a um Azure do conjunto de disponibilidade e é acessível através de um balanceador de carga do Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, máquina virtual, conjunto de disponibilidade, o Balanceador de carga e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ rede vnet](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede virtual do Azure e a sub-rede. |
| [Criar AZ público-ip da rede](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [Criar AZ lb de rede](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Cria um balanceador de carga do Azure. |
| [criar a sonda de lb AZ rede](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Cria uma sonda do Balanceador de carga. Uma sonda do Balanceador de carga é utilizada para monitorizar cada VM no conjunto de Balanceador de carga. Se qualquer VM torna-se inacessível, o tráfego não é encaminhado para a VM. |
| [Criar regra de lb AZ de rede](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria uma regra de Balanceador de carga. Neste exemplo, é criada uma regra para a porta 80. Como o tráfego HTTP chega ao balanceador de carga, é encaminhado para a porta 80 uma das VMs no conjunto LB. |
| [Criar AZ rede lb-nat-regras de entrada](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Cria a regra de tradução de endereços de rede (NAT) do Balanceador de carga.  As regras NAT de mapeiam uma porta de Balanceador de carga para uma porta numa VM. Neste exemplo, é criada uma regra NAT para o tráfego SSH para cada VM no conjunto de Balanceador de carga.  |
| [Criar AZ rede nsg](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a internet e a máquina virtual. |
| [criar regras de nsg AZ rede](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Cria uma regra NSG para permitir tráfego de entrada. Neste exemplo, a porta 22 é aberta para tráfego SSH. |
| [Criar AZ nic da rede](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Cria uma placa de rede virtual e anexa-lo para a rede virtual, uma sub-rede e um NSG. |
| [criar a vm AZ conjunto de disponibilidade](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria um conjunto de disponibilidade. Conjuntos de disponibilidade garantir a disponibilidade de aplicações, propagando-se as máquinas virtuais em recursos físicos, de modo a que o se ocorrer uma falha, o conjunto completo não é afetado. |
| [Criar AZ vm](/cli/azure/vm#create) | Cria a máquina virtual e liga-o para a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser credenciais administrativas e utilizadas.  |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script a CLI de redes do Azure adicionais podem ser encontrados no [redes do Azure documentação](../cli-samples.md).