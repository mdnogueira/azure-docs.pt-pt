---
title: Criar uma rede virtual com o portal do Azure | Microsoft Docs
description: Saiba como criar uma rede virtual com o portal do Azure | Resource Manager
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6e96471c4f61e1ebe15c23f87ac646001d8e30ee
ms.openlocfilehash: d68f6a7e935f530630ee33f48cfad1b9e01e66a8


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Criar uma rede virtual com o portal do Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

O Azure tem dois modelos de implementação: a implementação do Azure Resource Manager e a implementação clássica. A Microsoft recomenda a criação de recursos com o modelo de implementação Resource Manager. Para saber mais sobre as diferenças entre os dois modelos, veja o artigo [Understand Azure deployment models (Compreender os modelos de implementação do Azure)](../resource-manager-deployment-model.md).
 
Este artigo explica como criar uma VNet através do modelo de implementação Resource Manager no portal do Azure. Também pode criar uma VNet através do Resource Manager com outras ferramentas ou criá-la através do modelo de implementação clássica, ao selecionar outra opção na lista seguinte:

> [!div class="op_single_selector"]
- [Portal](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Modelo](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (Clássico)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (Clássico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (Clássica)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Para criar uma rede virtual com o portal do Azure, conclua os passos seguintes:

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Novo** > **Redes** > **Rede virtual**, conforme mostrado na imagem seguinte:

    ![Nova Rede Virtual](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. No painel **Rede virtual** que aparece, confirme que *Resource Manager* está selecionado e clique em **Criar**, conforme mostrado na imagem seguinte:

    ![Rede Virtual](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. No painel **Criar rede virtual** que apareceu, introduza *TestVNet* em **Nome**, *192.168.0.0/16* em **Espaço de endereços**, *FrontEnd* em **Nome a sub-rede** *192.168.1.0/24* em **Intervalo de endereços da sub-rede**, *TestRG* em **Grupo de recursos**, selecione a sua **Subscrição**, uma **Localização** e clique no botão **Criar**, conforme mostrado na imagem seguinte:

    ![Criar a rede virtual](./media/virtual-network-create-vnet-arm-pportal/3.png)

    Em alternativa, pode selecionar um grupo de recursos já existente. Para saber mais sobre os grupos de recursos, veja o artigo [Descrição Geral do Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). Também pode selecionar uma localização diferente. Para obter mais informações sobre o Azure localizações e as regiões, leia o artigo [Regiões do Azure](https://azure.microsoft.com/regions).

5. O portal só lhe permite criar uma sub-rede quando criar um VNet. Neste cenário, tem de ser criada uma segunda sub-rede após a criação da VNet. Para criar a segunda sub-rede, clique em **Todos os recursos** e clique em **TestVNet**, no painel **Todos os recursos**, conforme mostrado na imagem seguinte:

    ![VNet Criada](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. No painel **TestVNet** que aparece, clique em **Sub-rede**, clique em **+Sub-rede**, introduza *BackEnd* em **Nome**, *192.168.2.0/24* em **Intervalo de endereços**, no painel **Adicionar sub-rede** e clique em **OK**, conforme mostrado na imagem seguinte:

    ![Adicionar sub-rede](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. São listadas as duas sub-redes, conforme mostrado na imagem seguinte:
    
    ![Lista de sub-redes na VNet](./media/virtual-network-create-vnet-arm-pportal/6.png)

Este artigo explicou como criar uma rede virtual com duas sub-redes para fins de teste. Antes de criar uma rede virtual para utilização em produção, recomendamos a leitura dos artigos [Descrição Geral da Rede Virtual](virtual-networks-overview.md) e [Virtual network plan and design (Plano e conceção de redes virtuais)](virtual-network-vnet-plan-design-arm.md) para compreender totalmente as redes virtuais e todas as definições. 

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar:

- Uma máquina virtual (VM) a uma rede virtual, nos artigos [Criar VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Criar uma VM do Linux](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Em vez de criar um VNet e uma sub-rede nos passos dos artigos, pode selecionar uma VNet e uma sub-rede existentes às quais ligar uma VM.
- A rede virtual a outras redes virtuais, no artigo [Ligar VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- A rede virtual a uma rede no local através de uma rede privada virtual (VPN) site a site ou de um circuito do ExpressRoute. Leia os artigos [Connect a VNet to an on-premises network using a site-to-site VPN (Ligar uma VNet a uma rede no local com uma VPN site a site)](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) e [Link a VNet to an ExpressRoute circuit (Ligar uma VNet a um circuito do ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md) para saber como.


<!--HONumber=Nov16_HO3-->


