---
title: "Classes de recursos para a gestão de carga de trabalho - Azure SQL Data Warehouse | Microsoft Docs"
description: "Orientações para a utilização de classes de recursos para gerir a simultaneidade e recursos para consultas no armazém de dados SQL do Azure de computação."
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
ms.openlocfilehash: 122646f73b6e4e7c62eb0e6d4b6672b603d8acb2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="resource-classes-for-workload-management"></a>Classes de recursos para a gestão de carga de trabalho
Orientações para a utilização de classes de recursos para gerir o número de consultas em simultâneo que são executados em simultâneo e recursos para consultas no armazém de dados SQL do Azure de computação.
 
## <a name="what-is-workload-management"></a>O que é a gestão de carga de trabalho?
Gestão de carga de trabalho é a capacidade para otimizar o desempenho global de todas as consultas. Executa uma carga de trabalho bem atento consultas e operações de carga de forma eficiente, independentemente de estar computação intensivas ou e/s intensivas. 

Armazém de dados do SQL Server fornece capacidades de gestão de carga de trabalho para ambientes de vários utilizadores. Um armazém de dados não se destina a multi-inquilinos cargas de trabalho.

## <a name="what-are-resource-classes"></a>Quais são as classes de recurso?
Classes de recursos são os limites de recursos previamente determinada que regulam a execução da consulta. O SQL Data Warehouse limita os recursos de computação para cada consulta de acordo com a classe de recursos. 

Classes de recursos ajudam a gerir o desempenho global da sua carga de trabalho do armazém de dados. Utilizar classes de recursos de forma eficaz ajuda-o a gerir a sua carga de trabalho através da definição de limites no número de consultas que são executadas em simultâneo e os recursos de computação atribuídos a cada consulta. 

- Classes de recursos mais pequenas utilizam menos recursos de computação mas ativar maior simultaneidade de consulta global
- Classes de recurso maior fornecem mais recursos de computação mas restringir a simultaneidade de consulta

Classes de recurso foram concebidas para as atividades de gestão e manipulação de dados. Algumas consultas muito complexas beneficiarão também quando existem associações grande e ordena para que o sistema executa a consulta na memória, em vez de transbordar para disco.

As seguintes operações são regidas pelas classes de recursos:

* SELECIONE DE INSERT, UPDATE, DELETE
* SELECIONE (quando consultar tabelas de utilizador)
* ALTER INDEX - RECONSTRUÇÃO ou REORGANIZE
* ALTER TABLE REBUILD
* CRIAR O ÍNDICE
* CRIAR O ÍNDICE COLUMNSTORE EM CLUSTER
* CRIAR TABLE AS SELECT (CTAS)
* Carregamento de dados
* Operações de movimento de dados efetuadas pelo serviço de movimento de dados (DMS)

> [!NOTE]  
> SELECIONE as instruções em vistas de gestão dinâmica (DMVs) ou outro sistema vistas não são regidas por qualquer uma dos limites de concorrência. Pode monitorizar o sistema independentemente do número de consultas em execução no mesmo.
> 
> 

## <a name="static-and-dynamic-resource-classes"></a>Classes de recursos dinâmicos e estáticos

Existem dois tipos de classes de recurso: estáticos e dinâmicos.

- **Classes de recurso estático** atribuir a mesma quantidade de memória, independentemente do nível de serviço atual, o que é medido em [unidades do armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md). Este alocação estática significa no maiores níveis de serviço, que pode executar consultas mais em cada classe de recursos.  As classes de recurso estáticos são denominadas staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70 e staticrc80. Estas classes de recursos são mais adequadas para soluções que aumentar a classe de recursos para obter recursos de computação adicionais.

- **Classes de recursos dinâmicos** alocar uma variável quantidade de memória consoante o nível de serviço atual. Ao aumentar verticalmente para um maior nível de serviço, as suas consultas obtém automaticamente mais memória. As classes de recursos dinâmicos são denominadas smallrc, mediumrc, largerc e xlargerc. Estas classes de recursos são mais adequadas para soluções que aumento computação escala para obter recursos adicionais. 

O [escalões de desempenho](performance-tiers.md) use os mesmos nomes de classe de recursos, mas têm diferentes [especificações de memória e simultaneidade](performance-tiers.md). 


## <a name="assigning-resource-classes"></a>Atribuição de recursos de classes

Classes de recursos são implementadas por atribuir utilizadores a funções de base de dados. Quando um utilizador executa uma consulta, a consulta é executada com classe de recursos do utilizador. Por exemplo, quando um utilizador é membro da função de base de dados smallrc ou staticrc10, executam as consultas com pequenas quantidades de memória. Quando um utilizador de base de dados é um membro das funções de base de dados xlargerc ou staticrc80, executam as consultas com grandes quantidades de memória. 

