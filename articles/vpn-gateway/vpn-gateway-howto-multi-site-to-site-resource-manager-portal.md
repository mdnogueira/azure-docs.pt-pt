---
title: "Adicionar várias ligações do VPN gateway Site para Site a uma VNet: Portal do Azure: Resource Manager | Microsoft Docs"
description: "Adicione as ligações S2S de vários sites a um gateway VPN que tenha uma ligação existente"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: cherylmc
ms.openlocfilehash: 7ec57789ee76f4ec54e4f7b68ea75c19522f3d7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicionar uma ligação Site a Site para uma VNet com uma ligação de gateway VPN existente

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
> 

Este artigo explica como utilizar o portal do Azure para adicionar as ligações Site a Site (S2S) para um gateway VPN que tenha uma ligação existente. Este tipo de ligação é frequentemente referido como um configuration "multilocal". Pode adicionar uma ligação S2S para uma VNet que já tem uma ligação S2S, a ligação de ponto a Site ou a ligação VNet a VNet. Existem algumas limitações ao adicionar as ligações. Verifique o [antes de começar](#before) secção neste artigo para verificar antes de iniciar a configuração. 

Este artigo aplica-se a VNets criadas com o modelo de implementação Resource Manager com um gateway de RouteBased VPN. Estes passos não se aplicam a configurações de ligação coexistentes ExpressRoute/Site a Site. Consulte [ligações coexistentes ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre ligações coexistentes.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela medida que novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando estiver disponível um artigo, criamos uma ligação direta desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Antes de começar
Verifique os seguintes itens:

* Não estiver a criar uma ligação coexistentes ExpressRoute/S2S.
* Tiver uma rede virtual que foi criada utilizando o modelo de implementação Resource Manager com uma ligação existente.
* O gateway de rede virtual para a sua VNet é RouteBased. Se tiver um gateway de PolicyBased VPN, tem de eliminar o gateway de rede virtual e criar um novo gateway de VPN RouteBased.
* Nenhum dos intervalos de endereços sobrepor-se para qualquer um das VNets que está a ligar nesta VNet.
* Ter o dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Tem um endereço IP público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.

## <a name="part1"></a>Parte 1 - configurar uma ligação
1. Num browser, navegue para o [Portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **todos os recursos** e localize o **gateway de rede virtual** da lista de recursos e clique no mesmo.
3. No **gateway de rede Virtual** painel, clique em **ligações**.
   
    ![Painel de ligações](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Painel de ligações")<br>
4. No **ligações** painel, clique em **+ adicionar**.
   
    ![Botão de ligação de adicionar](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "adicionar botão de ligação")<br>
5. No **adicionar ligação** painel, preenchimento enviados os seguintes campos:
   
   * **Nome:** o nome que pretende dar ao site que está a criar a ligação.
   * **Tipo de ligação:** selecione **Site a site (IPsec)**.
     
     ![Painel de ligação de adicionar](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "painel de ligação de adicionar")<br>

## <a name="part2"></a>Parte 2 – Adicionar um gateway de rede local
1. Clique em **gateway de rede Local** ***escolher um gateway de rede local***. Esta ação irá abrir o **escolher gateway de rede local** painel.
   
    ![Gateway de rede local escolha](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "escolher gateway de rede local")<br>
2. Clique em **criar nova** para abrir o **criar gateway de rede local** painel.
   
    ![Painel de gateway de rede local criar](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "criar gateway de rede local")<br>
3. No **criar gateway de rede local** painel, preenchimento enviados os seguintes campos:
   
   * **Nome:** o nome que pretende dar ao recurso de gateway da rede local.
   * **Endereço IP:** o endereço IP público do dispositivo VPN no site que pretende ligar.
   * **Espaço de endereços:** o espaço de endereços que pretende ser encaminhados para o novo site de rede local.
4. Clique em **OK** no **criar gateway de rede local** painel para guardar as alterações.

## <a name="part3"></a>Parte 3 - adicionar a chave partilhada e criar a ligação
1. No **adicionar ligação** painel, adicione a chave partilhada que pretende utilizar para criar a ligação. Pode obter a chave partilhada do seu dispositivo VPN, ou aqui e, em seguida, configurar o dispositivo VPN para utilizar a mesma chave partilhada. O mais importante é que as chaves são iguais.
   
    ![Chave partilhada](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Chave partilhada")<br>
2. Na parte inferior do painel, clique em **OK** para criar a ligação.

## <a name="part4"></a>Parte 4 - verificar a ligação VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja o [percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) das máquinas virtuais para obter mais informações.
