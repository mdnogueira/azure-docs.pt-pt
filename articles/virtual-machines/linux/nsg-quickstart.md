---
title: Abrir portas para uma VM com Linux com o Azure CLI 2.0 | Microsoft Docs
description: "Saiba como abrir uma porta / criar um ponto final para a VM com Linux utilizando o modelo de implementação do Gestor de recursos do Azure e o 2.0 CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/21/2017
ms.author: iainfou
ms.openlocfilehash: d176187fe465264b5f433260de5178b48ca9dd4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Abrir portas e os pontos finais para uma VM com Linux com a CLI do Azure
Abrir uma porta ou criar um ponto final, para uma máquina virtual (VM) no Azure através da criação de um filtro de rede numa sub-rede ou interface de rede VM. Colocar estes filtros que controlam o tráfego de entrada e saído, num grupo de segurança de rede ligado para o recurso que recebe o tráfego. Vamos utilizar um exemplo comum de tráfego da web na porta 80. Este artigo mostra como abrir uma porta para uma VM com o 2.0 CLI do Azure. Também pode efetuar estes passos com a [CLI 1.0 do Azure](nsg-quickstart-nodejs.md).


## <a name="quick-commands"></a>Comandos rápidos
Para criar um grupo de segurança de rede e regras que tem a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem *myResourceGroup*, *myNetworkSecurityGroup*, e *myVnet*.

Criar o grupo de segurança de rede com [az rede nsg criar](/cli/azure/network/nsg#create). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup* no *eastus* localização:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adicionar uma regra com [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#create) para permitir tráfego HTTP para o seu servidor Web (ou ajustar o seus próprios cenário, tais como a conectividade de acesso ou a base de dados SSH). O exemplo seguinte cria uma regra com o nome *myNetworkSecurityGroupRule* para permitir tráfego TCP na porta 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```

Associar o grupo de segurança de rede com a interface de rede da VM (NIC) [atualização de nic de rede az](/cli/azure/network/nic#update). O exemplo seguinte associa uma NIC existente com o nome *myNic* com o grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Em alternativa, pode associar o seu grupo de segurança de rede com uma sub-rede de rede virtual com [atualização de sub-rede da vnet de rede de az](/cli/azure/network/vnet/subnet#update) , em vez de apenas para a interface de rede numa VM única. O exemplo seguinte associa uma sub-rede existente com o nome *mySubnet* no *myVnet* rede virtual com o grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem-lhe começar a trabalhar com tráfego que flui para a VM. Grupos de segurança de rede fornecem várias funcionalidades excelentes e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre [criar um grupo de segurança de rede e a ACL regras aqui](tutorial-virtual-network.md#secure-network-traffic).

Para aplicações web de elevada disponibilidade, deve colocar as VMs por trás de um balanceador de carga do Azure. O Balanceador de carga distribui o tráfego para VMs, com um grupo de segurança de rede que fornece a filtragem de tráfego. Para obter mais informações, consulte [como carregar equilibrar os computadores virtuais Linux no Azure para criar uma aplicação altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passos seguintes
Neste exemplo, criou uma regra para permitir tráfego HTTP simples. Pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

* [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../../virtual-network/virtual-networks-nsg.md)
