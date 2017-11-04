---
title: "Exemplo de script CLI do Azure - tráfego de rede de VM de filtro | Microsoft Docs"
description: "Script CLI do Azure de exemplo - filtrar o tráfego de rede VM de entrada e saído."
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
ms.openlocfilehash: 68ee013cff4e0be15af30239e0314f779f50177a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Filtrar o tráfego de rede VM de entrada e saído

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. Tráfego de rede de entrada para a sub-rede do front-end está limitado a HTTP, HTTPS e SSH, enquanto o tráfego de saída à Internet da sub-rede de back-end não é permitido. Depois de executar o script, terá de uma máquina virtual com dois NICs. Cada NIC está ligado a outra sub-rede.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

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
| [criar a sub-rede de rede AZ](/cli/azure/network/vnet/subnet#create) | Cria uma sub-rede de back-end. |
| [atualização de sub-rede da vnet de rede de AZ](/cli/azure/network/vnet/subnet#update) | Associa os NSGs a sub-redes. |
| [Criar AZ público-ip da rede](/cli/azure/network/public-ip#create) | Cria um endereço IP público para aceder a VM a partir da Internet. |
| [Criar AZ nic da rede](/cli/azure/network/nic#create) | Cria interfaces de rede virtual e anexa-las a sub-redes de front-end e back-end à rede virtual. |
| [Criar AZ rede nsg](/cli/azure/network/nsg#create) | Cria grupos de segurança (NSG) que estão associados às sub-redes de front-end e back-end de rede. |
| [criar regras de nsg AZ rede](/cli/azure/network/nsg/rule#create) |Cria regras do NSG que permitem ou bloquear portas específicas para sub-redes específicas. |
| [Criar AZ vm](/cli/azure/vm#create) | Cria máquinas virtuais e anexa um NIC para cada VM. Este comando também especifica a imagem de máquina virtual para utilizar e credenciais administrativas. |
| [eliminação do grupo de AZ](/cli/azure/group#delete) | Elimina um grupo de recursos e todos os recursos que nele contidos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](/cli/azure/overview).

Exemplos de script CLI redes adicionais podem ser encontrados no [documentação de descrição geral de funcionamento em rede do Azure](../cli-samples.md)