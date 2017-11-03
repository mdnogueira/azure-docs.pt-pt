---
title: CLI do Azure Script de exemplo - implementar a pilha LAMP num conjunto de dimensionamento com balanceamento de carga Machin de Virutal | Microsoft Docs
description: "Utilizar uma extensão de script personalizado para implementar a pilha de LAMP numa carga = dimensionamento da máquina de virtual com balanceamento definida no Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 23170923d7c05c9b7230cf331725250b2a3c0f09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Implementar a pilha LAMP num conjunto de dimensionamento da máquina de virtual com balanceamento de carga

Este exemplo cria um conjunto de dimensionamento de máquina virtual e aplica-se de uma extensão que executa um script personalizado para implementar a pilha LAMP em cada máquina virtual no conjunto de dimensionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Ligar

Utilize este código para ver como ligar as VMs e a escala definido.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, o conjunto de dimensionamento e VMs e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, máquina virtual, conjunto de disponibilidade, o Balanceador de carga e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ vmss](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Cria um conjunto de dimensionamento de máquina virtual |
| [Criar regra de lb AZ de rede](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Adicionar um ponto final com balanceamento de carga |
| [conjunto de extensão de vmss AZ](https://docs.microsoft.com/cli/azure/vmss/extension#az_vmss_extension_set) | Criar a extensão que executa o script personalizado na implementação de uma VM |
| [instâncias de atualização do vmss AZ](https://docs.microsoft.com/cli/azure/vmss#az_vmss_update_instances) | Execute o script personalizado nas instâncias de VM que foram implementadas antes da extensão foi aplicada ao conjunto de dimensionamento. |
| [escala de vmss AZ](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) | Aumentar verticalmente a escala definida adicionando mais instâncias VM. O script personalizado é executado nestes quando estão implementadas. |
| [lista de ip público de rede AZ](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list) | Obter os endereços IP das VMs criados por exemplo. |
| [Mostrar de lb AZ rede](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_show) | Obter o front-end e back-end portas utilizadas pelo balanceador de carga. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação de VM do Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
