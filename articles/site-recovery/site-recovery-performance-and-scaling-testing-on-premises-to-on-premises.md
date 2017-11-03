---
title: "Resultados do teste para replicação de Hyper-V entre sites com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo fornece informações sobre testes de desempenho para no local para a replicação de VMs de Hyper-V no local utilizando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 96ff404f-0d88-43fa-a00b-2dffde93d192
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: c221fe94c5301b0a36882d5ae1c57e523002ecc4
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="test-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Resultados do teste para no local para a replicação de Hyper-V no local com a recuperação de Site

Pode utilizar o Microsoft Azure Site Recovery para orquestrar e gerir a replicação de máquinas virtuais e servidores físicos para o Azure ou para um datacenter secundário. Este artigo fornece os resultados do teste de desempenho que fizemos quando replicar máquinas virtuais Hyper-V entre dois locais centros de dados.

## <a name="test-goals"></a>Objetivos de teste

Era o objetivo de testar examinar a forma como o Azure Site Recovery executa durante a replicação de estado de repouso. Estado de repouso replicação ocorre quando as máquinas virtuais concluiu a replicação inicial e estiver a sincronizar as alterações de delta. É importante medir o desempenho através de estado de repouso porque é o estado em que a maioria das máquinas virtuais permanecem, exceto se ocorrerem falhas inesperadas.

A implementação de teste consistiu em dois sites no local com um servidor do VMM em cada site. Esta implementação de teste é típica de implementação de sucursal/office head, com sede a agir como o site primário e o escritório de sucursal como o site secundário ou de recuperação.

## <a name="what-we-did"></a>O que fizemos

Eis o que podemos no teste passou:

1. Criar máquinas virtuais com modelos do VMM.
2. Iniciar máquinas virtuais e métricas de desempenho de linha de base de captura superior a 12 horas.
3. Nuvens criadas nos servidores primário e de recuperação do VMM.
4. Proteção da nuvem configurada no Azure Site Recovery, incluindo o mapeamento de nuvens de origem e de recuperação.
5. Ativar a proteção para máquinas virtuais e permitir-lhes a replicação inicial.
6. Aguardaram duas horas para estabilização de sistema.
7. Captura de métricas de desempenho superior a 12 horas, garantir que todas as máquinas virtuais permanecer num Estado de replicação esperado para esses 12 horas.
8. Medir as diferenças entre as métricas de desempenho da linha de base e as métricas de desempenho de replicação.


## <a name="primary-server-performance"></a>Desempenho do servidor primário

* Réplica do Hyper-V regista no modo assíncrono as alterações para um ficheiro de registo com tolerância de armazenamento mínima no servidor primário.
* Réplica do Hyper-V utiliza a cache de memória personalizada manter para minimizar o IOPS sobrecarga para o controlo. Armazena escreve o VHDX na memória e os remove da cache para o ficheiro de registo antes do tempo que o registo é enviado para o site de recuperação. Um disco libertação ocorre também se as escritas atingiu um limite predeterminado.
* O gráfico abaixo mostra a sobrecarga IOPS de estado de repouso para replicação. É possível ver que o IOPS sobrecarga devido a replicação é aproximadamente 5% que é bastante baixa.

