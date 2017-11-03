---
title: Como monitorizar a Cache de Redis do Azure | Microsoft Docs
description: "Saiba como monitorizar o estado de funcionamento e desempenho as instâncias de Cache de Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: sdanie
ms.openlocfilehash: 8996f5ce03e39557d9cc9c3de1ec214f5cd664b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-azure-redis-cache"></a>Como monitorizar a Cache de Redis do Azure
Cache de Redis do Azure utiliza [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ao fornecer várias opções para monitorizar as instâncias de cache. Pode ver as métricas, afixar gráficos de métricas ao Startboard, personalizar o intervalo de data e hora de monitorização de gráficos, adicionar e remover as métricas de gráficos e definir alertas quando forem cumpridas determinadas condições. Estas ferramentas permitem-lhe monitorizar o estado de funcionamento das suas instâncias de Cache de Redis do Azure e ajudar a gerir as suas aplicações de colocação em cache.

As métricas das instâncias de Cache de Redis do Azure são recolhidas através de Redis [informações](http://redis.io/commands/info) comando aproximadamente duas vezes por minuto e automaticamente armazenadas durante 30 dias (consulte [exportar métricas de cache](#export-cache-metrics) para configurar um política de retenção diferentes) para que possa ser apresentados nos gráficos métricas e avaliados pelas regras de alerta. Para obter mais informações sobre os diferentes valores de informações utilizadas para cada métrica da cache, consulte [as métricas disponíveis e relatórios intervalos](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Para ver métricas de cache, [procurar](cache-configure.md#configure-redis-cache-settings) para a instância da cache no [portal do Azure](https://portal.azure.com).  Cache de Redis do Azure fornece algumas gráficos incorporados no **descrição geral** painel e **Redis métricas** painel. Cada gráfico pode ser personalizado ao adicionar ou remover métricas e alterar o intervalo de relatórios.

![Métricas de redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Ver gráficos de métricas de pré-configurado

O **descrição geral** painel tem o seguinte pré-configurados gráficos de monitorização.

* [Gráficos de monitorização](#monitoring-charts)
* [Gráficos de utilização](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitorização
O **monitorização** secção o **descrição geral** painel tem **pedidos com êxito e pedidos sem êxito**, **obtém e define**, **ligações**, e **comandos Total** gráficos.

![Gráficos de monitorização](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gráficos de utilização
O **utilização** secção o **descrição geral** painel tem **carga do servidor de Redis**, **utilização de memória**, **rede certifique**, e **utilização da CPU** gráficos e apresenta também o **escalão de preço** para a instância da cache.

![Gráficos de utilização](./media/cache-how-to-monitor/redis-cache-usage-part.png)

O **escalão de preço** apresenta os preços da cache camada e pode ser utilizado para [escala](cache-how-to-scale.md) cache para um escalão de preço diferente.

## <a name="view-metrics-with-azure-monitor"></a>Veja as métricas com a monitorização do Azure
Para ver métricas de Redis e criem gráficos personalizados utilizando o Monitor do Azure, clique em **métricas** do **menu recursos**e personalizar o gráfico com as métricas pretendidas, reporting intervalo, o tipo de gráfico e muito mais.

![Métricas de redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Para obter mais informações sobre como trabalhar com a métrica utilizando o Monitor do Azure, consulte [descrição geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Métricas de cache de exportação
Por predefinição, as métricas de cache no Monitor do Azure são [armazenadas durante 30 dias](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) e, em seguida, eliminados. Para manter as suas métricas de cache durante um período superior a 30 dias, pode [designar uma conta de armazenamento](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) e especifique um **retenção (dias)** política para as métricas de cache. 

Para configurar uma conta de armazenamento para as métricas de cache:

1. Clique em **diagnóstico** do **menu recursos** no **a Cache de Redis** painel.
2. Clique em **no**.
3. Verifique **arquivo para uma conta de armazenamento**.
4. Selecione a conta de armazenamento para armazenar as métricas de cache.
5. Verifique o **1 minuto** caixa de verificação e especifique um **retenção (dias)** política. Se não pretender aplicar a qualquer política de retenção e manter os dados indefinidamente, defina **retenção (dias)** para **0**.
6. Clique em **Guardar**.

![Diagnóstico de redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Para além de arquivar as métricas de cache para o armazenamento, pode também [transmiti-los para um hub de eventos ou enviá-los para análise de registos](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

Para aceder à sua métricas, pode visualizá-las no portal do Azure, conforme descrito anteriormente neste artigo e também pode acedê-los utilizando o [API REST da Azure Monitor métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

> [!NOTE]
> Se alterar as contas de armazenamento, os dados na conta de armazenamento configurada anteriormente permanecem disponíveis para transferência, mas não é apresentada no portal do Azure.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>As métricas disponíveis e intervalos de relatórios
Métricas de cache são reportadas utilizando vários intervalos de relatórios, incluindo **decorridos desde hora**, **hoje**, **passado semana**, e **personalizada**. O **métrica** painel para cada gráfico métricas apresenta os valores mínimos, máximo e médio para cada métrica no gráfico e algumas métricas apresentam um total para o intervalo de relatórios. 

Cada métrica inclui duas versões. Uma métrica mede o desempenho para a cache completa e para caches que utilizam [clustering](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui `(Shard 0-9)` no desempenho de medidas de nome para um único ID de partição horizontal cache. Por exemplo, se tiver de uma cache de 4 shards, `Cache Hits` é a quantidade total de pedidos para a cache completa, e `Cache Hits (Shard 3)` está apenas os pedidos para esse ID de partição horizontal da cache.

> [!NOTE]
> Mesmo quando a cache está inativa sem aplicações de cliente ativo ligado, poderá ver alguma atividade de cache, tais como clientes ligados, utilização de memória e operações serem efetuadas. Esta atividade é normal durante a operação de uma instância da Cache de Redis do Azure.
> 
> 

| Métrica | Descrição |
| --- | --- |
| Acertos na cache |O número de pesquisas com êxito chaves durante o intervalo de relatórios especificado. Isto mapeia para `keyspace_hits` do Redis [informações](http://redis.io/commands/info) comando. |
| Pedidos de sem êxito na cache |O número de pesquisas de chaves falhou durante o intervalo de relatórios especificado. Isto mapeia para `keyspace_misses` do comando Redis informações. Pedidos de sem êxito na cache não significam necessariamente que há um problema com a cache. Por exemplo, ao utilizar o padrão de programação do lado da cache, uma aplicação procura primeiro na cache para um item. Se o item não existe (falha de acerto na cache), o item é obtido da base de dados e adicionado à cache para a próxima vez. Pedidos de sem êxito na cache são comportamento normal para o padrão de programação do lado da cache. Se o número de pedidos sem êxito de cache é superior ao esperado, examine a lógica de aplicação que preenche e lê a partir da cache. Se os itens estão a ser expulsos da cache devido a pressão de memória, em seguida, poderão existir alguns pedidos sem êxito de cache, mas seria uma melhor métrica para monitorizar pressão de memória `Used Memory` ou `Evicted Keys`. |
| Clientes ligados |O número de ligações de cliente para a cache durante o intervalo de relatórios especificado. Isto mapeia para `connected_clients` do comando Redis informações. Uma vez a [limite de ligação](cache-configure.md#default-redis-server-configuration) é tentativas de ligação subsequentes atingiu o número para a cache irá falhar. Tenha em atenção que, mesmo se não houver nenhuma aplicação de cliente do Active Directory, pode ainda existir algumas instâncias de clientes ligados devido a processos internos e ligações. |
| Expulsos as chaves |O número de itens expulsos da cache durante o intervalo especificado de relatórios devido ao `maxmemory` limite. Isto mapeia para `evicted_keys` do comando Redis informações. |
| Chaves expiradas |O número de itens expirado na cache de durante o intervalo de relatórios especificado. Este valor é mapeado para `expired_keys` do comando Redis informações. |
| Chaves totais  | O número máximo de chaves na cache durante o período de tempo de relatório passado. Isto mapeia para `keyspace` do comando Redis informações. Devido a uma limitação do sistema de métricas subjacente, de caches com clustering ativada, as chaves Total devolve o número máximo de chaves do ID de partição horizontal que tinha o número máximo de chaves durante o intervalo de relatórios.  |
| Obtém |O número de operações get da cache durante o intervalo de relatórios especificado. Este valor é a soma dos seguintes valores das informações de Redis, todos os comandos: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, e `cmdstat_getrange`e é equivalente à soma de acertos na cache e pedidos sem êxito durante o intervalo de relatórios. |
| Carga de servidor de redis |A percentagem de ciclos no qual o servidor de Redis está ocupado a processar e não espera inativo para mensagens. Se este contador atingir 100 significa que o servidor de Redis atingiu um limite de desempenho e a CPU não é possível processar funcionam qualquer o mais rapidamente. Se vir elevada carga de servidor de Redis, em seguida, verá as exceções de tempo limite no cliente. Neste caso, deve considerar como aumentar verticalmente ou os dados de criação de partições em várias caches. |
| Conjuntos de |O número de operações de conjunto para a cache durante o intervalo de relatórios especificado. Este valor é a soma dos seguintes valores das informações de Redis, todos os comandos: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` , e `cmdstat_setnx`. |
| Operações totais |O número total de comandos, processados pelo servidor de cache durante o intervalo de relatórios especificado. Este valor é mapeado para `total_commands_processed` do comando Redis informações. Tenha em atenção que quando a Cache de Redis do Azure é utilizada puramente para pub/sub existirá não haver métricas para `Cache Hits`, `Cache Misses`, `Gets`, ou `Sets`, mas será `Total Operations` métricas refletem a utilização de cache para operações de pub/sub. |
| Memória utilizada |A quantidade de memória de cache utilizada para pares chave/valor em cache em MB durante o intervalo de relatórios especificado. Este valor é mapeado para `used_memory` do comando Redis informações. Isto inclui os metadados ou fragmentação. |
| Memória utilizada RSS |A quantidade de memória de cache utilizada em MB durante o intervalo de relatórios especificado, incluindo a fragmentação e metadados. Este valor é mapeado para `used_memory_rss` do comando Redis informações. |
| CPU |A utilização da CPU do servidor de Cache de Redis do Azure como uma percentagem durante o intervalo de relatórios especificado. Este valor é mapeado para o sistema operativo `\Processor(_Total)\% Processor Time` contador de desempenho. |
| Cache de leitura |A quantidade de dados de leitura da cache em Megabytes por segundo (MB/s) durante o intervalo de relatórios especificado. Este valor é derivado de placas de interface de rede que suportam a máquina virtual que aloja a cache e não é específico do Redis. **Este valor corresponde à largura de banda de rede utilizada pela cache deste. Se pretender configurar alertas para os limites de largura de banda de rede do lado servidor, em seguida, crie-lo utilizando esta `Cache Read` contador. Consulte [esta tabela](cache-faq.md#cache-performance) para os limites de largura de banda observado para a cache de várias camadas e tamanhos de preço.** |
| Cache de escrita |A quantidade de dados escritos para a cache em Megabytes por segundo (MB/s) especificado durante o intervalo de relatórios. Este valor é derivado de placas de interface de rede que suportam a máquina virtual que aloja a cache e não é específico do Redis. Este valor corresponde a largura de banda de rede de dados enviados para a cache do cliente. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alertas
Pode configurar para receber alertas com base nos registos de métricas e atividade. Monitor do Azure permite-lhe configurar um alerta para fazer o seguinte quando aciona:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma aplicação de lógica do Azure

Para configurar regras de alerta para a sua cache, clique em **regras de alerta** do **menu recursos**.

![Monitorização](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Para obter mais informações sobre como configurar e utilizar alertas, consulte [descrição geral dos alertas](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Registos de Atividade
Registos de atividade fornecem informações aprofundadas as operações que foram executadas nas instâncias de Cache de Redis do Azure. Era anteriormente conhecido como "registos de auditoria" ou "registos operacionais". Utilizar registos de atividade, poderá determinar o "o que, quem e quando" para escrever qualquer operações (PUT, POST, DELETE) efetuadas nas instâncias de Cache de Redis do Azure. 

> [!NOTE]
> Registos de atividade não incluir operações de leitura (GET).
>
>

Para ver registos de atividade para a sua cache, clique em **registos de atividade** do **menu recursos**.

Para obter mais informações sobre os registos de atividade, consulte [descrição geral do registo de atividade do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











