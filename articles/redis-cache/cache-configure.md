---
title: Como configurar a Cache de Redis do Azure | Microsoft Docs
description: "Compreender a configuração de Redis predefinida para a Cache de Redis do Azure e saiba como configurar as instâncias de Cache de Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: sdanie
ms.openlocfilehash: 0274e58eb2e83202d4dbc58da0c67d0fdde22ede
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-azure-redis-cache"></a>Como configurar a Cache de Redis do Azure
Este tópico descreve como rever e atualizar a configuração das suas instâncias de Cache de Redis do Azure e abrange a configuração do servidor de Redis predefinido de instâncias de Cache de Redis do Azure.

> [!NOTE]
> Para obter mais informações sobre configurar e utilizar as funcionalidades de cache premium, consulte [como configurar a persistência](cache-how-to-premium-persistence.md), [como configurar o clustering](cache-how-to-premium-clustering.md), e [como configurar o suporte da Virtual Network](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Configurar definições da cache de Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Definições de Cache de Redis do Azure são visualizadas e configuradas no **a Cache de Redis** através do painel a **recursos Menu**.

![Definições da Cache de redis](./media/cache-configure/redis-cache-settings.png)

Pode ver e configurar as seguintes definições utilizando o **recursos Menu**.

* [Descrição geral](#overview)
* [Registo de atividades](#activity-log)
* [Controlo de acesso (IAM)](#access-control-iam)
* [Etiquetas](#tags)
* [Diagnosticar e resolver problemas](#diagnose-and-solve-problems)
* [Definições](#settings)
    * [Chaves de acesso](#access-keys)
    * [Definições avançadas](#advanced-settings)
    * [O Advisor de Cache de redis](#redis-cache-advisor)
    * [Dimensionamento](#scale)
    * [Tamanho do cluster de redis](#cluster-size)
    * [A persistência de dados de redis](#redis-data-persistence)
    * [Atualizações agendadas](#schedule-updates)
    * [Georreplicação](#geo-replication)
    * [Rede Virtual](#virtual-network)
    * [Firewall](#firewall)
    * [Propriedades](#properties)
    * [Bloqueia](#locks)
    * [Script de automatização](#automation-script)
* [Administração](#administration)
    * [Importar dados](#importexport)
    * [Exportar dados](#importexport)
    * [Reiniciar](#reboot)
* [Monitorização](#monitoring)
    * [Métricas de redis](#redis-metrics)
    * [Regras de alertas](#alert-rules)
    * [Diagnóstico](#diagnostics)
* [Suporte e as definições de resolução de problemas](#support-amp-troubleshooting-settings)
    * [Estado de funcionamento de recursos](#resource-health)
    * [Novo pedido de suporte](#new-support-request)


## <a name="overview"></a>Descrição geral

**Descrição geral** fornece-lhe informações básicas sobre a cache, como o nome, portas, métricas de cache selecionada e escalão de preço.

### <a name="activity-log"></a>Registo de atividades

Clique em **registo de atividade** para ver as ações executadas na sua cache. Também pode utilizar a filtragem para expandir esta vista para incluir outros recursos. Para obter mais informações sobre como trabalhar com registos de auditoria, consulte [auditar operações com o Resource Manager](../azure-resource-manager/resource-group-audit.md). Para obter mais informações sobre a monitorização de eventos de Cache de Redis do Azure, consulte [operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Controlo de acesso (IAM)

O **(IAM) do controlo de acesso** secção fornece suporte para o controlo de acesso baseado em funções (RBAC) no portal do Azure para ajudar as organizações que cumprem os requisitos de gestão de acesso simples e precisamente. Para obter mais informações, consulte [controlo de acesso baseado em funções no portal do Azure](../active-directory/role-based-access-control-configure.md).

### <a name="tags"></a>Etiquetas

O **etiquetas** secção ajuda a organizar os recursos. Para obter mais informações, consulte [utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

Clique em **diagnosticar e resolver problemas** devem ser fornecidas com estratégias e problemas comuns para resolvê-los.



## <a name="settings"></a>Definições
O **definições** secção permite-lhe aceder e configure as seguintes definições para a sua cache.

* [Chaves de acesso](#access-keys)
* [Definições avançadas](#advanced-settings)
* [O Advisor de Cache de redis](#redis-cache-advisor)
* [Dimensionamento](#scale)
* [Tamanho do cluster de redis](#cluster-size)
* [A persistência de dados de redis](#redis-data-persistence)
* [Atualizações agendadas](#schedule-updates)
* [Georreplicação](#geo-replication)
* [Rede Virtual](#virtual-network)
* [Firewall](#firewall)
* [Propriedades](#properties)
* [Bloqueia](#locks)
* [Script de automatização](#automation-script)



### <a name="access-keys"></a>Chaves de acesso
Clique em **chaves de acesso** para ver ou voltar a gerar as chaves de acesso para a sua cache. Estas chaves são utilizadas pelos clientes ao ligar à sua cache.

![As chaves de acesso da Cache de redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Definições avançadas
As seguintes definições são configuradas no **definições avançadas** painel.

* [Portas de acesso](#access-ports)
* [Políticas de memória](#memory-policies)
* [Notificações Keyspace (definições avançadas)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portas de acesso
Por predefinição, o acesso não SSL está desativado para as novas caches. Para ativar a porta não SSL, clique em **não** para **permitir o acesso apenas via SSL** no **definições avançadas** painel e clique em **guardar**.

![Portas de acesso de Cache de redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Políticas de memória
O **Maxmemory política**, **maxmemory-reservado**, e **reservados maxfragmentationmemory** definições o **definições avançadas** painel Configurar as políticas de memória para a cache.

![Política de Maxmemory de Cache de redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Política de Maxmemory** configura a política de expulsão para a cache e permite-lhe escolher entre as políticas de expulsão seguintes:

* `volatile-lru`-Esta é a predefinição.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para obter mais informações sobre `maxmemory` políticas, consulte [políticas de expulsão](http://redis.io/topics/lru-cache#eviction-policies).

O **reservados maxmemory** definição configura a quantidade de memória em MB que está reservado para operações de cache não tais como replicação durante a ativação pós-falha. A definição deste valor permite-lhe ter uma experiência mais consistente de servidor Redis ao varia de acordo com a carga. Este valor deve ser definido como superior para cargas de trabalho que são pesado de escrita. Quando a memória é reservada para operações, está disponível para armazenamento de dados em cache.

O **reservados maxfragmentationmemory** definição configura a quantidade de memória em MB que está reservado para acomodar a fragmentação de memória. A definição deste valor permite-lhe ter um servidor de Redis mais consistente experiência quando a cache está completa ou próximo completa e a fragmentação rácio também é elevado. Quando a memória é reservada para operações, está disponível para armazenamento de dados em cache.

Um aspeto a ter em consideração quando escolher um novo valor de reserva de memória (**reservados maxmemory** ou **reservados maxfragmentationmemory**) é a forma como esta alteração pode afetar uma cache que já está em execução com grandes quantidades de dados no mesmo. Por exemplo, se que tenha uma cache de 53 GB com 49 GB de dados, em seguida, altere o valor de reserva para 8 GB, irá remover a memória máxima disponível para o sistema para baixo até 45 GB. Se qualquer um dos atual `used_memory` ou seu `used_memory_rss` valores são superiores ao novo limite de 45 GB, em seguida, o sistema tem de expulsar dados até que ambas `used_memory` e `used_memory_rss` são abaixo 45 GB. Expulsão pode aumentar a fragmentação de memória e carga do servidor. Para mais informações sobre as métricas de cache, tais como `used_memory` e `used_memory_rss`, consulte [as métricas disponíveis e relatórios intervalos](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> O **reservados maxmemory** e **reservados maxfragmentationmemory** definições só estão disponíveis para Standard e Premium coloca em cache.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Notificações Keyspace (definições avançadas)
Redis keyspace notificações estão configuradas no **definições avançadas** painel. As notificações Keyspace permitem aos clientes receber notificações quando ocorrem determinados eventos.

![As definições avançada de Cache de redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Notificações Keyspace e **notificar-keyspace-eventos** definição só estão disponíveis para caches Standard e Premium.
> 
> 

Para obter mais informações, consulte [as notificações Keyspace de Redis](http://redis.io/topics/notifications). Para o código de exemplo, consulte o [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) ficheiros o [Olá mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplo.


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>O Advisor de Cache de redis
O **Advisor de Cache de Redis** painel apresenta as recomendações para a sua cache. Durante as operações normais, são apresentadas atualmente não existem recomendações. 

![Recomendações](./media/cache-configure/redis-cache-no-recommendations.png)

Caso se verifiquem as condições durante as operações da cache, tais como utilização elevada da memória, largura de banda de rede ou de carga do servidor, é apresentado um alerta no **a Cache de Redis** painel.

![Recomendações](./media/cache-configure/redis-cache-recommendations-alert.png)

Pode encontrar informações adicionais sobre o **recomendações** painel.

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Pode monitorizar estas métricas no [monitorização gráficos](cache-how-to-monitor.md#monitoring-charts) e [gráficos de utilização](cache-how-to-monitor.md#usage-charts) secções o **a Cache de Redis** painel.

Cada escalão de preço tem limites diferentes para ligações de cliente, memória e largura de banda. Se a cache se aproxima capacidade máxima para estas métricas através de um período de tempo prolongado, é criada uma recomendação. Para obter mais informações sobre as métricas e os limites revistos pelo **recomendações** ferramenta, consulte a tabela seguinte:

| Métrica da Cache de redis | Mais informações |
| --- | --- |
| Utilização de largura de banda de rede |[Desempenho de cache - largura de banda disponível](cache-faq.md#cache-performance) |
| Clientes ligados |[Configuração do servidor de predefinição Redis - maxclients](#maxclients) |
| Carga de servidor |[Gráficos de utilização - carga do servidor de Redis](cache-how-to-monitor.md#usage-charts) |
| Utilização de memória |[Desempenho de cache - tamanho](cache-faq.md#cache-performance) |

Para atualizar a cache, clique em **atualizar agora** para alterar o escalão de preço e [escala](#scale) sua cache. Para obter mais informações sobre como escolher um escalão de preço, consulte [que tamanho e a oferta da Cache de Redis devo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="scale"></a>Escala
Clique em **escala** para ver ou alterar o escalão de preço para a sua cache. Para obter mais informações sobre como aumentar, consulte [como Cache de Redis do Azure de escala](cache-how-to-scale.md).

![Escalão de preço de Cache de redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamanho do Cluster de redis
Clique em **tamanho do Cluster de Redis (pré-visualização)** para alterar o tamanho de cluster para uma execução cache premium com clustering ativada.

> [!NOTE]
> Tenha em atenção que enquanto o escalão Premium de Cache de Redis do Azure foi lançado para disponibilidade geral, a funcionalidade de tamanho de Cluster de Redis está atualmente em pré-visualização.
> 
> 

![Tamanho do cluster de redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, utilize o controlo de deslize ou escreva um número entre 1 e 10 no **contagem de partições horizontais** caixa de texto e clique em **OK** para guardar.

> [!IMPORTANT]
> Clustering de redis está disponível apenas para Premium caches. Para obter mais informações, veja [Como configurar o clustering de uma Cache de Redis do Azure Premium](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>A persistência de dados de redis
Clique em **persistência de dados de Redis** para ativar, desativar ou configurar a persistência de dados para a sua cache premium. Cache de Redis do Azure oferece a persistência de Redis através de [persistência RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) ou [persistência AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Para obter mais informações, consulte [como configurar a persistência para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> A persistência de dados redis está disponível apenas para Premium caches. 
> 
> 

### <a name="schedule-updates"></a>Atualizações agendadas
O **agendar atualizações** painel permite-lhe designar uma janela de manutenção para atualizações do servidor de Redis para a sua cache. 

> [!IMPORTANT]
> A janela de manutenção só se aplica a Redis atualizações do servidor e não para qualquer Azure atualizações ou atualizações do sistema operativo das VMs que a cache do anfitrião.
> 
> 

![Atualizações agendadas](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias pretendidos e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. Tenha em atenção que o tempo de janela de manutenção está em UTC. 

> [!IMPORTANT]
> O **agendar atualizações** funcionalidade só está disponível para caches de escalão Premium. Para obter mais informações e instruções, consulte [administração da Cache de Redis do Azure - agendar atualizações](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Georreplicação

O **georreplicação** painel fornece um mecanismo para ligar duas instâncias de Cache de Redis do Azure do escalão Premium. Uma cache está designado como cache de principal ligada e outra como a secundária cache ligada. A cache ligada secundária torna-se só de leitura e os dados escritos para a cache do principal são replicados para a cache ligada secundária. Esta funcionalidade pode ser utilizada para replicar uma cache em regiões do Azure.

> [!IMPORTANT]
> **Replicação geográfica** só está disponível para caches de escalão Premium. Para obter mais informações e instruções, consulte [como configurar a replicação geográfica para a Cache de Redis do Azure](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Rede Virtual
O **rede Virtual** secção permite-lhe configurar as definições de rede virtual para a sua cache. Para obter informações sobre como criar uma cache premium com a VNET suporta e atualizar as definições, consulte [como configurar o suporte de rede Virtual para uma Cache de Redis do Azure Premium](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Definições de rede virtual só estão disponíveis para caches premium que foram configuradas com suporte VNET durante a criação da cache. 
> 
> 

### <a name="firewall"></a>Firewall

Clique em **Firewall** para ver e configurar regras de firewall para a Cache de Redis do Azure Premium.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Pode especificar as regras de firewall com um intervalo de endereços IP de início e de fim. Quando as regras de firewall estão configuradas, apenas as ligações de cliente de intervalos de endereços IP especificados podem ligar à cache. Quando uma regra de firewall é guardada há um pequeno atraso antes da regra está em vigor. Este atraso é, normalmente, menos de um minuto.

> [!IMPORTANT]
> Ligações a partir da monitorização de sistemas a Cache de Redis do Azure são sempre permitidas, mesmo se as regras de firewall estão configuradas.
> 
> Regras de firewall só estão disponíveis para caches de escalão Premium.
> 
> 

### <a name="properties"></a>Propriedades
Clique em **propriedades** para ver informações sobre a cache, incluindo as portas e o ponto final da cache.

![Propriedades da Cache de redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Bloqueios
O **bloqueia** secção permite-lhe bloquear uma subscrição, grupo de recursos ou recursos para impedir que outros utilizadores na sua organização acidentalmente eliminem ou modificar a recursos críticos. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Script de automatização

Clique em **scripts de automatização** para criar e exportar um modelo dos recursos implementados para implementações futuras. Para obter mais informações sobre como trabalhar com modelos, consulte [implementar recursos com modelos Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Definições de administração
As definições no **administração** secção permitem-lhe realizar as seguintes tarefas administrativas para a sua cache. 

![Administração](./media/cache-configure/redis-cache-administration.png)

* [Importar dados](#importexport)
* [Exportar dados](#importexport)
* [Reiniciar](#reboot)


### <a name="importexport"></a>Importação/Exportação
Importar/exportar é uma operação de gestão de dados de Cache de Redis do Azure, que permite-lhe importar e exportar dados na cache por importar e exportar um instantâneo de base de dados de Cache de Redis (RDB) de uma cache premium para um blob de página numa conta de armazenamento do Azure. Importar/exportar permite-lhe migrar entre instâncias diferentes de Cache de Redis do Azure ou povoar a cache com os dados antes de utilização.

Importação pode ser utilizada para colocar ficheiros RDB compatíveis Redis a partir de qualquer servidor Redis em execução em qualquer nuvem ou o ambiente, incluindo Redis em execução no Linux, Windows ou qualquer fornecedor de nuvem como Amazon Web Services e outros. Importação de dados é uma forma fácil de criar uma cache com dados pré-preenchidos. Durante o processo de importação, a Cache de Redis do Azure carrega os ficheiros RDB storage do Azure para memória e, em seguida, insere as chaves para a cache.

Exportação permite-lhe exportar os dados armazenados na Cache de Redis do Azure para ficheiros RDB compatíveis de Redis. Pode utilizar esta funcionalidade para mover dados de uma instância da Cache de Redis do Azure para outra ou para outro servidor de Redis. Durante o processo de exportação, um ficheiro temporário é criado na VM que aloja a instância de servidor de Cache de Redis do Azure e o ficheiro é carregado para a conta de armazenamento designada. Quando concluir a operação de exportação com o estado de sucesso ou falha, o ficheiro temporário é eliminado.

> [!IMPORTANT]
> Importar/exportar só está disponível para caches de escalão Premium. Para obter mais informações e instruções, consulte [importar e exportar dados na Cache de Redis do Azure](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Reiniciar
O **reiniciar** painel permite-lhe reiniciar os nós da cache. Esta capacidade de reinício permite-lhe testar a aplicação para resiliência se ocorrer uma falha de um nó de cache.

![Reiniciar](./media/cache-configure/redis-cache-reboot.png)

Se tiver uma cache premium com clustering ativado, pode selecionar quais shards da cache para reiniciar o computador.

![Reiniciar](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reiniciar um ou mais nós da cache, selecione os nós pretendido e clique em **reiniciar**. Se tiver uma cache premium com clustering ativada, selecione o shard(s) para reiniciar o computador e, em seguida, clique em **reiniciar**. Após alguns minutos, o reinício de nós selecionados e são cópia online alguns minutos depois.

> [!IMPORTANT]
> Reiniciar o computador está agora disponível para todos os escalões de preços. Para obter mais informações e instruções, consulte [administração da Cache de Redis do Azure - reinício](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitorização

O **monitorização** secção permite-lhe configurar o diagnóstico e monitorização para a Cache de Redis. Para obter mais informações sobre a Cache de Redis do Azure de monitorização e diagnóstico, consulte [como monitorizar a Cache de Redis do Azure](cache-how-to-monitor.md).

![Diagnóstico](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas de redis](#redis-metrics)
* [Regras de alertas](#alert-rules)
* [Diagnóstico](#diagnostics)

### <a name="redis-metrics"></a>Métricas de redis
Clique em **Redis métricas** para [ver métricas](cache-how-to-monitor.md#view-cache-metrics) para a sua cache.

### <a name="alert-rules"></a>Regras de alertas

Clique em **regras de alerta** para configurar alertas com base nas métricas de Cache de Redis. Para obter mais informações, consulte [alertas](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnóstico

Por predefinição, as métricas de cache no Monitor do Azure são [armazenadas durante 30 dias](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) e, em seguida, eliminados. Para manter as suas métricas de cache durante um período superior a 30 dias, clique em **diagnóstico** para [configurar a conta de armazenamento](cache-how-to-monitor.md#export-cache-metrics) utilizada para armazenar o diagnóstico da cache.

>[!NOTE]
>Para além de arquivar as métricas de cache para o armazenamento, pode também [transmiti-los para um hub de eventos ou enviá-los para análise de registos](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

## <a name="support--troubleshooting-settings"></a>Suporte e as definições de resolução de problemas
As definições no **suporte + resolução de problemas** secção fornecer-lhe opções para resolver problemas com a cache.

![Suporte + resolução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Estado de funcionamento de recursos](#resource-health)
* [Novo pedido de suporte](#new-support-request)

### <a name="resource-health"></a>Estado de funcionamento de recursos
**Estado de funcionamento do recurso** monitoriza os seus recursos e indica o se estiver em execução conforme esperado. Para obter mais informações sobre o serviço de estado de funcionamento de recursos do Azure, consulte [descrição geral do Estado de funcionamento de recursos do Azure](../resource-health/resource-health-overview.md).

> [!NOTE]
> Estado de funcionamento do recurso é não consegue gerar relatórios sobre o estado de funcionamento de instâncias de Cache de Redis do Azure alojada numa rede virtual. Para obter mais informações, consulte [todas as funcionalidades de cache funcionam quando alojam uma cache numa VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Novo pedido de suporte
Clique em **novo pedido de suporte** para abrir um pedido de suporte para a sua cache.





## <a name="default-redis-server-configuration"></a>Configuração predefinida do servidor de Redis
Novas instâncias de Cache de Redis do Azure estão configuradas com os seguintes valores de configuração predefinida de Redis.

> [!NOTE]
> Não não possível alterar as definições nesta secção utilizando o `StackExchange.Redis.IServer.ConfigSet` método. Se este método é denominado com um dos comandos nesta secção, é emitida uma exceção semelhante ao seguinte:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Os valores que são configuráveis, tal como **política de memória máxima**, são configuráveis através do portal do Azure ou ferramentas de gestão da linha de comandos, como o Azure CLI ou PowerShell.
> 
> 

| Definição | Valor predefinido | Descrição |
| --- | --- | --- |
| `databases` |16 |O número predefinido de bases de dados é 16 mas também pode configurar um número diferente com base no escalão de preço. <sup>1</sup> a base de dados predefinida é a base de dados 0, pode selecionar uma base de por ligação utilizando outra `connection.GetDatabase(dbid)` onde `dbid` é um número entre `0` e `databases - 1`. |
| `maxclients` |Depende o escalão de preço<sup>2</sup> |Este é o número máximo de clientes ligados permitido ao mesmo tempo. Assim que for atingido o limite de Redis fechar todas as novas ligações, devolvendo um erro de 'atingido o número máximo de clientes'. |
| `maxmemory-policy` |`volatile-lru` |Política de Maxmemory destina-se a definição como Redis seleciona que remova quando `maxmemory` (o tamanho da cache que selecionou quando criou a cache da oferta) foi atingido. Com a Cache de Redis do Azure é a predefinição `volatile-lru`, que remove as chaves com uma expiração definida utilizando um algoritmo LRU. Esta definição pode ser configurada no portal do Azure. Para obter mais informações, consulte [políticas memória](#memory-policies). |
| `maxmemory-samples` |3 |Para guardar a memória, LRU e algoritmos TTL mínimo são approximated algoritmos em vez de algoritmos precisos. Por predefinição Redis três chaves verificações e escolhe que foi utilizado recentemente inferior. |
| `lua-time-limit` |5,000 |Tempo de execução máxima de um script de Lua em milissegundos. Se o tempo de execução máximo for atingido, os registos de Redis que um script ainda está em execução após o tempo máximo permitido e começa a responder às consultas com um erro. |
| `lua-event-limit` |500 |Tamanho máximo da fila de eventos de script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Os limites de memória intermédia de saída do cliente podem ser utilizados para forçar a interrupção de ligação de clientes que não são leitura de dados do servidor suficientemente rápido por algum motivo (um motivo comum é que um cliente Pub/Sub não pode consumir mensagens tão rápidas como publicador pode produzi-las). Para obter mais informações, consulte [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>o limite de `databases` é diferente para cada Cache de Redis do Azure escalão de preço e pode ser definido durante a criação da cache. Se não `databases` definição for especificada durante a criação da cache, a predefinição é de 16.

* Básico e padrão caches
  * C0 cache (250 MB) - até 16 bases de dados
  * C1 cache (1 GB) - até 16 bases de dados
  * C2 cache (2,5 GB) - até 16 bases de dados
  * C3 cache (6 GB) - até 16 bases de dados
  * C4 cache (13 GB) - até 32 bases de dados
  * C5 cache (26 GB) - até 48 bases de dados
  * C6 cache (53 GB) - até 64 bases de dados
* Premium caches
  * P1 (6 GB - 60 GB) - até 16 bases de dados
  * P2 (13 GB - 130 GB) - até 32 bases de dados
  * P3 (GB de 26-260 GB) - até 48 bases de dados
  * P4 (53 GB - 530 GB) - até 64 bases de dados
  * Todas as caches premium com o cluster de Redis ativados - o cluster de Redis só suporta a utilização da base de dados 0 o `databases` limite para qualquer cache premium com o cluster de Redis ativada eficazmente é 1 e o [selecione](http://redis.io/commands/select) comando não é permitido. Para obter mais informações, consulte [é necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Para mais informações sobre bases de dados, consulte [quais são as bases de dados de Redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> O `databases` definição pode ser configurada apenas durante a criação da cache e apenas com o PowerShell, CLI ou outros clientes de gestão. Para obter um exemplo de configuração `databases` durante a criação de cache com o PowerShell, consulte [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` é diferente para cada Cache de Redis do Azure escalão de preço.

* Básico e padrão caches
  * C0 cache (250 MB) - até 256 ligações
  * C1 cache (1 GB) - até 1000 ligações
  * C2 cache (2,5 GB) - até 2000 ligações
  * C3 cache (6 GB) - até 5000 ligações
  * C4 cache (13 GB) - até 10 000 ligações
  * C5 cache (26 GB) - até 15 000 ligações
  * C6 cache (53 GB) - até 20.000 ligações
* Premium caches
  * P1 (6 GB - 60 GB) - até 7.500 ligações
  * P2 (13 GB - 130 GB) - até 15 000 ligações
  * P3 (GB de 26-260 GB) - até 30 000 ligações
  * P4 (53 GB - 530 GB) - até 40 000 ligações

> [!NOTE]
> Enquanto cada tamanho da cache do permite *até* um determinado número de ligações, cada ligação Redis sobrecarga associado ao mesmo. Um exemplo deste tipo sobrecarga seria a utilização da memória e CPU devido a encriptação de TLS/SSL. O limite máximo de ligação para um tamanho de cache fornecido assume uma cache ligeiramente carregada. Se carregar de sobrecarga de ligação *plus* carga de operações do cliente excede a capacidade para o sistema, a cache pode ter problemas de capacidade, mesmo que não excedeu o limite de ligação para o tamanho atual da cache.
> 
> 



## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis comandos não suportados na Cache de Redis do Azure
> [!IMPORTANT]
> Porque a configuração e gestão de instâncias de Cache de Redis do Azure é gerida pela Microsoft, os comandos seguintes estão desativados. Se tentar invoke-los, receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIGURAÇÃO
> * DEPURAÇÃO
> * MIGRAR
> * GUARDAR
> * ENCERRAMENTO
> * SLAVEOF
> * CLUSTER - Cluster escrita comandos estão desativados mas Cluster só de leitura, os comandos permitidos.
> 
> 

Para obter mais informações sobre os comandos de Redis, consulte [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Consola de redis
Pode emitir comandos em segurança para as instâncias de Cache de Redis do Azure utilizando o **consola Redis**, que está disponível no portal do Azure para todos os escalões de cache.

> [!IMPORTANT]
> - A consola Redis não funciona com [VNET](cache-how-to-premium-vnet.md). Quando a cache faz parte de uma VNET, apenas os clientes na VNET podem aceder à cache. Uma vez consola Redis é executado no seu browser local, o que está fora da VNET, não é possível ligar à sua cache.
> - Nem todos os comandos de Redis são suportados na Cache de Redis do Azure. Para obter uma lista de comandos de Redis que se encontram desativados para a Cache de Redis do Azure, consulte o artigo anterior [Redis comandos não suportados na Cache de Redis do Azure](#redis-commands-not-supported-in-azure-redis-cache) secção. Para obter mais informações sobre os comandos de Redis, consulte [http://redis.io/commands](http://redis.io/commands).
> 
> 

Para aceder à consola Redis, clique em **consola** do **a Cache de Redis** painel.

![Consola de redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos contra a instância da cache, basta escreva o comando pretendido para a consola.

![Consola de redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Utilizando a consola Redis com uma cache premium em cluster

Quando a cache de cluster utilizando a consola Redis com um premium, pode emitir comandos para um único ID de partição horizontal da cache. Emitir um comando para um ID de partição horizontal específico, ligam pela primeira vez para o ID de partição horizontal pretendido clicando no selecionador de partições horizontais.

![Consola de redis](./media/cache-configure/redis-console-premium-cluster.png)

Se tentar aceder a uma chave que esteja armazenada num partições horizontais diferente que o ID de partição horizontal ligado, receberá uma mensagem de erro semelhante a mensagem seguinte.

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

No exemplo anterior, partições horizontais 1 é a partições horizontais selecionado, mas `myKey` está localizado em partições horizontais 0, conforme indicado pelo `(shard 0)` parte da mensagem de erro. Neste exemplo, para aceder à `myKey`, selecione de partições horizontais 0 utilizando o selecionador de partições horizontais e, em seguida, emita o comando pretendido.


## <a name="move-your-cache-to-a-new-subscription"></a>Mover a sua cache para uma nova subscrição
Pode mover a cache para uma nova subscrição clicando **mover**.

![Mover a Cache de Redis](./media/cache-configure/redis-cache-move.png)

Para informações sobre como mover os recursos de um grupo de recursos para outro e de uma subscrição para outro, consulte [mover recursos para o novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como trabalhar com os comandos de Redis, consulte [como executar comandos de Redis?](cache-faq.md#how-can-i-run-redis-commands)

