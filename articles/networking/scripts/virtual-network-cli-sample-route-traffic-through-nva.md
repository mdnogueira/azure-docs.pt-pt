---
title: "Exemplo de script CLI do Azure - encaminhar o tráfego através de uma aplicação virtual de rede | Microsoft Docs"
description: "Azure CLI script de exemplo - encaminhar o tráfego através de uma aplicação virtual de rede de firewall."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 78091b515c00591a4af8d807945475b6be50188a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Encaminhar o tráfego através de uma aplicação virtual de rede

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. Também cria uma VM com o reencaminhamento IP está ativado para encaminhar o tráfego entre as duas sub-redes. Depois de executar o script pode implementar o software de rede, como uma aplicação de firewall, para a VM.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script de exemplo


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e os grupos de segurança de rede. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](/cli/azure/group#create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ rede vnet](/cli/azure/network/vnet#create) | Cria uma rede virtual do Azure e a sub-rede do front-end. |
| [criar a sub-rede de rede AZ](/cli/azure/network/vnet/subnet#create) | Cria o back-end e sub-redes da rede de Perímetro. |
| [Criar AZ público-ip da rede](/cli/azure/network/public-ip#create) | Cria um endereço IP público para aceder a VM a partir da Internet. |
| [Criar AZ nic da rede](/cli/azure/network/nic#create) | Cria uma interface de rede virtual e ativar o reencaminhamento de IP para o mesmo. |
| [Criar AZ rede nsg](/cli/azure/network/nsg#create) | Cria um grupo de segurança de rede (NSG). |
| [criar regras de nsg AZ rede](/cli/azure/network/nsg/rule#create) | Cria regras do NSG que permitem que as portas HTTP e HTTPS de entrada para a VM. |
| [atualização de sub-rede da vnet de rede de AZ](/cli/azure/network/vnet/subnet#update)| Associa os NSGs e tabelas de rotas para sub-redes. |
| [criar a tabela de rotas do rede AZ](/cli/azure/network/route-table#create)| Cria uma tabela de rota para todas as rotas. |
| [Criar rota de tabela de rotas de rede AZ](/cli/azure/network/route-table/route#create)| Cria rotas para encaminhar o tráfego entre sub-redes e a Internet através da VM. |
| [Criar AZ vm](/cli/azure/vm#create) | Cria uma máquina virtual e anexa a NIC ao mesmo. Este comando também especifica a imagem de máquina virtual para utilizar e credenciais administrativas. |
| [eliminação do grupo de AZ](/cli/azure/group#delete) | Elimina um grupo de recursos e todos os recursos que nele contidos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](/cli/azure/overview).

Exemplos de script CLI redes adicionais podem ser encontrados no [documentação de descrição geral de funcionamento em rede do Azure](../cli-samples.md)