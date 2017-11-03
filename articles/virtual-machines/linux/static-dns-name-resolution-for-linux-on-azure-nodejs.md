---
title: "Utilizar o DNS interno para resolução de nomes VM no Azure | Microsoft Docs"
description: "Utilizar o DNS interno para resolução de nomes VM no Azure."
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
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: bfba2cf38a0624e8480a32bf153f391d820da5a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Utilizar o DNS interno para resolução de nomes VM no Azure

Este artigo mostra como definir estáticos nomes DNS internos para VMs com Linux através de cartões de Virtual NIC (VNic) e nomes de etiqueta DNS. Os nomes DNS estáticos são utilizados para serviços de infraestrutura permanente, como um servidor de compilação Jenkins, que é utilizado para este documento ou um servidor de Git.

Os requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Ficheiros de chaves públicas e privadas SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#quick-commands) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="quick-commands"></a>Comandos rápidos

Se precisar de realizar rapidamente a tarefa, a secção seguinte fornece detalhes sobre os comandos necessários. Mais informações e o contexto para cada passo é possível encontrar o resto do documento [Iniciar aqui](#detailed-walkthrough).  

Pré-requisitos: Grupo de recursos, VNet, NSG com SSH de entrada, sub-rede.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Criar um VNic com um nome DNS estático interno

O `-r` cli sinalizador é para definir a etiqueta DNS, que fornece o nome DNS estático para o VNic.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Implementar a VM numa VNet, NSG e, ligue o VNic

O `-N` liga o VNic a nova VM durante a implementação para o Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

Uma integração completa contínua e a implementação contínua (CiCd) determinados servidores de servidores de longa duração ou estático necessita de infraestrutura no Azure.  Recomenda-se que os recursos do Azure, como as redes virtuais (VNets) e os grupos de segurança de rede (NSGs), deverá ser estáticos e tempo lived recursos que raramente são implementados.  Assim que tiver sido implementada uma VNet, pode ser reutilizada por novas implementações sem qualquer afeta negativo para a infraestrutura.  Adicionar a esta rede estático de um servidor de repositório do Git e um servidor de automatização Jenkins fornece CiCd aos seus ambientes de desenvolvimento ou teste.  

Nomes DNS internos só estão resolvíveis dentro de uma rede virtual do Azure.  Dado que os nomes DNS internos, não são resolvíveis à internet fora, fornecer segurança adicional para a infraestrutura.

_Substitua os seus próprios nomenclatura de qualquer exemplos._

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

É necessário um grupo de recursos para organizar tudo que criamos nestas instruções.  Para obter mais informações sobre grupos de recursos do Azure, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Criar a VNet

O primeiro passo consiste em criar uma VNet para iniciar as VMs em.  A VNet contém uma sub-rede para esta instrução.  Para obter mais informações sobre as VNets do Azure, consulte [criar uma rede virtual, utilizando a CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Criar o NSG

A sub-rede é criada atrás de um grupo de segurança de rede existente, pelo que iremos criar o NSG antes da sub-rede.  Os NSGs do Azure são equivalentes para uma firewall na camada de rede.  Para obter mais informações sobre o Azure NSGs, consulte [como criar NSGs na CLI do Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Adicionar regra de permissão de um SSH de entrada

A VM com Linux tem acesso a partir da internet, pelo que não é necessária uma regra que permite o tráfego de entrada porta 22 sejam transferidos através da rede para a porta 22 na VM do Linux.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Adicionar uma sub-rede para a VNet

VMs dentro da VNet tem de estar localizadas numa sub-rede.  Cada VNet pode ter várias sub-redes.  Criar a sub-rede e associe a sub-rede com o NSG para adicionar uma firewall para a sub-rede.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

A sub-rede está agora adicionou dentro da VNet e associou com o NSG e a regra NSG.

## <a name="creating-static-dns-names"></a>Criação de nomes DNS estáticos

Azure é muito flexível, mas para utilizar nomes DNS para resolução de nomes de VMs, terá de criá-los como placas de rede Virtual (VNics) utilizando a etiquetagem de DNS.  VNics são importantes, como pode reutilize-as ao ligá-las para diferentes VMs, que mantém o VNic como um recurso estático enquanto as VMs podem ser temporárias.  Ao utilizar DNS etiquetagem no VNic, mas é capazes de permitir a resolução do nome simples de outras VMs na VNet.  Utilizando nomes resolvíveis permite que as outras VMs aceder ao servidor de automatização com o nome DNS `Jenkins` ou o servidor de Git como `gitrepo`.  Crie um VNic e associá-lo com a sub-rede que criou no passo anterior.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implemente a VM para a VNet e o NSG

Agora temos uma VNet, uma sub-rede dentro desse VNet e um NSG a agir como uma firewall para proteger os nosso sub-rede bloquear todo o tráfego de entrada, exceto a porta 22 para SSH.  A VM pode agora ser implementada no interior esta infraestrutura de rede existente.

Utilizar a CLI do Azure e o `azure vm create` comando, a VM com Linux for implementada para o grupo de recursos do Azure, VNet, sub-rede e existentes VNic.  Para obter mais informações sobre como utilizar a CLI para implementar uma VM completa, consulte [criar um ambiente de Linux completado utilizando a CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

Ao utilizar os sinalizadores da CLI para chamar recursos existentes, vamos dar instruções ao Azure para implementar a VM dentro da rede existente.  Para reiterate, depois de uma VNet e sub-rede tiverem sido implementados, pode ser deixados como estáticos ou permanentes recursos dentro da região do Azure.  

## <a name="next-steps"></a>Passos seguintes
* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux no Azure utilizando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
