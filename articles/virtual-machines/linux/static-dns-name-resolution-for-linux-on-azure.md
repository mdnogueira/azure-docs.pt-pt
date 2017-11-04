---
title: "Utilizar o DNS interno para a resolução de nome VM com o 2.0 CLI do Azure | Microsoft Docs"
description: "Como criar placas de interface de rede virtual e utilizar o DNS interno para a resolução de nome VM no Azure com o 2.0 CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
ms.openlocfilehash: 992920adb1ae3736d43cc5f0bbb2081a20a1674d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Criar placas de interface de rede virtual e utilizar o DNS interno para a resolução de nome VM no Azure
Este artigo mostra-lhe como definir estáticos nomes DNS internos para VMs com Linux com placas de interface de rede virtual (vNics) e nomes de etiqueta DNS com o 2.0 CLI do Azure. Também pode efetuar estes passos com a [CLI 1.0 do Azure](static-dns-name-resolution-for-linux-on-azure-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Os nomes DNS estáticos são utilizados para serviços de infraestrutura permanente, como um servidor de compilação Jenkins, que é utilizado para este documento ou um servidor de Git.

Os requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Ficheiros de chaves públicas e privadas SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, a secção seguinte fornece detalhes sobre os comandos necessários. Mais informações e o contexto de cada passo pode ser encontrado no resto do documento [Iniciar aqui](#detailed-walkthrough). Para executar estes passos, terá da versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Pré-requisitos: Grupo de recursos, a rede virtual e a sub-rede, grupo de segurança de rede com SSH de entrada.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Criar uma placa de interface de rede virtual com um nome DNS estático interno
Criar vNic com [nic da rede az criar](/cli/azure/network/nic#create). O `--internal-dns-name` sinalizador CLI é para definir a etiqueta DNS, que fornece o nome DNS estático para o cartão de interface de rede virtual (vNic). O exemplo seguinte cria um vNic denominado `myNic`, liga-se de que o `myVnet` rede virtual e cria um registo de nome DNS interno chamado `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Implementar uma VM e ligar o vNic
Crie uma VM com [az vm create](/cli/azure/vm#create). O `--nics` sinalizador liga o vNic para a VM durante a implementação para o Azure. O exemplo seguinte cria uma VM chamada `myVM` com discos gerida do Azure e anexa o vNic denominado `myNic` do passo anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

Uma integração completa contínua e a implementação contínua (CiCd) determinados servidores de servidores de longa duração ou estático necessita de infraestrutura no Azure. Recomenda-se que os recursos do Azure, como as redes virtuais e os grupos de segurança de rede são estáticos e tempo lived recursos que raramente são implementados. Assim que tiver sido implementada uma rede virtual, pode ser reutilizada por novas implementações sem qualquer afeta negativo para a infraestrutura. Posteriormente, pode adicionar um servidor do repositório de Git ou um servidor de automatização de Jenkins oferece CiCd nesta rede virtual para o seu desenvolvimento ou ambientes de teste.  

Nomes DNS internos só estão resolvíveis dentro de uma rede virtual do Azure. Dado que os nomes DNS internos, não são resolvíveis à internet fora, fornecer segurança adicional para a infraestrutura.

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem `myResourceGroup`, `myNic`, e `myVM`.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos
Em primeiro lugar, crie o grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado `myResourceGroup` no `westus` localização:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Criar a rede virtual

O passo seguinte consiste em criar uma rede virtual para iniciar as VMs em. A rede virtual contém uma sub-rede para esta instrução. Para obter mais informações sobre redes virtuais do Azure, consulte [criar uma rede virtual, utilizando a CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Criar a rede virtual com [az rede vnet criar](/cli/azure/network/vnet#create). O exemplo seguinte cria uma rede virtual denominada `myVnet` e sub-rede designada `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Criar o grupo de segurança de rede
Grupos de segurança de rede do Azure são equivalentes para uma firewall na camada de rede. Para obter mais informações sobre grupos de segurança de rede, consulte [como criar NSGs na CLI do Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Criar o grupo de segurança de rede com [az rede nsg criar](/cli/azure/network/nsg#create). O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Adicionar uma regra incorporada para permitir que o SSH
Adicionar uma regra de entrada para o grupo de segurança de rede com [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#create). O exemplo seguinte cria uma regra com o nome `myRuleAllowSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associar a sub-rede com o grupo de segurança de rede
Para associar a sub-rede com o grupo de segurança de rede, utilize [atualização de sub-rede da vnet de rede de az](/cli/azure/network/vnet/subnet#update). O exemplo seguinte associa o nome de sub-rede `mySubnet` com o grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Criar o cartão de interface de rede virtual e os nomes DNS estáticos
Azure é muito flexível, mas para utilizar nomes DNS para a resolução de nome VM, tem de criar rede virtual placas de interface (vNics) que incluem uma etiqueta de DNS. vNics são importantes, como pode reutilize-as ao ligá-las para diferentes VMs ao longo do ciclo de vida de infraestrutura. Esta abordagem mantém o vNic como um recurso estático enquanto as VMs podem ser temporárias. Ao utilizar DNS etiquetagem no vNic, mas é capazes de permitir a resolução do nome simples de outras VMs na VNet. Utilizando nomes resolvíveis permite que as outras VMs aceder ao servidor de automatização com o nome DNS `Jenkins` ou o servidor de Git como `gitrepo`.  

Criar vNic com [nic da rede az criar](/cli/azure/network/nic#create). O exemplo seguinte cria um vNic denominado `myNic`, liga-se de que o `myVnet` rede virtual denominada `myVnet`e cria um registo de nome DNS interno chamado `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implemente a VM para a infraestrutura de rede virtual
Agora temos uma rede virtual e uma sub-rede, um grupo de segurança de rede a agir como uma firewall para proteger os nosso sub-rede bloquear todo o tráfego de entrada, exceto a porta 22 para SSH e um vNic. Agora pode implementar uma VM no interior esta infraestrutura de rede existente.

Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada `myVM` com discos gerida do Azure e anexa o vNic denominado `myNic` do passo anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Ao utilizar os sinalizadores da CLI para chamar recursos existentes, vamos dar instruções ao Azure para implementar a VM dentro da rede existente. Para reiterate, depois de uma VNet e sub-rede tiverem sido implementados, pode ser deixados como estáticos ou permanentes recursos dentro da região do Azure.  

## <a name="next-steps"></a>Passos seguintes
* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux no Azure utilizando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
