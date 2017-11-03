---
title: Resolver problemas de desempenho de SQL Database do Azure com o Insights inteligente | Microsoft Docs
description: Inteligentes Insights ajuda a resolver problemas de desempenho de SQL Database do Azure.
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 85da2a521af0ca92c07d8b2041e92b98f98e9661
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Resolver problemas de desempenho de SQL Database do Azure com o Insights inteligente

Esta página fornece informações sobre problemas de desempenho de SQL Database do Azure que são detetados através de [Insights inteligente](sql-database-intelligent-insights.md) de registo de diagnóstico de desempenho de base de dados. Este registo de diagnóstico pode ser enviado para [Log Analytics do Azure](../log-analytics/log-analytics-azure-sql.md), [Event Hubs do Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Storage do Azure](sql-database-metrics-diag-logging.md#stream-into-storage), ou uma solução de terceiros para DevOps personalizado alertas e relatórios capacidades.

> [!NOTE]
> Para um base de dados SQL desempenho Resolução de problemas guia rápido através das informações inteligente, consulte o [recomendado a resolução de problemas de fluxo](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) fluxograma neste documento.
>

## <a name="detectable-database-performance-patterns"></a>Padrões de desempenho de base de dados detetável

Insights inteligentes Deteta automaticamente os problemas de desempenho com base de dados SQL baseada em tempos de espera de execução de consulta, erros ou tempos limite. -Lo, em seguida, produz padrões de desempenho detetado o registo de diagnóstico. Padrões de desempenho detetável estão resumidos na tabela seguinte:

| Padrões de desempenho detetável | Detalhes debitados |
| :------------------- | ------------------- |
| [Por se terem atingido os limites de recursos](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Consumo de recursos disponíveis (DTUs), os threads de trabalho de base de dados ou sessões de início de sessão de base de dados disponíveis na subscrição monitorizada atingiu limites, que faz com que problemas de desempenho de base de dados SQL. |
| [Aumento de carga de trabalho](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Foi detetada o aumento de carga de trabalho ou contínua acumulação de carga de trabalho na base de dados, que faz com que problemas de desempenho de base de dados SQL. |
| [Pressão de memória](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Trabalhadores que pediu memória concede tem de aguardar para as alocações de memória para quantidades significativas estatisticamente de tempo. Ou existe uma acumulação de aumento dos trabalhadores pedida concede de memória, que afeta o desempenho de base de dados SQL. |
| [Bloqueio](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Base de dados excessivo bloqueio foi detetado, que afeta o desempenho de base de dados SQL. |
| [Aumento MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | O nível máximo de opção de paralelismo (MAXDOP) foi alterada e que isso afeta a eficiência de execução da consulta. |
| [Pagelatch contenção](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Foi detetada a contenção Pagelatch, que afeta o desempenho de base de dados SQL. Vários threads em simultâneo tentam de aceder ao mesmo páginas de memória intermédia de dados em memória. Isto resulta em tempos de espera maiores, que afeta o desempenho de base de dados SQL. |
| [Índice em falta](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Foi detetado um problema de índice em falta, que afeta o desempenho de base de dados SQL. |
| [Nova consulta](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Foi detetada uma nova consulta, que afeta o desempenho de base de dados do SQL Server global. |
| [Estatística de espera invulgar](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Foram detetados tempos de espera de base de dados de atividade invulgar, que afeta o desempenho de base de dados SQL. |
| [TempDB contenção](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Vários threads tentam aceder os recursos de tempDB mesmo, o que faz com que um congestionamento que afeta o desempenho de base de dados SQL. |
| [Falta DTU do conjunto elástico](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Uma falta de eDTUs disponível no agrupamento elástico afeta o desempenho de base de dados SQL. |
| [Plano de regressão](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Foi detetada com um novo plano ou uma alteração na carga de trabalho de um plano existente, que afeta o desempenho de base de dados SQL. |
| [Alteração de valor de configuração de âmbito de base de dados](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Uma alteração da configuração na base de dados afeta o desempenho de base de dados SQL. |
| [Cliente lenta](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Foi detetado um cliente de aplicações lenta que não é possível ao consumir a saída da base de dados SQL suficientemente rápida, que afeta o desempenho de base de dados SQL. |
| [Mudança para versão anterior do escalão de preço](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Uma ação de mudança para versão anterior do escalão preço diminuído recursos disponíveis, que afeta o desempenho de base de dados SQL. |

> [!TIP]
> Para a otimização de desempenho contínuas da base de dados do SQL Server, ativar [otimização automática da base de dados do Azure SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning). Esta funcionalidade exclusiva de intelligence incorporada de base de dados SQL continuamente monitoriza a base de dados do SQL Server, automaticamente tunes índices e aplica-se correções de plano de execução de consulta.
>

A secção seguinte descreve os padrões de desempenho detetável listadas anteriormente em mais detalhe.

## <a name="reaching-resource-limits"></a>Por se terem atingido os limites de recursos

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável combina os problemas de desempenho relacionados com a atingir os limites de recursos disponíveis, os limites de trabalho e limites de sessão. Depois do problema de desempenho é detetado, um campo de descrição do registo de diagnóstico indica se o problema de desempenho está relacionado com recursos, o trabalho ou limites de sessão.

Recursos na base de dados do SQL Server são normalmente denominados [recursos DTU](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu). Estes consistem de uma medida combinada de CPU e e/s recursos (dados transação registo e e/s). O padrão de atingir os limites de recursos é reconhecido quando detetado degradação do desempenho de consulta é causada por atingir qualquer os limites de recursos de medida.

O recurso de limites de sessão indica o número de inícios de sessão em simultâneo disponíveis para a base de dados do SQL Server. Neste padrão de desempenho é reconhecido quando as aplicações que estão ligadas a bases de dados do SQL Server atingiu o número de inícios de sessão em simultâneo disponíveis para a base de dados. Se as aplicações tentam utilizar mais sessões que estão disponíveis numa base de dados, o desempenho das consultas é afetado.

Atingir os limites de trabalho é um incidente específico de atingir os limites de recursos porque não são contados trabalhadores disponíveis na utilização da DTU. Atingir os limites de trabalho numa base de dados pode provocar o aumento súbito de tempos de espera de recurso específico, que resulta numa degradação do desempenho de consulta.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz hashes de consulta de consultas que afetados o desempenho e percentagens de consumo de recursos. Pode utilizar estas informações como um ponto de partida para otimizar a carga de trabalho de base de dados. Em particular, pode otimizar as consultas que afetam a degradação do desempenho adicionando os índices. Ou pode otimizar a aplicações com uma distribuição de carga de trabalho ainda mais. Se não for possível reduzir as cargas de trabalho ou tornar otimizações, considere aumentar o escalão de preço da sua subscrição da base de dados SQL para aumentar a quantidade de recursos disponíveis.

Se tiver atingido os limites de sessão disponíveis, pode otimizar as suas aplicações, reduzir o número de inícios de sessão efetuadas à base de dados. Se não for possível reduzir o número de inícios de sessão das suas aplicações para a base de dados, considere aumentar o escalão de preço da base de dados. Ou pode dividir e mover a base de dados para várias bases de dados para uma distribuição mais com balanceamento de carga de trabalho.

Para mais sugestões sobre como resolver os limites de sessão, consulte [como lidar com os limites de inícios de sessão de máximos de base de dados SQL](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Para saber os limites de recursos disponíveis para o escalão de subscrição, consulte [dos limites de recursos de base de dados SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Aumento de carga de trabalho

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho identifica problemas causados por um aumento de carga de trabalho ou, na sua forma mais grave, pile-up uma carga de trabalho.

Esta deteção é efetuada através de uma combinação de várias métricas. A métrica básica medida é detetar um aumento na carga de trabalho comparada com a última linha de base de carga de trabalho. A outra forma de deteção baseia-se na medição de um grande aumento em threads de trabalho de Active Directory, que é suficientemente grande para afetar o desempenho de consulta.

Na sua forma mais grave, a carga de trabalho continuamente poderá pile cópias de segurança devido a incapacidade de base de dados do SQL Server para processar a carga de trabalho. O resultado é um tamanho de carga de trabalho continuamente crescente, que é a condição de pile-up da carga de trabalho. Devido a esta condição aumenta o tempo que a carga de trabalho aguarda para execução. Esta condição representa um dos problemas de desempenho de base de dados mais graves. Este problema foi detetado com a monitorização do aumento do número de threads de trabalho abortadas. 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico devolve o número de consultas aumentou cuja execução e o hash de consulta da consulta com maior contribuição para o aumento de carga de trabalho. Pode utilizar estas informações como um ponto de partida para otimizar a carga de trabalho. A consulta identificada como a maior contribuinte o aumento de carga de trabalho é especialmente útil como ponto de partida.

Pode considerar distribuir as cargas de trabalho mais uniformemente à base de dados. É aconselhável a consulta que está a afetar o desempenho através da adição de índices. Também poderá distribuir a carga de trabalho entre várias bases de dados. Se estas soluções não são possíveis, considere aumentar o escalão de preço da sua subscrição da base de dados SQL para aumentar a quantidade de recursos disponíveis.

## <a name="memory-pressure"></a>Pressão de memória

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho indica degradação no desempenho da base de dados atual causado por pressão de memória ou na sua forma mais grave uma condição de pile-up memória, em comparação comparada a linha de base de desempenho de sete dias anteriores.

Pressão de memória indica uma condição de desempenho em que é um grande número de threads de trabalho memória requerente concede na base de dados SQL. O elevado volume faz com que uma condição de utilização elevada da memória na qual a base de dados do SQL Server não consegue atribuir memória de forma eficiente para todos os trabalhadores que solicitá-la. Uma das razões mais comuns para este problema está relacionado com a quantidade de memória disponível para a base de dados SQL por um lado. Por outro lado, um aumento da carga de trabalho faz com que o aumento de threads de trabalho e a pressão de memória.

A forma mais grave da pressão de memória é a condição de pile-up de memória. Esta condição indica que um número mais elevado de threads de trabalho está a pedir concede de memória que existem consultas libertação da memória. Este número de threads de trabalho memória requerente concede também poderá ser continuamente aumentar (piling cópias de segurança) porque o motor de base de dados do SQL Server não é possível alocar memória de forma eficiente suficiente para satisfazer a procura. A condição de pile-up memória representa um dos problemas de desempenho de base de dados mais graves.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz os detalhes de arquivo de objeto de memória com clerk (ou seja, o thread de trabalho) marcada como a razão mais elevada para utilização elevada da memória e carimbos de data / hora relevantes. Pode utilizar estas informações como base para resolução de problemas. 

Pode otimizar ou remover consultas relacionadas com os clerks com a maior utilização da memória. Também pode certificar-se que não são consultar os dados que não pretende utilizar. É boa prática utilizar sempre uma cláusula WHERE nas suas consultas. Além disso, recomendamos que crie índices não em cluster para procurar os dados em vez de lê-lo.

Também pode reduzir a carga de trabalho, otimizar ou a distribuição dos mesmos através de várias bases de dados. Ou pode distribuir a carga de trabalho entre várias bases de dados. Se estas soluções não são possíveis, considere aumentar o escalão de preço da sua subscrição da base de dados SQL para aumentar a quantidade de recursos de memória disponíveis para a base de dados.

Para sugestões de resolução de problemas adicionais, consulte [memória concede meditation: mysterious consumidor de memória do SQL Server com muitos nomes](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Bloqueio

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho indica degradação no desempenho da base de dados atual no qual bloqueio excessivo da base de dados é detetado em comparação com a linha de base de desempenho de sete dias anteriores. 

No RDBMS moderna, bloqueio, é essencial para implementar sistemas multithread em que o desempenho é maximizado executando vários trabalhadores em simultâneo e transações paralelas de base de dados sempre que possível. Bloqueio neste contexto refere-se o mecanismo de acesso incorporados em que apenas uma única transação exclusivamente pode aceder a linhas, páginas, tabelas e os ficheiros que são necessários e não compitam com outra transação de recursos. Quando terminar a transação que bloqueado os recursos para utilização com os mesmos, o bloqueio nesses recursos for lançado, que lhe permite outras transações aceder aos recursos necessários. Para obter mais informações sobre o bloqueio de ficheiro, consulte [bloquear no motor de base de dados](https://msdn.microsoft.com/library/ms190615.aspx).

Se as transações executadas pelo motor de SQL Server estão a aguardar prolongados períodos de tempo para aceder aos recursos bloqueados para utilização, este tempo de espera faz com que o slowdown do desempenho de execução de cargas de trabalho. 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz os detalhes de bloqueio que pode utilizar como base para resolução de problemas. Pode analisar as consultas de bloqueios comunicadas, ou seja, as que introduzem a degradação do desempenho de bloqueio, consultas e removê-los. Em alguns casos, poderá ser bem-sucedida otimizar as consultas de bloqueios.

A forma mais simples e através de correção para atenuar o problema é a manter as transações curtas e para reduzir os requisitos de espaço de bloqueio das consultas mais dispendiosos. Pode dividir um lote grande de operações em operações mais pequenas. É boa prática reduzir os requisitos de espaço de bloqueio de consulta ao efetuar a consulta como eficiente possível. Reduza análises grande pois aumentar as possibilidades de impasses e afetar negativamente o desempenho de base de dados global. Para consultas identificadas que fazer com que o bloqueio de ficheiro, pode criar novos índices ou adicionar colunas para o índice existente para evitar as análises de tabela. 

Para mais sugestões, consulte [como resolver problemas de bloqueios que são causados por Escalamento de bloqueio no SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Aumento MAXDOP

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável indica uma condição em que um plano de execução de consultas que escolheu foi paralelizada mais do que devia ter sido-lo. O otimizador de consultas de base de dados SQL pode melhorar o desempenho da carga de trabalho ao executar consultas em paralelo para acelerar a coisas sempre que possível. Em alguns casos, paralelas trabalhadores uma consulta de processamento demora mais tempo a aguardar entre si para sincronizar e intercalar os resultados em comparação comparados a executar a mesma consulta com menos trabalhadores paralelas, ou mesmo em alguns casos em comparação com um thread de trabalho único.

O sistema especialista analisa o desempenho de base de dados atual, em comparação comparado o período de linha de base. Determina se uma consulta anteriormente em execução está em execução inferior que antes porque o plano de execução da consulta é mais paralelizado que deve ser.

A opção de configuração de servidor MAXDOP na base de dados do SQL Server é utilizada para controlar o número de núcleos de CPU podem ser utilizados para executar a mesma consulta em paralelo. 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz relacionados com consultas para as quais a duração de execução aumentado porque estes foram paralelizadas mais do que o se devem ter sido de hashes de consulta. O registo de saídas também CXP tempos de espera. Neste momento representa o tempo de um thread único organizador/coordenador (thread 0) está à espera que todos os outros threads seja concluída antes de intercalação os resultados e mover avançar. Além disso, o registo de diagnóstico produz os tempos de espera que as consultas fraco desempenho em execução geral. Pode utilizar estas informações como base para resolução de problemas.

Em primeiro lugar, otimizar ou simplifique consultas complexas. É boa prática dividir tarefas de lote longo para aqueles mais pequenos. Além disso, certifique-se de que criou índices para suportar as suas consultas. Pode também manualmente impor o grau de paralelismo (MAXDOP) máximo para uma consulta que foi sinalizada como desempenho fraco. Para configurar esta operação através da utilização de T-SQL, consulte [Configure a opção de configuração de servidor MAXDOP](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Definir o servidor MAXDOP a opção de configuração para zero (0), como um valor predefinido indica que a base de dados do SQL Server pode utilizar todos os núcleos de CPU lógicos disponíveis para parallelize threads para executar uma única consulta. Definição MAXDOP para um (1) indica que apenas um principal pode ser utilizado para uma execução de uma consulta simples. Em termos práticos, isto significa que o paralelismo está desativado. Consoante a base de maiúsculas e minúsculas por caso, informações no registo de núcleos disponíveis para a base de dados e diagnóstico, pode otimizar a opção de MAXDOP para o número de núcleos utilizado para execução paralela da consulta que poderá resolver o problema no seu caso.

## <a name="pagelatch-contention"></a>Pagelatch contenção

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho indica a degradação de desempenho de cargas de trabalho atual da base de dados devido a contenção pagelatch em comparação comparada a linha de base de carga de trabalho de sete dias anteriores.

Bloqueios simultâneos são mecanismos de sincronização simples utilizados pela base de dados do SQL Server para permitir multithreading. Garantir a consistência de estruturas de memória que incluem índices, páginas de dados e outras estruturas internas.

Existem muitos tipos de bloqueios simultâneos disponíveis na base de dados do SQL Server. Para efeitos de simplicidade, bloqueios simultâneos de memória intermédia são utilizados para proteger as páginas de memória no conjunto de memória intermédia. Bloqueios simultâneos de e/s são utilizados para proteger as páginas que ainda não foi carregadas para o conjunto de memória intermédia. Sempre que os dados são escritos ou ler a partir de uma página no conjunto de memória intermédia, um thread de trabalho tem de adquirir um bloqueio temporário de memória intermédia para a página primeiro. Sempre que um thread de trabalho tenta aceder a uma página que não está já disponível no conjunto de memória intermédia de dentro da memória, é efetuado um pedido de e/s ao carregar as informações necessárias do armazenamento. Esta sequência de eventos indica uma forma mais grave de degradação do desempenho.

Ocorre contenção nas bloqueios simultâneos página quando vários threads em simultâneo a tentativa de adquirir bloqueios simultâneos na mesma estrutura de memória, o que introduz um tempo de espera de uma maior para a execução da consulta. No caso de contenção de e/s pagelatch, quando dados tem de ser acedido a partir de armazenamento, o tempo de espera é ainda maior. -Pode afetar consideravelmente o desempenho da carga de trabalho. Pagelatch contenção é o cenário mais comuns de threads a aguardar entre si e a competir pela recursos em vários sistemas de CPU.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz pagelatch detalhes de contenção. Pode utilizar estas informações como base para resolução de problemas.

Porque um pagelatch é um mecanismo de controlo interno da base de dados do SQL Server, este determina automaticamente quando a utilizá-los. Decisões de aplicação, incluindo o design do esquema, podem afetar o comportamento de pagelatch devido ao comportamento de bloqueios simultâneos determinística.

É um método para processamento de contenção de bloqueio temporário substituir uma chave de índice sequenciais com uma chave nonsequential distribuir uniformemente inserções através de um intervalo de índice. Normalmente, uma coluna à esquerda no índice distribui a carga de trabalho proporcionalmente. Outro método para considere a criação de partições de tabela é. Criar um hash de esquema com uma coluna calculada na tabela particionada necessitar de criação de partições é uma abordagem comum para mitigar a contenção de bloqueio temporário excessiva. No caso de contenção de e/s pagelatch, introduzir os índices ajuda a mitigar este problema de desempenho. 

Para obter mais informações, consulte [diagnosticar e resolver bloquear temporariamente a contenção no SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (transferência PDF).

## <a name="missing-index"></a>Índice em falta

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho indica a atual da base de dados carga de trabalho degradação do desempenho em comparação comparada a linha de base de sete dias anterior devido a um índice em falta.

Um índice é utilizado para acelerar o desempenho das consultas. Fornece acesso rápido aos dados da tabela ao reduzir o número de páginas de conjunto de dados que têm de ser visitada ou analisados.

As consultas específicas que causou a degradação do desempenho são identificadas através desta deteção para o qual criar índices seria vantajoso o desempenho.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz hashes de consulta para as consultas que foram identificados para afetar o desempenho da carga de trabalho. Pode criar índices para estas consultas. Também pode otimizar ou remover estas consultas se estes não são necessários. É uma prática de um bom desempenho evitar a consultar os dados que não utilizem.

> [!TIP]
> Sabia que intelligence incorporada de base de dados SQL pode gerir automaticamente os índices best-realizar para as bases de dados?
>
> Para a otimização de desempenho contínuas da base de dados do SQL Server, recomendamos que ative [otimização automática da base de dados SQL](sql-database-automatic-tuning.md). Esta funcionalidade exclusiva de intelligence incorporada de base de dados SQL continuamente monitoriza a base de dados do SQL Server e automaticamente tunes e cria índices para as bases de dados.
>

## <a name="new-query"></a>Nova consulta

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho indica que uma nova consulta foi detetada que está a efetuar mal e afetar o desempenho da carga de trabalho em comparação comparado a linha de base de desempenho de sete dias.

Escrever uma consulta bom desempenho, por vezes, pode ser uma tarefa difícil. Para obter mais informações sobre como escrever consultas, consulte [as consultas SQL escrever](https://msdn.microsoft.com/library/bb264565.aspx). Para otimizar o desempenho de consulta existente, consulte o artigo [Otimização da consulta](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Resolução de problemas

O diagnóstico registar informações de saídas até duas novas maioria dos consome CPU consultas, incluindo os hashes de consulta. Porque a consulta detetada afeta o desempenho da carga de trabalho, pode otimizar a sua consulta. É boa prática obter apenas os dados a utilizar. Também recomendamos que utilize consultas com uma cláusula WHERE. Recomendamos também que simplificar consultas complexas e dividi-los em consultas mais pequenas. É recomendável outro dividir consultas de batch de grandes dimensões em consultas mais pequenas do batch. Introdução de índices para novas consultas, normalmente, é uma boa prática para atenuar este problema de desempenho.

Considere a utilização de [Azure SQL da base de dados Query Performance Insight](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Estatística de espera invulgar

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável indica uma degradação do desempenho de cargas de trabalho no qual são identificadas consultas fraco desempenho, em comparação com a linha de base de carga de trabalho de sete dias anteriores.

Neste caso, o sistema não é possível classificar as consultas fraco desempenho em outras categorias de padrão de desempenho detetável, mas que detetado a responsável pela regressão de estatística de espera. Por conseguinte, considera-los como consultas com *estatísticas de espera invulgar*, onde a estatística de espera invulgar responsável pela regressão também está exposta. 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz informações nos detalhes do tempo de espera invulgar, hashes de consulta de consultas afetadas e os tempos de espera.

Porque o sistema com êxito não foi possível identificar a causa de raiz para consultas fraco desempenho, as informações de diagnóstico são um ponto de partida para manual de resolução de problemas. Pode otimizar o desempenho das seguintes consultas. É uma boa prática obter apenas os dados que precisa para utilizar e para simplificar e dividir consultas complexas para aqueles mais pequenos. 

Para mais informações sobre como otimizar o desempenho das consultas, consulte [Otimização da consulta](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>TempDB contenção

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável indica uma condição de desempenho de base de dados em que existe um estrangulamento de threads a tentar aceder a recursos de tempDB. (Esta condição não está relacionado com e/s.) O cenário típico para este problema de desempenho é centenas de consultas em simultâneo que todos os criarem, utilizarem e, em seguida, remover tabelas de tempDB pequeno. O sistema detetou que o número de consultas em simultâneo utilizando as mesmo tabelas de tempDB aumentado com significância análises suficiente para afetar o desempenho de base de dados em comparação comparado a linha de base de desempenho de sete dias anteriores.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz detalhes de contenção de tempDB. Pode utilizar as informações como o ponto de partida para resolução de problemas. Existem dois aspetos pode pursue aliviar este tipo de contenção e aumentar o débito da carga de trabalho geral: pode deixar de utilizar as tabelas temporárias. Também é possível utilizar tabelas com otimização de memória. 

Para obter mais informações, consulte [introdução às tabelas com otimização de memória](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Falta DTU do conjunto elástico

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável indica uma degradação do desempenho de cargas de trabalho de base de dados atual em comparação comparada a linha de base de sete dias anterior. É devido a falta de DTUs disponíveis no agrupamento elástico da sua subscrição. 

Recursos na base de dados do SQL Server são normalmente denominados [recursos DTU](sql-database-what-is-a-dtu.md), que consistem de uma medida combinada de CPU e e/s recursos (dados transação registo e e/s). [Recursos do Azure conjunto elástico](sql-database-elastic-pool.md) são utilizadas como agrupamento de recursos eDTU disponíveis partilhado entre várias bases de dados para fins de dimensionamento. Quando os recursos eDTU disponíveis do conjunto elástico não são suficientemente grandes para suportar todas as bases de dados no conjunto, foi detetado um problema de desempenho de falta DTU do conjunto elástico pelo sistema.

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz informações sobre o conjunto elástico, apresenta uma lista de bases de dados de consumo de DTU superiores e fornece a percentagem de DTU do agrupamento utilizado pela base de dados superior a consumir.

Porque esta condição de desempenho está relacionada com várias bases de dados utilizando o mesmo conjunto de edtus que no agrupamento elástico, os passos de resolução de problemas focar-se nas bases de dados de consumo de DTU superiores. Pode reduzir a carga de trabalho nas bases de dados superior a consumir, que inclui a otimizar as consultas de consumo de parte superior nessas bases de dados. Também pode garantir que não são consultar os dados que não utilizem. Outra abordagem é a otimizar as aplicações através da utilização de bases de dados de consumo de DTU superiores e redistribuir a carga de trabalho entre várias bases de dados.

Se redução e a Otimização da carga de trabalho atual em bases de dados de consumo de DTU superiores não são possíveis, considere aumentar o conjunto elástico escalão de preço. Como aumentar resulta num aumento das DTUs disponíveis no agrupamento elástico.

## <a name="plan-regression"></a>Plano de regressão

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável indica uma condição em que a base de dados do SQL Server utiliza um plano de execução de consultas inferior ao ideal. Normalmente, o plano inferior ao ideal – faz com que a execução da consulta maiores, que já está a aguardar vezes para as atuais e outras consultas.

A base de dados do SQL Server determina o plano de execução de consulta com o menor custo para uma execução de consulta. Como o tipo de alteração de consultas e cargas de trabalho, por vezes, os planos existentes já não são eficientes ou, talvez a base de dados SQL efetuou uma avaliação de boa. Como um fim de correção, planos de execução de consulta podem ser manualmente forçados.

Neste padrão de desempenho detetável combina três casos diferentes de regressão plano: novo regressão de plano, regressão de plano antigo e carga de trabalho de planos alterados existente. O tipo específico de regressão plano que ocorreram é fornecido no *detalhes* propriedade no registo de diagnóstico.

A condição de regressão novo plano refere-se num Estado em que a base de dados SQL inicia a executar um novo plano de execução de consulta não está tão eficiente como o plano antigo. A condição de regressão plano antigo refere-se para o estado para a base de dados SQL muda de utilizar um plano de novo, mais eficiente para o plano antigo, o qual não como eficiente como novo plano. A regressão de carga de trabalho de planos alterados existente refere-se para o estado em que o antigo e os novos planos continuamente alternativo, com o equilíbrio mais avançar para o plano fraco desempenho.

Para obter mais informações sobre regressões plano, consulte [Novidades plano regressão no SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Resolução de problemas

O registo de diagnóstico produz o hashes de consulta, bom plano ID, ID de plano incorreta e IDs de consulta. Pode utilizar estas informações como base para resolução de problemas.

Pode analisar o plano é melhor efetuar para consultas específicas que pode identificar com os hashes de consulta fornecidos. Após determinar que plano funciona melhor para as suas consultas, pode forçá-lo manualmente. 

Para obter mais informações, consulte [Saiba como o SQL Server impede plano regressões](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Sabia que intelligence incorporada de base de dados SQL pode gerir automaticamente os planos de execução de consulta best-realizar para as bases de dados?
>
> Para a otimização de desempenho contínuas da base de dados do SQL Server, recomendamos que ative [otimização automática da base de dados SQL](sql-database-automatic-tuning.md). Esta funcionalidade exclusiva de intelligence incorporada de base de dados SQL continuamente monitoriza a base de dados do SQL Server e automaticamente tunes e cria consulta best-realizar planos de execução para as bases de dados.
>

## <a name="database-scoped-configuration-value-change"></a>Alteração de valor de configuração de âmbito de base de dados

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável indica uma condição em que uma alteração na configuração do âmbito de base de dados faz com que a regressão de desempenho que é detetado em comparação comparada o comportamento de carga de trabalho de base de dados de sete dias anteriores. Este padrão indica que uma alteração recente efetuada à configuração do âmbito de base de dados não parece ser vantajoso para o desempenho de base de dados.

As alterações de configuração de âmbito de base de dados podem ser definidas para cada base de dados individuais. Esta configuração é utilizada numa base caso a caso, para otimizar o desempenho da base de dados individuais. As seguintes opções podem ser configuradas para cada base de dados individuais: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES e PROCEDURE_CACHE ENCRIPTADO.

### <a name="troubleshooting"></a>Resolução de problemas

O diagnóstico do registo saídas âmbito de base de dados de alterações de configuração efetuadas recentemente que causou a degradação do desempenho em comparação comparada o comportamento de carga de trabalho de sete dias anteriores. Pode reverter as alterações de configuração para os valores anteriores. Também pode otimizar valor por valor até que o nível de desempenho pretendido for atingido. Pode copiar os valores de configuração de âmbito de base de dados de uma base de dados semelhante com o desempenho satisfatório. Se não for possível resolver o desempenho, reverter para os valores de predefinição de base de dados do SQL Server predefinidos e tentar ajustar a partir desta linha de base.

Para obter mais informações sobre a otimizar a configuração de âmbito de base de dados e a sintaxe de T-SQL na alteração da configuração, consulte [(Transact-SQL) de configuração de âmbito de base de dados de Alter](https://msdn.microsoft.com/en-us/library/mt629158.aspx).

## <a name="slow-client"></a>Cliente lenta

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável indica uma condição em que o cliente utilizando a base de dados do SQL Server não pode consumir o resultado da base de dados rápido como a base de dados envia os resultados. Porque a base de dados SQL não está a armazenar os resultados das consultas executados numa memória intermédia, atrasar e aguarda que o cliente consumir as saídas de consulta transmitidos antes de continuar. Esta condição também poderá estar relacionado com uma rede que não é suficientemente rápida suficiente para transmitir saídas da base de dados SQL para o cliente de consumo.

Esta condição é gerada apenas se um regressão de desempenho é detetado em comparação com o comportamento de carga de trabalho de base de dados de sete dias anteriores. Este problema de desempenho é detetado apenas se ocorrer uma degradação do desempenho estatisticamente significativas em comparação com um comportamento de desempenho anteriores.

### <a name="troubleshooting"></a>Resolução de problemas

Neste padrão de desempenho detetável indica uma condição do lado do cliente. Resolução de problemas é necessária a aplicação do lado do cliente ou de rede do lado do cliente. O registo de diagnóstico produz os hashes de consulta e tempos de espera que parecem ser à espera que o máximo para o cliente aceder aos mesmos dentro de duas horas anteriores. Pode utilizar estas informações como base para resolução de problemas.

Pode otimizar o desempenho da sua aplicação para o consumo destas consultas. Também pode considerar a problemas de latência de rede possível. Uma vez que o problema resultar numa degradação do desempenho foi baseado em alteração na última linha de base do desempenho de sete dias, pode investigar se as alterações recentes de rede ou de aplicação da condição causada este evento de desempenho de regressão. 

## <a name="pricing-tier-downgrade"></a>Mudança para versão anterior do escalão de preço

### <a name="what-is-happening"></a>O que acontece

Neste padrão de desempenho detetável indica uma condição em que o escalão de preço da sua subscrição da base de dados SQL foi alterada uma versão anterior. Devido a redução de recursos (DTUs) disponíveis para a base de dados, o sistema detetou uma redução no desempenho da base de dados atual, em comparação comparado a linha de base de sete dias anterior.

Além disso, poderia ser uma condição em que o escalão de preço da sua subscrição da base de dados SQL foi alterada uma versão anterior e, em seguida, atualizar para um escalão superior num período de tempo curto. Deteção desta degradação do desempenho temporário é debitada na secção Detalhes de registo de diagnóstico como uma mudança para versão anterior do escalão de preço e a atualização.

### <a name="troubleshooting"></a>Resolução de problemas

Se reduzir o escalão de preço e, por isso as DTUs disponíveis para a base de dados SQL e estiver satisfeito com o desempenho, não há nada que precisa de fazer. Se reduzir o escalão de preço e tiver unsatisfied com o desempenho de base de dados do SQL Server, reduza as cargas de trabalho de base de dados ou considere aumentar o escalão de preço para um nível mais elevado.

## <a name="recommended-troubleshooting-flow"></a>Fluxo de resolução de problemas recomendado

 Siga o fluxograma uma abordagem recomendada resolver problemas de desempenho utilizando Insights inteligente.

Acesso Insights inteligente através do portal do Azure acedendo a análise de SQL do Azure. Tentativa de localizar o alerta de desempenho recebidos e selecioná-lo. Identifique-se de que está a acontecer na página de deteções. Observe a análise da causa raiz fornecido do problema, texto da consulta, tendências de hora de consulta e evolução incidente. Tentativa de resolver o problema utilizando a recomendação de Insights inteligente para mitigar o problema de desempenho. 

[![Fluxograma de resolução de problemas](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Selecione o fluxograma para transferir uma versão PDF.

Insights inteligentes normalmente necessita de uma hora de tempo para executar a análise da causa raiz do problema de desempenho. Se não é possível localizar o problema no Insights inteligente e é fundamental para si, utilize o arquivo de consultas manualmente identificar a causa do problema de desempenho. (Normalmente, estes problemas são inferior a um horas depois.) Para obter mais informações, consulte [monitorizar o desempenho ao utilizar o arquivo de consultas](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Passos seguintes
- Saiba [Insights inteligente](sql-database-intelligent-insights.md) conceitos.
- Utilize o [registo de diagnóstico de desempenho inteligente Insights SQL Database do Azure](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitor [SQL Database do Azure utilizando o Azure SQL Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql).
- Saiba como [recolher e consumir dados de registo dos seus recursos do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
