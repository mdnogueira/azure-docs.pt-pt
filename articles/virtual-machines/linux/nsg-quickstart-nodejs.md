---
title: Abrir portas para uma VM com Linux com a CLI do Azure 1.0 | Microsoft Docs
description: "Saiba como abrir uma porta / criar um ponto final para a VM com Linux utilizando o modelo de implementação do Gestor de recursos do Azure e a CLI do Azure 1.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 847bc76c37ed929851712ba1c12463a01032e267
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Abrir portas e os pontos finais para uma VM com Linux no Azure utilizando a CLI do Azure 1.0
Abrir uma porta ou criar um ponto final, para uma máquina virtual (VM) no Azure através da criação de um filtro de rede numa sub-rede ou interface de rede VM. Colocar estes filtros que controlam o tráfego de entrada e saído, num grupo de segurança de rede ligado para o recurso que recebe o tráfego. Vamos utilizar um exemplo comum de tráfego da web na porta 80. Este artigo mostra-lhe como abrir uma porta para uma VM com a CLI do Azure 1.0.


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#quick-commands) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](nsg-quickstart.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="quick-commands"></a>Comandos rápidos
Para criar um grupo de segurança de rede e regras necessárias [a CLI do Azure 1.0](../../cli-install-nodejs.md) instalado e o modo Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes de parâmetros de exemplo incluídos *myResourceGroup*, *myNetworkSecurityGroup*, e *myVnet*.

Crie o grupo de segurança de rede, introduzir os seus próprios nomes e a localização correta. O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup* no *eastus* localização:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adicione uma regra para permitir tráfego HTTP para o seu servidor Web (ou ajustar o seus próprios cenário, tais como a conectividade de acesso ou a base de dados SSH). O exemplo seguinte cria uma regra com o nome *myNetworkSecurityGroupRule* para permitir tráfego TCP na porta 80:

```azurecli
azure network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow
```

Associe o grupo de segurança de rede a interface de rede da VM (NIC). O exemplo seguinte associa uma NIC existente com o nome *myNic* com o grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --name myNic
```

Em alternativa, pode associar o grupo de segurança de rede com uma sub-rede de rede virtual, em vez de apenas para a interface de rede numa VM única. O exemplo seguinte associa uma sub-rede existente com o nome *mySubnet* no *myVnet* rede virtual com o grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
azure network vnet subnet set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem-lhe começar a trabalhar com tráfego que flui para a VM. Grupos de segurança de rede fornecem várias funcionalidades excelentes e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre [criar um grupo de segurança de rede e a ACL regras aqui](../../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Pode definir grupos de segurança de rede e as regras de ACL como parte dos modelos Azure Resource Manager. Leia mais sobre [criar grupos de segurança de rede com modelos](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar de utilizar o reencaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna na sua VM, utilize um balanceador de carga e regras de tradução de endereços de rede (NAT). Por exemplo, poderá expor TCP porta 8080 externamente e tiver tráfego direcionado para a porta TCP 80 numa VM. Pode saber mais sobre [criação de um balanceador de carga para a Internet](../../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Passos seguintes
Neste exemplo, criou uma regra para permitir tráfego HTTP simples. Pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

* [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../../virtual-network/virtual-networks-nsg.md)
* [Descrição geral do Gestor de recursos do Azure para balanceadores de carga](../../load-balancer/load-balancer-arm.md)

