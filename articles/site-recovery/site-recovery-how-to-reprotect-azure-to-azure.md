---
title: "Como Reproteção de efetuar a ativação pós-falha de máquinas virtuais do Azure para primário região do Azure | Microsoft Docs"
description: "Após a ativação pós-falha de VMs de uma região do Azure para outro, pode utilizar o Azure Site Recovery para proteger as máquinas na direção inversa. Aprenda os passos fazer uma reproteção antes de uma ativação pós-falha novamente."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: rajanaki
ms.openlocfilehash: 3e614b6c3c8358585f3b502f301cc659d2088e2f
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="reprotect-from-failed-over-azure-region-back-to-primary-region"></a>Reproteção da ativação pós-falha região do Azure para a região primária



>[!NOTE]
>
> Replicação de recuperação de site para máquinas virtuais do Azure está atualmente em pré-visualização.


## <a name="overview"></a>Descrição geral
Quando lhe [ativação pós-falha](site-recovery-failover.md) as máquinas virtuais de uma região do Azure para outro, as máquinas virtuais estão num Estado não protegido. Se quiser colocá-los para a região primária, terá de primeiro proteger as máquinas virtuais e, em seguida, a ativação pós-falha novamente. Há qualquer diferença entre como a ativação pós-falha numa direção ou outros. De igual modo, após ativar a proteção das máquinas virtuais, há qualquer diferença entre a ativação pós-falha de post reproteção ou a reativação pós-falha da post.
Para explicar os fluxos de trabalho de reproteção e para evitar confusões, consulte o site primário do máquinas protegidas como Oriental região e o site de recuperação das máquinas como Sudeste asiático região. Durante a ativação pós-falha, as máquinas virtuais irão arrancar na região Sudeste asiático. Antes de a reativação pós-falha, terá de voltar a proteger as máquinas virtuais do Sudeste asiático voltar a Ásia Oriental. Este artigo descreve os passos sobre como voltar a proteger.

> [!WARNING]
> Se tiver [concluído migração](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), mover a máquina virtual para outro grupo de recursos ou eliminar a máquina virtual do Azure, não é possível voltar a proteger ou reativação pós-falha da máquina virtual.

Depois de reproteção concluída e estiver a replicar máquinas virtuais protegidas, pode iniciar uma ativação pós-falha nas máquinas virtuais para colocá-los a Ásia Oriental região.

