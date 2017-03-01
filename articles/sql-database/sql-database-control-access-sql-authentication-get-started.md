---
title: "Autenticação do SQL: firewalls, autenticação e acesso à Base de Dados SQL do Azure | Microsoft Docs"
description: "Neste tutorial de introdução, vai aprender a utilizar o SQL Server Management Studio e o Transact-SQL para trabalhar com regras de firewall ao nível do servidor e da base de dados, com a autenticação do SQL e com inícios de sessão, utilizadores e funções para conceder acesso e controlo aos servidores e bases de dados da Base de Dados SQL do Azure."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c70b3b23fa95af6614c34bd951943f0559409220
ms.openlocfilehash: cf43790c329ef156ae17579d2281c861533ec201


---
# <a name="sql-server-authentication-access-and-database-level-firewall-rules"></a>Autenticação do SQL Server, acesso e regras de firewall ao nível da base de dados

Neste tutorial, vai aprender a utilizar o SQL Server Management Studio para trabalhar com a autenticação do SQL Server e com inícios de sessão, utilizadores e funções de base de dados que concedem acesso e permissões aos servidores e bases de dados da Base de Dados SQL do Azure. Depois de concluir este tutorial, ficará a saber como:

- Criar inícios de sessão e utilizadores com base na autenticação do SQL Server
- Adicionar utilizadores a funções e conceder permissões a funções
- Utilizar T-SQL para criar uma regra de firewall ao nível da base de dados e ao nível do servidor 
- Utilizar o SSMS para se ligar como um utilizador específico a uma base de dados específica
- Ver permissões de utilizador na base de dados mestra e em bases de dados de utilizador

**Estimativa de tempo**: a conclusão deste tutorial demora, aproximadamente, 45 minutos (partindo do princípio de que já cumpriu os pré-requisitos).

