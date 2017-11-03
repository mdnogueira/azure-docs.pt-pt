---
title: "Monitorização de SQL Database do Azure utilizar as vistas de gestão dinâmica | Microsoft Docs"
description: "Saiba como detetar e diagnosticar problemas de desempenho comuns utilizando vistas de gestão dinâmica para monitorizar a base de dados do Microsoft Azure SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: d08f505f-3c62-47d4-bab7-35c9a834b79b
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 298adcad78163e82a926abb684a172d56b4a33dd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitorização da Base de Dados SQL do Azure utilizando vistas de gestão dinâmica
Base de dados do Microsoft Azure SQL permite que um subconjunto de vistas de gestão dinâmica para diagnosticar problemas de desempenho, que podem ser causados por consultas bloqueadas ou de execução longa, congestionamentos de recursos, planos de consulta fraco e assim sucessivamente. Este tópico fornece informações sobre como detetar problemas de desempenho comuns ao utilizar as vistas de gestão dinâmica.

Base de dados SQL suporta parcialmente três categorias de vistas de gestão dinâmica:

* Vistas de gestão dinâmica relacionadas com a base de dados.
* Vistas de gestão dinâmica relacionadas com a execução.
* Vistas de gestão dinâmica relacionadas com a transação.

Para obter informações detalhadas sobre as vistas de gestão dinâmica, consulte [funções (Transact-SQL) e vistas de gestão dinâmica](https://msdn.microsoft.com/library/ms188754.aspx) no SQL Server Books Online.

## <a name="permissions"></a>Permissões
Necessita de consultar uma vista de gestão dinâmica na base de dados do SQL Server, **vista de estado de base de dados** permissões. O **vista de estado de base de dados** permissão devolve informações sobre todos os objetos na base de dados atual.
Para conceder a **vista de estado de base de dados** permissão a um utilizador de base de dados específica, execute a seguinte consulta:

```GRANT VIEW DATABASE STATE TO database_user; ```

Numa instância do SQL Server no local, vistas de gestão dinâmica devolvem informações de estado do servidor. Na base de dados do SQL Server, devolvem informações relativas a lógica base de dados atual apenas.

## <a name="calculating-database-size"></a>A calcular o tamanho da base de dados
A seguinte consulta devolve o tamanho da base de dados (em megabytes):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

A seguinte consulta devolve o tamanho dos objetos individuais (em megabytes) na base de dados:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Ligações de monitorização
Pode utilizar o [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) vista para obter informações sobre as ligações estabelecidas para um servidor específico da SQL Database do Azure e os detalhes de cada ligação. Além disso, o [sys.dm exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) vista é útil ao obter informações sobre todas as ligações de utilizador do Active Directory e tarefas internas.
A consulta seguinte obtém informações sobre a ligação atual:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Ao executar o **sys.dm_exec_requests** e **sys.dm exec_sessions vistas**, se tiver **vista de estado de base de dados** permissão na base de dados, consulte a executar todas as sessões na base de dados; caso contrário, verá apenas a sessão atual.
> 
> 

## <a name="monitoring-query-performance"></a>Monitorização do desempenho de consulta
Lenta ou de longa execução de consultas pode consumir recursos significativos de sistema. Esta secção demonstra como utilizar vistas de gestão dinâmica para detetar alguns problemas de desempenho de consulta comuns. É uma referência anterior, mas ainda útil para resolução de problemas, a [resolução de problemas de desempenho no SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artigo no Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Localizar consultas principais N
O exemplo seguinte devolve informações sobre as consultas de cinco superiores classificado média do tempo de CPU. Neste exemplo agrega as consultas de acordo com os respetivos hash de consulta, para que as consultas logicamente equivalentes são agrupadas pelo respetivo consumo de recursos cumulativa.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitorização consultas bloqueadas
As consultas lentas ou execução longa podem contribuir para o consumo excessivo de recursos e ser consequence de consultas bloqueadas. A causa do bloqueio pode ser design da aplicação fraco, planos de consulta inválido, falta de índices útil e assim sucessivamente. Pode utilizar a vista de sys.dm_tran_locks para obter informações sobre a atividade atual de bloqueio na base de dados SQL do Azure. Por exemplo de código, consulte [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) no SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Monitorização planos de consulta
Um plano de consulta ineficaz também pode aumentar o consumo da CPU. O exemplo seguinte utiliza o [exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) vista para determinar que consulta utiliza a CPU mais cumulativa.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Consultar também
[Introdução à base de dados SQL](sql-database-technical-overview.md)

