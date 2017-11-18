---
title: "Aplicações de multi-inquilinos com RLS e ferramentas de base de dados elástica | Microsoft Docs"
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
ms.date: 11/16/2017
ms.author: thmullan;torsteng
ms.openlocfilehash: f874800e8647eac6ed94945d79e904df86207b0f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicações de multi-inquilinos com ferramentas de base de dados elástica e a segurança ao nível da linha

[Ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md) e [ao nível da linha (RLS) de segurança] [ rls] cooperam para ativar o dimensionamento a camada de dados de uma aplicação multi-inquilino com a SQL Database do Azure. Em conjunto, estas tecnologias ajudam a criar uma aplicação que tenha uma camada de dados altamente dimensionável. A camada de dados suporta o multi-inquilinos shards e utiliza **ADO.NET SqlClient** ou **do Entity Framework**. Para obter mais informações, consulte [padrões de estrutura de aplicações de SaaS multi-inquilino com a SQL Database do Azure](saas-tenancy-app-design-patterns.md).

- **Ferramentas de base de dados elástica** permitir que os programadores ampliar a camada de dados com as práticas de fragmentação padrão, utilizando bibliotecas .NET e modelos de serviço do Azure. Gerir shards utilizando o [biblioteca de clientes de base de dados elásticas] [ s-d-elastic-database-client-library] ajuda a automatizar e simplificar muitos das infraestrutura tarefas normalmente associadas a fragmentação.
- **Ao nível da linha segurança** permite aos programadores armazenar em segurança dados de múltiplos inquilinos na mesma base de dados. As políticas de segurança RLS filtragem as linhas que não pertence ao inquilino ao executar uma consulta. Centralizar a lógica de filtro no interior da base de dados simplifica a manutenção e reduz o risco de um erro de segurança. A alternativa de entidade confiadora em todo o código de cliente para segurança enfore é risco.

Utilizando estas funcionalidades em conjunto, uma aplicação pode armazenar dados de múltiplos inquilinos na mesma base de dados de partições horizontais. Este custo inferior por inquilino quando os inquilinos partilham uma base de dados. Ainda a mesma aplicação pode também oferecem inquilinos respetivo premium a opção de pagar os seus próprios dedicado inquilino único ID de partição horizontal. Uma vantagem de isolamento de inquilino único é firmer garantias de desempenho. Numa base de dados único inquilino, não há nenhum outro inquilino competir pela recursos.

O objetivo é utilizar a biblioteca de cliente da base de dados elástica [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md) APIs para se ligarem automaticamente a cada inquilino fornecido para a base de dados de partições horizontais correto. Apenas um ID de partição horizontal contém um valor de TenantId específico para o inquilino especificado. O TenantId é o *chave de fragmentação*. Depois da ligação for estabelecida, uma política de segurança RLS na base de dados garante que o inquilino especificado pode aceder apenas as linhas de dados que contém o TenantId.

> [!NOTE]
> O identificador de inquilino pode consistir em mais de uma coluna. Para sua comodidade é este debate, partimos do informally TenantId uma única coluna.

![Arquitetura de aplicação Blogging][1]

## <a name="download-the-sample-project"></a>Transferir o projeto de exemplo

### <a name="prerequisites"></a>Pré-requisitos

