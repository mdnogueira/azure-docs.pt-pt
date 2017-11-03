---
title: "Como configurar a persistência de dados para uma Cache de Redis do Azure Premium"
description: "Saiba como configurar e gerir as instâncias de Cache de Redis do Azure Premium camada de persistência de dados"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: sdanie
ms.openlocfilehash: 638f0154d3a4fd091197a2da86374a053b31c4c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Como configurar a persistência de dados para uma Cache de Redis do Azure Premium
Cache de Redis do Azure tem ofertas de cache diferente que fornecem flexibilidade na escolha de funcionalidades, incluindo funcionalidades do escalão Premium, tais como clustering, persistência e suporte da virtual network e tamanho da cache. Este artigo descreve como configurar a persistência numa instância de Cache de Redis do Azure premium.

Para obter informações sobre outras funcionalidades de cache premium, consulte [introdução para o escalão Premium do Azure Redis Cache](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>O que é a persistência de dados?
[Persistência de redis](https://redis.io/topics/persistence) permite-lhe manter os dados armazenados no Redis. Também pode criar instantâneos e cópias de segurança de dados, o que pode carregar em caso de falha de hardware. Esta é das vantagens enorme através de escalão básicas ou Standard onde todos os dados são armazenadas na memória e só pode haver potencial perda de dados em caso de falha em que nós de Cache estão em baixo. 

Cache de Redis do Azure oferece a persistência de Redis utilizando os seguintes modelos:

* **Persistência RDB** -persistência quando RDB (base de dados de Redis) estiver configurada, a Cache de Redis do Azure persistir um instantâneo da cache de Redis num formato binário para o disco com base numa frequência de cópia de segurança configurável de Redis. Se ocorrer um evento de catastrófica que desativa a cache de primário e réplica, a cache é é reconstruir utilizando o instantâneo mais recente. Saiba mais sobre o [vantagens](https://redis.io/topics/persistence#rdb-advantages) e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) de persistência de RDB.
* **Persistência AOF** -persistência quando AOF (Append só ficheiros) estiver configurada, a Cache de Redis do Azure guarda cada operação de escrita para um registo que é guardado, pelo menos, uma vez por segundo para uma conta de armazenamento do Azure. Se ocorrer um evento de catastrófica que desativa a cache de primário e réplica, a cache é é reconstruir utilizando as operações de escrita armazenado. Saiba mais sobre o [vantagens](https://redis.io/topics/persistence#aof-advantages) e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) de persistência de AOF.

Persistência é configurada a partir de **nova Cache de Redis** painel durante a criação da cache e no **menu recursos** para premium existente coloca em cache.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois de um escalão de preços premium é selecionado, clique em **persistência de Redis**.

![Persistência de redis][redis-cache-persistence]

Os passos na secção seguinte descrevem como configurar a persistência de Redis na sua nova cache premium. Depois de ter configurada a persistência de Redis, clique em **criar** para criar a nova cache premium com a persistência de Redis.

## <a name="enable-redis-persistence"></a>Ativar a persistência de Redis

Redis persistência está ativada o **persistência de dados de Redis** painel escolhendo um **RDB** ou **AOF** persistência. Para as novas caches, este painel é acedido durante o processo de criação de cache, conforme descrito na secção anterior. Para caches existentes, o **persistência de dados de Redis** é acessível a partir do painel a **menu recursos** para a sua cache.

![Definições de redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Configurar a persistência RDB

Para ativar a persistência RDB, clique em **RDB**. Para desativar a persistência do RDB numa cache premium anteriormente ativado, clique em **desativado**.

![RDB persistência de redis][redis-cache-rdb-persistence]

Para configurar o intervalo de cópia de segurança, selecione um **frequência de cópia de segurança** na lista pendente. As opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**e **24 horas**. Este intervalo inicia contando após a conclusão com êxito a operação de cópia de segurança anterior e quando este período de uma nova cópia de segurança é iniciada.

Clique em **conta de armazenamento** para selecionar a conta de armazenamento a utilizar e escolha o o **chave primária** ou **chave secundária** para utilizar o **dechavedearmazenamento** pendente. Tem de escolher uma conta do storage na mesma região que a cache e um **armazenamento Premium** conta é recomendada porque o armazenamento premium tem um maior débito. 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência é novamente gerada, tem de reconfigurar a chave pretendida a **chave de armazenamento** pendente.
> 
> 

Clique em **OK** para guardar a configuração de persistência.

A próxima cópia de segurança (ou a primeira cópia de segurança para as novas caches) é iniciada depois de decorrido o intervalo de frequência de cópia de segurança.

## <a name="configure-aof-persistence"></a>Configurar a persistência AOF

Para ativar a persistência AOF, clique em **AOF**. Para desativar a persistência do AOF numa cache premium anteriormente ativado, clique em **desativado**.

![AOF persistência de redis][redis-cache-aof-persistence]

Para configurar a persistência de AOF, especifique um **primeira conta de armazenamento**. Esta conta de armazenamento tem de ser na mesma região que a cache e um **armazenamento Premium** conta é recomendada porque o armazenamento premium tem um maior débito. Opcionalmente, pode configurar uma conta de armazenamento adicional com o nome **segunda conta de armazenamento**. Se estiver configurada uma segunda conta de armazenamento, as operações de escrita para a cache de réplica são escritas esta segunda conta de armazenamento. Para cada conta de armazenamento configurados, escolha o **chave primária** ou **chave secundária** para utilizar o **chave de armazenamento** pendente. 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência é novamente gerada, tem de reconfigurar a chave pretendida a **chave de armazenamento** pendente.
> 
> 

Quando a persistência AOF está ativada, escreva operações para a cache são guardadas para a conta do storage designada (ou contas, se tiver configurado uma segunda conta de armazenamento). Se ocorrer uma falha catastrófica, que demora para baixo cache primário e de réplica, o registo AOF armazenado é utilizada para reconstruir a cache.

## <a name="persistence-faq"></a>Persistência FAQ
A lista seguinte contém as respostas a perguntas mais comuns sobre a persistência de Cache de Redis do Azure.

* [Pode ativar persistência numa cache criada anteriormente?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Pode ativar persistência AOF e RDB ao mesmo tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [O modelo de persistência devo escolher?](#which-persistence-model-should-i-choose)
* [O que acontece se posso ter ampliada para um tamanho diferente e é restaurar uma cópia de segurança que foi feita antes da operação de dimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Persistência RDB
* [Posso alterar a frequência de cópia de segurança RDB depois de criar a cache?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Por que motivo se tiver uma frequência de cópia de segurança RDB de 60 minutos há mais de 60 minutos entre cópias de segurança?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [O que acontece para cópias de segurança antigas do RDB quando é feita uma nova cópia de segurança?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Persistência AOF
* [Quando devo utilizar uma segunda conta de armazenamento?](#when-should-i-use-a-second-storage-account)
* [É o efeito de persistência de AOF ao longo, latência ou desempenho da minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Como posso remover a segunda conta de armazenamento?](#how-can-i-remove-the-second-storage-account)
* [O que é um reescrever e como afeta a minha cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [O que posso esperar quando dimensionamento uma cache com AOF ativado?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Como os meus dados AOF estão organizados no armazenamento?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Pode ativar persistência numa cache criada anteriormente?
Sim, a persistência de Redis pode ser configurada durante a criação de cache e no existente caches premium.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Pode ativar persistência AOF e RDB ao mesmo tempo?

Não, pode ativar apenas RDB ou AOF, mas não ambos simultaneamente.

### <a name="which-persistence-model-should-i-choose"></a>O modelo de persistência devo escolher?

Persistência AOF guarda cada escrita um registo, que tem algumas impacto sobre débito, comparada a persistência de RDB que guarda as cópias de segurança com base num intervalo de cópia de segurança configurado, com um impacto mínimo no desempenho. Escolha persistência AOF se o objetivo principal é para minimizar a perda de dados e pode processar um decréscimo no débito para a sua cache. Escolha persistência RDB se pretender manter o débito ideal em cache, mas ainda pretendem um mecanismo para recuperação de dados.

* Saiba mais sobre o [vantagens](https://redis.io/topics/persistence#rdb-advantages) e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) de persistência de RDB.
* Saiba mais sobre o [vantagens](https://redis.io/topics/persistence#aof-advantages) e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) de persistência de AOF.

Para obter mais informações sobre o desempenho ao utilizar a persistência AOF, consulte [efeito de persistência de Does AOF ao longo, latência ou desempenho da minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontece se posso ter ampliada para um tamanho diferente e é restaurar uma cópia de segurança que foi feita antes da operação de dimensionamento?

Para a persistência RDB e AOF:

* Se tiver ampliada para um tamanho maior, não há nenhum impacto sobre.
* Se tiver ampliada para um tamanho mais pequeno e tiver um personalizado [bases de dados](cache-configure.md#databases) que é maior do que o [limite de bases de dados](cache-configure.md#databases) para o novo tamanho, dados nessas bases de dados não são restaurados. Para obter mais informações, consulte [é meu bases de dados personalizadas definição afetados durante o dimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se tiver ampliada para um tamanho mais pequeno e não é suficiente espaço num pequeno para armazenar todos os dados da última cópia de segurança, as chaves serão expulso durante o processo de restauro, normalmente, a utilização de [allkeys lru](http://redis.io/topics/lru-cache) política de expulsão.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Posso alterar a frequência de cópia de segurança RDB depois de criar a cache?
Sim, pode alterar a frequência de cópia de segurança para a persistência RDB no **persistência de dados de Redis** painel. Para obter instruções, consulte [persistência Redis configurar](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que motivo se tiver uma frequência de cópia de segurança RDB de 60 minutos há mais de 60 minutos entre cópias de segurança?
O intervalo de frequência de cópia de segurança de persistência RDB não inicia até o processo de cópia de segurança anterior foi concluída com êxito. Se a frequência de cópia de segurança é de 60 minutos e demora um processo de cópia de segurança 15 minutos para concluir com êxito, a próxima cópia de segurança não iniciar até 75 minutos após a hora de início da cópia de segurança anterior.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>O que acontece para cópias de segurança antigas do RDB quando é feita uma nova cópia de segurança?
Todos os RDB persistência cópias de segurança, com exceção dos mais recentes são eliminadas automaticamente. Esta eliminação pode não ocorrer imediatamente, mas as cópias de segurança mais antigas não são mantidas indefinidamente.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando devo utilizar uma segunda conta de armazenamento?

Deve utilizar uma segunda conta de armazenamento para a persistência AOF quando considerar que tiver superior do que as operações de conjunto esperado na cache.  Configurar a conta de armazenamento secundário ajuda a garantir a que sua cache não atingir os limites de largura de banda de armazenamento.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>É o efeito de persistência de AOF ao longo, latência ou desempenho da minha cache?

Persistência AOF afetará débito por sobre 15% – 20% quando a cache é inferior a carga máxima (CPU e o servidor carregar em 90%). Deverá não existir problemas de latência quando a cache estiver dentro destes limites. No entanto, a cache será alcançar estes limites mais cedo com AOF ativada.

### <a name="how-can-i-remove-the-second-storage-account"></a>Como posso remover a segunda conta de armazenamento?

Pode remover a conta de armazenamento secundário de persistência de AOF definindo a segunda conta de armazenamento para ser o mesmo que a primeira conta de armazenamento. Para obter instruções, consulte [persistência configurar AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>O que é um reescrever e como afeta a minha cache?

Quando o ficheiro AOF passa a ser suficientemente grande, um reescrever automaticamente em fila na cache. Reescrever redimensiona o ficheiro AOF com o conjunto mínimo de operações necessárias para criar o conjunto de dados atual. Durante a reescritas, espera-se atingir os limites de desempenho mais cedo especialmente ao lidar com grandes conjuntos de dados. Reescritas ocorrerem inferior, muitas vezes, como o ficheiro AOF fica maior, mas irá demorar bastante tempo quando ocorre.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>O que posso esperar quando dimensionamento uma cache com AOF ativado?

Se o ficheiro AOF no momento da dimensionamento é significativamente grande, esperam que a operação de dimensionamento demorar mais do que o esperado, uma vez que esta será possível recarregar o ficheiro após terminar o dimensionamento.

Para obter mais informações sobre como aumentar, consulte [o que acontece se posso ter ampliada para um tamanho diferente e é restaurar uma cópia de segurança que foi feita antes da operação de dimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Como os meus dados AOF estão organizados no armazenamento?

Os dados armazenados nos ficheiros AOF estão divididos em várias blobs de páginas por nó para aumentar o desempenho de guardar os dados para o armazenamento. A tabela seguinte apresenta o número de blobs de páginas são utilizados para cada escalão de preço:

| Escalão Premium | Blobs |
|--------------|-------|
| P1           | 4 por ID de partição horizontal    |
| P2           | 8 por ID de partição horizontal    |
| P3           | 16 por ID de partição horizontal   |
| P4           | 20 por ID de partição horizontal   |

Quando clustering estiver ativado, cada partições horizontais na cache tem o próprio conjunto de blobs de páginas, conforme indicado na tabela anterior. Por exemplo, uma cache de P2 com três shards distribui respetivo ficheiro AOF 24 blobs de páginas (8 blobs por ID de partição horizontal, com 3 shards).

Após um reescrever, dois conjuntos de ficheiros AOF existem no armazenamento. Reescritas ocorrerem em segundo plano e de acréscimo para o primeiro conjunto de ficheiros, enquanto as operações de conjunto que são enviadas para a cache durante reescrever a acrescentar para o segundo conjunto. Uma cópia de segurança é armazenada temporariamente durante reescritas em caso de falha, mas é eliminada imediatamente após a conclusão de uma conversão.


## <a name="next-steps"></a>Passos seguintes
Saiba como utilizar as funcionalidades de cache do mais premium.

* [Introdução ao escalão Premium de Cache de Redis do Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
