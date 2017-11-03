---
title: "Monitorização na base de dados do Azure para PostgreSQL | Microsoft Docs"
description: "Este artigo descreve as métricas de monitorização e alertas da base de dados do Azure para PostgreSQL, incluindo a CPU, os limites, armazenamento e estatísticas de ligação."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: d48159fbc5e52bf1916a744e3912ca7ceb0ab60f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>Monitorização na base de dados do Azure para PostgreSQL
Dados sobre os servidores de monitorização ajuda-o a resolver problemas e otimizar a sua carga de trabalho. Base de dados do Azure para PostgreSQL fornece várias métricas que permitem determinar o comportamento de recursos que suportam o servidor de PostgreSQL. 

## <a name="metrics"></a>Métricas
Todas as métricas do Azure tem uma frequência de um minuto e cada métrica fornece 30 dias do histórico. 

Pode configurar alertas nas métricas. Para orientações passo a passo, consulte [como configurar alertas](howto-alert-on-metric.md). 

Outras tarefas incluem como configurar as ações automatizadas, efetuar análises avançadas e arquivar histórico. Para obter mais informações, consulte o [descrição geral do Azure métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas estão disponíveis para a base de dados do Azure para PostgreSQL:

|Métrica|Nome a apresentar métrica|Unidade|Descrição|
|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|A percentagem de CPU em utilização.|
|compute_limit|Limite de unidade de computação|Contagem|Número máximo deste servidor de unidades de computação|
|compute_consumption_percent|Percentagem de unidade de computação|Percentagem|A percentagem de unidades de computação utilizada fora do servidor 's máxima.|
|memory_percent|Percentagem de memória|Percentagem|A percentagem de memória em utilização.|
|io_consumption_percent|Percentagem de e/s|Percentagem|A percentagem de e/s em utilização.|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento utilizado fora do servidor 's máxima.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em utilização. O armazenamento utilizado pelo serviço inclui os ficheiros de base de dados, os registos de transações e os registos do servidor.|
|storage_limit|Limite de armazenamento|Bytes|Armazenamento máximo para este servidor.|
|active_connections|Totais ligações ativas|Contagem|O número de ligações ativas para o servidor.|
|connections_failed|Totais de ligações com falhas|Contagem|O número de ligações com falhas ao servidor.|


> [!NOTE]
> Computação que unidade é composta de memória e CPU. A percentagem de unidade de computação é máxima (% de memória, cpu de %). Examine os gráficos de memória e cpu para identificar qual é a contribuir para alterações de percentagem de unidade de computação. Para obter mais informações, consulte [computação unidades](concepts-compute-unit-and-storage.md).

## <a name="next-steps"></a>Passos seguintes
- Para orientações passo a passo, consulte [como configurar alertas](howto-alert-on-metric.md). 
- Para obter mais informações sobre como aceder e exportar com o portal do Azure, a REST API ou a CLI de métricas, consulte o [descrição geral do Azure métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
