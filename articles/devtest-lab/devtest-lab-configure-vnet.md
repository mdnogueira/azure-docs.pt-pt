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
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 19dbb1625f46f8864413dc538a96b2413bc6eea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurar uma rede virtual no Azure DevTest Labs
Conforme explicado no artigo, [adicionar uma VM com artefactos para um laboratório](devtest-lab-add-vm-with-artifacts.md), quando criar uma VM num laboratório, pode especificar uma rede virtual configurada. Um cenário para efetuar este procedimento é se precisa de aceder aos recursos de rede empresarial do suas VMs através da rede virtual que foi configurada com o ExpressRoute ou VPN de site para site. As secções seguintes mostram como adicionar a sua rede virtual existente para as definições de rede Virtual de um laboratório para que fique disponível para escolher durante a criação de VMs.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurar uma rede virtual para um laboratório no portal do Azure
Os seguintes passos guiá-lo a adicionar uma rede virtual existente (e a sub-rede) para um laboratório para que possa ser utilizado ao criar uma VM no mesmo laboratório. 

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido. 
4. No painel do laboratório, selecione **políticas de configuração e**.
5. No laboratório de **políticas de configuração e** painel, selecione **redes virtuais**.
6. No **redes virtuais** painel, é apresentada uma lista de redes virtuais configurados para o laboratório atual, bem como a rede virtual predefinida que é criada para o laboratório. 
7. Selecione **+ adicionar**.
   
    ![Adicionar uma rede virtual existente para o laboratório](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. No **rede Virtual** painel, selecione **[rede virtual selecione]**.
   
    ![Selecione uma rede virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. No **escolha de rede virtual** painel, selecione a rede virtual pretendida. O painel mostra todas as redes virtuais que estão na mesma região na subscrição, como o laboratório.  
10. Depois de selecionar uma rede virtual, é encaminhado para o **rede Virtual** clique a sub-rede na lista na parte inferior do painel.

    ![Lista de sub-rede](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    É apresentado o painel sub-rede de laboratório.

    ![Painel sub-rede de laboratório](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. Especifique um **nome de sub-rede de laboratório**.
12. Para permitir uma sub-rede ser utilizado num laboratório de criação de VM, selecione **utilização na criação da máquina virtual**.
13. Para ativar um [partilhado endereço IP público](devtest-lab-shared-ip.md), selecione **ativar partilhado IP público**.
14. Para permitir que os endereços IP públicos numa sub-rede, selecione **permitir a criação de IP pública**.
15. No **máximo máquinas de virtuais por utilizador** campo, especifique as VMs máximas por utilizador para cada sub-rede. Se pretender que um número ilimitado de VMs, deixe este campo em branco.
16. Selecione **OK** para fechar o painel sub-rede de laboratório.
17. Selecione **guardar** para fechar o painel de rede Virtual.
18. Agora que a rede virtual estiver configurada, pode ser selecionada quando criar uma VM. 
    Para ver como criar uma VM e especifique uma rede virtual, consulte o artigo [adicionar uma VM com artefactos para um laboratório](devtest-lab-add-vm-with-artifacts.md). 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de adicionar a rede virtual pretendida para o laboratório, o passo seguinte consiste em [adicionar uma VM para o laboratório](devtest-lab-add-vm-with-artifacts.md).

