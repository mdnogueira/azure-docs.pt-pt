---
title: "Replicar VMs do Azure para uma região secundária com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como replicar as VMs do Azure com uma região do Azure para noutra região, utilizando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais do Azure para outra região do Azure


Este artigo descreve como replicar máquinas virtuais do Azure (VMs) numa região do Azure, para uma região secundária do Azure, utilizando o serviço do Azure Site Recovery.

>[!NOTE]
>
> Replicação de VM do Azure com a recuperação de Site está atualmente em pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

* Deve ter um cofre dos serviços de recuperação no local. Recomendamos que crie o Cofre na região de destino para o qual pretende que as VMs para replicar.
* Se estiver a utilizar as regras de grupos de segurança de rede (NSG) ou um proxy de firewall para controlar o acesso a conectividade de internet de saída nas VMs do Azure, certifique-se de que permitem a URLs necessários ou IPs. [Saiba mais](./site-recovery-azure-to-azure-networking-guidance.md).
* Se tiver o ExpressRoute ou de uma ligação VPN entre no local e a localização de origem no Azure, [saber como configurá-los](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).
* Tem a sua conta de utilizador do Azure [permissões específicas](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines), para ativar a replicação da VM do Azure.
Deve ser ativada a sua subscrição do Azure para criar as VMs na localização de destino que pretende utilizar como uma região de recuperação após desastre. Contacte o suporte para ativar a quota necessária.

## <a name="enable-replication"></a>Ativar a replicação

Neste procedimento, Ásia Oriental é utilizada como a localização de origem e o Sudeste asiático como destino.

1. Clique em **+ replicar** no cofre para ativar a replicação para máquinas virtuais.
2. Certifique-se de que **origem:** está definido como **Azure**.
3. Definir **localização de origem** a Ásia Oriental.
4. No **modelo de implementação**, selecione **clássico** ou **Resource Manager**.
5. No **grupo de recursos**, selecione o grupo ao qual pertencem as VMs de origem. Todas as VMs no grupo de recursos selecionado são apresentadas.

    ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. No **máquinas virtuais > Selecione as máquinas virtuais**, clique e selecione cada VM que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.

    ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. Em **definições** > **localização de destino**, especifique para onde replica a origem de dados da VM. Recuperação de sites fornece uma lista de regiões de destino adequado, consoante a região das VMs selecionadas.
8. Recuperação de sites define predefinido as definições de destino. Estes podem ser modificadas conforme necessário.

    - **Grupo de recursos de destino**. Por predefinição, a recuperação de Site cria um novo grupo de recursos na região de destino com o sufixo "asr". Se o grupo de recursos criado já existir, é reutilizado.
    - **Rede virtual de destino**. Por predefinição, a recuperação de sites cria uma nova rede virtual na região de destino, com o sufixo "asr". Esta rede é mapeada para a sua rede de origem. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre mapeamento da rede.
    - **As contas de armazenamento de destino**. Por predefinição, a recuperação de sites cria uma nova conta de armazenamento de destino corresponde a configuração de armazenamento VM de origem. Se a conta criada já existir, é reutilizado.
    - **As contas de armazenamento em cache**. O Azure Site Recovery cria uma conta de armazenamento de cache adicional, a região de origem. Todas as alterações nas VMs de origem são controladas e enviadas para a conta de armazenamento de cache antes de replicação para a localização de destino.
    - **Conjunto de disponibilidade**. Por predefinição, a recuperação de Site cria uma novo conjunto de disponibilidade na região de destino, com um sufixo "asr". Se o conjunto criado já existir, é reutilizado.
    - **Política de replicação**. Recuperação de sites define as definições de histórico de retenção do ponto de recuperação e a frequência de instantâneos consistentes da aplicação. Por predefinição, a recuperação de sites cria uma nova política de replicação com as predefinições de 24 horas para a retenção do ponto de recuperação e 60 minutos para a frequência de instantâneos consistentes com aplicações.

    ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. Clique em **ativar replicação** para começar a proteger as VMs.

## <a name="customize-target-resources"></a>Personalizar a recursos de destino

1. Modificar qualquer uma destas predefinições de destino:

    - **Grupo de recursos de destino**. Selecione qualquer grupo de recursos na lista de todos os grupos de recursos na localização de destino, dentro da subscrição.
    - **Rede virtual de destino**. Selecione na lista de todas as redes virtuais na localização de destino.
    - **Conjunto de disponibilidade** só é possível adicionar as definições de conjuntos de disponibilidade a VMs localizadas num conjunto na região de origem.
    - **As contas de armazenamento de destino**: adicionar qualquer conta que está disponível.

        ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Clique em **criar o recurso de destino** > **ativar replicação**. Durante a replicação inicial, o estado da VM pode demorar algum tempo a atualizar. Clique em **atualizar** para obter o estado mais recente.

    ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. Depois das VMs estão protegidas, verifique o estado de funcionamento da VM na **replicado itens**.



## <a name="next-steps"></a>Passos seguintes
[Saiba](../azure-to-azure-tutorial-dr-drill.md) como executar uma ativação pós-falha de teste.

