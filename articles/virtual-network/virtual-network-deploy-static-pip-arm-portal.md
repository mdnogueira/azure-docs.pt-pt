---
title: "Criar uma VM com um endereço IP público estático - portal do Azure | Microsoft Docs"
description: "Saiba como criar uma VM com um endereço IP público estático no portal do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 233e4eea8439320c1c7446e2c2b2e9d379351a3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Criar uma VM com um endereço IP público estático no portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI do Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modelo](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação Resource Manager, que a Microsoft recomenda-se para a maioria das implementações novas em vez do modelo de implementação clássica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Criar uma VM com um IP público estático

Para criar uma VM com um endereço IP público estático no portal do Azure, execute os seguintes passos:

1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
2. No canto superior esquerdo do portal, clique em **novo**>>**computação**>**Windows Server 2012 R2 Datacenter**.
3. No **selecionar um modelo de implementação** lista, selecione **Resource Manager** e clique em **criar**.
4. No **Noções básicas** painel, introduza as informações de VM, conforme mostrado abaixo e, em seguida, clique em **OK**.
   
    ![Portal do Azure – Noções básicas](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. No **escolher um tamanho** painel, clique em **A1 padrão** conforme mostrado abaixo e, em seguida, clique em **selecione**.
   
    ![Portal do Azure – escolher um tamanho](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. No **definições** painel, clique em **endereço IP público**, em seguida, no **Criar endereço IP público** painel, em **atribuição**, clique em **estático** conforme mostrado abaixo. E, em seguida, clique em **OK**.
   
    ![Portal do Azure – Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. No **definições** painel, clique em **OK**.
8. Reveja o **resumo** painel, conforme mostrado abaixo e, em seguida, clique em **OK**.
   
    ![Portal do Azure – Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Repare no novo mosaico no dashboard.
   
    ![Portal do Azure – Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Assim que a VM é criada, o **definições** é apresentado o painel conforme mostrado abaixo
    
    ![Portal do Azure – Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

