---
title: "Monitorizar as métricas do servidor de Analysis Services do Azure | Microsoft Docs"
description: "Saiba como monitorizar as métricas do servidor de Analysis Services no portal do Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/14/2017
ms.author: owend
ms.openlocfilehash: f9b32029f0a7065fff73ddb6417fc5c1c7e658a5
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="monitor-server-metrics"></a>Métricas do servidor de monitor

Do Analysis Services fornece as métricas para o ajudar a monitorizar o desempenho e o estado de funcionamento dos seus servidores. Por exemplo, monitorize a memória e utilização da CPU, número de ligações de cliente e o consumo de recursos de consulta. Como a maioria dos outros serviços do Azure, o Analysis Services utiliza a mesma estrutura de monitorização. Para obter mais informações, consulte [métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Para efetuar um diagnóstico mais aprofundado, controlar o desempenho e identificar tendências em vários recursos de serviço num grupo de recursos ou subscrição, utilize [Azure Monitor](https://azure.microsoft.com/services/monitor/). Monitor do Azure (serviço) poderá resultar num serviço sujeito a faturação.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Para monitorizar as métricas para um servidor de Analysis Services

1. No portal do Azure, selecione **métricas**.

    ![Monitorizar no portal do Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. No **as métricas disponíveis**, selecione as métricas para incluir no gráfico. 

    ![Gráfico do monitor](./media/analysis-services-monitor/aas-monitor-chart.png)

## <a name="server-metrics"></a>Métricas do servidor
Utilize esta tabela para determinar quais as métricas são melhores para o seu cenário de monitorização. Apenas as métricas da mesma unidade podem ser apresentadas no mesmo gráfico.

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Comprimento de fila de tarefa de conjunto de comandos|Contagem|Média|Número de tarefas na fila do conjunto de threads de comando.|
|CurrentConnections|Ligação: Ligações atuais|Contagem|Média|Número atual de ligações de cliente estabelecido.|
|CurrentUserSessions|Sessões de utilizador atual|Contagem|Média|Número atual de sessões de utilizador estabelecida.|
|mashup_engine_memory_metric|M motor memória|Bytes|Média|Utilização de memória por processos de motor da aplicação híbrida|
|mashup_engine_qpu_metric|Motor de M QPU|Contagem|Média|Utilização QPU por processos de motor da aplicação híbrida|
|memory_metric|Memória|Bytes|Média|Memória. No intervalo 0-25 GB para S1, 0 a 50 GB para S2 e 0-100 GB para S4|
|memory_thrashing_metric|Memória Thrashing|Percentagem|Média|Thrashing média da memória.|
|CleanerCurrentPrice|Memória: Preço atual de limpeza|Contagem|Média|Preços atual de memória, $/ byte/hora, normalizado a 1000.|
|CleanerMemoryNonshrinkable|Memória: Memória nonshrinkable de limpeza|Bytes|Média|Quantidade de memória, em bytes, não sujeitos a remoção de limpeza por segundo plano.|
|CleanerMemoryShrinkable|Memória: Memória de limpeza que possam ser encolhida|Bytes|Média|Quantidade de memória, em bytes, sujeita a remoção de limpeza por segundo plano.|
|MemoryLimitHard|Memória: Disco rígido de limite de memória|Bytes|Média|Limite de memória de disco rígido, ficheiro de configuração.|
|MemoryLimitHigh|Memória: Limite de memória elevada|Bytes|Média|Limite de memória elevada, ficheiro de configuração.|
|MemoryLimitLow|Memória: Baixa de limite de memória|Bytes|Média|Limite de memória insuficiente, ficheiro de configuração.|
|MemoryLimitVertiPaq|Memória: VertiPaq de limite de memória|Bytes|Média|Limite de memória, ficheiro de configuração.|
|MemoryUsage|Memória: Utilização de memória|Bytes|Média|Utilização de memória do processo de servidor, conforme utilizado para calcular preço de memória de limpeza. Igual a Process\PrivateBytes mais o tamanho dos dados de mapeamento de memória, ignorando quaisquer memória, o que foi mapeada ou atribuída pelo motor de análise de memória (VertiPaq) que excedam o limite de memória do motor do contador.|
|Quota|Memória: Quota|Bytes|Média|Quota atual de memória, em bytes. Quota de memória também é conhecido como uma reserva de memória ou de concessão de memória.|
|QuotaBlocked|Memória: Quota bloqueado|Contagem|Média|Número atual de pedidos de quota são bloqueada até que outros quotas de memória são libertadas.|
|VertiPaqNonpaged|Memória: VertiPaq na memória não paginável|Bytes|Média|Bytes de memória bloqueadas no conjunto de trabalho para utilização pelo motor dentro da memória.|
|VertiPaqPaged|Memória: VertiPaq bloco paginado|Bytes|Média|Bytes de memória paginada em utilização para dados em memória.|
|ProcessingPoolJobQueueLength|Comprimento de fila de tarefa de conjunto de processamento|Contagem|Média|Número de tarefas não-I/O na fila do conjunto de threads de processamento.|
|RowsConvertedPerSec|Processamento: Converter o linhas por seg|CountPerSecond|Média|Taxa de linhas converter durante o processamento.|
|RowsReadPerSec|Processamento: Linhas lidas por seg|CountPerSecond|Média|Taxa de linhas lida todas as bases de dados relacionais.|
|RowsWrittenPerSec|Processamento: Linhas escritas por seg|CountPerSecond|Média|Taxa de linhas escritos durante o processamento.|
|qpu_metric|QPU|Contagem|Média|QPU. Intervalo de 0-100 para S1, 0-200 para S2 e 0-400 para S4|
|QueryPoolBusyThreads|Threads de ocupado de conjunto de consulta|Contagem|Média|Número de threads ocupados no conjunto de threads de consulta.|
|SuccessfullConnectionsPerSec|Ligações com êxito por seg|CountPerSecond|Média|Taxa de conclusões de ligação com êxito.|
|CommandPoolBusyThreads|Threads: Threads ocupado do conjunto de comandos|Contagem|Média|Número de threads ocupados no conjunto de threads de comando.|
|CommandPoolIdleThreads|Threads: Threads de inatividade do conjunto de comandos|Contagem|Média|Número de threads Inativos no conjunto de threads de comando.|
|LongParsingBusyThreads|Threads: Análise de threads ocupados de longa|Contagem|Média|Número de threads ocupados no conjunto de threads longa análise.|
|LongParsingIdleThreads|Threads: Longa análise threads Inativos|Contagem|Média|Número de threads Inativos no conjunto de threads longa análise.|
|LongParsingJobQueueLength|Threads: Análise longa comprimento da fila de tarefas|Contagem|Média|Número de tarefas na fila do conjunto de threads longa análise.|
|ProcessingPoolIOJobQueueLength|Threads: Conjunto de comprimento de fila de trabalho de e/s de processamento|Contagem|Média|Número de tarefas de e/s na fila do conjunto de threads de processamento.|
|ProcessingPoolBusyIOJobThreads|Threads: Threads de trabalho de e/s ocupados do conjunto de processamento|Contagem|Média|Número de threads em execução tarefas de e/s no conjunto de threads de processamento.|
|ProcessingPoolBusyNonIOThreads|Threads: Threads de não-I/O ocupado do conjunto de processamento|Contagem|Média|Número de threads em execução de tarefas não-I/O no conjunto de threads de processamento.|
|ProcessingPoolIdleIOJobThreads|Threads: Conjunto Inativos threads de trabalho de e/s de processamento|Contagem|Média|Número de threads de inatividade para as tarefas de e/s no conjunto de threads de processamento.|
|ProcessingPoolIdleNonIOThreads|Threads: Threads de não-I/O inativo do conjunto de processamento|Contagem|Média|Número de threads Inativos no conjunto de threads de processamento dedicado a tarefas não-I/O.|
|QueryPoolIdleThreads|Threads: Threads de inatividade do conjunto de consulta|Contagem|Média|Número de threads de inatividade para as tarefas de e/s no conjunto de threads de processamento.|
|QueryPoolJobQueueLength|Threads: Comprimento de fila de tarefa de conjunto de consulta|Contagem|Média|Número de tarefas na fila do conjunto de threads de consulta.|
|ShortParsingBusyThreads|Threads: Curto período análise threads ocupados|Contagem|Média|Número de threads ocupados no conjunto de threads de análise curto.|
|ShortParsingIdleThreads|Threads: Curto período análise threads Inativos|Contagem|Média|Número de threads Inativos no conjunto de threads de análise curto.|
|ShortParsingJobQueueLength|Threads: Curto período comprimento de fila de tarefa de análise|Contagem|Média|Número de tarefas na fila do conjunto de threads de análise curto.|
|TotalConnectionFailures|Falhas de ligação total|Contagem|Média|Total de tentativas de ligação falhadas.|
|TotalConnectionRequests|Pedidos de ligação total|Contagem|Média|Pedidos de ligação total. |

## <a name="next-steps"></a>Passos seguintes
[Monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Métricas no Monitor de Azure REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx)