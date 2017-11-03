---
title: "Relatórios em bases de dados de nuvem de escalamento horizontal | Microsoft Docs"
description: "como configurar elásticas consultas através de partições horizontais"
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: f86eccb8-6323-4ba7-8559-8a7c039049f3
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: mlandzic
ms.openlocfilehash: 41accea2e94fc763d0dcbba709829ec07453da78
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Relatórios em bases de dados de nuvem de escalamento horizontal (pré-visualização)
![Consultar em partições horizontais][1]

Bases de dados em partição horizontal distribuam linhas por um dados expandido terminar camada. O esquema é idêntico em todos os participantes bases de dados, também conhecidos como criação de partições horizontais. Utilizando uma consulta elástica, pode criar relatórios que abrangem todas as bases de dados numa base de dados em partição horizontal.

Para uma introdução, consulte [Reporting entre bases de dados de nuvem de escalamento horizontal](sql-database-elastic-query-getting-started.md).

Para bases de dados não-em partição horizontal, consulte [consulta em bases de dados de nuvem com diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Pré-requisitos
* Crie um mapa de partições horizontais utilizando a biblioteca de cliente da base de dados elásticas. consulte [gestão de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md). Ou utilize a aplicação de exemplo no [começar a utilizar as ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md).
* Em alternativa, consulte [Migrar bases de dados existentes para bases de dados de escalamento horizontal](sql-database-elastic-convert-to-use-elastic-tools.md).
* O utilizador precisa de ter permissão ALTER qualquer origem de dados externa. Esta permissão está incluído com a permissão ALTER DATABASE.
* São necessárias permissões de ALTER qualquer origem de dados externa para fazer referência à origem de dados subjacente.

## <a name="overview"></a>Descrição geral
As declarações de criar a representação de metadados da sua camada de dados fragmentados na base de dados elásticas consulta. 

1. [CRIAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR UM ÂMBITO DE BASE DE DADOS CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR ORIGEM DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Criar chave mestra de base de dados no âmbito e as credenciais
A credencial é utilizada pela consulta elástica para estabelecer a ligação para as bases de dados remotas.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Certifique-se de que o *"\<username\>"* não inclui quaisquer *"@servername"* sufixo. 
> 
> 

## <a name="12-create-external-data-sources"></a>1.2 Criar origens de dados externas
Sintaxe:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                              
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Exemplo
    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );

Obter a lista de origens de dados externas atual: 

    select * from sys.external_data_sources; 

A origem de dados externa referencia o mapa de partições horizontais. Uma consulta elástica, em seguida, utiliza a origem de dados externo e o mapa de partições horizontais subjacente para enumerar as bases de dados que participam na camada de dados. As mesmas credenciais são utilizadas para ler o mapa de partições horizontais e aceder aos dados nos shards durante o processamento de uma consulta elástico. 

## <a name="13-create-external-tables"></a>1.3 criar tabelas externas
Sintaxe:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Exemplo**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 

    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
         SCHEMA_NAME = 'orders', 
         OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Obter a lista de tabelas externas da base de dados atual: 

    SELECT * from sys.external_tables; 

Para remover as tabelas externas:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Observações
Os dados\_cláusula origem define a origem de dados externa (um mapa de partições horizontais) que é utilizada para a tabela externa.  

O esquema\_nome e o OBJETO\_cláusulas nome mapeiam a definição de tabela externa para uma tabela de um esquema diferente. Se for omitido, o esquema do objeto remoto é prestado "dbo" e o respetivo nome é assumido ser idêntico ao nome de tabela externa que está a ser definido. Isto é útil se o nome da sua tabela remota já estiver atribuído na base de dados onde pretende criar a tabela externa. Por exemplo, pretende definir uma tabela externa para obter uma vista de agregação de vistas de catálogo ou camada DMVs nos seus dados expandidos terminar. Uma vez que as vistas de catálogo e DMVs já existem localmente, não é possível utilizar os respetivos nomes para a definição de tabela externa. Em vez disso, utilize um nome diferente e utilizar a vista de catálogo ou o DMV nome no esquema\_nome e/ou OBJETO\_cláusulas de nome. (Consulte o exemplo abaixo). 

A cláusula de distribuição Especifica a distribuição de dados utilizada para esta tabela. O processador de consultas utiliza as informações fornecidas na cláusula de distribuição para criar os planos de consulta mais eficientes.  

1. **A** significa dados horizontalmente estão particionados em bases de dados. A chave de criação de partições para a distribuição de dados é o **< sharding_column_name >** parâmetro.
2. **REPLICADO** significa que existem idênticas cópias da tabela em cada base de dados. É da responsabilidade do cliente, certifique-se de que as réplicas são idênticas em bases de dados.
3. **ARREDONDAR\_Round ROBIN** significa que a tabela horizontalmente está particionada utilizando um método de distribuição de aplicações dependentes. 

**Referência de camada de dados**: da tabela externa DDL refere-se a uma origem de dados externas. A origem de dados externo Especifica um mapa de partições horizontais que fornece a tabela externa com as informações necessárias para localizar todas as bases de dados na camada de dados. 

### <a name="security-considerations"></a>Considerações de segurança
Os utilizadores com acesso à tabela externa automaticamente obterem acesso às tabelas remotas subjacentes sob a credencial fornecida na definição de origem de dados externas. Evite indesejada elevação de privilégios através a credencial da origem de dados externas. Utilize GRANT ou REVOKE para uma tabela externa como se fosse uma tabela normal.  

Assim que definiu a sua origem de dados externos e as tabelas externas, agora pode utilizar o T-SQL completo sobre as tabelas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemplo: consultar as bases de dados de particionadas horizontais
A seguinte consulta efetua uma junção de três vias entre armazéns, encomendas pendentes e linhas de ordem e utiliza vários agregados e um filtro seletivo. Parte do pressuposto de criação de partições (1) horizontal (fragmentação) e (2) que armazéns, encomendas pendentes e linhas de ordem estão em partição horizontal pela coluna de id de armazém e que a consulta elástica pode localizar conjuntamente as associações nos shards e processar a parte da consulta no shards em paralelo dispendiosas. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimento para execução remota de T-SQL armazenado: sp\_execute_remote
Consulta elástica também apresenta um procedimento armazenado que fornece acesso direto para os shards. O procedimento armazenado é chamado [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) e podem ser utilizados para executar os procedimentos armazenados remotos ou um código de T-SQL nas bases de dados remotas. Demora os seguintes parâmetros: 

* Nome da origem de dados (nvarchar): O nome da origem de dados externas do tipo RDBMS. 
* Consulta (nvarchar): consulta o T-SQL para ser executada em cada partição horizontal. 
* Declaração de parâmetro (nvarchar) - opcional: de cadeia com definições de tipo de dados para os parâmetros utilizados no parâmetro de consulta (como sp_executesql). 
* Lista de valores de parâmetro - opcional: lista de valores separados por vírgulas de valores de parâmetros (por exemplo, sp_executesql).

O sp\_executar\_remoto utiliza a origem de dados externa fornecida nos parâmetros de invocação para executar a instrução de T-SQL indicada nas bases de dados remotas. Utiliza a credencial da origem de dados externos a ligar a base de dados do Gestor de shardmap e as bases de dados remotas.  

Exemplo: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas
Utilizar regulares cadeias de ligação do SQL Server para ligar a sua aplicação, as ferramentas de integração do BI e os dados na base de dados com as definições de tabela externa. Certifique-se de que o SQL Server é suportada como uma origem de dados para a ferramenta. Em seguida, referência a base de dados elásticas consulta como qualquer outra base de dados do SQL Server ligado para a ferramenta e tabelas externas de utilização da sua aplicação ou a ferramenta como se estivessem tabelas locais. 

## <a name="best-practices"></a>Melhores práticas
* Certifique-se de que a base de dados do ponto final de consulta elástico foi indicado acesso para a base de dados shardmap e todas as partições horizontais através de firewalls de BD do SQL.  
* Validar ou impor a distribuição de dados definida da tabela externa. Se a distribuição de dados real é diferente de distribuição especificada na definição de tabela, as suas consultas poderão produzir resultados inesperados. 
* Consulta elástica atualmente não efetuar a eliminação de partições horizontais quando predicados sobre a chave de fragmentação permitiria-lo em segurança excluir determinadas shards de processamento.
* Consulta elástica funciona melhor para consultas onde é possível efetuar a maioria a computação os shards. Obter, normalmente, o melhor desempenho de consulta com os predicados de filtro seletiva que pode ser avaliado no shards ou associações através de chaves de criação de partições que podem ser executadas de forma a partição alinhada em todas as partições horizontais. Outros padrões de consulta poderão ter de carregar grandes quantidades de dados das shards ao nó principal e podem efetuar mal

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição geral da consulta elástica, consulte [descrição geral de consulta elástico](sql-database-elastic-query-overview.md).
* Para um tutorial de criação de partições vertical, consulte [introdução à consulta de base de dados em vários locais (criação de partições vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e exemplos de dados verticalmente particionadas, consulte [consultar verticalmente particionada dados)](sql-database-elastic-query-vertical-partitioning.md)
* Para um tutorial horizontal criação de partições (fragmentação), consulte [como começar a consultar elástico (fragmentação) de partições horizontais](sql-database-elastic-query-getting-started.md).
* Consulte [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que servem como shards num esquema de partições horizontal.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
