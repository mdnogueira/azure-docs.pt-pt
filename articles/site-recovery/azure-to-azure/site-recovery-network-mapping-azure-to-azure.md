---
title: "Mapeamento de rede entre as duas regiões do Azure no Azure Site Recovery | Microsoft Docs"
description: "O Azure Site Recovery coordena a replicação, ativação pós-falha e recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre a ativação pós-falha para o Azure ou num datacenter secundário."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: pratshar
ms.openlocfilehash: eb58c8b52147b09619fa155ffdda6f257f7772ca
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Mapeamento de rede entre as duas regiões do Azure


Este artigo descreve como mapear redes virtuais do Azure, das regiões do Azure dois entre si. Mapeamento de rede assegura que, quando a máquina virtual replicada é criada na região do Azure de destino, é criado na rede virtual que está mapeada para a rede virtual da máquina virtual de origem.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de mapear redes Certifique-se, criou [redes virtuais do Azure](../../virtual-network/virtual-networks-overview.md) na origem e destino regiões do Azure.

## <a name="map-networks"></a>Mapear redes

Para mapear uma Azure virtual network numa região do Azure para outra rede virtual noutra região, aceda a infraestrutura de recuperação de Site -> mapeamento da rede (para máquinas virtuais do Azure) e crie um mapeamento de rede.

![Mapeamento da rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


No exemplo abaixo a minha máquina virtual está em execução na Ásia Oriental região e está a ser replicada para Sudeste asiático.

Selecione a rede de origem e de destino e, em seguida, clique em OK para criar um mapeamento de rede a partir de Oriental para Sudeste asiático.

![Mapeamento da rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Fazer a mesma coisa para criar um mapeamento de rede do Sudeste asiático a Ásia Oriental.  
![Mapeamento da rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Mapeamento de rede ao ativar a replicação

Se não é executado o mapeamento da rede quando estiver a replicar uma máquina virtual pela primeira vez a partir de uma região do Azure para outro, em seguida, pode escolher a rede de destino como parte do mesmo processo. Recuperação de sites cria os mapeamentos de rede da região de origem para a região de destino e a região de destino para a região de origem com base nesta seleção.   

![Mapeamento da rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Por predefinição, a recuperação de sites cria uma rede na região de destino que é idêntica à rede de origem e adicionando '-asr' como um sufixo ao nome de rede de origem. Pode escolher uma rede já criada ao clicar em Personalizar.

![Mapeamento da rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


Se o mapeamento da rede é o tiver feito, não é possível alterar a rede virtual de destino durante a ativação da replicação. Para alterá-la, modifique o mapeamento de rede existente.  

![Mapeamento da rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Mapeamento da rede](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Se modificar um mapeamento de rede de região-1 para 2 região, certifique-se que modificar o mapeamento da rede de região 2 a região-1 também.
>
>


## <a name="subnet-selection"></a>Seleção de sub-rede
Sub-rede de máquina virtual de destino está selecionado com base no nome de sub-rede da máquina virtual de origem. Se existir uma sub-rede com o mesmo nome da máquina virtual de origem disponíveis na rede de destino, que é escolhido para a máquina virtual de destino. Se não existir nenhuma sub-rede com o mesmo nome na rede de destino, em seguida, por ordem alfabética primeira sub-rede é escolhido como a sub-rede de destino. Pode modificar esta sub-rede acedendo a computação e rede definições da máquina virtual.

![Modificar a sub-rede](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Endereço IP

Endereço IP para cada interface de rede da máquina virtual de destino é escolhido, da seguinte forma:

### <a name="dhcp"></a>DHCP
Se a interface de rede da máquina virtual de origem estiver a utilizar DHCP, a interface de rede da máquina virtual de destino seja também definida como DHCP.

### <a name="static-ip"></a>IP estático
Se a interface de rede da máquina virtual de origem estiver a utilizar IP estático, a interface de rede da máquina virtual de destino também está definida para utilizar IP estático. IP estático é escolhido, da seguinte forma:

#### <a name="same-address-space"></a>Mesmo espaço de endereços

Se a sub-rede de origem e a sub-rede de destino tem o mesmo espaço de endereços, o IP de destino está definido mesmo que o IP da interface de rede da máquina virtual de origem. Se o mesmo IP não estiver disponível, alguns outro IP disponível está definido como o IP de destino.

#### <a name="different-address-space"></a>Espaço de endereços diferente

Se a sub-rede de origem e a sub-rede de destino tem espaço de endereços diferente, o IP de destino está definido como qualquer IP disponível na sub-rede de destino.

Pode modificar o IP de destino em cada interface de rede acedendo às definições de computação e rede da máquina virtual.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [redes orientações para replicar as VMs do Azure](site-recovery-azure-to-azure-networking-guidance.md).
