---
title: "Como configurar a replicação geográfica para a Cache de Redis do Azure | Microsoft Docs"
description: "Saiba como replicar as instâncias de Cache de Redis do Azure em regiões geográficas."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: sdanie
ms.openlocfilehash: 332326ce4188385aa6e569c812e16c3daa68bd5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Como configurar a replicação geográfica para a Cache de Redis do Azure

A georreplicação fornece um mecanismo para ligar duas instâncias de Cache de Redis do Azure do escalão Premium. Uma cache está designado como cache de principal ligada e outra como a secundária cache ligada. A cache ligada secundária torna-se só de leitura e os dados escritos para a cache do principal são replicados para a cache ligada secundária. Esta funcionalidade pode ser utilizada para replicar uma cache em regiões do Azure. Este artigo fornece um guia para configurar a georreplicação das suas instâncias de Cache de Redis do Azure do escalão Premium.

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de georreplicação

Para configurar a georreplicação entre duas caches, devem ser cumpridos os seguintes pré-requisitos:

- Tem de ser ambas as caches [escalão Premium](cache-premium-tier-intro.md) coloca em cache.
- Tem de ser ambas as caches na mesma subscrição do Azure.
- A cache ligada secundária tem de ser o mesmo escalão de preço ou um escalão de preço superior à cache de principal ligado.
- Se a cache de ligado primária tiver clustering ativada, a cache ligada secundária tem de ter clustering ativada com o mesmo número de partições horizontais como cache de principal ligado.
- Ambas as caches tem de ser criados e num Estado de execução.
- Persistência não deve ser ativada em qualquer um dos cache.
- A georreplicação entre caches na mesma VNET é suportada. Também é suportada a georreplicação entre caches em VNETs diferentes, desde que as duas VNETs estão configuradas de forma a que recursos as VNETs estão capazes de alcançar entre si através de ligações TCP.

Depois da georreplicação está configurada, as seguintes restrições aplicam-se a par da cache ligado:

