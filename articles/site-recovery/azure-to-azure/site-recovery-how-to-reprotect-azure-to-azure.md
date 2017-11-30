---
title: "Como Reproteção de efetuar a ativação pós-falha de máquinas virtuais do Azure para primário região do Azure | Microsoft Docs"
description: "Após a ativação pós-falha de VMs de uma região do Azure para outro, pode utilizar o Azure Site Recovery para proteger as máquinas na direção inversa. Aprenda os passos fazer uma reproteção antes de uma ativação pós-falha novamente."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 0004323fee44c1433cdd3c39fc1fa7dad965dbf7
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="reprotect-azure-vms-back-to-the-primary-region"></a>Proteja as VMs do Azure para a região primária



>[!NOTE]
>
> Replicação de recuperação de site para máquinas de virtuais (VMs) do Azure está atualmente em pré-visualização.


Quando lhe [falhar](../site-recovery-failover.md) VMs de uma região do Azure para outro, falha na ativação pós-falha das VMs está num Estado não protegido. Se quiser colocá-los para a região primária, terá de iniciar pela primeira vez a replicar as VMs e, em seguida, efetuar a ativação pós-falha novamente. Não há qualquer diferença na ativação pós-falha na direção ou outro um. Da mesma forma, depois de ativar a replicação de VM, não há qualquer diferença entre só pós-falha ou post-reativação pós-falha.

Para explicar o processo, por exemplo, partimos do princípio que o site primário das VMs protegidas é Oriental região, sendo o site de recuperação Sudeste asiático. Durante a ativação pós-falha, ativação pós-falha de VMs para a região Sudeste asiático. Antes de falhar novamente, terá de replicar as VMs do Sudeste asiático, voltar a Ásia Oriental. Este artigo descreve os passos necessários para voltar.

> [!WARNING]
> Se tiver concluído migração e mover a máquina virtual para outro grupo de recursos (ou eliminar a máquina virtual do Azure) não pode falhar novamente.

Depois de termina só e estiver a replicar as VMs, pode iniciar uma ativação pós-falha nas VMs, colocá-los para a região Ásia Oriental.

## <a name="prerequisites"></a>Pré-requisitos
1. A VM deve ter sido consolidada.
2. O site de destino (Oriental) deve estar disponível e deve ser capaz de acesso/criar novos recursos nessa região.

## <a name="reprotect"></a>Voltar a proteger

Siga estes passos para voltar a proteger uma VM utilizando as predefinições.

1. No **cofre** > **replicado itens**, faça duplo clique VM que a ativação pós-falha e, em seguida, selecione **voltar a proteger**. Também pode clique na máquina e selecione **voltar a proteger** de botões de comando.

  ![Só](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Certifique-se de que a direção da replicação **Sudeste asiático a Ásia Oriental**, está selecionado.

  ![Voltar a proteger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Reveja o **grupo de recursos, conjuntos de disponibilidade, armazenamento e rede** informações e clique em **OK**. Se existirem quaisquer recursos marcados (novo), estes são criados durante só.

Isto aciona uma tarefa só que implementa o site de destino com os dados mais recentes e, depois de concluído, replica os deltas antes da ativação para Sudeste asiático.

### <a name="reprotect-customization"></a>Proteja personalização
Se pretender escolher a conta de armazenamento de extrair ou a rede durante reproteção, pode fazê-lo ao utilizar a opção de personalizar fornecida na página de.

![Personalizar a opção](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Pode personalizar as seguintes propriedades da máquina virtual de destino durante só.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Notas  |
|---------|---------|
|Grupo de recursos de destino     | Pode optar por alterar o grupo de recursos de destino no qual será criada ésimo máquina. Como parte de reproteção, a máquina virtual de destino será eliminada, por conseguinte, pode escolher um novo grupo de recursos na qual pode criar a ativação pós-falha post VM         |
|Rede Virtual de destino     | Rede não pode ser alterada durante só. Para alterar a rede, Refazer o mapeamento da rede.         |
|Armazenamento de destino     | Pode alterar a conta de armazenamento para o qual a máquina virtual será criada ativação pós-falha de post.         |
|Armazenamento de cache     | Pode especificar uma conta de armazenamento de cache que será utilizada durante a replicação. Se com as predefinições, uma nova conta de armazenamento de cache será criada, se já existir.         |
|Conjunto de Disponibilidade     |Se a máquina virtual na Ásia Oriental faz parte de um conjunto de disponibilidade, pode escolher um conjunto de disponibilidade para a máquina virtual de destino no Sudeste asiático. As predefinições irão encontrar o conjunto de disponibilidade SEA existente e tentar utilizá-lo. Durante a personalização, pode especificar um conjunto de AV totalmente novo.         |


### <a name="what-happens-during-reprotect"></a>O que acontece durante reproteção?

Tal como após o primeiro ativar a proteção, seguem-se de artefactos obterem criados se utilizar as predefinições.
1. É criada uma conta de armazenamento de cache na região Ásia Oriental.
2. Se a conta de armazenamento de destino (a conta armazenamento original da VM Sudeste asiático) não existe, é criado um novo. O nome é uma conta de armazenamento da máquina virtual a Ásia Oriental com "asr" o sufixo.
3. Se o conjunto de destino AV não existir, e as predefinições detetar que tem de criar um novo conjunto de AV, em seguida, será criado como parte da tarefa só. Se tiver personalizado, em seguida, o conjunto de AV selecionado será utilizado.


Seguem-se a lista dos passos acontecer se acionar uma tarefa só. Este é o caso que a máquina de virtual do lado de destino existe.

1. Os artefactos necessários são criados como parte de reproteção. Se já existirem, em seguida, são reutilizados.
2. A máquina de virtual do lado (Sudeste asiático) de destino é primeiro desligada, se estiver em execução.
3. Disco de destino do lado da máquina virtual é copiado pelo Azure Site Recovery num contentor como um blob de seed.
4. A máquina de virtual do lado de destino, em seguida, é eliminada.
5. O blob de seed é utilizado pela máquina virtual do lado (Oriental) de origem atual para replicar. Isto garante que apenas deltas são replicadas.
6. As alterações principais entre o disco de origem e o blob de seed são sincronizadas. Esta operação pode demorar algum tempo a concluir.
7. Uma vez concluída a tarefa só, a replicação de diferenças começa que cria um ponto de recuperação de acordo com a política.

> [!NOTE]
> Não é possível proteger um nível de plano de recuperação. Apenas pode voltar a proteger a um nível VM por.

Depois do que for bem sucedida, a máquina virtual entrará num estado protegido.

## <a name="next-steps"></a>Passos seguintes

Depois da máquina virtual entrou num estado protegido, pode iniciar uma ativação pós-falha. A ativação pós-falha irá encerrar a máquina virtual numa região do Azure de Oriental e, em seguida, criar e arrancar a máquina virtual a região Sudeste asiático. Por conseguinte, não há um pequeno período de indisponibilidade para a aplicação. Por isso, escolha a hora de ativação pós-falha quando a aplicação pode tolerar um tempo de inatividade. É recomendado que execute uma ativação pós-falha de teste da máquina virtual pela primeira vez, para se certificar de futuras cópias de segurança corretamente, antes de iniciar uma ativação pós-falha.

-   [Passos para iniciar a ativação pós-falha da máquina virtual de teste](../site-recovery-test-failover-to-azure.md)

-   [Para iniciar a ativação pós-falha da máquina virtual](../site-recovery-failover.md)
