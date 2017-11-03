---
title: "Criar uma VM com Linux no Azure com vários NICs | Microsoft Docs"
description: "Saiba como criar uma VM com Linux com vários NICs anexados ao mesmo utilizando os modelos do Azure CLI ou o Gestor de recursos."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 93a32ae7ec0cf73825791e8c8bc3d388cf999ece
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="create-a-linux-virtual-machine-with-multiple-nics-using-the-azure-cli-10"></a>Criar uma máquina virtual Linux com vários NICs com a CLI do Azure 1.0
Pode criar uma máquina virtual (VM) no Azure com várias interfaces de rede virtual (NICs) ligadas ao mesmo. Um cenário comum é ter diferentes sub-redes para a conectividade de front-end e back-end ou uma rede dedicada para uma solução de monitorização ou cópia de segurança. Este artigo fornece comandos rápidos para criar uma VM com vários NICs anexados ao mesmo. Diferentes [tamanhos de VM](sizes.md) suportar um número de NICs variando, por isso, tamanho da VM em conformidade.

> [!WARNING]
> Terá de ligar vários NICs quando criar uma VM - não é possível adicionar NICs para uma VM existente com a CLI do Azure 1.0. Pode [adicionar NICs a uma VM existente com o 2.0 CLI do Azure](multiple-nics.md). Também pode [criar uma VM com base nos discos virtuais originais](copy-vm.md) e criar vários NICs como implementar a VM.


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#create-supporting-resources) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](multiple-nics.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="create-supporting-resources"></a>Criar recursos de suporte
Certifique-se de que tem o [CLI do Azure](../../cli-install-nodejs.md) a sessão e utilizar o modo Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes de parâmetros de exemplo incluídos *myResourceGroup*, *mystorageaccount*, e *myVM*.

Em primeiro lugar, crie um grupo de recursos. O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização:

```azurecli
azure group create myResourceGroup --location eastus
```

Crie uma conta de armazenamento para armazenar as suas VMs. O exemplo seguinte cria uma conta de armazenamento com o nome *mystorageaccount*:

```azurecli
azure storage account create mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --kind Storage \
    --sku-name PLRS
```

Crie uma rede virtual para ligar as VMs para. O exemplo seguinte cria uma rede virtual denominada *myVnet* com um prefixo de endereço de *192.168.0.0/16*:

```azurecli
azure network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefixes 192.168.0.0/16
```

Criar duas sub-redes da rede virtual - uma para o tráfego de front-end e outra para o tráfego de back-end. O exemplo seguinte cria duas sub-redes, com o nome *mySubnetFrontEnd* e *mySubnetBackEnd*:

```azurecli
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetFrontEnd \
    --address-prefix 192.168.1.0/24
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar vários NICs
Pode ler mais detalhes sobre [implementar vários NICs com a CLI do Azure](../../virtual-machines/linux/multiple-nics.md), incluindo o processo de ciclo através de criar todos os NICs de processamento de scripts.

O exemplo seguinte cria dois NICs, com o nome *myNic1* e *myNic2*, com um NIC a ligação a cada sub-rede:

```azurecli
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic1 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetFrontEnd
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic2 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetBackEnd
```

Normalmente, também cria um [grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md) ou [Balanceador de carga](../../load-balancer/load-balancer-overview.md) para ajudar a gerir e distribuir o tráfego entre as suas VMs. O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Os NICs de enlace para o grupo de segurança de rede utilizando `azure network nic set`. O exemplo seguinte vincula *myNic1* e *myNic2* com *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Criar uma VM e anexe os NICs
Ao criar a VM, agora especificar vários NICs. Em vez de utilizar `--nic-name` para fornecer um único NIC, em alternativa, utilize `--nic-names` e fornecer uma lista separada por vírgulas de NICs. Terá também de tenha cuidado quando selecionar o tamanho da VM. Existem limites para o número total de NICs que pode adicionar a uma VM. Leia mais sobre [tamanhos de VM com Linux](sizes.md). O exemplo seguinte mostra como especificar vários NICs e, em seguida, um tamanho VM que suporta a utilização de vários NICs (*Standard_DS2_v2*):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Criar vários NICs com modelos do Resource Manager
Modelos Azure Resource Manager utilizam declarativos ficheiros JSON para definir o seu ambiente. Pode ler um [descrição geral do Gestor de recursos do Azure](../../azure-resource-manager/resource-group-overview.md). Modelos do Resource Manager fornecem uma forma de criar várias instâncias de um recurso durante a implementação, tais como criar vários NICs. Utilizar *cópia* para especificar o número de instâncias para criar:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre [criar várias instâncias utilizando *cópia*](../../resource-group-create-multiple.md). 

Também pode utilizar um `copyIndex()` a em seguida, anexe um número para um nome de recurso, o que permite-lhe criar `myNic1`, `myNic2`, etc. O seguinte mostra um exemplo de acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pode ler um exemplo completo de [criar vários NICs com modelos do Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Passos seguintes
Certifique-se de que revê [tamanhos de VM com Linux](sizes.md) ao tentar criar uma VM com vários NICs. Preste atenção para o número máximo de NICs suporta cada tamanho da VM. 

Lembre-se de que não é possível adicionar NICs adicionais para uma VM existente, tem de criar todos os NICs quando implementar a VM. Tenha cuidado quando planear as implementações para se certificar de que tem todos os a conectividade de rede necessários proposto.

