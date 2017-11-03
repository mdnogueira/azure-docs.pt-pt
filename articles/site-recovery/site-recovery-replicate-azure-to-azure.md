---
title: "Replicar aplicações (do Azure para o Azure) | Microsoft Docs"
description: "Este artigo descreve como configurar a replicação de máquinas virtuais com uma região do Azure para outra região no Azure."
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
ms.date: 5/22/2017
ms.author: asgang
ms.openlocfilehash: f9f97cf840b722c8cfee169dd1640e0682f287ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais do Azure para outra região do Azure



>[!NOTE]
>
> Replicação de recuperação de site para máquinas virtuais do Azure está atualmente em pré-visualização.

Este artigo descreve como configurar a replicação de máquinas virtuais com uma região do Azure para outra região do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* O artigo pressupõe que já sabe sobre recuperação de Site e o Cofre de serviços de recuperação. Tem de ter um pre 'Cofre dos serviços de recuperação' criado.

    >[!NOTE]
    >
    > Recomenda-se que crie o 'Cofre de serviços de recuperação"na localização onde pretende que as VMs para replicar. Por exemplo, se a localização de destino "E.U.A. Central", crie o Cofre "E.U.A. Central".

* Se estiver a utilizar o proxy de firewall ou de regras de grupos de segurança de rede (NSG) para controlar o acesso a conectividade de internet de saída nas VMs do Azure, certifique-se de que a lista branca os URLs ou IPs necessária. Consulte [documento de orientação do funcionamento em rede](./site-recovery-azure-to-azure-networking-guidance.md) para obter mais detalhes.

* Se tiver o ExpressRoute ou de uma ligação VPN entre no local e a localização de origem no Azure, siga [considerações de recuperação de Site para o Azure expressroute no local / Configuração de VPN](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration) documento.

* A conta de utilizador do Azure tem de ter determinados [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para ativar a replicação da máquina virtual do Azure.

* Deve ser ativada a sua subscrição do Azure para criar as VMs na localização de destino que pretende utilizar como região DR. Pode contactar o suporte para ativar a quota necessária.

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Ative a replicação do cofre do Azure Site Recovery
Nesta ilustração, iremos irá replicar as VMs em execução no 'Oriental' localização do Azure para a localização ' Sudeste Asiático '. Os passos são os seguintes:

 Clique em **+ replicar** no cofre para ativar a replicação para máquinas virtuais.

1. **Origem:** refere-se para o ponto de origem das máquinas que neste caso é **Azure**.

2. **Localização de origem:** é a região do Azure a partir do qual pretende proteger as máquinas virtuais. Para nesta ilustração, a localização de origem ser 'Oriental'

3. **Modelo de implementação:** refere-se ao modelo de implementação do Azure máquinas de origem. Pode selecionar qualquer uma das clássico ou do resource manager e as máquinas que pertençam ao modelo específico serão apresentadas para proteção no próximo passo.

      >[!NOTE]
      >
      > Apenas pode replicar uma máquina virtual clássica e recuperá-la como uma máquina virtual clássica. Não é possível recuperá-la como uma máquina virtual do Gestor de recursos.

4. **Grupo de recursos:** é o grupo de recursos para o qual as máquinas virtuais de origem pertence. Todas as VMs no grupo de recursos selecionado serão apresentadas para proteção no próximo passo.

    ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

No **máquinas virtuais > Selecione as máquinas virtuais**, clique e selecione cada máquina que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em OK.
    ![Ativar replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


Na secção de definições pode configurar as propriedades do site de destino

1. **Localização de destino:** esta é a localização onde os dados de máquina virtual de origem serão replicados. Consoante a sua localização máquinas selecionadas, a recuperação de sites fornece a lista de regiões de destino adequado.

    > [!TIP]
    > É recomendado para manter a localização de destino mesmo a partir da recuperação do seu Cofre de serviços.

2. **Grupo de recursos de destino:** é o grupo de recursos para que todos os seus máquinas virtuais replicadas irão pertencer. Por predefinição a ASR irá criar um novo grupo de recursos na região de destino com um nome que o sufixo "asr". No caso de grupo de recursos criado pelo ASR já existir, será reutilizada. Também pode optar por personalizá-la conforme mostrado na secção abaixo.    
3. **Rede Virtual de destino:** por predefinição, a ASR irá criar uma nova rede virtual na região de destino com um nome que o sufixo "asr". Isto será mapeado para a sua rede de origem e será utilizado para qualquer proteção futura.

    > [!NOTE]
    > [Verifique os detalhes de rede](site-recovery-network-mapping-azure-to-azure.md) para saber mais sobre o mapeamento da rede.

4. **As contas de armazenamento de destino:** por predefinição, a ASR irá criar a nova conta de armazenamento de destino mimicking a configuração de armazenamento VM de origem. No caso de conta de armazenamento criada por ASR já existir, será reutilizada.

5. **As contas de armazenamento em cache:** ASR tem de conta de armazenamento adicional chamou o armazenamento de cache na região de origem. Todas as alterações a acontecer nas VMs de origem são controladas e enviadas para a conta de armazenamento de cache antes de replicar os para a localização de destino.

6. **Conjunto de disponibilidade:** por predefinição, a ASR irá criar uma novo conjunto de disponibilidade na região de destino com um nome que o sufixo "asr". No caso de conjunto de disponibilidade criado pelo ASR já existir, será reutilizada.

7.  **Política de replicação:** define as definições de recuperação ponto retenção histórico e aplicação consistente frequência de instantâneos. Por predefinição, a ASR irá criar uma nova política de replicação com as predefinições dos ' 24 horas para retenção do ponto de recuperação e ' 60 minutos para a frequência de instantâneos consistentes da aplicação.

    ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personalizar a recursos de destino

No caso de pretender alterar as predefinições utilizadas por ASR, pode alterar as definições com base nas suas necessidades.

1. **Personalizar:** clique nele para alterar as predefinições utilizadas por ASR.

2. **Grupo de recursos de destino:** pode selecionar o grupo de recursos na lista de todos os grupos de recursos existente na localização de destino dentro da subscrição.

3. **Rede Virtual de destino:** pode encontrar a lista da rede virtual na localização de destino.

4. **Conjunto de disponibilidade:** só é possível adicionar as definições de conjuntos de disponibilidade para máquinas virtuais que fazem parte de disponibilidade na região de origem.

5. **Contas de armazenamento de destino:**

![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG) clique em **criar o recurso de destino** e ativar a replicação


Depois de máquinas virtuais estão protegidas pode verificar o estado de funcionamento de VMs em **replicado itens**

>[!NOTE]
>Durante o período de tempo da replicação inicial existe foi uma possibilidade que estado demora tempo a atualizar e não consegue ver o progresso durante algum tempo. Pode clicar no botão Atualizar no topo do painel para obter o estado mais recente.
>

![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>Passos seguintes
- [Saiba mais](site-recovery-test-failover-to-azure.md) sobre como executar uma ativação pós-falha de teste.
- [Saiba mais](site-recovery-failover.md) sobre os diferentes tipos de ativações pós-falha e como executá-los.
- Saiba mais sobre [planos de recuperação a utilizar](site-recovery-create-recovery-plans.md) para reduzir o RTO.
- Saiba mais sobre [trocar as VMs do Azure](site-recovery-how-to-reprotect.md) após a ativação pós-falha.
