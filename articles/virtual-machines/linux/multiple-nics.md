---
title: "Criar uma VM com Linux no Azure com vários NICs | Microsoft Docs"
description: "Saiba como criar uma VM com Linux com vários NICs anexados ao mesmo utilizando os modelos do Azure CLI 2.0 ou do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 0c41388623b82421bd09f31fbc4b3769de758e4c
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Como criar uma máquina virtual Linux no Azure com a rede de várias placas de interface
Pode criar uma máquina virtual (VM) no Azure com várias interfaces de rede virtual (NICs) ligadas ao mesmo. Um cenário comum é ter diferentes sub-redes para a conectividade de front-end e back-end ou uma rede dedicada para uma solução de monitorização ou cópia de segurança. Este artigo fornece detalhes sobre como criar uma VM com vários NICs anexados e como adicionar ou remover NICs de VM existente. Diferentes [tamanhos de VM](sizes.md) suportar um número de NICs variando, por isso, tamanho da VM em conformidade.

Este artigo fornece detalhes sobre como criar uma VM com vários NICs com o 2.0 CLI do Azure. Também pode efetuar estes passos com a [CLI 1.0 do Azure](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Criar recursos de suporte
Instalar a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) e início de sessão para um Azure conta através de [início de sessão az](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes de parâmetros de exemplo incluídos *myResourceGroup*, *mystorageaccount*, e *myVM*.

Em primeiro lugar, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização:

```azurecli
az group create --name myResourceGroup --location eastus
```

Criar a rede virtual com [az rede vnet criar](/cli/azure/network/vnet#create). O exemplo seguinte cria uma rede virtual denominada *myVnet* e sub-rede designada *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Crie uma sub-rede para o tráfego de back-end com [az rede vnet sub-rede](/cli/azure/network/vnet/subnet#create). O exemplo seguinte cria uma sub-rede designada *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Criar um grupo de segurança de rede com [az rede nsg criar](/cli/azure/network/nsg#create). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar vários NICs
Criar dois NICs com [nic da rede az criar](/cli/azure/network/nic#create). O exemplo seguinte cria dois NICs, com o nome *myNic1* e *myNic2*, ligados do grupo de segurança de rede com um NIC a ligação a cada sub-rede:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Criar uma VM e anexe os NICs
Quando criar a VM, especifique os NICs que criou com `--nics`. Terá também de tenha cuidado quando selecionar o tamanho da VM. Existem limites para o número total de NICs que pode adicionar a uma VM. Leia mais sobre [tamanhos de VM com Linux](sizes.md). 

Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

## <a name="add-a-nic-to-a-vm"></a>Adicionar uma NIC para uma VM
Os passos anteriores, criados uma VM com vários NICs. Também pode adicionar NICs a uma VM existente com o 2.0 CLI do Azure. Diferentes [tamanhos de VM](sizes.md) suportar um número de NICs variando, por isso, tamanho da VM em conformidade. Se necessário, pode [redimensionar uma VM](change-vm-size.md).

Crie outra NIC com [nic da rede az criar](/cli/azure/network/nic#create). O exemplo seguinte cria um NIC com o nome *myNic3* ligada ao back-end sub-rede e de rede de grupo de segurança criado nos passos anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para adicionar uma NIC para uma VM existente, Desalocação da VM com [az vm desalocar](/cli/azure/vm#deallocate). O exemplo seguinte deallocates VM com o nome *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adicionar a NIC com [az vm nic adicionar](/cli/azure/vm/nic#add). O exemplo seguinte adiciona *myNic3* para *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Iniciar a VM com [início de vm az](/cli/azure/vm#start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

## <a name="remove-a-nic-from-a-vm"></a>Remover um NIC de uma VM
Para remover um NIC de VM existente, Desalocação da VM com [az vm desalocar](/cli/azure/vm#deallocate). O exemplo seguinte deallocates VM com o nome *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Remova a NIC com [az vm nic remover](/cli/azure/vm/nic#remove). O exemplo a seguir remove *myNic3* de *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Iniciar a VM com [início de vm az](/cli/azure/vm#start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
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


## <a name="configure-guest-os-for-multiple-nics"></a>Configurar o SO convidado para vários NICs
Quando adicionar vários NICs para uma VM com Linux, terá de criar regras de encaminhamento. Estas regras permitir que a VM enviar e receber tráfego, que pertence a um NIC específico. Caso contrário, tráfego que pertença a *eth1*, não pode ser processada por exemplo, corretamente, a rota predefinida definida.

Para corrigir este problema de encaminhamento, adicione duas tabelas de encaminhamento para */etc/iproute2/rt_tables* da seguinte forma:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Para efetuar a alteração persistente e aplicadas durante a ativação da pilha de rede, edite */etc/sysconfig/network-scipts/ifcfg-eth0* e */etc/sysconfig/network-scipts/ifcfg-eth1*. Alterar a linha *"NM_CONTROLLED = yes"* para *"NM_CONTROLLED = não"*. Sem este passo, o adicional regras/encaminhamento não são automaticamente aplicadas.
 
Em seguida, expandem as tabelas de encaminhamento. Vamos supor que temos a seguinte configuração no local:

*Encaminhamento*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*Interfaces*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

Em seguida, teria de criar os seguintes ficheiros e adicionar as regras adequadas e as rotas a cada:

- */etc/sysconfig/Network-scripts/rule-eth0*

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- */etc/sysconfig/Network-scripts/route-eth0*

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- */etc/sysconfig/Network-scripts/rule-eth1*

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- */etc/sysconfig/Network-scripts/route-eth1*

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

Para aplicar as alterações, reinicie o *rede* service da seguinte forma:

```bash
systemctl restart network
```

As regras de encaminhamento corretamente estão agora no local e pode estabelecer ligação com a interface, conforme necessário.


## <a name="next-steps"></a>Passos seguintes
Reveja [tamanhos de VM com Linux](sizes.md) ao tentar criar uma VM com vários NICs. Preste atenção para o número máximo de NICs suporta cada tamanho da VM. 