> [!NOTE]
> Este tutorial ajuda-o a aprender os conteúdos dos tópicos seguintes: [Acesso e controlo à Base de Dados SQL](sql-database-control-access.md), [Inícios de sessão, utilizadores e funções de base de dados](sql-database-manage-logins.md), [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx), [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx) e [Regras de firewall da Base de Dados SQL](sql-database-firewall-configure.md). Para obter um tutorial sobre a autenticação do Azure Active Directory, veja [Introdução à autenticação do Azure AD](sql-database-control-access-aad-authentication-get-started.md).
>  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta do Azure**. Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](https://azure.microsoft.com/free/) ou [Ativar as vantagens de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Permissões de criação do Azure**. Tem de conseguir ligar ao portal do Azure com uma conta que seja membro do proprietário da subscrição ou da função de contribuinte. Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), veja o artigo [Introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md).

* **SQL Server Management Studio**. Pode transferir e instalar a versão mais recente do SQL Server Management Studio (SSMS) em [Download SQL Server Management Studio (Transferir o SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx). Utilize sempre a versão mais recente do SSMS quando ligar à Base de Dados SQL do Azure, uma vez que são continuamente lançadas capacidades novas.

* **Concluir o tutorial de base**. Concluiu a [Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure através do portal do Azure e do SQL Server Management Studio](sql-database-get-started.md) ou a [versão do PowerShell](sql-database-get-started-powershell.md) equivalente deste tutorial. Se não, conclua este tutorial de pré-requisitos ou execute o script do PowerShell no final da [versão do PowerShell](sql-database-get-started-powershell.md) do tutorial antes de continuar.



## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Iniciar sessão no portal do Azure com a sua conta do Azure
Os passos deste procedimento mostram-lhe como ligar ao portal do Azure com a sua conta do Azure](https://account.windowsazure.com/Home/Index).

1. Abra o browser da sua preferência e ligue-se ao [Portal do Azure](https://portal.azure.com/).
2. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
3. Quando for apresentada a página **Iniciar sessão**, forneça as credenciais da sua subscrição.
   
   ![Iniciar sessão](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="view-logical-server-security-information-in-the-azure-portal"></a>Ver informações de segurança do servidor lógico no portal do Azure

Os passos deste procedimento mostram-lhe como ver informações sobre a configuração de segurança do seu servidor lógico no portal do Azure.

1. Abra o painel **SQL Server** do seu servidor e veja as informações na página **Descrição geral**.

   ![Conta de administrador do servidor no portal do Azure](./media/sql-database-control-access-sql-authentication-get-started/sql_admin_portal.png)

2. Tome nota do nome do administrador do servidor do seu servidor lógico. 

3. Se não se lembrar da palavra-passe, clique em **Repor palavra-passe** para definir uma nova.

4. Se precisar de obter as informações de ligação deste servidor, clique em **Propriedades**.

## <a name="view-server-admin-permissions-using-ssms"></a>Ver permissões de administrador de servidor com o SSMS

Os passos deste procedimento mostram-lhe como ver as informações da conta de administrador do servidor e as respetivas permissões na base de dados mestra e em bases de dados de utilizador.

1. Abra o SQL Server Management Studio e utilize a Autenticação do SQL Server e a Conta de administrador do servidor para ligar ao servidor como o administrador do servidor.

   ![ligar ao servidor](./media/sql-database-get-started/connect-to-server.png)

2. Clique em **Ligar**.

   ![ligado ao servidor](./media/sql-database-get-started/connected-to-server.png)

3. No Object Explorer, expanda **Segurança** e **Inícios de Sessão** para ver os inícios de sessão atuais do seu servidor. O único início de sessão em servidores novos é o da conta de administrador do servidor.

   ![Início de sessão de administrador do servidor](./media/sql-database-control-access-sql-authentication-get-started/server_admin_login.png)

4. No Object Explorer, expanda **Bases de dados**, expanda **Bases de dados do sistema**, expanda **mestra**, expanda **Segurança** e expanda **Utilizadores** para ver a conta de utilizador que foi criada para o início de sessão de administrador do servidor nesta base de dados.

   ![conta de utilizador da base de dados mestra de administrador do servidor](./media/sql-database-control-access-sql-authentication-get-started/master_database_user_account_for_server_admin.png)

   > [!NOTE]
   > Para obter informações sobre as outras contas de utilizador que aparecem no nó Utilizadores, veja [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx).
   >

5. No Object Explorer, clique com o botão direito do rato em **mestra** e clique em **Nova Consulta** para abrir uma janela de consultas ligada à base de dados mestra.
6. Na janela de consultas, execute a consulta seguinte para devolver informações sobre o utilizador que está a executar a consulta. 

   ```
   SELECT USER;
   ```

   ![selecione a consulta de utilizador na base de dados mestra](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_master_database.png)

7. Na janela de consultas, execute a consulta seguinte para devolver informações sobre as permissões do utilizador sqladmin na base de dados **mestra**. 

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals p
   JOIN sys.database_permissions prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'sqladmin';
   ```

   ![permissões do administrador de servidor na base de dados mestra](./media/sql-database-control-access-sql-authentication-get-started/server_admin_permissions_in_master_database.png)

   >[!NOTE]
   > O administrador de servidor tem permissões para ligar à base de dados mestra, criar inícios de sessão e utilizadores, selecionar informações da tabela sys.sql_logins e adicionar utilizadores às funções de base de dados dbmanager e dbcreator. Estas permissões são um acrescento às permissões concedidas à função pública da qual todos os utilizadores herdam permissões (como permissões para selecionar informações de determinadas tabelas). Veja [Permissões (Permissões)](https://msdn.microsoft.com/library/ms191291.aspx) para obter mais informações.
   >

8. No Object Explorer, expanda **blankdb**, expanda **Segurança** e expanda **Utilizadores** para ver a conta de utilizador que foi criada para o início de sessão de administrador desta base de dados (e de cada base de dados de utilizador).

   ![contas de utilizador em blankdb](./media/sql-database-control-access-sql-authentication-get-started/user_accounts_in_blankdb.png)

9. No Object Explorer, clique com o botão direito do rato em **blankdb** e, em seguida, clique em **Nova Consulta**.

10. Na janela de consultas, execute a consulta seguinte para devolver informações sobre o utilizador que está a executar a consulta.

   ```
   SELECT USER;
   ```

   ![selecionar a consulta de utilizador na base de dados blankdb](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_blankdb_database.png)

11. Na janela de consultas, execute a consulta seguinte para devolver informações sobre as permissões do utilizador dbo. 

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'dbo';
   ```

   ![permissões do administrador de servidor na base de dados blankdb](./media/sql-database-control-access-sql-authentication-get-started/server_admin_permissions_in_blankdb_database.png)

   > [!NOTE]
   > O utilizador dbo é membro da função pública e também da função de base de dados fixa db_owner. Veja [Database-Level Roles (Funções ao Nível da Base de Dados)](https://msdn.microsoft.com/library/ms189121.aspx) para obter mais informações.
   >

## <a name="create-a-new-user-with-select-permissions"></a>Criar um utilizador novo com permissões SELECT

Os passos deste procedimento mostram-lhe como criar um utilizador ao nível da base de dados, testar as permissões predefinidas de um utilizador novo (através da função pública), conceder permissões **SELECT** e ver estas permissões modificadas.

> [!NOTE]
> Os utilizadores ao nível da base de dados também são conhecidos como [utilizadores contidos](https://msdn.microsoft.com/library/ff929188.aspx) e aumentam a portabilidade da base de dados. Para obter informações sobre os benefícios da portabilidade, veja [Configure and manage Azure SQL Database security for geo-restore or failover to a secondary server (Configurar e gerir a segurança da Base de Dados SQL do Azure para georrestauro ou ativação pós-falha para um servidor secundário)](sql-database-geo-replication-security-config.md).
>

1. No Object Explorer, clique com o botão direito do rato em **sqldbtutorialdb** e clique em **Nova Consulta**.
2. Execute a declaração seguinte nesta janela de consultas para criar um utilizador com o nome **user1** na base de dados sqldbtutorialdb.

   ```
   CREATE USER user1
   WITH PASSWORD = 'p@ssw0rd';
   ```
   ![novo utilizador user1 sqldbtutorialdb](./media/sql-database-control-access-sql-authentication-get-started/new_user_user1_aw.png)

3. Na janela de consultas, execute a consulta seguinte para devolver informações sobre as permissões do utilizador1.

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'user1';
   ```

   ![permissões do utilizador novo numa base de dados de utilizador](./media/sql-database-control-access-sql-authentication-get-started/new_user_permissions_in_user_database.png)

   > [!NOTE]
   > Os utilizadores novos nas bases de dados só têm as permissões herdadas da função pública.
   >

4. Utilize a declaração **EXECUTE AS USER** para executar as consultas seguintes e tentar consultar a tabela SalesLT.ProductCategory na base de dados sqldbtutorialdb como **user1** apenas com as permissões herdadas da função pública.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![sem permissões select](./media/sql-database-control-access-sql-authentication-get-started/no_select_permissions.png)

   > [!NOTE]
   > Por predefinição, a função pública não concede permissões **SELECT** em objetos de utilizador.
   >

5. Execute a declaração seguinte para conceder permissões **SELECT** na tabela SalesLT.ProductCategory a **user1**.

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
   ```

   ![conceder permissões select](./media/sql-database-control-access-sql-authentication-get-started/grant_select_permissions.png)

6. Execute as consultas seguintes para consultar com êxito a tabela SalesLT.ProductCategory na base de dados sqldbtutorialdb como **user1**.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![permissões select](./media/sql-database-control-access-sql-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-using-t-sql"></a>Criar uma regra de firewall ao nível da base de dados com T-SQL

Os passos deste procedimento mostram-lhe como criar uma regra de firewall ao nível da base de dados com o procedimento armazenado do sistema [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx). As regras de firewall ao nível da base de dados possibilitam aos administradores de servidores permitir utilizadores através da firewall da Base de Dados SQL do Azure apenas para bases de dados específicas.

> [!NOTE]
> As [regras ao nível da base de dados](sql-database-firewall-configure.md) aumentam a portabilidade da sua base de dados. Para obter informações sobre os benefícios da portabilidade, veja [Configure and manage Azure SQL Database security for geo-restore or failover to a secondary server (Configurar e gerir a segurança da Base de Dados SQL do Azure para georrestauro ou ativação pós-falha para um servidor secundário)](sql-database-geo-replication-security-config.md).
>

> [!IMPORTANT]
> Para testar uma regra de firewall ao nível da base de dados, ligue a partir de outro computador (ou elimine a regra de firewall ao nível do servidor no portal do Azure).
>

1. Abra o SQL Server Management Studio num computador no qual não tem uma regra de firewall ao nível do servidor.

2. Na janela **Ligar ao Servidor**, introduza o nome do servidor e as informações de autenticação para ligar através da autenticação do SQL Server com a conta de **user1**. 
    
   ![Ligar como o utilizador1 sem a regra de firewall1](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule1.png)

3. Clique em **Opções** para especificar a base de dados à qual quer ligar e, em seguida, escreva **sqldbtutorialdb** na caixa pendente **Ligar à Base de Dados**, no separador **Propriedades da Ligação**.
   
   ![Ligar como o utilizador1 sem a regra de firewall2](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule2.png)

4. Clique em **Ligar**. 

   Aparece uma caixa de diálogo que o informa de que o computador através do qual está a tentar ligar à Base de Dados SQL não tem uma regra de firewall que permita o acesso à base de dados. 

   ![Ligar como o utilizador1 sem a regra de firewall4](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule4.png)


5. Copie o endereço IP de cliente desta caixa de diálogo para utilização no passo 8.
6. Clique em **OK** para fechar a caixa de diálogo de erro, mas não feche a caixa de diálogo **Ligar ao Servidor**.
7. Mude para um computador para o qual já tenha criado uma regra de firewall ao nível do servidor. 
8. Ligue à base de dados sqldbtutorialdb no SSMS como o administrador do servidor e execute a declaração seguinte para criar uma firewall ao nível da base de dados com o endereço IP (ou com o intervalo de endereços) do passo 5.  

   ```
   EXEC sp_set_database_firewall_rule @name = N'sqldbtutorialdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

   ![adicionar regra de firewall](./media/sql-database-control-access-sql-authentication-get-started/user1_add_rule_aw.png)

9. Mude novamente de computador e clique em **Ligar**, na caixa de diálogo **Ligar ao Servidor**, para ligar a sqldbtutorialdb como user1. 

   > [!NOTE]
   > Depois de criar a regra de firewall ao nível da base de dados, esta pode demorar até cinco minutos a ficar ativa.
   >

10. Depois de ligar com êxito, expanda **Bases de dados**, no Object Explorer. Repare que **user1** só pode ver a base de dados **sqldbtutorialdb**.

   ![Ligar como o utilizador1 com a regra de firewall1](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_rule1.png)

11. Expanda **sqldbtutorialdb** e, em seguida, expanda **Tabelas**. Repare que o utilizador1 só tem permissão para ver uma única tabela, a tabela **SalesLT.ProductCategory**. 

   ![Ligar como o user1 e ver objects1](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_view_objects1.png)

## <a name="create-a-new-user-as-dbowner-and-a-database-level-firewall-rule"></a>Criar um utilizador novo como db_owner e uma regra de firewall ao nível da base de dados

Os passos deste procedimento mostram-lhe como criar um utilizador noutra base de dados com as permissões de função de base de dados db_owner e uma firewall ao nível da base de dados para essa base de dados. Este utilizador novo com a associação de função **db_owner** só se poderá ligar e gerir esta base de dados individual.

1. Mude para o computador que tem ligação à Base de Dados SQL com a conta de administrador de servidor.
2. Abra uma janela de consultas ligada à base de dados **blankdb** e execute a declaração seguinte para criar um utilizador com o nome blankdbadmin na base de dados blankdb.

   ```
   CREATE USER blankdbadmin
   WITH PASSWORD = 'p@ssw0rd';
   ```

3. Na janela da mesma consulta, execute a declaração seguinte para adicionar o utilizador blankdbadmin à função de base de dados db_owner. Este utilizador pode agora executar todas as ações necessárias para gerir a base de dados blankdb.

   ```
   ALTER ROLE db_owner ADD MEMBER blankdbadmin; 
   ```

4. Na janela da mesma consulta, execute a declaração seguinte para criar uma firewall ao nível da base de dados ao executar [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) através do endereço IP do passo 4 do procedimento anterior (ou um intervalo de endereços IP de utilizadores desta base de dados):

   ```
   EXEC sp_set_database_firewall_rule @name = N'blankdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

5. Mude de computador (para um para o qual tenha criado uma regra de firewall ao nível da base de dados) e ligue à base de dados blankdb com a conta de utilizador blankdbadmin.
6. Abra uma janela de consultas da base de dados blankdb e execute a declaração seguinte para criar um utilizador com o nome blankdbuser1 na base de dados blankdb.

   ```
   CREATE USER blankdbuser1
   WITH PASSWORD = 'p@ssw0rd';
   ```
 
7. Conforme necessário para o seu ambiente de aprendizagem, crie uma regra de firewall ao nível da base de dados adicional para este utilizador. No entanto, se tiver criado a regra de firewall ao nível da base de dados com um intervalo de endereços IP, essa regra poderá não ser necessária.

## <a name="grant-dbmanager-permissions-and-create-a-server-level-firewall-rule"></a>Conceder permissões de dbmanager e criar uma regra de firewall ao nível do servidor

Os passos deste tutorial mostram-lhe como criar um início de sessão e um utilizador na base de dados mestra com permissões para criar e gerir novas bases de dados de utilizador. Também lhe mostram como criar uma regra de firewall ao nível do servidor adicional com Transact-SQL através de [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx). 

> [!IMPORTANT]
>A primeira regra de firewall ao nível do servidor tem de ser sempre criada no Azure (no portal do Azure, com o PowerShell ou com a API REST).
>

> [!IMPORTANT]
> Para que o de administrador de servidor possa delegar permissões de criação de bases de dados a outro utilizador, é necessário criar inícios de sessão na base de dados mestra e contas de utilizador a partir de inícios de sessão. No entanto, criar inícios de sessão e depois criar utilizadores a partir de inícios de sessão diminui a portabilidade do seu ambiente.
>

1. Mude para o computador que tem ligação à Base de Dados SQL com a conta de administrador de servidor.
2. Abra uma janela de consultas ligada à base de dados mestra e execute a declaração seguinte para criar um início de sessão com o nome dbcreator na base de dados mestra.

   ```
   CREATE LOGIN dbcreator
   WITH PASSWORD = 'p@ssw0rd';
   ```

3. Na janela da mesma consulta, 

   ```
   CREATE USER dbcreator
   FROM LOGIN dbcreator;
   ```

3. Na janela da mesma consulta, execute a consulta seguinte para adicionar o utilizador dbcreator à função de base de dados dbmanager. Este utilizador pode agora criar e gerir bases de dados criadas pelo utilizador.

   ```
   ALTER ROLE dbmanager ADD MEMBER dbcreator; 
   ```

4. Na janela da mesma consulta, execute a consulta seguinte para criar uma firewall ao nível do servidor ao executar [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) com um endereço IP adequado ao seu ambiente:

   ```
   EXEC sp_set_firewall_rule @name = N'dbcreatorFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

5. Mude de computador (para um para o qual tenha criado uma regra de firewall ao nível do servidor) e ligue à base de dados mestra com a conta de utilizador dbcreator.
6. Abra uma janela de consultas para base de dados mestra e execute a consulta seguinte para criar uma base de dados com o nome foo.

   ```
   CREATE DATABASE FOO (EDITION = 'basic');
   ```
 7. Opcionalmente, elimine esta base de dados para poupar dinheiro com a declaração seguinte:

   ```
   DROP DATABASE FOO;
   ```

## <a name="complete-script"></a>Script completo

Para criar os inícios de sessão e os utilizadores, adicioná-los a funções, conceder-lhes permissões, criar regras de firewall ao nível da base de dados e criar regras de firewall ao nível do servidor, execute as declarações seguintes nas bases de dados adequadas do seu servidor.

### <a name="master-database"></a>base de dados mestra
Execute estas declarações na base de dados mestra com a Conta de administrador de servidor, adicionando os endereços ou intervalos de endereços IP apropriados.

```
CREATE LOGIN dbcreator WITH PASSWORD = 'p@ssw0rd';
CREATE USER dbcreator FROM LOGIN dbcreator;
ALTER ROLE dbmanager ADD MEMBER dbcreator;
EXEC sp_set_firewall_rule @name = N'dbcreatorFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
```

### <a name="sqldbtutorialdb-database"></a>base de dados sqldbtutorialdb
Execute estas declarações na base de dados sqldbtutorialdb com a Conta de administrador de servidor, adicionando os endereços ou intervalos de endereços IP apropriados.

```
CREATE USER user1 WITH PASSWORD = 'p@ssw0rd';
GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
EXEC sp_set_database_firewall_rule @name = N'sqldbtutorialdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
```

### <a name="blankdb-database"></a>base de dados blankdb
Execute estas declarações na base de dados blankdb com a Conta de administrador de servidor, adicionando os endereços ou intervalos de endereços IP apropriados.

```
CREATE USER blankdbadmin
   WITH PASSWORD = 'p@ssw0rd';
ALTER ROLE db_owner ADD MEMBER blankdbadmin;
EXEC sp_set_database_firewall_rule @name = N'blankdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
CREATE USER blankdbuser1
   WITH PASSWORD = 'p@ssw0rd';
```

## <a name="next-steps"></a>Passos seguintes
- Para obter uma descrição geral do acesso e controlo na Base de Dados SQL, veja [Acesso e controlo da Base de Dados SQL](sql-database-control-access.md).
- Para obter uma descrição geral de inícios de sessão, utilizadores e funções de base de dados da Base de Dados SQL, veja [Inícios de sessão, utilizadores e funções de base de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](sql-database-firewall-configure.md).
- Para obter um tutorial sobre a autenticação do Azure Active Directory, veja [Autenticação e autorização do Azure AD](sql-database-control-access-aad-authentication-get-started.md).




<!--HONumber=Feb17_HO3-->