Publique comentários ou perguntas no final deste artigo ou no [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos
1. As máquinas virtuais devem ter sido consolidadas.
2. O site de destino - neste caso, a região do Azure de Oriental deve estar disponível e deve ser capaz de acesso/criar novos recursos nessa região.

## <a name="steps-to-reprotect"></a>Passos para voltar a proteger

Seguem-se os passos para voltar a proteger uma máquina virtual utilizando as predefinições.

1. No **cofre** > **replicado itens**, faça duplo clique na máquina virtual que é foi efetuada a ativação pós-falha e, em seguida, selecione **voltar a proteger**. Também pode clique na máquina e selecione **voltar a proteger** de botões de comando.

![Com o botão direito clique para voltar a proteger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. No painel, repare que a direção da proteção, **Sudeste asiático a Ásia Oriental**, já está selecionada.

![Proteja o painel](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Reveja o **grupo de recursos, conjuntos de disponibilidade, armazenamento e rede** informações e clique em OK. Se existirem quaisquer recursos marcados (novo), serão criadas como parte de reproteção.

A tarefa de reproteção será o seeding primeiro site de destino (SEA neste caso) com os dados mais recentes e depois de concluído, irá replicar as diferenças antes a ativação pós-falha para Sudeste asiático.

### <a name="reprotect-customization"></a>Proteja personalização
Se pretender escolher a conta de armazenamento de extrair ou a rede durante reproteção, pode fazê-lo ao utilizar a opção de personalizar fornecida no painel de reproteção.

![Personalizar a opção](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Pode personalizar as seguintes propriedades da máquina de virtual de destino putador durante reproteção.

![Personalizar o painel](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Notas  |
|---------|---------|
|Grupo de recursos de destino     | Pode optar por alterar o grupo de recursos de destino no qual será criada ésimo máquina. Como parte de reproteção, a máquina virtual de destino será eliminada, por conseguinte, pode escolher um novo grupo de recursos na qual pode criar a ativação pós-falha post VM         |
|Rede Virtual de destino     | Rede não pode ser alterada durante a reproteção jb. Para alterar a rede, Refazer o mapeamento da rede.         |
|Armazenamento de destino     | Pode alterar a conta de armazenamento para o qual a máquina virtual será criada ativação pós-falha de post.         |
|Armazenamento de cache     | Pode especificar uma conta de armazenamento de cache que será utilizada durante a replicação. Se com as predefinições, uma nova conta de armazenamento de cache será criada, se já existir.         |
|Conjunto de Disponibilidade     |Se a máquina virtual na Ásia Oriental faz parte de um conjunto de disponibilidade, pode escolher um conjunto de disponibilidade para a máquina virtual de destino no Sudeste asiático. As predefinições irão encontrar o conjunto de disponibilidade SEA existente e tentar utilizá-lo. Durante a personalização, pode especificar um conjunto de AV totalmente novo.         |


### <a name="what-happens-during-reprotect"></a>O que acontece durante reproteção?

Tal como após o primeiro ativar a proteção, seguem-se de artefactos obterem criados se utilizar as predefinições.
1. É criada uma conta de armazenamento de cache na região Ásia Oriental.
2. Se a conta de armazenamento de destino (a conta armazenamento original da VM Sudeste asiático) não existe, é criado um novo. O nome é uma conta de armazenamento da máquina virtual a Ásia Oriental com "asr" o sufixo.
3. Se o conjunto de destino AV não existir, e as predefinições detetar que tem de criar um novo conjunto de AV, em seguida, será criado como parte da tarefa de reproteção. Se tiver personalizado a reproteção, em seguida, o conjunto de AV selecionado será utilizado.
4.

Seguem-se a lista dos passos acontecer se acionar uma tarefa de reproteção. Este é o caso que a máquina de virtual do lado de destino existe.

1. Os artefactos necessários são criados como parte de reproteção. Se já existirem, em seguida, são reutilizados.
2. A máquina de virtual do lado (Sudeste asiático) de destino é primeiro desligada, se estiver em execução.
3. Disco de destino do lado da máquina virtual é copiado pelo Azure Site Recovery num contentor como um blob de seed.
4. A máquina de virtual do lado de destino, em seguida, é eliminada.
5. O blob de seed é utilizado pela máquina virtual do lado (Oriental) de origem atual para replicar. Isto garante que apenas deltas são replicadas.
6. As alterações principais entre o disco de origem e o blob de seed são sincronizadas. Esta operação pode demorar algum tempo a concluir.
7. Uma vez concluída a tarefa de reproteção, começa-se a replicação de diferenças que cria um ponto de recuperação de acordo com a política.

> [!NOTE]
> Não é possível proteger um nível de plano de recuperação. Apenas pode voltar a proteger a um nível VM por.

Depois da tarefa de reproteção concluída com êxito, a máquina virtual entrará num estado protegido.

## <a name="next-steps"></a>Passos seguintes

Depois da máquina virtual entrou num estado protegido, pode iniciar uma ativação pós-falha. A ativação pós-falha irá encerrar a máquina virtual numa região do Azure de Oriental e, em seguida, criar e arrancar a máquina virtual a região Sudeste asiático. Por conseguinte, não há um pequeno período de indisponibilidade para a aplicação. Por isso, escolha a hora de ativação pós-falha quando a aplicação pode tolerar um tempo de inatividade. Recomenda-se que teste primeiro a ativação pós-falha da máquina virtual para se certificar de que estará disponível corretamente, antes de iniciar uma ativação pós-falha.

-   [Passos para iniciar a ativação pós-falha da máquina virtual de teste](site-recovery-test-failover-to-azure.md)

-   [Para iniciar a ativação pós-falha da máquina virtual](site-recovery-failover.md)
