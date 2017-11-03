---
title: Gerir os NSGs no portal do Azure | Microsoft Docs
description: Saiba como gerir os NSGs existentes no portal do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-nsgs-using-the-portal"></a>Gerir os NSGs através do portal

> [!div class="op_single_selector"]
> * [Portal](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [CLI do Azure](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação Resource Manager, que a Microsoft recomenda-se para a maioria das implementações novas em vez do modelo de implementação clássica.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Obter as informações
Pode ver os seus NSGs existentes, obter as regras para um NSG existente e descobrir os recursos que um NSG é associado a.

### <a name="view-existing-nsgs"></a>Ver os NSGs existentes

Para ver todos os NSGs existentes numa subscrição, execute os seguintes passos:

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.

2. Clique em **Procurar >** > **grupos de segurança de rede**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Verifique a lista de NSGs no **grupos de segurança de rede** painel.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>Vista NSGs num grupo de recursos

Para ver a lista de NSGs no **RG NSG** recursos de grupo, execute os seguintes passos:

1. Clique em **grupos de recursos >** > **RG NSG** > **...** .

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Na lista de recursos, procure itens a apresentar no ícone NSG, conforme mostrado no **recursos** painel abaixo.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>Listar todas as regras para um NSG

Para ver as regras de um NSG denominado **NSG-front-end**, conclua os seguintes passos:

1. Do **grupos de segurança de rede** painel, ou o **recursos** painel mostrado acima, clique em **NSG-front-end**.

2. No **definições** separador, clique em **regras de segurança de entrada**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. O **regras de segurança de entrada** painel é apresentado como mostrado abaixo.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. No **definições** separador, clique em **regras de segurança de saída** para ver as regras de saída.

    > [!NOTE]
    > Para ver as regras predefinidas, clique o **predefinido regras** na parte superior do painel que apresenta as regras.
    >

### <a name="view-nsgs-associations"></a>Ver as associações de NSGs

Para ver os recursos que o **NSG-front-end** NSG é associado ao, conclua os seguintes passos:

1. Do **grupos de segurança de rede** painel, ou o **recursos** painel mostrado acima, clique em **NSG-front-end**.

2. No **definições** separador, clique em **sub-redes** para ver as sub-redes estão associadas ao NSG.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. No **definições** separador, clique em **interfaces de rede** para ver o que estão associados ao NSG NICs.

## <a name="manage-rules"></a>Gerir as regras
Pode adicionar regras a um NSG existente, edite as regras existentes e remova regras.

### <a name="add-a-rule"></a>Adicionar uma regra
Para adicionar uma regra que permite **entrada** tráfego para a porta **443** partir de qualquer máquina para o **NSG-front-end** NSG, conclua os seguintes passos:

1. Do **grupos de segurança de rede** painel, ou o **recursos** painel mostrado acima, clique em **NSG-front-end**.
2. No **definições** separador, clique em **regras de segurança de entrada**.
3. No **regras de segurança de entrada** painel, clique em **adicionar**. Em seguida, no **Adicionar regra de segurança de entrada** painel, preencher os valores, conforme mostrado abaixo e, em seguida, clique em **OK**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    Após alguns segundos, tenha em atenção a nova regra no **regras de segurança de entrada** painel.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Alterar uma regra
Para alterar a regra criada acima para permitir tráfego de entrada do **Internet** apenas, conclua os seguintes passos:

1. Do **grupos de segurança de rede** painel, ou o **recursos** painel mostrado acima, clique em **NSG-front-end**.
2. No **definições** separador, clique em regras criada acima.
3. No **https permitir** painel, altere o **origem** propriedade conforme mostrado abaixo e, em seguida, clique em **guardar**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Eliminar uma regra

Para eliminar a regra criada acima, conclua os seguintes passos:

1. Do **grupos de segurança de rede** painel, ou o **recursos** painel mostrado acima, clique em **NSG-front-end**.
2. No **definições** separador, clique em regras criada acima.
3. No **https permitir** painel, clique em **eliminar**e, em seguida, clique em **Sim**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Gerir as associações
Pode associar um NSG a sub-redes e NICs. Também pode desassociar um NSG a partir de qualquer recurso que está associado.

### <a name="associate-an-nsg-to-a-nic"></a>Associa um NSG a um NIC
Para associar o **NSG-front-end** NSG para o **TestNICWeb1** NIC, conclua os seguintes passos:

1. Do **grupos de segurança de rede** painel, ou o **recursos** painel mostrado acima, clique em **NSG-front-end**.
2. No **definições** separador, clique em **interfaces de rede** > **associar** > **TestNICWeb1**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Desassociar um NSG a partir de uma NIC

Ao desassociar a **NSG-front-end** NSG do **TestNICWeb1** NIC, conclua os seguintes passos:

1. No portal do Azure, clique em **grupos de recursos >** > **RG NSG** > **...**   >  **TestNICWeb1**.

2. No **TestNICWeb1** painel, clique em **alterar segurança...**   >  **Nenhum**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> Também pode utilizar este painel para associar o NIC para qualquer NSG existente.
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>Desassociar um NSG de sub-rede

Ao desassociar a **NSG-front-end** NSG do **front-end** sub-rede, conclua os seguintes passos:

1. No portal do Azure, clique em **grupos de recursos >** > **RG NSG** > **...**   >  **TestVNet**.

2. No **definições** painel, clique em **sub-redes** > **front-end** > **grupo de segurança de rede** > **nenhum**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. No **front-end** painel, clique em **guardar**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Associa um NSG a uma sub-rede

Para associar o **NSG-front-end** NSG para o **FronEnd** sub-rede novamente, execute os seguintes passos:

1. No portal do Azure, clique em **grupos de recursos >** > **RG NSG** > **...**   >  **TestVNet**.
2. No **definições** painel, clique em **sub-redes** > **front-end** > **grupo de segurança de rede** > **NSG-front-end**.
3. No **front-end** painel, clique em **guardar**.

> [!NOTE]
> Também pode associar um NSG a uma sub-rede de thh NSG **definições** painel.
>

## <a name="delete-an-nsg"></a>Eliminar um NSG
Só é possível eliminar um NSG, se não está associado a qualquer recurso. Para eliminar um NSG, conclua os seguintes passos:.

1. No portal do Azure, clique em **grupos de recursos >** > **RG NSG** > **...**   >  **NSG-front-end**.
2. No **definições** painel, clique em **interfaces de rede**.
3. Se existirem quaisquer NICs listados, clique o NIC e siga o passo 2 [desassociar um NSG a partir de uma NIC](#Dissociate-an-NSG-from-a-NIC).
4. Repita o passo 3 para cada NIC.
5. No **definições** painel, clique em **sub-redes**.
6. Se existirem quaisquer sub-redes listados, clique a sub-rede e siga os passos 2 e 3 na [desassociar um NSG de sub-rede](#Dissociate-an-NSG-from-a-subnet).
7. Desloca da esquerda para o **NSG-front-end** painel, em seguida, clique em **eliminar** > **Sim**.

    ![Portal do Azure – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Passos seguintes
* [Ativar o registo](virtual-network-nsg-manage-log.md) para NSGs.