![Resultados primários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Réplica do Hyper-V utiliza a memória no servidor primário para otimizar o desempenho de disco. Como é mostrado no seguinte gráfico, memória sobrecarga em todos os servidores no cluster primário é marginal. A memória sobrecarga apresentada é a percentagem de memória utilizada pela replicação em comparação comparada a memória total instalada no servidor de Hyper-V.

![Resultados primários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Réplica do Hyper-V tem mínimo sobrecarga da CPU. Conforme apresentado no gráfico, overhead da replicação está no intervalo de % 2 a 3.

![Resultados primários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

## <a name="secondary-recovery-server-performance"></a>Desempenho do servidor secundário (de recuperação)

Réplica do Hyper-V utiliza uma pequena quantidade de memória no servidor de recuperação para otimizar o número de operações de armazenamento. O gráfico resume a utilização da memória no servidor de recuperação. A memória sobrecarga apresentada é a percentagem de memória utilizada pela replicação em comparação comparada a memória total instalada no servidor de Hyper-V.

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

A quantidade de operações de e/s no site de recuperação é uma função do número de operações de escrita no site primário. Vamos observar as operações de e/s totais no site de recuperação in comparison with as operações de e/s totais exibidas as operações no site primário. Os gráficos mostram que o total de IOPS no site de recuperação

* Cerca de 1,5 vezes a operação de escrita IOPS principal.
* Cerca de 37% do IOPS total no site primário.

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

## <a name="effect-on-network-utilization"></a>Efeito na utilização de rede

Uma média de 275 Mb por segundo de largura de banda de rede foi utilizada entre os nós primário e de recuperação (com compressão ativado) contra uma largura de banda existente de 5 Gb por segundo.

![Utilização de rede de resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

## <a name="effect-on-vm-performance"></a>Efeito no desempenho da VM

Uma consideração importante é o impacto da replicação em cargas de trabalho de produção em execução nas máquinas virtuais. Se o site primário tem é aprovisionado para replicação, não deve existir qualquer impacto nas cargas de trabalho. Mecanismo de controlo de lightweight de réplica de Hyper-V assegura que as cargas de trabalho em execução nas máquinas virtuais não são afetadas durante a replicação de estado de repouso. Esta situação é ilustrada no seguintes gráficos.

Este gráfico mostra o IOPS realizadas por máquinas virtuais que executam cargas de trabalho diferentes antes e após a replicação foi ativada. Pode observar o que não há qualquer diferença entre os dois.

![Resultados do efeito de réplica](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

O gráfico seguinte mostra o débito de máquinas virtuais que executam cargas de trabalho diferentes antes e após a replicação foi ativada. Pode observar a que a replicação não tem nenhum impacto significativo.

![Efeitos de réplica de resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

## <a name="conclusion"></a>Conclusão

Os resultados mostram claramente a que o Azure Site Recovery, conjugada com a réplica do Hyper-V, dimensiona bem com o mínimo de sobrecarga de um cluster de grandes dimensões.  O Azure Site Recovery fornece implementação simples, a replicação, a gestão e monitorização. Réplica do Hyper-V fornece a infraestrutura necessária para o dimensionamento de replicação com êxito. Para planear uma implementação ideal sugerimos que transferiu o [Planeador de capacidade de réplica do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Detalhes de ambiente de teste

### <a name="primary-site"></a>Site primário

* O site primário tem um cluster de cinco servidores de Hyper-V 470 máquinas virtuais em execução.
* As máquinas virtuais executar cargas de trabalho diferentes e têm proteção do Azure Site Recovery ativada.
* Armazenamento para o nó de cluster é fornecido por uma SAN iSCSI. Modelo – Hitachi HUS130.
* Cada servidor de cluster tem quatro placas de rede (NICs) de um Gbps.
* Duas das placas de rede estão ligadas a uma rede privada iSCSI e dois estão ligados a uma rede de enterprise externo. Uma das redes externas está reservada para comunicações de cluster apenas.

![Requisitos de hardware principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

| Servidor | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores de Hyper-V num cluster: <br />ESTLAB HOST11<br />ESTLAB HOST12<br />ESTLAB HOST13<br />ESTLAB HOST14<br />ESTLAB HOST25 |128ESTLAB HOST25 tem 256 |R820 Dell™ PowerEdge™ |Intel CPUs CPU E5-4620 0 @ 2.20GHz |4 |Posso Gbps x 4 |Windows Server R2 de 2012 Datacenter (x64) + a função Hyper-V |
| Servidor do VMM |2 | | |2 |1 Gbps |Windows Server da base de dados 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-recovery-site"></a>Site secundário (recuperação)

* O site secundário tem um cluster de ativação pós-falha de seis nós.
* Armazenamento para o nó de cluster é fornecido por uma SAN iSCSI. Modelo – Hitachi HUS130.

![Especificação de hardware principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

| Servidor | RAM | Modelo | Processador | Número de processadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores de Hyper-V num cluster: <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB HOST10 |96 |R720 Dell™ PowerEdge™ |Intel CPUs CPU E5-2630 0 @ 2.30GHz |2 |Posso Gbps x 4 |Windows Server R2 de 2012 Datacenter (x64) + a função Hyper-V |
| ESTLAB HOST17 |128 |R820 Dell™ PowerEdge™ |Intel CPUs CPU E5-4620 0 @ 2.20GHz |4 | |Windows Server R2 de 2012 Datacenter (x64) + a função Hyper-V |
| ESTLAB HOST24 |256 |R820 Dell™ PowerEdge™ |Intel CPUs CPU E5-4620 0 @ 2.20GHz |2 | |Windows Server R2 de 2012 Datacenter (x64) + a função Hyper-V |
| Servidor do VMM |2 | | |2 |1 Gbps |Windows Server da base de dados 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Cargas de trabalho do servidor

* Para fins de teste, iremos selecionado cargas de trabalho normalmente utilizadas em cenários de cliente da empresa.
* Utilizamos [IOMeter](http://www.iometer.org) com característica a carga de trabalho resumida na tabela de simulação.
* Todos os perfis de IOMeter são definidos para escrita aleatórias bytes para simular worst-case escrever padrões para cargas de trabalho.

| Carga de trabalho | Tamanho de e/s (KB) | % Acesso | % De leitura | E/s pendentes | Padrão de e/s |
| --- | --- | --- | --- | --- | --- |
| Servidor de ficheiros |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Todos os 100% aleatório |
| SQL Server (volume 1) SQL Server (volume 2) |864 |100%100% |70%0% |88 |% de 100% random100 sequencial |
| Troca |32 |100% |67% |8 |100% aleatório |
| Estação de trabalho/VDI |464 |66%34% |70%95% |11 |Ambos os aleatório 100% |
| Servidor de ficheiros do Web |4864 |33%34%33% |95%95%95% |888 |Todos os 75% aleatório |

### <a name="vm-configuration"></a>Configuração de VM

* 470 máquinas virtuais no cluster primário.
* Todas as máquinas virtuais com o disco do VHDX.
* Máquinas virtuais de executar cargas de trabalho resumidas na tabela. Todos os foram criados com modelos do VMM.

| Carga de trabalho | # VMs | RAM mínima (GB) | RAM máxima (GB) | Tamanho de disco lógico (GB) por VM | IOPS máximo |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Servidor do Exchange |71 |1 |4 |552 |10 |
| Servidor de ficheiros |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Servidor Web |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Definições de recuperação do site

* O Azure Site Recovery foi configurado para no local para a proteção no local
* O servidor VMM tem quatro nuvens configuradas, que contém os servidores de cluster do Hyper-V e as respetivas máquinas virtuais.

| Nuvem VMM principal | Máquinas virtuais protegidas na nuvem | Frequência de replicação | Pontos de recuperação adicionais |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 min |Nenhuma |
| PrimaryCloudRpo30s |47 |seg 30 |Nenhuma |
| PrimaryCloudRpo30sArp1 |47 |seg 30 |1 |
| PrimaryCloudRpo5m |235 |5 minutos |Nenhuma |

### <a name="performance-metrics"></a>Métricas de desempenho

A tabela resume as métricas de desempenho e os contadores que foram avaliados na implementação.

| Métrica | Contador |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Memória disponível |MBytes de \Memory\Available |
| IOPS |\PhysicalDisk ( total) \Disk transferências/seg |
| Segundo (IOPS) de operações de leitura de VM |Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Read operações por segundo |
| Operações de escrita (IOPS) de VM/seg |Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Write operações/S |
| Débito de leitura de VM |Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Read Bytes/seg |
| Débito de escrita VM |Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Write Bytes/seg |

## <a name="next-steps"></a>Passos seguintes

[Configurar a replicação entre dois sites VMM no local](site-recovery-vmm-to-vmm.md)