Para aumentar a classe de recursos de um utilizador, utilize o procedimento armazenado [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Para diminuir a classe de recursos, utilize [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

A classe de recursos do administrador do serviço é fixo e não pode ser alterada.  O administrador de serviço é o utilizador criado durante o processo de aprovisionamento.

### <a name="default-resource-class"></a>Classe de recursos predefinidos
Por predefinição, cada utilizador é membro da classe de recurso pequeno, **smallrc**. 

### <a name="resource-class-precedence"></a>Precedência de classe de recursos
Os utilizadores podem ser membros de várias classes de recursos. Quando um utilizador pertence a mais do que uma classe de recursos:

- Classes de recursos dinâmicos têm precedência sobre as classes de recurso estático. Por exemplo, se um utilizador for um membro de mediumrc(dynamic) e staticrc80 (estáticas), executam consultas com mediumrc.
- Classes de recurso maiores têm precedência sobre classes de recursos mais pequenas. Por exemplo, se um utilizador for um membro de mediumrc e largerc, executam consultas com largerc. Da mesma forma, se um utilizador for um membro de staticrc20 e statirc80, executam consultas com staticrc80 alocações de recursos.

### <a name="queries-exempt-from-resource-classes"></a>Consultas excluídos classes de recursos
Algumas consultas são sempre executam na classe de recurso smallrc, apesar do utilizador é membro de uma classe de recurso maior. Estas consultas excluídas não contam para o limite de concorrência. Por exemplo, se o limite de concorrência 16, número de utilizadores que pode selecionar das vistas de sistema sem afetar os blocos de concorrência disponíveis.

As seguintes declarações excluídos de classes de recursos e são sempre executados no smallrc:

* Criar ou remover a tabela
* FALHA DE ALTER TABLE... PARTIÇÃO de intercalação, divisão ou comutador
* ALTERAR A DESATIVAÇÃO DO ÍNDICE
* REMOVER O ÍNDICE
* CRIAR, ATUALIZAR ou DROP STATISTICS
* TRUNCAR A TABELA
* ALTER AUTORIZAÇÃO
* CRIAR O INÍCIO DE SESSÃO
* CRIAR, alterar ou remover utilizador
* CREATE, ALTER ou LARGAR procedimento
* Criar ou remover vista
* INSERIR VALORES
* SELECIONAR vistas de sistema e DMVs
* EXPLICAR
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="recommendations"></a>Recomendações
É recomendável criar um utilizador que se dedica à execução de um tipo específico de consulta ou operações de carga. Em seguida, atribua esse utilizador uma classe de recursos permanente em vez de alterar a classe de recursos com frequência. Uma vez que as classes de recurso estático suportar maior controlo global da carga de trabalho sugerimos também utilizar essas primeiro antes de considerar as classes de recursos dinâmicos.

### <a name="resource-classes-for-load-users"></a>Classes de recursos para os utilizadores de carga
`CREATE TABLE`índices columnstore utiliza em cluster por predefinição. A compressão de dados para um columnstore índice é uma operação de intensivos em termos de memória e pressão de memória pode reduzir a qualidade do índice. Por conseguinte, é mais provável exigir uma classe de recurso superior quando carregar os dados. Para garantir a cargas tem memória suficiente, pode criar um utilizador que está designado para executar cargas e atribua esse utilizador a uma classe de recurso superior.

A memória necessária para processar cargas de forma eficiente depende a natureza a tabela carregada e o tamanho dos dados. Para obter mais informações sobre os requisitos de memória, consulte [maximizando a qualidade de rowgroup](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Depois de ter determinado o requisito de memória, escolha se pretende atribuir o utilizador de carga para uma classe de recurso estático ou dinâmico.

- Quando os requisitos de memória de tabela coincidir com um intervalo específico, utilize uma classe de recurso estático. Executam cargas com memória adequada. Quando dimensionar o armazém de dados, as cargas não precisam de mais memória. Ao utilizar uma classe de recurso estáticos, permanecem constantes as alocações de memória. Este consistência conserva valiosos memória e permite mais consultas para executar em simultâneo. Recomendamos que novas soluções utilizem as classes de recurso estático pela primeira vez, como estes fornecem maior controlo.
- Utilize uma classe de recursos dinâmicos quando os requisitos de memória de tabela variam em grande escala. Carrega poderão necessitar de mais memória do que o DWU atual ou nível de cDWU fornece. Por conseguinte, o armazém de dados de dimensionamento adiciona mais memória para operações de carga, que permite que carrega efetuar mais rapidamente.

### <a name="resource-classes-for-queries"></a>Classes de recursos para consultas

Algumas consultas são intensivas de computação e alguns não foram.  

- Seleccione uma classe de recursos dinâmicos quando consultas complexas, mas não necessita de concorrência elevada.  Por exemplo, gerar relatórios diários ou semanais é uma necessidade ocasional de recursos. Se os relatórios são processar grandes quantidades de dados, o armazém de dados de dimensionamento fornece mais memória a classe de recursos existente do utilizador.
- Escolha uma classe de recurso estático quando variam expectativas de recurso ao longo do dia. Por exemplo, uma classe de recurso estático funciona bem quando o armazém de dados está a ser consultado por muitas pessoas. Quando o dimensionamento do armazém de dados, a quantidade de memória alocada para o utilizador não é alterado. Por conseguinte, as consultas mais podem ser executadas em paralelo no sistema.

Selecionar uma concessão de memória adequada depende em vários fatores, tais como a quantidade de dados consultados, a natureza os esquemas de tabela e vários associação, selecione e grupo predicados. Em geral, alocar mais memória permite consultas para agilizar a conclusão, embora reduza a simultaneidade geral. Se não houver simultaneidade um problema excessiva alocar memória não afetar o débito. 

Para otimizar o desempenho, utilize as classes de recursos diferente. O fornecem secção seguinte, um procedimento armazenado que o ajuda a descobrir a melhor classe de recursos.

## <a name="example-code-for-finding-the-best-resource-class"></a>Código de exemplo para localizar a classe de recursos melhores
 
Pode utilizar o seguinte procedimento armazenado para descobrir a concessão de memória e de concorrência por classe de recursos num determinado SLO e classe de recurso melhor mais próximo para operações CCI que consomem muita memória na tabela CCI não particionadas uma classe de recursos específico:

Eis o objetivo deste procedimento armazenado:  
1. Para ver a simultaneidade e memória conceder por classe de recursos num SLO indicado. Utilizador tem de fornecer um valor nulo para o esquema e tablename conforme mostrado neste exemplo.  
2. Para ver a classe de recursos de melhor mais próxima para o CCI intensivos em termos de memória de operações (a carga, a tabela de cópia, reconstrua o índice, etc.) não particionada CCI tabela uma classe de recursos específico. O procedimento armazenado utiliza o esquema de tabela para obter a concessão de memória necessária.

### <a name="dependencies--restrictions"></a>As dependências e restrições:
- Este procedimento armazenado não foi concebido para calcular o requisito de memória para uma tabela particionada cci.    
- Este procedimento armazenado não tem o requisito de memória em consideração para a parte SELECIONE CTAS/INSERT-SELECIONAR e pressupõe que é um SELECIONE.
- Este procedimento armazenado utiliza uma tabela temporária, que está disponível na sessão onde este procedimento armazenado foi criado.    
- Este procedimento armazenado depende as ofertas de atuais (por exemplo, configuração de hardware, DMS configuração) e, se qualquer um dos que é alterado, em seguida, este procedimento armazenado não funcionar corretamente.  
- Este procedimento armazenado depende de limite de concorrência oferecido existentes e se o que é alterado, em seguida, este procedimento armazenado não funciona corretamente.  
- Este procedimento armazenado depende de ofertas de classe de recursos existentes e se o que é alterado, em seguida, este procedimento armazenado não funciona corretamente.  

>  [!NOTE]  
>  Se não estiver a obter o resultado depois de executar o procedimento armazenado com parâmetros fornecidos, em seguida, pode haver dois casos. <br />1. O parâmetro de armazém de dados contém um valor inválido de SLO <br />2. Em alternativa, não há nenhuma classe de recurso correspondente para a operação de CCI na tabela. <br />Por exemplo, no DW100, a concessão de memória máxima disponível é 400 MB, e se o esquema da tabela é suficientemente ao nível para cruzada o requisito de 400 MB.
      
### <a name="usage-example"></a>Exemplo de utilização:
Sintaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU:Ou forneça um parâmetro nulo para extrair o DWU atual da base de dados do armazém de dados ou forneça quaisquer DWU suportado no formato 'DW100'
2. @SCHEMA_NAME:Forneça um nome de esquema da tabela
3. @TABLE_NAME:Forneça um nome de tabela de interesse

Exemplos de executar este procedimento armazenado:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A seguinte instrução cria Table1 que é utilizada nos exemplos anteriores.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definição de procedimento armazenado

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a gestão de utilizadores de base de dados e segurança, consulte [proteger uma base de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre como maiores classes de recurso podem melhorar a qualidade de índice columnstore em cluster, consulte [otimizações de memória para compressão columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
