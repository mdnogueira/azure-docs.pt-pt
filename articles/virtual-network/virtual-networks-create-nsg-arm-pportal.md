---
title: "Criar rede grupos de segurança - portal do Azure | Microsoft Docs"
description: "Saiba como criar e implementar grupos de segurança de rede através do portal do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 865032f350735d35668bb199ccf1ef3f0fae81de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-network-security-groups-using-the-azure-portal"></a>Criar grupos de segurança no portal do Azure de rede

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação do Resource Manager. Também pode [criar NSGs no modelo de implementação clássica](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

O exemplo do PowerShell comandos abaixo esperam num ambiente simple já criado com base no cenário acima. Se pretender executar os comandos, como são apresentados neste documento, criar primeiro o ambiente de teste ao implementar [este modelo](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), clique em **implementar no Azure**, substitua os valores de parâmetro predefinidos se necessário e siga as instruções no portal. Os passos abaixo utilizam **RG NSG** como o nome do grupo de recursos do modelo foi implementado.

## <a name="create-the-nsg-frontend-nsg"></a>Criar o NSG NSG-front-end
Para criar o **NSG-front-end** NSG, conforme mostrado no cenário acima, siga os passos abaixo.

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Procurar >** > **grupos de segurança de rede**.
   
    ![Portal do Azure – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. No **grupos de segurança de rede** painel, clique em **adicionar**.
   
    ![Portal do Azure – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. No **criar grupo de segurança de rede** painel, criar um NSG denominado *NSG-front-end* no *RG NSG* grupo de recursos e, em seguida, clique **criar**.
   
    ![Portal do Azure – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Criar regras num NSG existente
Para criar regras num NSG existente do portal do Azure, siga os passos abaixo.

1. Clique em **Procurar >** > **grupos de segurança de rede**.
2. Na lista de NSGs, clique em **NSG-front-end** > **regras de segurança de entrada**
   
    ![Portal do Azure – NSG-front-end](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Na lista de **regras de segurança de entrada**, clique em **adicionar**.
   
    ![Portal do Azure – Adicionar regra](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. No **Adicionar regra de segurança de entrada** painel, criar uma regra com o nome *regra web* com a prioridade de *200* permitir o acesso através de *TCP* à porta *80* para qualquer VM a partir de qualquer origem e, em seguida, clique em **OK**. Tenha em atenção que a maioria destas definições já está a valores predefinidos.
   
    ![Portal do Azure – as definições da regra](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Após alguns segundos, verá a nova regra no NSG.
   
    ![Portal do Azure – nova regra](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Repita os passos 6 para criar uma regra de entrada com o nome *rdp regra* com uma prioridade de *250* permitir o acesso através de *TCP* à porta *3389* para qualquer VM a partir de qualquer origem.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associar o NSG à sub-rede de Front-End
1. Clique em **Procurar >** > **grupos de recursos** > **RG NSG**.
2. No **RG NSG** painel, clique em **...**   >  **TestVNet**.
   
    ![Portal do Azure – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. No **definições** painel, clique em **sub-redes** > **front-end** > **grupo de segurança de rede** > **NSG-front-end**.
   
    ![Portal do Azure – definições da sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. No **front-end** painel, clique em **guardar**.
   
    ![Portal do Azure – definições da sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Criar o NSG NSG-back-end
Para criar o **NSG-back-end** NSG e associá-lo para o **back-end** sub-rede, siga os passos abaixo.

1. Repita os passos [criar o NSG NSG-front-end](#Create-the-NSG-FrontEnd-NSG) para criar um NSG denominado *NSG-back-end*
2. Repita os passos [criar regras num NSG existente](#Create-rules-in-an-existing-NSG) para criar o **entrada** regras na tabela abaixo.
   
   | Regra de entrada | Regra de saída |
   | --- | --- |
   | ![Portal do Azure - regra de entrada](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portal do Azure - regra de saída](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Repita os passos [associar o NSG para sub-rede FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) para associar o **NSG-back-end** NSG para o **back-end** sub-rede.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [gerir NSGs existentes](virtual-network-manage-nsg-arm-portal.md)
* [Ativar o registo](virtual-network-nsg-manage-log.md) para NSGs.

