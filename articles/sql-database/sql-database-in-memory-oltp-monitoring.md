---
title: "Monitorizar o armazenamento de dentro da memória XTP | Microsoft Docs"
description: "Monitor de armazenamento de dentro da memória XTP e estimativa utilizam, capacidade; Resolva o erro de capacidade 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: 613a9ced91d71cc9a65ea67e6ede1a78a03b4bd5
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>Armazenamento de monitor de memória OLTP
Quando utilizar [OLTP na memória](sql-database-in-memory.md), dados em tabelas com otimização de memória e variáveis de tabela residem no armazenamento do OLTP dentro da memória. Cada escalão de serviço Premium tem um tamanho de armazenamento de OLTP na memória máximo, o que é descrito da [única dos limites de recursos de base de dados](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e [dos limites de recursos do conjunto elástico](sql-database-resource-limits.md#elastic-pool-change-storage-size). Depois deste limite for excedido, insira e atualizar operações poderão começam a falhar (com o erro 41823). Nesse momento, será necessário para eliminar dados para reclamar memória, ou atualizar o escalão de desempenho da base de dados.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinar se a dados se encaixa na extremidade do armazenamento em memória
Determine os caps de armazenamento dos escalões de serviço Premium diferentes. Consulte [única dos limites de recursos de base de dados](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e [dos limites de recursos do conjunto elástico](sql-database-resource-limits.md#elastic-pool-change-storage-size).

Estimar requisitos de memória para funciona de uma tabela com otimização de memória da mesma forma para o SQL Server que se faz na SQL Database do Azure. Demorar alguns minutos para rever nesse tópico de [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tenha em atenção que a tabela e linhas de variável de tabela, bem como índices, contam para o tamanho de dados de utilizador máximo. Além disso, ALTER TABLE tem espaço suficiente para criar uma nova versão do respetivos índices e a tabela inteira.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Pode monitorizar a utilização de memória de armazenamento como uma percentagem da extremidade do armazenamento para o escalão de desempenho no [portal do Azure](https://portal.azure.com/): 

1. No painel da base de dados, localize a caixa de utilização de recursos e clique em Editar.
2. Selecione a métrica `In-Memory OLTP Storage percentage`.
3. Para adicionar um alerta, clique na caixa para abrir o painel de métrico de utilização de recursos, em seguida, clique em Adicionar alerta.

Ou utilize a seguinte consulta para mostrar a utilização de memória de armazenamento:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Corrija situações de memória esgotada - erro 41823
Resultados de execução-memória esgotada em operações de inserção, ATUALIZAÇÃO e criar a falhar com a mensagem de erro 41823.

Mensagem de erro 41823 indica que as variáveis de tabela e tabelas com otimização de memória excedeu o tamanho máximo.

Para resolver este erro, está:

* Eliminar dados de tabelas com otimização de memória, potencialmente descarregar dados para tabelas tradicionais, com base em disco; ou,
* Atualize o escalão de serviço para um com armazenamento suficiente na memória para os dados que tem de manter em tabelas com otimização de memória.

## <a name="next-steps"></a>Passos seguintes
Para monitorizar orientações, consulte [monitorização SQL Database do Azure utilizar as vistas de gestão dinâmica](sql-database-monitoring-with-dmvs.md).
