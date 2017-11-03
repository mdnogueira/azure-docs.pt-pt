---
title: "Solução de capacidade e o desempenho no Log Analytics do Azure | Microsoft Docs"
description: "Utilize a solução de capacidade e o desempenho na análise de registos para ajudar a compreender a capacidade dos seus servidores de Hyper-V."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: banders
ms.openlocfilehash: 5ca005127721092b8efcf0ac83cc967ab15fe72d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Planear a capacidade de máquina virtual de Hyper-V com a solução de capacidade e o desempenho (pré-visualização)

![Símbolo de capacidade e o desempenho](./media/log-analytics-capacity/capacity-solution.png)

Pode utilizar a solução de capacidade e o desempenho na análise de registos para ajudar a compreender a capacidade dos seus servidores de Hyper-V. A solução fornece informações para o seu ambiente de Hyper-V para mostrar a utilização geral (CPU, memória e o disco) de anfitriões e as VMs em execução nesses anfitriões Hyper-V. As métricas são recolhidas para a CPU, memória e discos em todos os seus anfitriões e as VMs em execução nos mesmos.

Solução:

-   Mostra os anfitriões com maior e mais baixa utilização da CPU e memória
-   Mostra as VMs com maior e mais baixa utilização da CPU e memória
-   Mostra as VMs com a utilização de IOPS e débito mais elevada e mais baixa
-   Mostra quais as VMs estão em execução os anfitriões
-   Mostra os discos superiores com débito elevado, latência e IOPS, nos volumes partilhados de cluster
- Permite-lhe personalizar e filtrar com base nos grupos

> [!NOTE]
> A versão anterior da solução de capacidade e o desempenho chamada capacidade de gestão necessário System Center Operations Manager e do Microsoft System Center Virtual Machine Manager. Esta solução atualizada não tem as dependências.


## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|---|---|---|
| [Agentes do Windows](log-analytics-windows-agents.md) | Sim | A solução recolhe informações de dados de capacidade e o desempenho de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não    | A solução recolhe informações de dados de capacidade e o desempenho de agentes diretos do Linux.|
| [Grupo de gestão do SCOM](log-analytics-om-agents.md) | Sim |A solução recolhe dados de capacidade e o desempenho de agentes num grupo de gestão do SCOM ligado. Não é necessária uma ligação direta do agente do SCOM para OMS. Os dados são reencaminhados do grupo de gestão para o repositório do OMS.|
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | Armazenamento do Azure não inclui dados de desempenho e da capacidade.|

## <a name="prerequisites"></a>Pré-requisitos

- Windows ou agentes do Operations Manager tem de estar instalados no Windows Server 2012 ou superiores anfitriões de Hyper-V, não as máquinas virtuais.


## <a name="configuration"></a>Configuração

Execute o passo seguinte para adicionar a solução de capacidade e desempenho para a sua área de trabalho.

- Adicionar a solução de capacidade e desempenho para a sua área de trabalho do OMS utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).

## <a name="management-packs"></a>Pacotes de gestão

Se o grupo de gestão do SCOM é ligado à sua área de trabalho do OMS, em seguida, os seguintes pacotes de gestão serão instalados no SCOM quando adicionar esta solução. Estes pacotes de gestão não precisam de configurações nem de manutenção.

- Microsoft.IntelligencePacks.CapacityPerformance

O evento 1201 assemelha-se:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Quando a solução de capacidade e o desempenho é atualizada, irá alterar o número de versão.

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](log-analytics-om-agents.md).

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução de capacidade e desempenho para a sua área de trabalho, a capacidade e o desempenho é adicionada ao dashboard geral. Este mosaico mostra uma contagem do número de anfitriões de Hyper-V está ativas e o número de máquinas virtuais ativas que foram monitorizados para o período de tempo selecionado.

