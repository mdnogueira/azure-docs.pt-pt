---
title: Configurar uma rede virtual no Azure DevTest Labs | Microsoft Docs
description: "Saiba como configurar uma rede virtual existente e sub-rede e utilizá-los numa VM com o Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: tarcher
ms.openlocfilehash: 7048d5d1054906d712ac0422dacfe1ccca97fcc0
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurar uma rede virtual no Azure DevTest Labs
Conforme explicado no artigo [adicionar uma VM para um laboratório](devtest-lab-add-vm.md), quando criar uma VM num laboratório, pode especificar uma rede virtual configurada. Por exemplo, poderá ter de aceder aos recursos de rede empresarial do suas VMs através da rede virtual que foi configurada com o ExpressRoute ou VPN de site para site.

Este artigo explica como adicionar a sua rede virtual existente para as definições de rede Virtual de um laboratório para que fique disponível para escolher durante a criação de VMs.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurar uma rede virtual para um laboratório no portal do Azure
Os seguintes passos guiá-lo a adicionar uma rede virtual existente (e a sub-rede) para um laboratório para que possa ser utilizado ao criar uma VM no mesmo laboratório. 

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório pretendido. 
1. No painel principal o laboratório, selecione **políticas de configuração e**.

    ![O laboratório configuração e as políticas de acesso](./media/devtest-lab-configure-vnet/policies-menu.png)
1. No **recursos EXTERNOS** secção, selecione **redes virtuais**. É apresentada uma lista de redes virtuais configurados para o laboratório atual, bem como a rede virtual predefinida criada para o laboratório. 
1. Selecione **+ adicionar**.
   
    ![Adicionar uma rede virtual existente para o laboratório](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. No **rede Virtual** painel, selecione **[rede virtual selecione]**.
   
    ![Selecione uma rede virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. No **escolha de rede virtual** painel, selecione a rede virtual pretendida. É apresentada uma lista que mostra todas as redes virtuais que estão na mesma região na subscrição, como o laboratório.
1. Depois de selecionar uma rede virtual, é encaminhado para o **rede Virtual** painel. Selecione a sub-rede na lista na parte inferior.

    ![Lista de sub-rede](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    É apresentado o painel sub-rede de laboratório.

    ![Painel sub-rede de laboratório](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Especifique um **nome de sub-rede de laboratório**.
   - Para permitir uma sub-rede ser utilizado num laboratório de criação de VM, selecione **utilização na criação da máquina virtual**.
   - Para ativar um [partilhado endereço IP público](devtest-lab-shared-ip.md), selecione **ativar partilhado IP público**.
   - Para permitir que os endereços IP públicos numa sub-rede, selecione **permitir a criação de IP pública**.
   - No **máximo máquinas de virtuais por utilizador** campo, especifique as VMs máximas por utilizador para cada sub-rede. Se pretender que um número ilimitado de VMs, deixe este campo em branco.
1. Selecione **OK** para fechar o painel sub-rede de laboratório.
1. Selecione **guardar** para fechar o painel de rede Virtual.

Agora que a rede virtual estiver configurada, pode ser selecionada quando criar uma VM. Para ver como criar uma VM e especifique uma rede virtual, consulte o artigo [adicionar uma VM com artefactos para um laboratório](devtest-lab-add-vm-with-artifacts.md). 

Do Azure [documentação das redes virtuais](https://docs.microsoft.com/azure/virtual-network) fornece mais informações sobre como utilizar as VNets, incluindo como configurar e gerir uma VNet e ligá-lo à sua rede no local.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de adicionar a rede virtual pretendida para o laboratório, o passo seguinte consiste em [adicionar uma VM para o laboratório](devtest-lab-add-vm-with-artifacts.md).