- A cache secundária ligado é só de leitura; Pode ler a partir do mesmo, mas não é possível escrever os dados. 
- Todos os dados que se encontrava na cache ligada secundária antes de é adicionada a ligação são removidos. Se a georreplicação, subsequentemente, é removida no entanto, os dados replicados permanecem na cache secundária ligada.
- Não é possível iniciar um [dimensionamento operação](cache-how-to-scale.md) em qualquer um dos cache ou [alterar o número de partições horizontais](cache-how-to-premium-clustering.md) se a cache tiver clustering ativada.
- Não é possível ativar a persistência na cache.
- Pode utilizar [exportar](cache-how-to-import-export-data.md#export) com a cache, mas só pode [importação](cache-how-to-import-export-data.md#import) na cache de principal ligada.
- Não é possível eliminar cache ligado ou o grupo de recursos que contém os mesmos, até que remova a ligação de replicação geográfica. Para obter mais informações, consulte [por que motivo a operação falhar quando estava a eliminar o meu ligado cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se as dois caches estiverem em regiões diferentes, os custos de saída de rede aplicam-se aos dados replicados em regiões à cache secundária ligada. Para obter mais informações, consulte [quanto does-lo de custos para replicar os meus dados em regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Não há nenhum ativação pós-falha automática à cache ligada secundária se a cache do principal (e a respetiva réplica) forem abaixo. Por ordem para aplicações de cliente de ativação pós-falha, terá de remover a ligação de replicação geográfica e as aplicações de cliente para a cache que foi anteriormente ligado cache secundária do ponto manualmente. Para obter mais informações, consulte [como falhar ao longo para a cache ligada secundária funciona?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicionar uma ligação de replicação geográfica

1. Para ligar duas caches premium em conjunto para georreplicação, clique em **georreplicação** no menu de recurso da cache pretendida como principal ligado colocar em cache e, em seguida, clique em **adicionar ligação de replicação de cache** do **georreplicação** painel.

    ![Adicionar ligação](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome da cache secundária pretendido do **caches compatíveis** lista. Se a cache pretendida não for apresentada na lista, certifique-se de que o [pré-requisitos georreplicação](#geo-replication-prerequisites) para a cache pretendida secundária são cumpridos. Para filtrar as caches por região, clique em região pretendida no mapa para visualizar apenas esses caches no **caches compatíveis** lista.

    ![Caches compatíveis georreplicação](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Também pode iniciar o processo de ligação ou ver detalhes sobre a cache secundária utilizando o menu de contexto.

    ![Menu de contexto georreplicação](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Clique em **ligação** para associar as duas caches em conjunto e iniciar o processo de replicação.

    ![Caches de ligação](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Pode ver o progresso do processo de replicação no **georreplicação** painel.

    ![Estado da ligação](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Também pode ver o estado da ligação no **descrição geral** painel caches primários e secundários.

    ![Estado da cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Assim que o processo de replicação estiver concluído, o **ligação estado** alterações **com êxito**.

    ![Estado da cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Durante o processo de ligação, a cache ligada primária permanece disponível para utilização, mas a cache ligada secundária não está disponível até concluir o processo de ligação.

## <a name="remove-a-geo-replication-link"></a>Remover uma ligação de replicação geográfica

1. Para remover a ligação entre dois caches e pare a replicação geográfica, clique em **desassociar caches** do **georreplicação** painel.
    
    ![Desassociar caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Quando tiver concluído o processo de unlinking, a cache secundária está disponível para leituras e escritas.

>[!NOTE]
>Quando a ligação de replicação geográfica for removida, os dados replicados da cache de principal ligado permanecem na cache secundária.
>
>

## <a name="geo-replication-faq"></a>Replicação geográfica FAQ

- [Pode utilizar a georreplicação com uma cache de escalão Standard ou Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [A minha cache está disponível para utilização durante o processo de ligação ou unlinking?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Posso ligar mais de dois caches em conjunto?](#can-i-link-more-than-two-caches-together)
- [Pode ligar duas caches de diferentes subscrições do Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Pode ligar duas caches com tamanhos diferentes?](#can-i-link-two-caches-with-different-sizes)
- [Pode utilizar a georreplicação com clustering ativado?](#can-i-use-geo-replication-with-clustering-enabled)
- [Pode utilizar a georreplicação com os meus caches numa VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [O que é a agenda de replicação de Redis georreplicação?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto replicação georreplicação necessário?](#how-long-does-geo-replication-replication-take)
- [O ponto de recuperação de replicação é garantido?](#is-the-replication-recovery-point-guaranteed)
- [Posso utilizar o PowerShell ou a CLI do Azure para gerir a georreplicação?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto-lo de custos para replicar os meus dados em regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Por que motivo a operação falhar quando estava a eliminar o meu ligado cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Que região deve utilizar para a minha cache ligado secundária?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Como funciona a falhar ao longo para a cache ligada secundária?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Pode utilizar a georreplicação com uma cache de escalão Standard ou Basic?

Não, georreplicação está disponível apenas para caches de escalão Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>A minha cache está disponível para utilização durante o processo de ligação ou unlinking?

- Quando ligar duas caches em conjunto para georreplicação, a cache ligada primária permanece disponível para utilização, mas a cache ligada secundária não está disponível até concluir o processo de ligação.
- Ao remover a ligação de replicação geográfica entre duas caches, ambas as caches permanecem disponíveis para utilização.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso ligar mais de dois caches em conjunto?

Não, quando utilizar a georreplicação pode apenas ligar duas caches em conjunto.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Pode ligar duas caches de diferentes subscrições do Azure?

Não, ambas as caches tem de ser na mesma subscrição do Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Pode ligar duas caches com tamanhos diferentes?

Sim, desde que a cache ligada secundária é maior do que a cache principal ligada.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Pode utilizar a georreplicação com clustering ativado?

Sim, desde que ambas as caches tenham o mesmo número de partições horizontais.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Pode utilizar a georreplicação com os meus caches numa VNET?

Sim, a georreplicação das caches em VNETs são suportados. 

- A georreplicação entre caches na mesma VNET é suportada.
- Também é suportada a georreplicação entre caches em VNETs diferentes, desde que as duas VNETs estão configuradas de forma a que recursos as VNETs estão capazes de alcançar entre si através de ligações TCP.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>O que é a agenda de replicação de Redis georreplicação?

A replicação não ocorrer numa agenda específica, é revertidos contínua e assíncrona todas as escritas feitas para primário instantaneamente assíncrona são replicadas no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto replicação georreplicação necessário?

A replicação está incremental, assíncrono e contínua e o tempo que demora, normalmente, não é muito diferente de latência de em regiões. Em determinadas circunstâncias, em determinadas alturas secundário poderá ser necessário fazer uma sincronização completa dos dados a partir de principal. O tempo de replicação, neste caso é dependente do número de fatores, como: carga sobre a cache principal, a largura de banda disponível na máquina de cache, inter-latência de região etc. Por exemplo, com base em alguns testes encontrámos saída essa hora de replicação para uma completo 53 GB georreplicação emparelhe no Leste dos EUA e regiões EUA oeste podem estar em qualquer lugar entre 5 a 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação de replicação é garantido?

Atualmente, para caches no modo de georreplicação, persistência e importar/exportar funcionalidade está desativada. Para que, no caso de um cliente iniciou a ativação pós-falha ou em casos onde uma ligação de replicação foi interrompida entre o par georreplicação, secundário irá reter de memória dados tem ser sincronizados a partir de principal até esse ponto de tempo. Não há nenhuma garantia de ponto de recuperação fornecida estas situações.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso utilizar o PowerShell ou a CLI do Azure para gerir a georreplicação?

Neste momento, só pode gerir a georreplicação no portal do Azure.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto-lo de custos para replicar os meus dados em regiões do Azure?

Quando utilizar a georreplicação, os dados da cache de principal ligado são replicados para a cache ligada secundária. Se as caches ligadas dois estiverem na mesma região do Azure, há sem encargos durante a transferência de dados. Se as caches ligadas dois estiverem em diferentes regiões do Azure, a taxa de transferência de dados de Georreplicação é o custo de largura de banda de replicar os dados para outro região do Azure. Para obter mais informações, consulte [detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Por que motivo a operação falhar quando estava a eliminar o meu ligado cache?

Quando dois caches ligadas em conjunto, não é possível eliminar a cache ou o grupo de recursos que contém-los até que remova a ligação de replicação geográfica. Se tentar eliminar o grupo de recursos que contém uma ou ambas as caches ligadas, os outros recursos no grupo de recursos são eliminados, mas o grupo de recursos permanece no `deleting` caches de estado e quaisquer ligados do grupo de recursos permanecem no `running` estado. Para concluir a eliminação do grupo de recursos e as caches ligadas dentro da mesma, quebrar a ligação de replicação geográfica conforme descrito em [remover uma ligação de replicação geográfica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Que região deve utilizar para a minha cache ligado secundária?

Em geral, é recomendado para a sua cache existir na mesma região do Azure da aplicação que acede ao mesmo. Se a sua aplicação tiver uma região primária e de contingência, as caches primários e secundários devem existir nessas regiões mesmos. Para obter mais informações sobre regiões emparelhadas, consulte [melhores práticas – regiões do Azure emparelhado](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como funciona a falhar ao longo para a cache ligada secundária?

A versão inicial do georreplicação, a Cache de Redis do Azure não suporta ativação pós-falha automática em regiões do Azure. Replicação geográfica é utilizada principalmente num cenário de recuperação após desastre. Num cenário de recuperação distater, os clientes devem trazer a pilha de aplicação completo numa região de cópia de segurança na forma coordenada em vez de permitir que os componentes de aplicações individuais decidir quando mudar para as respetivas cópias de segurança por si próprios. Isto é especialmente relevante para Redis. Uma das principais vantagens do Redis é o que é um arquivo de muito baixa latência. Se Redis utilizado por uma aplicação falhar noutra região do Azure, mas não a camada de computação, o tempo adicionado ida e volta teria um impacto evidente no desempenho. Por este motivo, gostaríamos de evitar a falha de Redis através de automaticamente devido a problemas de disponibilidade transitório.

Atualmente, para iniciar a ativação pós-falha, terá de remover a ligação de replicação geográfica no portal do Azure e, em seguida, altere o ponto final de ligação no cliente Redis da cache de principal ligada à cache secundária (anteriormente ligada). Quando as duas caches são desassociar, a réplica torna-se de uma cache de leitura e escrita regular novamente e aceita os pedidos diretamente a partir de clientes de Redis.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [escalão Premium do Azure Redis Cache](cache-premium-tier-intro.md).

