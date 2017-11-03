---
title: "Exemplo de Script do CLI do Azure - balanceamento de carga de vários sites com a CLI do Azure | Microsoft Docs"
description: "Exemplo de Script do CLI do Azure - balanceamento de carga de vários sites na mesma máquina virtual"
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
ms.openlocfilehash: 98b07bfabf2d01c7ae3db7365cfbab3639c6f026
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-balance-multiple-websites"></a>Balanceamento de carga de vários sites

Este script de exemplo cria uma rede virtual com duas máquinas virtuais (VM) que são membros de um conjunto de disponibilidade. Um balanceador de carga direciona o tráfego de dois endereços IP separados para as duas VMs. Depois de executar o script, pode implementar o software de servidor web para as VMs e anfitrião vários web sites, cada um com o seu próprio endereço IP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, rede virtual, o Balanceador de carga e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ rede vnet](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede virtual do Azure e a sub-rede. |
| [Criar AZ público-ip da rede](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [Criar AZ lb de rede](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Cria um balanceador de carga do Azure. |
| [criar a sonda de lb AZ rede](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Cria uma sonda do Balanceador de carga. Uma sonda do Balanceador de carga é utilizada para monitorizar cada VM no conjunto de Balanceador de carga. Se qualquer VM torna-se inacessível, o tráfego não é encaminhado para a VM. |
| [Criar regra de lb AZ de rede](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria uma regra de Balanceador de carga. Neste exemplo, é criada uma regra para a porta 80. Como o tráfego HTTP chega ao balanceador de carga, é encaminhado para a porta 80 uma das VMs no conjunto de Balanceador de carga. |
| [Criar AZ rede lb front-end-ip](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Crie um endereço IP de front-end de Balanceador de carga. |
| [Criar AZ rede lb-conjunto de endereços](https://docs.microsoft.com/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Cria um conjunto de endereços de back-end. |
| [Criar AZ nic da rede](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Cria uma placa de rede virtual e anexa-lo para a rede virtual e a sub-rede. |
| [criar a vm AZ conjunto de disponibilidade](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria um conjunto de disponibilidade. Conjuntos de disponibilidade garantir a disponibilidade de aplicações, propagando-se as máquinas virtuais em recursos físicos, de modo a que o se ocorrer uma falha, o conjunto completo não é afetado. |
| [Criar nic de rede AZ ip-config](https://docs.microsoft.com/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Cria um confiuration IP. Tem de ter a funcionalidade de Microsoft.Network/AllowMultipleIpConfigurationsPerNic ativada para a sua subscrição. Apenas uma configuração pode ser designada como a configuração de IP primária por NIC, utilizando o – sinalizador disponibilizar principal. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Cria a máquina virtual e liga-o para a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser credenciais administrativas e utilizadas.  |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI redes adicionais podem ser encontrados no [documentação de descrição geral de funcionamento em rede do Azure](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
