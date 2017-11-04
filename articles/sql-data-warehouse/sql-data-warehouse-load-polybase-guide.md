---
title: Guia para utilizar o PolyBase no SQL Data Warehouse | Microsoft Docs
description: "Diretrizes e recomendações para utilizar o PolyBase em cenários de armazém de dados do SQL Server."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 9/13/2017
ms.custom: loading
ms.author: cakarst;barbkess
ms.openlocfilehash: e8ae0eb96200c167a8758df4ce20b51452cc59a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guia para utilizar o PolyBase no SQL Data Warehouse
Este guia fornece informações práticas para utilizar o PolyBase no SQL Data Warehouse.

Para começar a utilizar, consulte o [carregar dados com o PolyBase] [ Load data with PolyBase] tutorial.

## <a name="rotating-storage-keys"></a>Rodar chaves de armazenamento
Ocasionalmente, deverá alterar a chave de acesso para o armazenamento de blob por motivos de segurança.

A forma mais elegante para efetuar esta tarefa é seguido um processo conhecido como "rodar as chaves". Poderá ter reparado que tem duas chaves de armazenamento para a sua conta de armazenamento de Blobs. Isto é, de modo a que pode efetuar a transição

Rodar as chaves de conta de armazenamento do Azure é um processo de três passo simples

1. Criar segunda credenciais de um âmbito de base de dados com base na chave de acesso secundária de armazenamento
2. Criar segunda origem de dados externa com base em desativar esta credencial nova
3. Remova e criar as tabelas externas apontar para a nova origem de dados externas

Quando tiver de ter migrado todos os seus externo tabelas para a nova origem de dados externos, em seguida, pode realizar as tarefas de limpeza:

1. Remova primeiro origem de dados externa
2. Largar primeira base de dados no âmbito credencial com base na chave de acesso de armazenamento primário
3. Inicie sessão no Azure e voltar a gerar a chave de acesso primária pronta para a próxima vez



## <a name="load-data-with-external-tables"></a>Carregar dados com as tabelas externas
Neste exemplo carrega dados do armazenamento de BLOBs para base de dados do armazém de dados do SQL Server.

Armazenar dados diretamente remove o tempo de transferência de dados para consultas. Armazenar dados com um índice columnstore melhora o desempenho de consulta para consultas de análise por até 10x.

Este exemplo utiliza a instrução CREATE TABLE AS SELECT para carregar dados. A nova tabela herda as colunas nomeadas na consulta. Os tipos de dados dessas colunas que herda da definição de tabela externa.