- Utilizar o Visual Studio (2012 ou superior) 
- Criar três bases de dados de SQL do Azure 
- Transferir o projeto de exemplo: [elástico ferramentas DB para SQL do Azure - Shards multi-inquilino](http://go.microsoft.com/?linkid=9888163)
  - Preencha as informações para as bases de dados no início do **Program.cs** 

Este projeto expande um descrito em [elástico ferramentas DB para SQL do Azure - Entity Framework integração](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) , adicionando suporte para bases de dados do multi-inquilino de partição horizontal. O projeto baseia-se uma aplicação de consola simples para a criação de blogues e publicações. O projeto inclui quatro inquilinos, mais duas bases de dados do multi-inquilino de partição horizontal. Esta configuração é ilustrada no diagrama anterior. 

Crie e execute a aplicação. Esta execução bootstraps Gestor de mapa de partições horizontais das ferramentas de base de dados elástica e efetua os seguintes testes: 

1. Utilizar o Entity Framework e LINQ, criar um novo blogue e, em seguida, apresentar todos os blogues para cada inquilino
2. Utilizar ADO.NET SqlClient, apresentar todos os blogues para um inquilino
3. Tentar inserir um blogue para o inquilino errado verificar que emitido um erro  

Tenha em atenção que, porque a RLS ainda não foi ativada nas bases de dados de partição horizontal, cada um destes testes de revela um problema: inquilinos são capazes de ver blogues que não pertencem aos mesmos e a aplicação não é impediu a inserir um blogue para o inquilino errado. O resto deste artigo descreve como resolver estes problemas através da imposição de isolamento de inquilinos com RLS. Existem dois passos: 

1. **A camada da aplicação**: modificar o código de aplicação sempre definir o TenantId atual na sessão\_contexto depois de abrir uma ligação. O projeto de exemplo já define o TenantId desta forma. 
2. **Camada de dados**: criar uma política de segurança RLS em cada base de dados de partições horizontais às linhas de filtro com base no TenantId armazenado numa sessão\_contexto. Criar uma política para cada uma das suas bases de dados de partição horizontal, caso contrário, as linhas do multi-inquilinos shards não são filtradas. 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. A camada da aplicação: conjunto TenantId na sessão\_contexto

Primeiro, ligar a uma base de dados de partições horizontais utilizando as APIs de encaminhamento de dados dependentes da biblioteca de clientes de base de dados elástica. A aplicação ainda tem dizer a base de dados que TenantId está a utilizar a ligação. O TenantId indica a política de segurança RLS que linhas tem de ser filtradas como pertencendo ao outros inquilinos. Armazenar o TenantId atual no [sessão\_contexto](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) da ligação.

Uma alternativa à sessão\_contexto consiste em utilizar [contexto\_informações](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Mas sessão\_contexto é uma opção melhor. SESSÃO\_contexto é mais fáceis de utilizar, devolve nulo por predefinição e suporta pares chave-valor.

### <a name="entity-framework"></a>Entity Framework

Para aplicações utilizando o Entity Framework, a abordagem mais fácil consiste em definir a sessão\_contexto dentro a substituição de ElasticScaleContext descrito [dependentes dados encaminhamento com EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Criar e executar um SqlCommand que define o TenantId na sessão\_contexto para shardingKey especificado para a ligação. Em seguida, regresse a ligação mediada através do encaminhamento de dados dependentes. Desta forma, apenas terá de escrever o código de uma vez para definir a sessão\_contexto. 

```csharp
// ElasticScaleContext.cs 
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection 
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
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

Agora a sessão\_contexto é automaticamente definido com o TenantId especificado sempre que é invocada ElasticScaleContext: 

```csharp
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
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

Para aplicações ADO.NET SqlClient a utilizar, crie uma função de wrapper em torno do método ShardMap.OpenConnectionForKey. Ter o wrapper de definir automaticamente TenantId na sessão\_contexto para o TenantId atual antes de regressar de uma ligação. Para garantir que esse sessão\_contexto está sempre definido, só deverá abrir ligações a utilização desta função de wrapper.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
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

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Camada de dados: Criar política de segurança ao nível da linha

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Criar uma política de segurança para filtrar as linhas que pode aceder a cada inquilino

Agora que a aplicação é a definição sessão\_contexto com o TenantId atual antes de consulta, pode filtrar uma política de segurança RLS consultas e linhas de exclusão que tenham um TenantId diferentes.  

RLS está implementada Transact-SQL. Uma função definida pelo utilizador define a lógica de acesso e uma política de segurança está vinculado a esta função para qualquer número de tabelas. Para este projeto:

1. A função verifica que a aplicação está ligada à base de dados e de que o TenantId armazenados na sessão\_contexto corresponde o TenantId de uma linha fornecida.
    - A aplicação está ligada, em vez de outro utilizador SQL.

2. Um predicado de filtro permite que as linhas que cumprem o filtro de TenantId para passar para a ATUALIZAÇÃO, SELECIONE e eliminar consultas.
    - Um predicado de bloco impede que as linhas que não cumpram o filtro de ser INSERTed ou atualizada.
    - Se sessão\_contexto não foi definido, a função devolve NULL e não existem linhas são visíveis ou pode ser inserido. 

Para ativar RLS em todas as partições horizontais, execute o seguinte T-SQL ao utilizar o Visual Studio (SSDT), SSMS ou o script do PowerShell incluído no projeto. Ou se estiver a utilizar [as tarefas de base de dados elásticas](sql-database-elastic-jobs-overview.md), pode automatizar a execução deste T-SQL em todas as partições horizontais.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO 
```

> [!TIP]
> Num projeto complexo que poderá ter de adicionar o predicado no centenas de tabelas, que pode ser tedious. Não há um procedimento armazenado do programa auxiliar que gera uma política de segurança e automaticamente adiciona um predicado em todas as tabelas de um esquema. Para obter mais informações, consulte o blogue em [aplicam-se a segurança ao nível da linha para todas as tabelas - o script de programa auxiliar (blogue)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Se executar novamente a aplicação de exemplo, os inquilinos ver apenas as linhas que pertencem aos mesmos. Além disso, a aplicação não é possível inserir linhas que pertencem aos inquilinos daquela atualmente ligado à base de dados de partições horizontais. Além disso, a aplicação não é possível atualizar o TenantId nas quaisquer linhas que pode ver. Se a aplicação tenta efetuar um, é gerado um DbUpdateException.

Se adicionar uma nova tabela mais tarde, altere a política de segurança para adicionar predicados de bloco e de filtro na tabela de novo.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adicionar restrições predefinidas para preencher automaticamente o TenantId para inserções

Pode colocar uma restrição default em cada tabela para preencher automaticamente o TenantId com o valor atualmente armazenado na sessão\_contexto ao inserir linhas. Segue-se um exemplo. 

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO 
```

Agora a aplicação não tem de especificar um TenantId ao inserir linhas: 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Se utilizar restrições predefinidas para um projeto do Entity Framework, recomenda-se que *não* incluir a coluna de TenantId no seu modelo de dados do EF. Esta recomendação-se ao facto do Entity Framework automaticamente consulta forneça os valores predefinidos que substituem as restrições de predefinido criadas no T-SQL que utilizam sessão\_contexto.
> Para utilizar restrições predefinidas no projeto de exemplo, por exemplo, que deve remover o TenantId DataClasses.cs (e execute Add-migração na consola do Gestor de pacotes) e utiliza T-SQL para Certifique-se de que o campo só existe nas tabelas de base de dados. Desta forma, EF fornecer automaticamente os valores predefinidos incorreto ao inserir dados.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcional) Ativar um *Superutilizador* para aceder a todas as linhas

Algumas aplicações poderão pretender criar um *Superutilizador* quem pode aceder a todas as linhas. Um Superutilizador foi possível ativar a comunicação entre todos os inquilinos em todas as partições horizontais. Ou um Superutilizador poderá realizar operações de intercalação de divisão em partições horizontais que envolvam a mover inquilino linhas entre as bases de dados.

Para permitir um Superutilizador, crie um novo utilizador do SQL Server (`superuser` neste exemplo) em cada base de dados de partições horizontais. Em seguida, altere a política de segurança com uma nova função de predicado que permite que este utilizador aceder a todas as linhas. Este tipo uma função é dado em seguida.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Manutenção

- **Adicionar novo shards**: executar o script T-SQL para ativar a RLS em qualquer shards nova, caso contrário, que não são filtradas consultas nestes shards.
- **Adicionar novas tabelas**: adicionar um predicado de filtro e bloquear a política de segurança em todas as partições horizontais sempre que é criada uma nova tabela. Caso contrário, não são filtradas consultas na tabela de novo. Esta adição pode ser automatizada através da utilização de um acionador DDL, conforme descrito em [aplicam-se a segurança ao nível da linha automaticamente a tabelas recém-criado (blogue)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Resumo

Ferramentas de base de dados elástica e a segurança ao nível da linha podem ser utilizados em conjunto para aumentar horizontalmente a camada de dados de uma aplicação com suporte para ambos os multi-inquilino e de inquilino único shards. Shards multi-inquilinos podem ser utilizados para armazenar dados de forma mais eficiente. Esta eficiência é pronunciada em que um grande número de inquilinos têm apenas de alguns linhas de dados. Shards de inquilino único podem suportar inquilinos premium que têm requisitos de isolamento e de desempenho mais restrito.  Para obter mais informações, consulte [referência de segurança ao nível da linha][rls].

## <a name="additional-resources"></a>Recursos adicionais

- [O que é um conjunto elástico do Azure?](sql-database-elastic-pool.md)
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
- [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md)
- [Autenticação em aplicações multi-inquilino, com o Azure AD e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicação Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de funcionalidades

Para perguntas, contacte-no [fórum de base de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). E adicione quaisquer pedidos de funcionalidades para o [fórum de comentários da base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

