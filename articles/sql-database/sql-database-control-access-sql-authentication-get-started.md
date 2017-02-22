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
ms.date: 01/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 356cc4c6d8e25d36880e4b12bf471326e61990c3
ms.openlocfilehash: 275a33567fa1472573bc8abc87948ad306e853f0


---
# <a name="sql-database-tutorial-sql-server-authentication-logins-and-user-accounts-database-roles-permissions-server-level-firewall-rules-and-database-level-firewall-rules"></a>Tutorial da Base de Dados SQL: autenticação do SQL Server, inícios de sessão e contas de utilizador, funções de base de dados, permissões, regras de firewall ao nível do servidor e regras de firewall ao nível da base de dados)
Neste tutorial de introdução, vai aprender a utilizar o SQL Server Management Studio para trabalhar com a autenticação do SQL Server e com inícios de sessão, utilizadores e funções de base de dados que concedem acesso e permissões aos servidores e bases de dados da Base de Dados SQL do Azure. Vai aprender a:

- Ver permissões de utilizador na base de dados mestra e em bases de dados de utilizador
- Criar inícios de sessão e utilizadores com base na autenticação do SQL Server
- Conceder permissões ao nível de todo o servidor e específicas para bases de dados aos utilizadores
- Iniciar sessão numa base de dados de utilizador como utilizador não administrador
- Criar regras de firewall ao nível da base de dados para utilizadores de bases de dados
- Criar regras de firewall ao nível do servidor para administradores do servidor

**Estimativa de tempo**: a conclusão deste tutorial demora, aproximadamente, 45 minutos (partindo do princípio de que já cumpriu os pré-requisitos).

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar as vantagens de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Tem de conseguir ligar ao portal do Azure com uma conta que seja membro do proprietário da subscrição ou da função de contribuinte. Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), veja o artigo [Introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md).

* Concluiu a [Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure através do portal do Azure e do SQL Server Management Studio](sql-database-get-started.md) ou a [versão do PowerShell](sql-database-get-started-powershell.md) equivalente deste tutorial. Se não, conclua este tutorial de pré-requisitos ou execute o script do PowerShell no final da [versão do PowerShell](sql-database-get-started-powershell.md) do tutorial antes de continuar.