CREATE TABLE AS SELECT é um altamente performant instrução de Transact-SQL que carrega os dados em paralelo para todos os nós de computação do seu SQL Data Warehouse.  Foi desenvolvido originalmente para o motor de processamento paralelo em grande escala (MPP) no Analytics Platform System e agora no armazém de dados do SQL Server.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Consulte [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

> [!NOTE]
> Uma carga utilizando uma tabela externa pode falhar com o erro *"consulta abortada – foi atingido o limiar máximo rejeitar ao ler a partir de uma origem externa"*. Isto indica que contém os dados externos *desatualização* registos. Um registo de dados é considerado 'desatualização' se o número/tipos de dados real de colunas não correspondem as definições da coluna da tabela externa ou se os dados não está em conformidade com o formato de ficheiro externo especificado. Para corrigir este problema, certifique-se de que a tabela externa e definições do formato de ficheiro externo estão corretas e os dados externos está em conformidade com estas definições. No caso de um subconjunto de registos de dados externas são com falhas, pode optar por rejeitar estes registos para as suas consultas utilizando as opções de rejeitar no criar DDL de tabela externa.
> 
> 


## <a name="create-statistics-on-newly-loaded-data"></a>Criar estatísticas nos dados recentemente carregados
O Azure SQL Data Warehouse ainda não suporta a criação ou atualização automática de estatísticas.  Para obter o melhor desempenho das consultas, é importante que sejam criadas estatísticas em todas as colunas de todas as tabelas após o primeiro carregamento ou após a ocorrência de quaisquer alterações substanciais nos dados.  Para obter uma explicação detalhada das estatísticas, veja o tópico [Statistics (Estatísticas)][Statistics] no grupo de tópicos Programar.  Segue-se um breve exemplo de como criar estatísticas a tabela carregada neste exemplo.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-with-external-tables"></a>Exportar dados com as tabelas externas
Esta secção mostra como exportar dados do armazém de dados do SQL Server para o armazenamento de Blobs do Azure utilizando as tabelas externas. Este exemplo utiliza CREATE externo TABLE AS SELECT que é um altamente performant instrução de Transact-SQL para exportar os dados em paralelo de todos os nós de computação.

O exemplo seguinte cria uma tabela externa Weblogs2014 utilizando as definições da coluna e dos dados de dbo. Blogues de tabela. A definição de tabela externa é armazenada no armazém de dados do SQL Server e os resultados da instrução SELECT são exportados para o diretório "/ Arquivar/log2014 /" sob o contentor de blob especificado pela origem de dados. Os dados são exportados no formato de ficheiro de texto especificado.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>Isolar ao carregar os utilizadores
Muitas vezes, é necessário ter vários utilizadores podem carregar dados para um armazém de dados do SQL Server. Porque o [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] requer permissões de controlo da base de dados, irá surgir com vários utilizadores com acesso de controlo ao longo de todos os esquemas. Para limitar este, pode utilizar a instrução de controlo de NEGAR.

Exemplo: Considere schema_A de esquemas de base de dados para Depart A e schema_B para user_A de utilizadores de base de dados B permitem Depart e user_B ser utilizadores para PolyBase carregar no Depart A e B, respetivamente. Ambos os tem sido concedidos permissões de base de dados de controlo.
Os criadores de A e B agora o bloqueio de esquema pendente os respetivos esquemas com NEGAÇÃO:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 Com esta opção, user_A e user_B devem agora ser bloqueadas do esquema de outros Depart.
 
## <a name="polybase-performance-optimizations"></a>Otimizações de desempenho do PolyBase
Para alcançar ideal de carregamento de desempenho com o PolyBase sugerimos o seguinte:
- Divida grandes ficheiros comprimidos em mais pequeno ficheiros comprimidos. Os tipos de compressão suportados atualmente não são divisíveis. Como resultado, o desempenho será afetado por carregar um único ficheiro de grandes dimensões.
- Para a velocidade de carregamento mais rápida, carregue para um round_robin, área dinâmica para dados tabela de teste. Esta será a forma mais eficiente para mover os dados de camada de armazenamento para o armazém de dados.
- Todos os formatos de ficheiro têm características de desempenho diferentes. Para a carga mais rápida, utilize comprimido delimitada por ficheiros de texto. A diferença entre UTF-8 e UTF-16 desempenho é mínima.
- Colocalizar a camada de armazenamento e o armazém de dados para minimizar a latência
- Dimensione o seu armazém de dados se espera que uma tarefa de carregamento de grandes dimensões.

## <a name="polybase-limitations"></a>Limitações do PolyBase
O PolyBase no armazém de dados do SQL Server tem as seguintes limitações que precisam de ser levada em consideração quando conceber uma tarefa de carregamento:
- Uma única linha não pode ser maior número de bytes 1.000.000. Isto acontece independentemente do esquema de tabela definido.
- Quando exportar dados para um formato de ficheiro ORC das colunas pesadas de texto do SQL Server ou o Azure SQL Data Warehouse podem ser limitados para apenas 50 colunas devido à java erros de memória. Para contornar este problema, exporte apenas um subconjunto de colunas.





## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como mover dados para o SQL Data Warehouse, consulte o [descrição geral da migração de dados][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
