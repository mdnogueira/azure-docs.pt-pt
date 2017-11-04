---
title: Criar uma rede virtual com a CLI do Azure 1.0 | Microsoft Docs
description: Saiba como criar uma rede virtual com a CLI do Azure 1.0 | Gestor de recursos.
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Criar uma rede virtual com a CLI do Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

O Azure tem dois modelos de implementação: a implementação do Azure Resource Manager e a implementação clássica. A Microsoft recomenda a criação de recursos com o modelo de implementação Resource Manager. Para saber mais sobre as diferenças entre os dois modelos, veja o artigo [Understand Azure deployment models (Compreender os modelos de implementação do Azure)](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [CLI 2.0 do Azure](virtual-networks-create-vnet-arm-cli.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos
- [Azure CLI 1.0](#create-a-virtual-network) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Para criar uma rede virtual com a CLI do Azure, execute os seguintes passos:

1. Instalar e configurar a CLI do Azure, seguindo os passos a [instalar e configurar a CLI do Azure](../cli-install-nodejs.md) artigo.

2. Crie uma VNet e uma sub-rede:

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Resultado esperado:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Parâmetros utilizados:

   * **-- vnet**. Nome da VNet a ser criada. Para o nosso cenário *TestVNet*.
   * **-e (ou - espaço de endereços)**. Espaço de endereços da VNet. Para o nosso cenário *192.168.0.0*
   * **-i (ou - cidr)**. Máscara de rede no formato CIDR. Para o nosso cenário *16*.
   * **-n (ou - nome de sub-rede**). Nome da sub-rede primeiro. Para o nosso cenário *FrontEnd*.
   * **-p (ou - ip de início de sub-rede)**. Endereço IP inicial para a sub-rede ou espaço de endereços de sub-rede. Para o nosso cenário *192.168.1.0*.
   * **-r (ou - sub-rede cidr)**. Máscara de rede no formato CIDR para a sub-rede. Para o nosso cenário *24*.
   * **-l (ou --location)**. Região do Azure onde a VNet será criada. Para o nosso cenário *EUA Central*.

3. Crie uma sub-rede:

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Resultado esperado:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Parâmetros utilizados:

   * **-t (ou -- vnet-name**. Nome da VNet onde a sub-rede será criada. Para o nosso cenário *TestVNet*.
   * **-n (ou --name)**. Nome da nova sub-rede. Para o nosso cenário *back-end*.
   * **-a (or --address-prefix)**. Bloco CIDR da sub-rede. Quatro nosso cenário, *192.168.2.0/24*.
   
4. Para ver as propriedades da nova VNet:

    ```azurecli
    azure network vnet show
    ```
   
    Resultado esperado:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar:

- Uma máquina virtual (VM) a uma rede virtual ao ler o [criar uma VM com Linux](../virtual-machines/linux/quick-create-cli.md) artigo. Em vez de criar um VNet e uma sub-rede nos passos dos artigos, pode selecionar uma VNet e uma sub-rede existentes às quais ligar uma VM.
- A rede virtual a outras redes virtuais, no artigo [Ligar VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- A rede virtual a uma rede no local através de uma rede privada virtual (VPN) site a site ou de um circuito do ExpressRoute. Saiba como ao ler o [ligar uma VNet a uma rede no local através de uma VPN de site para site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) e [ligar uma VNet a um circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).