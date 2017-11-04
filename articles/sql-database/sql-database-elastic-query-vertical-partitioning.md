---
title: Consulta em bases de dados de nuvem com o esquema diferentes | Microsoft Docs
description: "como configurar a base de dados entre consultas através de partições verticais"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: 84c261f2-9edc-42f4-988c-cf2f251f5eff
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
ms.openlocfilehash: d57f45066387f451463a38d76d3fe6adab77e41f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Consultar em bases de dados de nuvem com diferentes esquemas (pré-visualização)
![Consultar em tabelas de bases de dados diferentes][1]

Verticalmente particionada bases de dados utilizam conjuntos diferentes de tabelas de bases de dados diferentes. Isto significa que o esquema é diferente em bases de dados diferentes. Por exemplo, todas as tabelas de inventário são numa base de dados enquanto todas as tabelas relacionadas com a gestão de contas numa segunda base de dados. 

## <a name="prerequisites"></a>Pré-requisitos
* O utilizador precisa de ter permissão ALTER qualquer origem de dados externa. Esta permissão está incluído com a permissão ALTER DATABASE.
* São necessárias permissões de ALTER qualquer origem de dados externa para fazer referência à origem de dados subjacente.

## <a name="overview"></a>Descrição geral

> [!NOTE]
> Ao contrário com criação de partições horizontais, estas instruções DDL depende da definição de uma camada de dados com um mapa de partições horizontais através da biblioteca de clientes de base de dados elástica.
>

1. [CRIAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR UM ÂMBITO DE BASE DE DADOS CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR ORIGEM DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Criar chave mestra de base de dados no âmbito e as credenciais
A credencial é utilizada pela consulta elástica para estabelecer a ligação para as bases de dados remotas.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Certifique-se de que o `<username>` não inclui quaisquer **"@servername"** sufixo. 
>

## <a name="create-external-data-sources"></a>Criar origens de dados externas
Sintaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> O parâmetro de tipo tem de ser definido **RDBMS**. 
>

### <a name="example"></a>Exemplo
O exemplo seguinte ilustra a utilização de instrução CREATE para origens de dados externas. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Para obter a lista de origens de dados externas atual: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabelas externas
Sintaxe:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Exemplo
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

O exemplo seguinte mostra como obter a lista de tabelas externas da base de dados atual: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Observações
Consulta elástica expande a sintaxe de tabela externa existente para definir as tabelas externas que utilizam as origens de dados externas do tipo RDBMS. Uma definição de tabela externa para a criação de partições vertical abrange os seguintes aspetos: 

* **Esquema**: da tabela externa DDL define um esquema que podem utilizar as suas consultas. O esquema fornecido na sua definição de tabela externa tem de corresponder ao esquema das tabelas na base de dados remota, armazenar os dados reais. 
* **Referência de base de dados remota**: da tabela externa DDL refere-se a uma origem de dados externas. A origem de dados externo Especifica o nome do servidor lógico e o nome de base de dados da base de dados remoto armazenar os dados da tabela real. 

A sintaxe para criar as tabelas externas com uma origem de dados externa, conforme descrito na secção anterior, é o seguinte: 

A cláusula DATA_SOURCE define a origem de dados externa (ou seja, a base de dados remota em caso de criação de partições vertical) que é utilizada para a tabela externa.  

As cláusulas SCHEMA_NAME e OBJECT_NAME fornecem a capacidade de mapear a definição de tabela externa a uma tabela de um esquema diferente na base de dados remoto ou a uma tabela com um nome diferente, respetivamente. Isto é útil se pretender definir uma tabela externa para uma vista de catálogo ou DMV na sua base de dados remota - ou quaisquer outra situação em que o nome de tabela remota já estiver atribuído localmente.  

A seguinte instrução DDL ignora uma definição de tabela externa existente a partir do catálogo local. Não afeta a base de dados remota. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Permissões de tabela externa CREATE/DROP**: são necessárias permissões de ALTER qualquer origem de dados externa para a tabela externa DDL que também é necessário para fazer referência à origem de dados subjacente.  

## <a name="security-considerations"></a>Considerações de segurança
Os utilizadores com acesso à tabela externa automaticamente obterem acesso às tabelas remotas subjacentes sob a credencial fornecida na definição de origem de dados externas. Cuidadosamente deve gerir o acesso à tabela externa para evitar indesejada elevação de privilégios através a credencial da origem de dados externas. Permissões de SQL regulares podem ser utilizadas para conceder ou REVOGAR o acesso a uma tabela externa como se fosse uma tabela normal.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exemplo: consultar verticalmente particionada bases de dados
A seguinte consulta executa uma associação de três vias entre duas tabelas locais para as ordens e linhas de ordem e a tabela remota para clientes. Este é um exemplo do caso de utilização de dados de referência para a consulta elástico: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


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
Pode utilizar regulares cadeias de ligação do SQL Server para ligar as ferramentas de integração do BI e os dados para as bases de dados no servidor de base de dados SQL que tenha consulta elástica ativada e tabelas externas definidas. Certifique-se de que o SQL Server é suportada como uma origem de dados para a ferramenta. Em seguida, consulte a base de dados elásticas consulta e as respetivas tabelas externas, tal como qualquer outro SQL Server da base de dados que seria se ligam a com a ferramenta. 

## <a name="best-practices"></a>Melhores práticas
* Certifique-se de que a base de dados do ponto final de consulta elástico foi indicado acesso à base de dados remoto ao permitir o acesso para serviços do Azure na sua configuração de firewall da BD do SQL. Certifique-se também de que a credencial fornecida na definição de origem de dados externas pode iniciar com êxito na base de dados remoto e tem as permissões para aceder à tabela remota.  
* Consulta elástica funciona melhor para consultas onde a maioria da computação pode ser feita nas bases de dados remotas. Normalmente, obter o melhor desempenho de consulta com os predicados de filtro seletiva que pode ser avaliado nas bases de dados remotas ou associações que podem ser efetuadas completamente na base de dados remota. Outros padrões de consulta poderão ter de carregar grandes quantidades de dados a partir da base de dados remota e podem efetuar mal. 

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição geral da consulta elástica, consulte [descrição geral de consulta elástico](sql-database-elastic-query-overview.md).
* Para um tutorial de criação de partições vertical, consulte [introdução à consulta de base de dados em vários locais (criação de partições vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para um tutorial horizontal criação de partições (fragmentação), consulte [como começar a consultar elástico (fragmentação) de partições horizontais](sql-database-elastic-query-getting-started.md).
* Para consultas de sintaxe e exemplos de dados particionadas horizontalmente, consulte [consultar horizontalmente particionada dados)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que servem como shards num esquema de partições horizontal.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
