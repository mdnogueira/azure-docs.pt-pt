---
title: "Aplicações de multi-inquilinos com ferramentas de base de dados elástica e a segurança ao nível da linha"
description: "Utilize as ferramentas de base de dados elásticas com segurança ao nível da linha para criar uma aplicação com uma camada de dados altamente dimensionável."
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: 
manager: jhubbard
author: tmullaney
ms.assetid: e72d3cfe-e9be-4326-b776-9c6d96c0a18e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: thmullan;torsteng
ms.openlocfilehash: 4b4ec29bb53bdce413dadf7cb0751433b9ca686c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicações de multi-inquilinos com ferramentas de base de dados elástica e a segurança ao nível da linha
[Ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md) e [ao nível da linha (RLS) de segurança](https://msdn.microsoft.com/library/dn765131) disponibilizam um conjunto potente de capacidades para a camada de dados de uma aplicação multi-inquilino com a SQL Database do Azure de dimensionamento de forma flexível e eficiente. Para obter mais informações, consulte [padrões de estrutura de aplicações de SaaS multi-inquilino com a SQL Database do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) para obter mais informações. 

Este artigo ilustra como utilizar estas tecnologias em conjunto para criar uma aplicação com uma camada de dados altamente dimensionável, que suporta shards de multi-inquilinos, utilizando **ADO.NET SqlClient** e/ou **do Entity Framework**.  

* **Ferramentas de base de dados elástica** permitir que os programadores ampliar a camada de dados de uma aplicação através de práticas de fragmentação de norma da indústria, utilizando um conjunto de bibliotecas .NET e modelos de serviço do Azure. Gerir shards com utilizando a biblioteca de clientes de base de dados elásticas ajuda a automatizar e simplificar muitos das infraestrutura tarefas normalmente associadas a fragmentação. 
* **Ao nível da linha segurança** permite aos programadores armazenar dados de múltiplos inquilinos na mesma base de dados através de políticas de segurança para filtrar as linhas que não pertence ao inquilino ao executar uma consulta. Centralizar a lógica de acesso com RLS no interior da base de dados, em vez de na aplicação, simplifica a manutenção e reduz o risco de erro, como uma aplicação do código base cresce. 

Utilizar estas funcionalidades em conjunto, uma aplicação pode beneficiar ganhos de reduções e a eficiência de custos armazenando dados de múltiplos inquilinos na mesma base de dados de partições horizontais. Ao mesmo tempo, uma aplicação ainda tem a flexibilidade para oferecer shards isolados, único inquilino de inquilinos de "premium", que requerem mais rigorosas garantias de desempenho, uma vez que o multi-inquilinos shards garante a distribuição de recurso igual entre inquilinos.  

Em suma, elástico da base de dados da biblioteca de clientes [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md) APIs ligarem automaticamente os inquilinos para a base de dados de partições horizontais correto, que contém as respetivas chaves de fragmentação (geralmente, um "TenantId"). Assim que estiver ligado, uma política de segurança RLS na base de dados garante que os inquilinos só podem aceder a linhas que contêm os respetivos TenantId. Presume-se que todas as tabelas contém uma coluna do TenantId para indicar que linhas pertencem a cada inquilino. 

![Arquitetura de aplicação Blogging][1]

## <a name="download-the-sample-project"></a>Transferir o projeto de exemplo
### <a name="prerequisites"></a>Pré-requisitos
* Utilizar o Visual Studio (2012 ou superior) 
* Criar três bases de dados de SQL do Azure 
* Transferir o projeto de exemplo: [elástico ferramentas DB para SQL do Azure - Shards multi-inquilino](http://go.microsoft.com/?linkid=9888163)
  * Preencha as informações para as bases de dados no início do **Program.cs** 

Este projeto expande um descrito em [elástico ferramentas DB para SQL do Azure - Entity Framework integração](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) , adicionando suporte para bases de dados do multi-inquilino de partição horizontal. Baseia-se uma aplicação de consola simples para a criação de blogues e mensagens, com quatro inquilinos e duas bases de dados do multi-inquilino de partição horizontal conforme ilustrado no diagrama anterior. 

Crie e execute a aplicação. Isto bootstraps Gestor de mapa de partições horizontais das ferramentas de base de dados elástica e execute os seguintes testes: 

1. Utilizar o Entity Framework e LINQ, criar um novo blogue e, em seguida, apresentar todos os blogues para cada inquilino
2. Utilizar ADO.NET SqlClient, apresentar todos os blogues para um inquilino
3. Tentar inserir um blogue para o inquilino errado verificar que emitido um erro  

Tenha em atenção que, porque a RLS ainda não foi ativada nas bases de dados de partição horizontal, cada um destes testes de revela um problema: inquilinos são capazes de ver blogues que não pertencem aos mesmos e a aplicação não é impediu a inserir um blogue para o inquilino errado. O resto deste artigo descreve como resolver estes problemas através da imposição de isolamento de inquilinos com RLS. Existem dois passos: 

1. **A camada da aplicação**: modificar o código de aplicação sempre definir o TenantId atual no SESSION_CONTEXT depois de abrir uma ligação. O projeto de exemplo tem o tiver feito. 
2. **Camada de dados**: criar uma política de segurança RLS em cada base de dados de partições horizontais às linhas de filtro com base no TenantId armazenado no SESSION_CONTEXT. Terá de efetuar este procedimento para cada um dos seus partições horizontais bases de dados, caso contrário, linhas no multi-inquilino shards não são filtrados. 

## <a name="step-1-application-tier-set-tenantid-in-the-sessioncontext"></a>Camada de aplicação do passo 1): definir o TenantId no SESSION_CONTEXT
Depois de ligar a uma base de dados de partição horizontal com APIs de encaminhamento de dados dependentes a biblioteca de clientes de base de dados elástica, a aplicação ainda deverá dizer a base de dados que TenantId está a utilizar essa ligação para que uma política de segurança RLS pode filtrar as linhas que pertençam a outros inquilinos. O modo recomendado para passar esta informação é para armazenar o TenantId atual para essa ligação no [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). (Nota: pode utilizar em alternativa [CONTEXT_INFO](https://msdn.microsoft.com/library/ms180125.aspx), mas SESSION_CONTEXT é uma opção melhor porque é mais fácil de utilizar, devolve NULL por predefinição e suporta pares chave-valor.)

### <a name="entity-framework"></a>Entity Framework
Para aplicações utilizando o Entity Framework, a abordagem mais fácil consiste em definir SESSION_CONTEXT dentro a substituição de ElasticScaleContext descrita [dependentes dados encaminhamento com EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Antes de o devolver a ligação mediada através do encaminhamento de dados dependentes, criar e executar um SqlCommand que define 'TenantId' no SESSION_CONTEXT para shardingKey especificada para essa ligação. Desta forma, apenas terá de escrever o código de uma vez para definir o SESSION_CONTEXT. 

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data-dependent routing. This call opens a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call fails for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Agora a SESSION_CONTEXT é automaticamente definido com o TenantId especificado sempre que é invocada ElasticScaleContext: 

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
    {     
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
        foreach (var item in query)     
        {       
            Console.WriteLine(item.Name);     
        }   
    } 
}); 
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient
Para aplicações utilizando ADO.NET SqlClient, a abordagem recomendada é criar uma função de wrapper em torno ShardMap.OpenConnectionForKey() define automaticamente 'TenantId' no SESSION_CONTEXT para o TenantId correto antes de regressar de uma ligação. Para garantir que SESSION_CONTEXT está sempre definido, só deve abrir ligações a utilização desta função de wrapper.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs are listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="step-2-data-tier-create-row-level-security-policy"></a>Camada de dados do passo 2): Criar política de segurança ao nível da linha
### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Criar uma política de segurança para filtrar as linhas que pode aceder a cada inquilino
Agora que a aplicação é a definição SESSION_CONTEXT com o TenantId atual antes de consulta, uma política de segurança RLS pode filtrar consultas e excluir as linhas que têm um TenantId diferentes.  

RLS está implementado em T-SQL: uma função definida pelo utilizador define a lógica de acesso e uma política de segurança está vinculado a esta função para qualquer número de tabelas. Para este projeto, a função verifica que a aplicação (em vez de outro utilizador de SQL Server) está ligado à base de dados e que o TenantId armazenados no SESSION_CONTEXT corresponde o TenantId de uma linha fornecida. Um predicado de filtro permite que as linhas que satisfaçam estas condições para passar o filtro para consultas SELECT, UPDATE e DELETE; e um predicado de bloco impede que as linhas que violam estas condições sejam INSERTed ou atualizada. Se não tiver sido definida SESSION_CONTEXT, devolve que nulo e não existem linhas são visíveis ou pode ser inserido. 

Para ativar RLS, execute o seguinte T-SQL em todas as partições horizontais utilizando o Visual Studio (SSDT), SSMS ou o script do PowerShell incluído no projeto (ou se estiver a utilizar [as tarefas de base de dados elásticas](sql-database-elastic-jobs-overview.md), pode utilizá-lo a automatizar a execução deste T-SQL em todas as partições horizontais): 

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult          
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [!TIP]
> Para projetos mais complexos que tem de adicionar o predicado no centenas de tabelas, pode utilizar um procedimento armazenado do programa auxiliar que gera automaticamente uma política de segurança a adição de um predicado em todas as tabelas de um esquema. Para obter mais informações, consulte [aplicam-se a segurança ao nível da linha para todas as tabelas - o script de programa auxiliar (blogue)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).  
> 
> 

Se executar novamente a aplicação de exemplo, os inquilinos ver apenas as linhas que pertencem aos mesmos. Além disso, a aplicação não é possível inserir linhas que pertençam a inquilinos que o atualmente ligado à base de dados de partição horizontal e não é possível atualizar linhas visíveis a ter um TenantId diferentes. Se a aplicação tenta efetuar um, é gerado um DbUpdateException.

Se adicionar uma nova tabela mais tarde, alterar a política de segurança e adicionar um filtro e bloquear predicados na tabela nova: 

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adicionar restrições predefinidas para preencher automaticamente o TenantId para inserções
Pode colocar uma restrição default em cada tabela para preencher automaticamente o TenantId com o valor atualmente armazenado em SESSION_CONTEXT ao inserir linhas. Por exemplo: 

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

Agora a aplicação não tem de especificar um TenantId ao inserir linhas: 

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Se utilizar restrições predefinidas para um projeto do Entity Framework, é recomendado que não incluir a coluna de TenantId no seu modelo de dados do EF. Isto acontece porque as consultas do Entity Framework fornecem automaticamente os valores predefinidos que substituem as restrições de predefinido criadas no T-SQL que utilizam SESSION_CONTEXT. Para utilizar restrições predefinidas no projeto de exemplo, por exemplo, que deve remover o TenantId DataClasses.cs (e execute Add-migração na consola do Gestor de pacotes) e utiliza T-SQL para Certifique-se de que o campo só existe nas tabelas de base de dados. Desta forma, EF fornecer automaticamente os valores predefinidos incorreto ao inserir dados. 
> 
> 

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcional) Ativar um "Superutilizador" aceder a todas as linhas
Algumas aplicações, poderão pretender criar um "Superutilizador" quem pode aceder a todas as linhas, por exemplo, para ativar os relatórios em todos os inquilinos em todas as partições horizontais ou para efetuar operações de divisão/intercalação em shards que envolvam a mover inquilino linhas entre as bases de dados. Para ativar esta opção, deve criar um novo utilizador do SQL Server ("Superutilizador" neste exemplo) em cada base de dados de partições horizontais. Em seguida, altere a política de segurança com uma nova função de predicado que permite que este utilizador aceder a todas as linhas:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### <a name="maintenance"></a>Manutenção
* **Adicionar novo shards**: executar o script T-SQL para ativar a RLS em qualquer shards nova, caso contrário, que não são filtradas consultas nestes shards.
* **Adicionar novas tabelas**: adicionar um predicado de filtro e bloqueio para a política de segurança em todas as partições horizontais sempre que é criada uma nova tabela, caso contrário, consultas na nova tabela não são filtrados. Isto pode ser automatizado através de um acionador DDL, conforme descrito em [aplicam-se a segurança ao nível da linha automaticamente a tabelas recém-criado (blogue)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Resumo
Ferramentas de base de dados elástica e a segurança ao nível da linha podem ser utilizados em conjunto para aumentar horizontalmente a camada de dados de uma aplicação com suporte para ambos os multi-inquilino e de inquilino único shards. Shards multi-inquilinos podem ser utilizados para armazenar dados de forma mais eficiente (especialmente nos casos em que um grande número de inquilinos têm apenas de alguns linhas de dados), ao inquilino único shards podem ser utilizados para suportar inquilinos premium com requisitos de desempenho e o isolamento mais restrito.  Para obter mais informações, consulte [referência de segurança ao nível da linha](https://msdn.microsoft.com/library/dn765131). 

## <a name="additional-resources"></a>Recursos adicionais
* [O que é um conjunto elástico do Azure?](sql-database-elastic-pool.md)
* [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
* [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Autenticação em aplicações multi-inquilino, com o Azure AD e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Aplicação Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de funcionalidades
Para perguntas sobre, entrar-no [fórum de base de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para pedidos de funcionalidades, adicioná-los para o [fórum de comentários da base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->


