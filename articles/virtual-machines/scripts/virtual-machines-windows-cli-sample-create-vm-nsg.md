---
title: Script CLI do Azure de exemplo - criar duas VMs com um NSG interno e externo | Microsoft Docs
description: Script CLI do Azure de exemplo - criar duas VMs com NSG interno e externo
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: 84092e3a70f1bfde923ba3395fbc0a46c11e233e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Proteger o tráfego de rede entre as máquinas virtuais

Este script cria duas máquinas virtuais e protege o tráfego de entrada para ambos. Uma máquina virtual está acessível na internet e tiver um grupo de segurança de rede (NSG) configurado para permitir o tráfego na porta 3389 e a porta 80. A segunda máquina virtual não está acessível na internet e um NSG foi configurado para permitir apenas o tráfego na primeira máquina virtual. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ rede vnet](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede virtual do Azure e a sub-rede. |
| [criar a sub-rede da vnet AZ rede](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Cria uma sub-rede. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Cria a máquina virtual e liga-o para a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser utilizada e credenciais administrativas.  |
| [actualização da regra AZ rede nsg](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_update) | Atualiza uma regra NSG. Neste exemplo, a regra de back-end é atualizada para passar o tráfego apenas a partir da sub-rede do front-end. |
| [lista de regras de nsg de rede AZ](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_list) | Devolve informações sobre uma regra de grupo de segurança de rede. Neste exemplo, o nome da regra é armazenado numa variável para utilização posterior no script. |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação do Azure Windows VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
