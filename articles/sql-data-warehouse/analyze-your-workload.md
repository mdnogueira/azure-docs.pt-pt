---
title: Analisar a carga de trabalho - Azure SQL Data Warehouse | Microsoft Docs
description: "Técnicas para analisar a atribuição de prioridades de consulta para a carga de trabalho no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 98617f6b8366662e52d00420adc4c81abffc598d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="analyze-your-workload"></a>Analisar a carga de trabalho
Técnicas para analisar a atribuição de prioridades de consulta para a carga de trabalho no Azure SQL Data Warehouse.

## <a name="workload-groups"></a>Grupos de carga de trabalho 
O SQL Data Warehouse implementa classes de recursos através da utilização de grupos de carga de trabalho. Existem um total de oito grupos de carga de trabalho que controlam o comportamento das classes de recursos entre os vários tamanhos DWU. Para qualquer DWU, o SQL Data Warehouse utiliza apenas quatro grupos de carga de oito trabalho. Isto faz sentido porque cada grupo de carga de trabalho é atribuído a uma das quatro classes de recursos: smallrc mediumrc, largerc, ou xlargerc. A importância de compreender os grupos de carga de trabalho é que alguns destes grupos de carga de trabalho estiverem definido como superiores a *importância*. Importância é utilizada para a CPU agendamento. Consultas executadas com importância elevada irão receber três vezes mais ciclos da CPU que as com importância Média. Por conseguinte, mapeamentos de ranhura de concorrência também determinam a prioridade da CPU. Quando uma consulta consome ranhuras 16 ou mais, que seja executada como importância elevada.

A tabela seguinte mostra os mapeamentos de importância para cada grupo de carga de trabalho.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapeamentos de grupo de carga de trabalho para as ranhuras de simultaneidade e importância

| Grupos de carga de trabalho | Mapeamento de ranhura de concorrência | MB / distribuição (elasticidade) | MB / distribuição (computação) | Mapeamento de importância |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Médio             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Médio             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | Médio             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Médio             |
| SloDWGroupC04   | 16                       |  1,600                         | 4000                        | Elevado               |
| SloDWGroupC05   | 32                       |  3,200                         | 8000                        | Elevado               |
| SloDWGroupC06   | 64                       |  6,400                         | 16,000                      | Elevado               |
| SloDWGroupC07   | 128                      | 12,800                         | 32,000                      | Elevado               |
| SloDWGroupC08   | 256                      | 25,600                         | 64,000                      | Elevado               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
Do **alocação e o consumo de ranhuras de concorrência** gráfico, pode ver que um DW500 utiliza 1, 4, 8 ou ranhuras de concorrência 16 para smallrc, mediumrc, largerc e xlargerc, respetivamente. Pode procurar esses valores no gráfico anterior para determinar a importância para cada classe de recursos.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapeamento de DW500 de classes de recursos para importância
| Classe de recursos | Grupo de carga de trabalho | Ranhuras de concorrência utilizadas | MB / distribuição | Importância |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Médio     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Médio     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Médio     |
| xlargerc       | SloDWGroupC04  | 16                     | 1,600             | Elevado       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Médio     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Médio     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Médio     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Médio     |
| staticrc50     | SloDWGroupC03  | 16                     | 1,600             | Elevado       |
| staticrc60     | SloDWGroupC03  | 16                     | 1,600             | Elevado       |
| staticrc70     | SloDWGroupC03  | 16                     | 1,600             | Elevado       |
| staticrc80     | SloDWGroupC03  | 16                     | 1,600             | Elevado       |

## <a name="view-workload-groups"></a>Grupos de carga de trabalho de vista
Pode utilizar a seguinte consulta DMV para examinar as diferenças na alocação de recursos de memória em detalhe da perspectiva do Governador de recursos ou para analisar a utilização do Active Directory e histórico dos grupos de carga de trabalho quando a resolução de problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Deteção de consulta em fila e outros DMVs
Pode utilizar o `sys.dm_pdw_exec_requests` DMV para identificar as consultas que estão a aguardar na fila de concorrência. Consulta a aguardar uma ranhura de concorrência terá um Estado de **suspenso**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Funções de gestão de carga de trabalho podem ser visualizadas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A consulta seguinte mostra a qual cada utilizador tem atribuída para a função.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

O SQL Data Warehouse tem os seguintes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: consultas manter-se fora do framework de ranhura de concorrência. Consultas DMV e sistema funciona como `SELECT @@VERSION` são exemplos de consultas locais.
* **UserConcurrencyResourceType**: consultas manter-se no interior do framework de ranhura de concorrência. Consultas de tabelas do utilizador final representam exemplos que pretende utilizar este tipo de recurso.
* **DmsConcurrencyResourceType**: aguarda resultantes de operações de movimento de dados.
* **BackupConcurrencyResourceType**: este espera indica que uma base de dados está a ser efetuada. O valor máximo para este tipo de recurso é 1. Se tiverem sido solicitadas várias cópias de segurança ao mesmo tempo, os outros ficarão em fila.

O `sys.dm_pdw_waits` DMV pode ser utilizado para ver quais os recursos que está a aguardar um pedido.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

O `sys.dm_pdw_resource_waits` DMV mostra apenas os poucos recursos consumidos por uma consulta fornecida. Tempo de espera de recursos apenas mede o tempo à espera de recursos para ser fornecido, por oposição a tempo de espera de sinal, que é o tempo que demora para os SQL servers subjacentes agendar a consulta para a CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
Também pode utilizar o `sys.dm_pdw_resource_waits` DMV calcular quantos ranhuras de concorrência tenham sido concedidas.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

O `sys.dm_pdw_wait_stats` DMV pode ser utilizado para análise de tendências históricos de poucos.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a gestão de utilizadores de base de dados e segurança, consulte [proteger uma base de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre como maiores classes de recurso podem melhorar a qualidade de índice columnstore em cluster, consulte [reconstruir índices para melhorar a qualidade de segmento].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[reconstruir índices para melhorar a qualidade de segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