![Mosaico de capacidade e o desempenho](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>Utilização de revisão

Clique no mosaico para abrir o dashboard de desempenho e capacidade de capacidade e o desempenho. O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta uma lista de até dez itens que correspondem aos critérios dessa coluna para o âmbito e o intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo**, na parte inferior da coluna, ou ao clicar no cabeçalho da coluna.

- **Anfitriões**
    - **Utilização da CPU do anfitrião** mostra uma tendência gráfica de utilização da CPU de computadores de anfitrião e uma lista de anfitriões, com base no período de tempo selecionado. Coloque o cursor sobre o gráfico de linhas para ver os detalhes para um ponto específico no tempo. Clique em gráfico para ver mais detalhes na pesquisa de registo. Clique em qualquer nome de anfitrião para abrir a pesquisa de registo e ver os detalhes do contador de CPU para as VMs alojadas.
    - **Utilização da memória de anfitrião** mostra uma tendência gráfica da utilização de memória de computadores de anfitrião e uma lista de anfitriões, com base no período de tempo selecionado. Coloque o cursor sobre o gráfico de linhas para ver os detalhes para um ponto específico no tempo. Clique em gráfico para ver mais detalhes na pesquisa de registo. Clique em qualquer nome de anfitrião para abrir a pesquisa de registo e ver os detalhes do contador de memória para VMs alojadas.
- **Máquinas Virtuais**
    - **Utilização de CPU VM** mostra uma tendência gráfica de utilização da CPU de máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Coloque o cursor sobre o gráfico de linhas para ver os detalhes para um ponto específico no tempo para as parte superiores 3 VMs. Clique em gráfico para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver os detalhes do contador agregados da CPU para a VM.
    - **Utilização de memória da VM** mostra uma tendência gráfica da utilização de memória de máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Coloque o cursor sobre o gráfico de linhas para ver os detalhes para um ponto específico no tempo para as parte superiores 3 VMs. Clique em gráfico para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver os detalhes do contador de agregados de memória para a VM.
    - **VM Total disco IOPS** mostra uma tendência gráfica do disco total IOPS para máquinas virtuais e uma lista de máquinas virtuais com o IOPS para cada um, com base no período de tempo selecionado. Coloque o cursor sobre o gráfico de linhas para ver os detalhes para um ponto específico no tempo para as parte superiores 3 VMs. Clique em gráfico para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver os detalhes do contador de disco agregado IOPS para a VM.
    - **VM débito Total do disco** mostra uma tendência gráfica do débito total do disco para máquinas virtuais e uma lista de máquinas virtuais com o débito total do disco para cada um, com base no período de tempo selecionado. Coloque o cursor sobre o gráfico de linhas para ver os detalhes para um ponto específico no tempo para as parte superiores 3 VMs. Clique em gráfico para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver os detalhes do contador débito agregado total do disco para a VM.
- **Volumes Partilhados de cluster**
    - **Total de débito** mostra a soma de ambas as leituras e escritas em volumes partilhados de cluster.
    - **Total de IOPS** mostra a soma das operações de entrada/saída por segundo em volumes partilhados de cluster.
    - **Total de latência** apresenta a latência total nos volumes partilhados de cluster.
- **Anfitrião densidade** superior mosaico mostra o número total de anfitriões e máquinas virtuais disponíveis para a solução. Clique no mosaico superior para ver detalhes adicionais na pesquisa de registo. Também apresenta uma lista de todos os anfitriões e o número de máquinas virtuais que estão alojados. Clique num anfitrião para explorar os resultados VM na pesquisa de registo.


![Painel de anfitriões de dashboard](./media/log-analytics-capacity/dashboard-hosts.png)

![Painel de máquinas virtuais do dashboard](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>Avaliar o desempenho

Os ambientes informáticos de produção diferem significativamente de uma organização para outra. Além disso, capacidade e o desempenho de cargas de trabalho poderão dependem da forma como as VMs estão em execução, e o que considerar normal. Procedimentos específicos para o ajudar a medida de desempenho, provavelmente, não seria aplicadas ao seu ambiente. Portanto, mais generalizada prescritiva orientações melhor é adequada para o ajudar. A Microsoft publica uma variedade de artigos de orientação prescritiva para ajudar a medir o desempenho.

Para resumir, a solução recolhe dados de desempenho e capacidade de uma variedade de origens, incluindo os contadores de desempenho. Utilize os dados de desempenho e da capacidade que apresentadas várias analisa na solução e comparar os resultados para as no [medir o desempenho no Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) artigo. Embora o artigo foi publicado algum tempo há, as métricas, considerações e diretrizes ainda são válidas. O artigo contém ligações para outros recursos úteis.


## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte fornece pesquisas de registo de exemplo para dados de desempenho e capacidade recolhidos e calculado por esta solução.

| Consulta | Descrição |
|---|---|
| Todas as configurações de memória de anfitrião | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="Host Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| Todas as configurações de memória da VM | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="VM Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| Divisão de IOPS de disco Total em todas as VMs | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Reads/s" OR CounterName="VHD Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Divisão de débito Total do disco em todas as VMs | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Read MB/s" OR CounterName="VHD Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Divisão de IOPS Total em todos os CSVs | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Reads/s" OR CounterName="CSV Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Divisão de débito Total em todos os CSVs | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read MB/s" OR CounterName="CSV Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Divisão de latência Total em todos os CSVs | <code> Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read Latency" OR CounterName="CSV Write Latency") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas acima serão alteradas para as seguintes.

> | Consulta | Descrição |
|:--- |:--- |
| Todas as configurações de memória de anfitrião | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e CounterName = = "Atribuído MB de memória do anfitrião" &#124; resumir os MB = avg(CounterValue) por InstanceName |
| Todas as configurações de memória da VM | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e CounterName = = "MB de memória atribuída de VM" &#124; resumir os MB = avg(CounterValue) por InstanceName |
| Divisão de IOPS de disco Total em todas as VMs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "VHD leituras/s" ou CounterName = = "VHD escritas/s") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Divisão de débito Total do disco em todas as VMs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "VHD leitura MB/s" ou CounterName = = "MB/s de escrita de VHD") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Divisão de IOPS Total em todos os CSVs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "CSV leituras/s" ou CounterName = = "CSV escritas/s") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Divisão de débito Total em todos os CSVs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "CSV leituras/s" ou CounterName = = "CSV escritas/s") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Divisão de latência Total em todos os CSVs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "Latência de leitura do CSV" ou CounterName = = "Latência de escrita de CSV") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisas de registo na análise de registos](log-analytics-log-searches.md) para ver os dados de desempenho e capacidade de detalhado.
