---
title: Como repor o interface de rede para a VM do Windows Azure | Microsoft Docs
description: Mostra como repor o interface de rede para a VM do Windows Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/26/2017
ms.author: genli
ms.openlocfilehash: b8eecb9d546d5922844ede3744d80b951334b5d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Como repor o interface de rede para a VM do Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Não é possível ligar ao Microsoft Azure Windows Máquina Virtual (VM) depois de desativar a predefinição de Interface de rede (NIC) ou manualmente, define um IP estático para o NIC. Este artigo mostra como repor a interface de rede para a VM do Windows Azure, que irá resolver o problema de ligação remota.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Repor a interface de rede

### <a name="for-classic-vms"></a>Para VMs clássicas

Para repor a interface de rede, siga estes passos:

1.  Aceda ao [Portal do Azure]( https://ms.portal.azure.com).
2.  Selecione **máquinas virtuais (clássicas)**.
3.  Selecione a Máquina Virtual afetados.
4.  Selecione **endereços IP**.
5.  Se o **atribuição de IP privado** não é **estático**, altere-o para **estático**.
6.  Alterar o **endereço IP** para outro endereço IP que está disponível na sub-rede.
7.  Selecione guardar.
8.  A máquina virtual será reiniciado para inicializar o NIC novo para o sistema.
9.  Tente RDP para a máquina. Se tiver êxito, pode alterar o endereço IP privado para original se desejar. Caso contrário, pode mantê-lo. 

### <a name="for-vms-deployed-in-resource-group-model"></a>Para as VMs implementadas no modelo de grupo de recursos

1.  Aceda ao [Portal do Azure]( https://ms.portal.azure.com).
2.  Selecione a Máquina Virtual afetados.
3.  Selecione **Interfaces de rede**.
4.  Selecione a Interface de rede associado à sua máquina
5.  Selecione **configurações de IP**.
6.  Selecione o IP. 
7.  Se o **atribuição de IP privado** não é **estático**, altere-o para **estático**.
8.  Alterar o **endereço IP** para outro endereço IP que está disponível na sub-rede.
9. A máquina virtual será reiniciado para inicializar o NIC novo para o sistema.
10. Tente RDP para a máquina. Se tiver êxito, pode alterar o endereço IP privado para original se desejar. Caso contrário, pode mantê-lo. 

## <a name="delete-the-unavailable-nics"></a>Eliminar os NICs indisponíveis
Depois de ambiente de trabalho remoto para a máquina, pode, tem de eliminar os NICs antigos para evitar o potencial problema:

1.  Abra o Gestor de dispositivos.
2.  Selecione **vista** > **Mostrar ocultos dispositivos**.
3.  Selecione **adaptadores de rede**. 
4.  Verifique se os adaptadores designados como "Placa de rede do Microsoft Hyper-V".
5.  Poderá ver um adaptador disponível que está a cinzento. O adaptador com o botão direito e selecione desinstalar.

    ![a imagem de NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Desinstale apenas os adaptadores disponíveis com o nome "Placa de rede do Microsoft Hyper-V". Se desinstalar qualquer um dos outros adaptadores de ocultos, causam problemas adicionais.
    >
    >

6.  Agora todos os adaptador indisponível deve ser desmarcadas do sistema.