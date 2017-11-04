---
title: Monitorizar a carga de trabalho com DMVs | Microsoft Docs
description: Saiba como monitorizar a carga de trabalho com DMVs.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 69ecd479-0941-48df-b3d0-cf54c79e6549
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: 7ce6c2cdf1e28852da536414533ccdcdaeb437e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitorizar a carga de trabalho com DMVs
Este artigo descreve como utilizar as vistas de gestão dinâmica (DMVs) para monitorizar a carga de trabalho e investigar a execução de consultas no armazém de dados SQL do Azure.

## <a name="permissions"></a>Permissões
Para consultar os DMVs neste artigo, necessita de permissão de vista de estado de base de dados ou controlo. Normalmente, conceder vista base de dados de estado é a permissão preferencial é muito mais restritiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Ligações de monitor
Todos os inícios de sessão ao SQL Data Warehouse com sessão iniciados [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Este DMV contém os últimos 10 000 inícios de sessão.  O session_id é a chave primária e tem atribuído sequencialmente para cada início de sessão de novo.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Execução de consulta do monitor
Todas as consultas executadas no armazém de dados do SQL Server são registadas [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Este DMV contém as últimos 10 000 consultas executadas.  O request_id exclusivamente identifica cada consulta e é a chave primária para este DMV.  O request_id é atribuído sequencialmente para cada nova consulta e é o prefixo QID, que representa o ID de consulta.  Consultar este DMV para um determinado session_id mostra todas as consultas para um início de sessão especificado.

> [!NOTE]
> Os procedimentos armazenados utilizam vários IDs de pedido.  Os IDs de pedido são atribuídos por ordem sequencial. 
> 
> 

Eis os passos a seguir para investigar os planos de execução de consulta e as horas para uma consulta específica.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>PASSO 1: Identificar a consulta que pretende investigar
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Nos resultados de consulta anterior, **tenha em atenção o ID do pedido** da consulta que pretende investigar.

As consultas no **suspenso** Estado estão a ser colocados em fila devido a limites de concorrência. Estas consultas são também apresentados na consulta aguarda sys.dm_pdw_waits com um tipo de UserConcurrencyResourceType. Consulte [simultaneidade e carga de trabalho de gestão] [ Concurrency and workload management] para obter mais detalhes sobre os limites de concorrência. As consultas também podem aguardar por outros motivos, tais como de bloqueios de objeto.  Se a consulta está à espera de um recurso, consulte o artigo [investigar consultas aguardar recursos] [ Investigating queries waiting for resources] mais abaixo neste artigo.

Para simplificar a pesquisa de uma consulta na tabela sys.dm_pdw_exec_requests, utilize [etiqueta] [ LABEL] para atribuir um comentário a sua consulta que pode ser pesquisada na vista de sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>PASSO 2: Investigar o plano de consulta
Utilize o ID do pedido para obter o plano SQL (DSQL) distribuído a consulta de [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando um plano DSQL está a demorar mais que o esperado, a causa pode ser um plano complexo com vários passos DSQL ou apenas um passo demorar muito tempo.  Se o plano de vários passos com várias operações de movimentação, é aconselhável as distribuições de tabela para reduzir o movimento de dados. O [tabela distribuição] [ Table distribution] artigo explica a razão pela qual os dados têm de ser movidos para resolver uma consulta e explica algumas estratégias de distribuição para minimizar o movimento de dados.

Para investigação mais aprofundada detalhes sobre um único passo, o *operation_type* coluna de execução longa passo de consulta e tenha em atenção o **passo índice**:

* Continuar com o passo 3a para **operações SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Continuar com o passo 3b para **operações de movimento de dados**: ShuffleMoveOperation BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>PASSO 3a: investigar SQL nas bases de dados distribuídas
Utilize o ID do pedido e o índice de passo para obter detalhes da [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], que contém informações de execução do passo de consulta em todas as bases de dados distribuídas.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando o passo de consulta está em execução, [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] pode ser utilizado para obter o plano de estimado do SQL Server da cache do plano de SQL Server para o passo em execução numa distribuição específica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>Passo 3b: investigar o movimento de dados nas bases de dados distribuídas
Utilize o ID do pedido e o índice de passo para obter informações sobre um passo de movimento de dados em execução em cada distribuição de [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Verifique o *total_elapsed_time* coluna para ver se uma determinada distribuição está a demorar significativamente maior do que outras pessoas para movimento de dados.
* Para a distribuição de longa execução, verifique o *rows_processed* coluna para ver se o número de linhas a ser movidos dessa distribuição é significativamente maior do que outras pessoas. Se Sim, isto pode indicar dissimetrias dos seus dados subjacentes.

Se estiver a executar a consulta, [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] pode ser utilizado para obter o plano de estimado do SQL Server da cache de planos de SQL Server para o passo de SQL Server em execução dentro de uma distribuição específica.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Consultas de espera de monitor
Se descobrir que progresso não é efetuar a consulta porque está a aguardar para um recurso, segue-se uma consulta que mostra todos os recursos que está a aguardar uma consulta.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se a consulta está ativamente aguardar a resposta de recursos a partir de outra consulta, em seguida, o estado será **AcquireResources**.  Se a consulta tem todos os recursos necessários, em seguida, o estado será **Granted**.

## <a name="monitor-tempdb"></a>Monitor tempdb
A utilização de tempdb elevado pode ser a causa de raiz para um desempenho lento e fora de problemas de memória. Verifique primeiro se tiver dados qualidade dissimetrias ou fraco rowgroups e tome as ações adequadas. Considere a dimensionar o seu armazém de dados se encontrar tempdb atingir os limites durante a execução de consulta. O seguinte descreve como identificar a utilização de tempdb por consulta em cada nó. 

Crie a vista seguinte para associar o id de nó adequado para sys.dm_pdw_sql_requests. Isto permitirá tirar partido de outras DMVs pass-through e associar as tabelas com sys.dm_pdw_sql_requests.

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
Execute a seguinte consulta para monitorizar tempdb:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>Monitor de memória

Memória pode ser a causa de raiz para um desempenho lento e fora de problemas de memória. Verifique primeiro se tiver dados qualidade dissimetrias ou fraco rowgroups e tome as ações adequadas. Considere a dimensionar o seu armazém de dados se encontrar utilização de memória do SQL Server atingir os limites durante a execução de consulta.

A seguinte consulta devolve o SQL Server memória e utilização pressão de memória por nó:   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>Tamanho do registo de transação de monitor
A seguinte consulta devolve o tamanho do registo de transações em cada distribuição. Verifique se tem dados qualidade dissimetrias ou fraco rowgroups e tome as ações adequadas. Se um dos ficheiros de registo está a atingir 160GB, deve considerar como aumentar verticalmente a sua instância ou limitar o tamanho da transação. 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
AND counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>Monitorizar a reversão da transacção de registo
Se as suas consultas estão a falhar ou a demorar muito tempo para continuar, pode verificar e monitorizar se tiver quaisquer transações reverter.
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="next-steps"></a>Passos seguintes
Consulte [vistas de sistema] [ System views] para obter mais informações sobre DMVs.
Consulte [melhores práticas do SQL Data Warehouse] [ SQL Data Warehouse best practices] para obter mais informações sobre as melhores práticas

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