> [!NOTE]
> Este tutorial ajuda-o a aprender os conteúdos dos tópicos de aprendizagem seguintes: [Acesso e controlo à Base de Dados SQL](sql-database-control-access.md), [Inícios de sessão, utilizadores e funções de base de dados](sql-database-manage-logins.md), [Principais](https://msdn.microsoft.com/library/ms181127.aspx), [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx) e [Regras de firewall da Base de Dados SQL](sql-database-firewall-configure.md).
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Iniciar sessão no portal do Azure com a sua conta do Azure
Com a [subscrição existente](https://account.windowsazure.com/Home/Index), siga estes passos para se ligar ao portal do Azure.

1. Abra o browser da sua preferência e ligue-se ao [Portal do Azure](https://portal.azure.com/).
2. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
3. Quando for apresentada a página **Iniciar sessão**, forneça as credenciais da sua subscrição.
   
   ![Iniciar sessão](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="view-information-about-the-security-configuration-for-your-logical-server"></a>Ver informações sobre a configuração de segurança do servidor lógico

Nesta secção do tutorial, vai ver informações sobre a configuração de segurança do seu servidor lógico no portal do Azure.

1. Abra o painel **SQL Server** do servidor lógico e veja as informações na página **Descrição geral**.

   ![Conta de administrador do servidor no portal do Azure](./media/sql-database-control-access-sql-authentication-get-started/sql_admin_portal.png)

2. Aponte o nome da Conta de administrador do servidor do servidor lógico. Se não se lembrar da palavra-passe, clique em **Repor palavra-passe** para definir uma nova.

> [!NOTE]
> Para rever as informações da ligação deste servidor, aceda a [Ver ou atualizar definições do servidor](sql-database-view-update-server-settings.md). Nesta série de tutoriais, o nome do servidor completamente qualificado é “sqldbtutorialserver.database.windows.net”.
>

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Ligar ao servidor SQL através do SQL Server Management Studio (SSMS)

1. Se ainda não o fez, transfira e instale a versão mais recente do SSMS em [Transferir o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para se manter atualizado, a versão mais recente do SSMS avisa-o quando existe uma nova versão disponível para transferência.

2. Após a instalação, escreva **Microsoft SQL Server Management Studio** na caixa de pesquisa do Windows e clique em **Enter** para abrir o SSMS.

   ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)

3. Na caixa de diálogo **Ligar ao Servidor**, introduza as informações necessárias para ligar ao seu servidor SQL através da Autenticação do SQL Server e da Conta de administrador do servidor.

   ![ligar ao servidor](./media/sql-database-get-started/connect-to-server.png)

4. Clique em **Ligar**.

   ![ligado ao servidor](./media/sql-database-get-started/connected-to-server.png)

## <a name="view-the-server-admin-account-and-its-permissions"></a>Ver a Conta de administrador do servidor e as respetivas permissões 
Nesta secção do tutorial, vai ver as informações da conta de administrador do servidor e as respetivas permissões na base de dados mestra e em bases de dados de utilizador.

1. No Object Explorer, expanda **Segurança** e **Inícios de sessão** para ver os inícios de sessão existentes para o seu servidor da Base de Dados SQL do Azure. Repare que é apresentado um início de sessão para a Conta de administrador de servidor especificada durante o aprovisionamento - o início de sessão sqladmin desta série de tutoriais.

   ![Início de sessão de administrador do servidor](./media/sql-database-control-access-sql-authentication-get-started/server_admin_login.png)

2. No Object Explorer, expanda a opção **Bases de Dados**, **Bases de Dados do Sistema**, **mestra**, **Segurança** e, por fim, **Utilizadores**. Observe que foi criada uma conta de utilizador na base de dados mestra para o Início de sessão de administrador do servidor, sendo o nome da conta de utilizador o mesmo do início de sessão (os nomes não têm de ser iguais, mas é uma melhor prática, para evitar confusões).

   ![conta de utilizador da base de dados mestra de administrador do servidor](./media/sql-database-control-access-sql-authentication-get-started/master_database_user_account_for_server_admin.png)

   > [!NOTE]
   > Para obter informações sobre as outras contas de utilizador que aparecem, veja [Principals (Principais](https://msdn.microsoft.com/library/ms181127.aspx).
   >

3. No Object Explorer, clique com o botão direito do rato em **mestra** e clique em **Nova Consulta** para abrir uma janela de consultas ligada à base de dados mestra.
4. Na janela de consultas, execute a consulta seguinte para devolver informações sobre o utilizador que está a executar a consulta. Repare que é devolvido sqladmin para a conta de utilizador que está a executar a consulta (veremos um resultado diferente quando consultarmos uma base de dados de utilizador mais à frente neste procedimento).

   ```
   SELECT USER;
   ```

   ![selecione a consulta de utilizador na base de dados mestra](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_master_database.png)

5. Na janela de consultas, execute a consulta seguinte para devolver informações sobre as permissões do utilizador sqladmin. Repare que sqladmin tem permissões para ligar à base de dados mestra, criar inícios de sessão e utilizadores, selecionar informações da tabela sys.sql_logins e adicionar utilizadores às funções de base de dados dbmanager e dbcreator. Estas permissões são um acrescento às permissões concedidas à função pública da qual todos os utilizadores herdam permissões (como permissões para selecionar informações de determinadas tabelas). Veja [Permissões (Permissões)](https://msdn.microsoft.com/library/ms191291.aspx) para obter mais informações.

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

6. No Object Explorer, expanda **blankdb**, **Segurança** e, por fim, **Utilizadores**. Repare que não existe nenhuma conta de utilizador com o nome sqladmin nesta base de dados.

   ![contas de utilizador em blankdb](./media/sql-database-control-access-sql-authentication-get-started/user_accounts_in_blankdb.png)

7. No Object Explorer, clique com o botão direito do rato em **blankdb** e, em seguida, clique em **Nova Consulta**.

8. Na janela de consultas, execute a consulta seguinte para devolver informações sobre o utilizador que está a executar a consulta. Observe que é devolvido dbo para a conta de utilizador que está a executar esta consulta (por predefinição, o Início de sessão de administrador do Servidor é mapeado para a conta de utilizador dbo em cada base de dados de utilizador).

   ```
   SELECT USER;
   ```

   ![selecionar a consulta de utilizador na base de dados blankdb](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_blankdb_database.png)

9. Na janela de consultas, execute a consulta seguinte para devolver informações sobre as permissões do utilizador dbo. Repare que dbo é membro da função pública e também da função de base de dados fixa db_owner. Veja [Database-Level Roles (Funções ao Nível da Base de Dados)](https://msdn.microsoft.com/library/ms189121.aspx) para obter mais informações.

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

10. Opcionalmente, repita os três passos anteriores para a base de dados de utilizador AdventureWorksLT.

## <a name="create-a-new-user-in-the-adventureworkslt-database-with-select-permissions"></a>Criar um utilizador novo na base de dados AdventureWorksLT com permissões SELECT

Nesta secção do tutorial, vai criar uma conta de utilizador na base de dados AdventureWorksLT, testar as permissões deste utilizador como membro da função pública, conceder permissões SELECT ao utilizador e testar novamente as permissões do mesmo.

> [!NOTE]
> Os utilizadores ao nível da base de dados ([utilizadores contidos](https://msdn.microsoft.com/library/ff929188.aspx)) aumentam a portabilidade da sua base de dados, uma capacidade que vamos explorar em tutoriais posteriores.
>

1. No Object Explorer, clique com o botão direito do rato em **AdventureWorksLT** e clique em **Nova Consulta** para abrir uma janela de consultas ligada à base de dados AdventureWorksLT.
2. Execute a declaração seguinte para criar um utilizador com o nome utilizador1 na base de dados AdventureWorksLT.

   ```
   CREATE USER user1
   WITH PASSWORD = 'p@ssw0rd';
   ```
   ![novo utilizador utilizador1 de AdventureWorksLT](./media/sql-database-control-access-sql-authentication-get-started/new_user_user1_aw.png)

3. Na janela de consultas, execute a consulta seguinte para devolver informações sobre as permissões do utilizador1. Repare que as únicas permissões que o utilizador1 tem são as herdadas da função pública.

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

4. Execute as consultas seguintes para tentar consultar uma tabela na base de dados AdventureWorksLT como Utilizador1.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![sem permissões select](./media/sql-database-control-access-sql-authentication-get-started/no_select_permissions.png)

5. Execute a declaração seguinte para conceder permissões SELECT na tabela ProductCategory no esquema SalesLT ao utilizador1.

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
   ```

   ![conceder permissões select](./media/sql-database-control-access-sql-authentication-get-started/grant_select_permissions.png)

6. Execute as consultas seguintes para tentar consultar uma tabela na base de dados AdventureWorksLT como Utilizador1.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![permissões select](./media/sql-database-control-access-sql-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-for-an-adventureworkslt-database-user"></a>Criar uma regra de firewall ao nível da base de dados para um utilizador da base de dados AdventureWorksLT

Nesta secção do tutorial, vai tentar iniciar sessão num computador com um endereço IP diferente, criar uma regra de firewall ao nível da base de dados como o Administrador do servidor e, depois, iniciar sessão com esta nova regra de firewall ao nível da base de dados. 

> [!NOTE]
> As [regras de firewall ao nível da base de dados](sql-database-firewall-configure.md) aumentam a portabilidade da sua base de dados, uma capacidade que vamos explorar em tutoriais posteriores.
>

1. Noutro computador para o qual ainda não criado uma regra de firewall ao nível do servidor, abra o SQL Server Management Studio.

   > [!IMPORTANT]
   > Utilize sempre a versão mais recente do SSMS disponível em [Download SQL Server Management Studio (Transferir o SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx). 
   >

2. Na janela **Ligar ao Servidor**, introduza o nome do servidor e as informações de autenticação para ligar através da autenticação do SQL Server com a conta de utilizador1. 
    
   ![Ligar como o utilizador1 sem a regra de firewall1](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule1.png)

3. Clique em **Opções** para especificar a base de dados à qual quer ligar e, em seguida, escreva **AdventureWorksLT** na caixa pendente **Ligar à Base de Dados**, no separador **Propriedades da Ligação**.
   
   ![Ligar como o utilizador1 sem a regra de firewall2](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule2.png)

4. Clique em **Ligar**. Aparece uma caixa de diálogo que o informa de que o computador através do qual está a tentar ligar à Base de Dados SQL não tem uma regra de firewall que permita o acesso à base de dados. A caixa de diálogo que recebe tem duas variações, consoante os passos que seguiu anteriormente no que diz respeito às firewalls, mas, regra geral, recebe a primeira caixa apresentada.

   ![Ligar como o utilizador1 sem a regra de firewall3](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule3.png)

   ![Ligar como o utilizador1 sem a regra de firewall4](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule4.png)

   > [!NOTE]
   > As versões mais recentes do SSMS incluem uma funcionalidade que permite aos proprietários de subscrições e aos contribuidores iniciar sessão no Microsoft Azure e criar uma regra de firewall ao nível do servidor.
   > 

4. Copie o endereço IP de cliente desta caixa de diálogo para utilização no passo 7.
5. Clique em **Cancelar**, mas não feche a caixa de diálogo **Ligar ao Servidor**.
6. Mude para um computador para o qual já tenha criado uma regra de firewall ao nível do servidor e ligue ao seu servidor com a Conta de administrador de servidor.
7. Numa janela de consulta nova ligada à base de dados AdventureWorksLT como Administrador de servidor, execute a declaração seguinte para criar uma regra de firewall ao nível da base de dados ao executar [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) através da utilização do endereço IP do passo 4:

   ```
   EXEC sp_set_database_firewall_rule @name = N'AdventureWorksLTFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

   ![adicionar regra de firewall](./media/sql-database-control-access-sql-authentication-get-started/user1_add_rule_aw.png)

8. Mude novamente de computador e clique em **Ligar**, na caixa de diálogo **Ligar ao Servidor**, para ligar a AdventureWorksLT como o utilizador1. 

   ![Ligar como o utilizador1 com a regra de firewall1](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_rule1.png)

9. No Object Explorer, expanda **Bases de Dados**, **AdventureWorksLT** e, por fim, **Tabelas**. Repare que o utilizador1 só tem permissão para ver uma única tabela, a tabela **SalesLT.ProductCategory**. 

   ![Ligar como o utilizador1 e ver objetos1](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_view_objects1.png)

10. No Object Explorer, clique com o botão direito do rato em **SalesLT.ProductCategory** e clique em **Selecionar as Primeiras 1000 Linhas**.   

   ![utilizador1 consulta1](./media/sql-database-control-access-sql-authentication-get-started/user1_query1.png)

   ![utilizador1 resultados da consulta1](./media/sql-database-control-access-sql-authentication-get-started/user1_query1_results.png)

## <a name="create-a-new-user-in-the-blankdb-database-with-dbowner-database-role-permissions-and-a-database-level-firewall-rule"></a>Criar um utilizador novo na base de dados blankdb com permissões de função de base de dados db_owner e uma regra de firewall ao nível da base de dados

Nesta secção do tutorial, vai criar um utilizador na base de dados blankdb com as permissões de função de base de dados db_owner e criar uma firewall ao nível da base de dados para esta base de dados com a Conta de administrador de servidor. 

1. Mude para o computador que tem ligação à Base de Dados SQL com a Conta de administrador de servidor.
2. Abra uma janela de consultas ligada à base de dados blankdb e execute a declaração seguinte para criar um utilizador com o nome blankdbadmin na base de dados blankdb.

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
 
7. Conforme necessário para o seu ambiente de aprendizagem, crie uma regra de firewall ao nível da base de dados adicional para este utilizador. 

## <a name="create-a-new-login-and-user-in-the-master-database-with-dbmanager-permissions-and-create-a-server-level-firewall-rule"></a>Criar um início de sessão e um utilizador novos na base de dados mestra com permissões dbmanager e criar uma regra de firewall ao nível do servidor

Nesta secção do tutorial, vai criar um início de sessão e um utilizador na base de dados mestra com permissões para criar e gerir novas bases de dados de utilizador. Também vai criar uma regra de firewall ao nível do servidor adicional com Transact-SQL através de [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx).

> [!NOTE]
> Para que o titular da Conta de administrador de servidor possa delegar permissões de criação de bases de dados a outro utilizador, é necessário criar inícios de sessão na base de dados mestra e contas de utilizador a partir de inícios de sessão. Contudo, criar inícios de sessão e utilizadores a partir de inícios de sessão diminui a portabilidade do seu ambiente, cujas consequências vamos explorar em tutoriais posteriores, incluindo como antecipar e processar como parte do planeamento da recuperação após desastre.
>

1. Mude para o computador que tem ligação à Base de Dados SQL com a Conta de administrador de servidor.
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

4. Na janela da mesma consulta, execute a consulta seguinte para criar uma firewall ao nível do servidor ao executar [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) através de um endereço IP adequado ao seu ambiente:

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

### <a name="adventureworkslt-database"></a>Base de dados AdventureWorksLT
Execute estas declarações na base de dados AdventureWorksLT com a Conta de administrador de servidor, adicionando os endereços ou intervalos de endereços IP apropriados.

```
CREATE USER user1 WITH PASSWORD = 'p@ssw0rd';
GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
EXEC sp_set_database_firewall_rule @name = N'AdventureWorksLTFirewallRule', 
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
- Para obter um tutorial sobre como utilizar a autenticação do Azure Active Directory, veja [SQL Database tutorial: AAD authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules (Tutorial da Base de Dados SQL: autenticação do AAD, inícios de sessão e contas de utilizador, funções de base de dados, permissões, regras de firewall ao nível do servidor e regras de firewall ao nível da base de dados)](sql-database-control-access-sql-authentication-get-started.md).




<!--HONumber=Jan17_HO3-->


