---
title: "Autenticação e Autorização da Base de Dados SQL: Conceder Acesso | Microsoft Docs"
description: "Saiba mais sobre a gestão da segurança da Base de Dados SQL, mais concretamente como gerir o acesso às bases de dados e a segurança dos inícios de sessão através da conta principal ao nível do servidor."
keywords: "segurança de base de dados sql,gestão de segurança da base de dados,segurança de início de sessão,segurança de base de dados,acesso de base de dados"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 0a65a93f-d5dc-424b-a774-7ed62d996f8c
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/14/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 498ee06fedf986a1b7fb4e362c4b5cb688f6a685
ms.openlocfilehash: 37cce678e0a9dfb081719cee4be7a04a838c1012


---
# <a name="sql-database-authentication-and-authorization-granting-access"></a>Autenticação e Autorização da Base de Dados SQL: Conceder Acesso
> [!div class="op_single_selector"]
> * [Tutorial de introdução](sql-database-get-started-security.md)
> * [Conceder acesso](sql-database-manage-logins.md)
> 
> 

Comece aqui para obter uma descrição geral dos conceitos de acesso da Base de Dados SQL para administradores, não administradores e funções.

## <a name="unrestricted-administrative-accounts"></a>Contas administrativas sem restrições
Existem duas contas administrativas possíveis com permissões sem restrições de acesso à base de dados mestra virtual e a todas as bases de dados de utilizador. Estas contas são denominadas contas principais ao nível do servidor.

### <a name="azure-sql-database-subscriber-account"></a>Conta de subscritor da Base de Dados SQL do Azure
Quando é criada uma instância SQL lógica, é criada conta de início de sessão individual, denominada Conta de Subscritor da Base de Dados SQL. Liga-se através da autenticação do SQL Server (nome de utilizador e palavra-passe). Esta conta é administradora na instância do servidor lógico e em todas as bases de dados de utilizador ligadas a essa instância. As permissões da Conta de Subscritor não podem ser restritas. Só pode existir uma destas contas.

### <a name="azure-active-directory-administrator"></a>Administrador do Azure Active Directory
Também é possível configurar uma conta do Azure Active Directory como administrador. Esta conta pode ser um Utilizador individual do Azure AD ou pode ser um Grupo do Azure AD que contenha vários Utilizadores do Azure AD. Configurar um administrador do Azure AD é opcional. Contudo, este tem de ser configurado se quiser utilizar a Autenticação do Windows para que as contas do Azure AD se liguem à Base de Dados SQL. Para obter mais informações sobre a configuração do acesso do Azure Active Directory, veja [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Ligar à Base de Dados SQL ou ao SQL Data Warehouse Com a Autenticação do Azure Active Directory)](sql-database-aad-authentication.md) e [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Suporte do SSMS para Azure AD MFA com Base de Dados SQL e SQL Data Warehouse)](sql-database-ssms-mfa-authentication.md).

### <a name="configuring-the-firewall"></a>Configurar a firewall
Quando a firewall ao nível do servidor está configurada, a Conta de Subscritor da Base de Dados SQL do Azure e a conta do Azure Active Directory podem ligar-se à base de dados mestra e a todas as bases de dados de utilizador. A firewall ao nível do servidor pode ser configurada através do portal. Depois de estabelecida uma ligação, também podem ser configuradas regras de firewall ao nível de servidor adicionais ao utilizar a instrução Transact-SQL [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx). Para obter mais informações sobre as regras de firewall ao nível do servidor, veja [How: Configure an Azure SQL server firewall using the Azure portal (Procedimento: configurar uma firewall da Base de Dados SQL com o portal do Azure)](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Caminho de acesso do administrador
Quando a firewall ao nível do servidor está devidamente configurada, a Conta de Subscritor da Base de Dados SQL e os Administradores do SQL Server do Azure Active Directory podem ligar-se com ferramentas de cliente, como o SQL Server Management Studio ou o SQL Server Data Tools. Só as ferramentas mais recentes proporcionam todas as funcionalidades e capacidades. O diagrama seguinte mostra uma configuração típica para as duas contas de administrador.
    ![Caminho de acesso do administrador](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Quando utilizar uma porta aberta na firewall ao nível do servidor, os administradores podem ligar-se a qualquer Base de Dados SQL.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Ligar a uma base de dados com o SQL Server Management Studio
Para obter uma introdução sobre como ligar com o SQL Server Management Studio, veja [Ligar à Base de Dados SQL com o SQL Server Management Studio e executar uma consulta T-SQL de exemplo](sql-database-connect-query-ssms.md).

> [!IMPORTANT]
> É recomendado utilizar sempre a versão mais recente do Management Studio, para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="additional-special-accounts"></a>Contas especiais adicionais
A Base de Dados SQL fornece duas funções administrativas restritas na base de dados mestra virtual, às quais podem ser adicionadas contas de utilizador.

### <a name="database-creators"></a>Criadores de base de dados
As contas administrativas podem criar novas bases de dados. Para criar uma conta adicional que pode criar bases de dados, tem de criar um utilizador na base de dados mestra e adicioná-lo à função de base de dados especial **dbmanager**. O utilizador pode ser um utilizador de base de dados contido ou um utilizador com base num início de sessão do SQL Server na base de dados mestra virtual.

1. Ao utilizar uma conta de administrador, ligue à base de dados mestra virtual.
2. Passo opcional: crie um início de sessão da autenticação do SQL Server, utilizando a instrução [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx). Instrução de exemplo:
   
   ```
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > Utilize uma palavra-passe forte ao criar um utilizador de base de dados contido ou de início de sessão. Para obter mais informações, veja [Strong Passwords (Palavras-passe Fortes)](https://msdn.microsoft.com/library/ms161962.aspx).
   > 
   > 
   
   Para melhorar o desempenho, os início de sessão (principais ao nível do servidor) são temporariamente colocados em cache ao nível da base de dados. Para atualizar a cache de autenticação, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).
3. Na base de dados mestra virtual, crie um utilizador com a instrução [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx). O utilizador pode ser um utilizador de base de dados contido do Azure Active Directory (se tiver configurado o ambiente para autenticação do Azure AD), um utilizador de base de dados contido de autenticação do SQL Server ou um utilizador de autenticação do SQL Server com base num início de sessão de autenticação do SQL Server (criado no passo anterior). Instruções de exemplo:
   
   ```
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
   CREATE USER Tran WITH PASSWORD = '<strong_password>';
   CREATE USER Mary FROM LOGIN Mary; 
   ```
4. Adicione o utilizador novo à função de base de dados **dbmanager** com a instrução [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx). Instruções de exemplo:
   
   ```
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > O dbmanager é uma função de base de dados na base de dados mestra virtual, pelo que só pode adicionar um utilizador à função dbmanager. Não pode adicionar um início de sessão ao nível do servidor à função de nível de base de dados.
   > 
   > 
5. Se necessário, configure a firewall ao nível do servidor para permitir que o novo utilizador se ligue.

Agora, o utilizador pode ligar à base de dados mestra virtual e criar bases de dados novas. A conta que cria a base de dados torna-se na proprietária da base de dados.

### <a name="login-managers"></a>Gestores de início de sessão
Se pretender, pode seguir os mesmos passos (criar um início de sessão e um utilizador e adicionar um utilizador à função **loginmanager**) para permitir que um utilizador possa criar inícios de sessão novos na base de dados mestra virtual. Normalmente, isto não é necessário, uma vez que a Microsoft recomenda utilizar utilizadores de base de dados contidos, o que autentica ao nível da base de dados em vez de utilizar os utilizadores com base em inícios de sessão. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Utilizadores não administradores
Geralmente, as contas de não administrador não precisam de acesso à base de dados mestra virtual. Utilize a instrução [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) para criar os utilizadores de base de dados contidos na base de dados ao nível da base de dados. O utilizador pode ser um utilizador de base de dados contido do Azure Active Directory (se tiver configurado o ambiente para autenticação do Azure AD), um utilizador de base de dados contido de autenticação do SQL Server ou um utilizador de autenticação do SQL Server com base num início de sessão de autenticação do SQL Server (criado no passo anterior). Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx). 

Para criar utilizadores, ligue à base de dados e execute instruções semelhantes aos exemplos seguintes:

```
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Inicialmente, apenas um dos administradores ou o proprietário da base de dados pode criar utilizadores. Para permitir que outros utilizadores criem utilizadores novos, conceda-lhes a permissão `ALTER ANY USER`, mediante uma instrução como:

```
GRANT ALTER ANY USER TO Mary;
```

Para conceder aos utilizadores adicionais controlo total da base de dados, torne-os membros da função de base de dados fixa **db_owner** com a instrução `ALTER ROLE`.

> [!NOTE]
> O principal motivo para criar utilizadores de base de dados com base em inícios de sessão é ter utilizadores de autenticação do SQL Server que precisam de aceder a várias bases de dados. Os utilizadores com base em inícios de sessão são associados ao início de sessão e é mantida apenas uma palavra-passe para esse início de sessão. Os utilizadores da base de dados contidos nas bases de dados individuais são entidades individuais e cada uma mantém a sua própria palavra-passe. Isto pode baralhar os utilizadores de base de dados contidos, se estes não mantiverem as palavras-passe idênticas.
> 
> 

### <a name="configuring-the-database-level-firewall"></a>Configurar a firewall ao nível da base de dados
Como melhor prática, os utilizadores não administradores só devem ter acesso às bases de dados que utilizam através da firewall. Em vez de autorizar os endereços IP deles através da firewall ao nível do servidor e conceder acesso a todas as bases de dados, utilize a instrução [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) para configurar a firewall ao nível da base de dados. Não é possível configurar a firewall ao nível da base de dados no portal.

### <a name="non-administrator-access-path"></a>Caminho de acesso para não administradores
Quando a firewall ao nível da base de dados está devidamente configurada, os utilizadores da base de dados podem ligar através de ferramentas de cliente, como o SQL Server Management Studio ou o SQL Server Data Tools. Só as ferramentas mais recentes proporcionam todas as funcionalidades e capacidades. O diagrama seguinte mostra um caminho típico de acesso para não administradores.
![Caminho de acesso para não administradores](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupos e funções
A gestão de acessos eficaz utiliza permissões atribuídas a grupos e funções em vez de utilizadores individuais. Por exemplo, se utilizar a autenticação do Azure Active Directory:

* Coloque os utilizadores do Azure Active Directory num grupo do Azure Active Directory. Crie um utilizador de base de dados contido para o grupo. Coloque um ou mais utilizadores de base de dados numa função de base de dados. E, em seguida, atribua permissões à função de base de dados.

Se utilizar a autenticação do SQL Server:

* Crie utilizadores de base de dados contidos na base de dados. Coloque um ou mais utilizadores de base de dados numa função de base de dados. E, em seguida, atribua permissões à função de base de dados.

As funções de base de dados podem ser as funções incorporadas, tais como **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** e **db_denydatareader**. **db_owner** é, geralmente, utilizada para conceder permissão total apenas a alguns utilizadores. As outras funções de base de dados fixas são úteis para colocar bases de dados simples em desenvolvimento rapidamente, mas não são recomendadas para a maioria das bases de dados de produção. Por exemplo, a função de base de dados fixa **db_datareader** concede acesso de leitura a todas as tabelas na base de dados, o que, regra geral, é mais do que o estritamente necessário. É muito melhor utilizar a instrução [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) para criar as suas próprias funções de base de dados definidas pelo utilizador e conceder, cuidadosamente, a cada função o menor número de permissões necessárias para a atividade em causa. Quando um utilizador é membro de várias funções, agrega as permissões de todas essas funções.

## <a name="permissions"></a>Permissões
Existem mais de 100 permissões que podem ser individualmente concedidas ou negadas na Base de Dados SQL. Muitas destas permissões são aninhadas. Por exemplo, a permissão `UPDATE` num esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Tal como na maioria dos sistemas de permissões, a recusa de uma permissão sobrepõe-se a uma atribuição. Devido à natureza aninhada e ao número de permissões, desenhar um sistema de permissões que proteja as bases de dados adequadamente pode envolver um cuidadoso estudo. Comece com a lista de permissões em [Permissions (Database Engine) (Permissões [Motor de Base de Dados])](https://msdn.microsoft.com/library/ms191291.aspx) e reveja o [gráfico em tamanho de cartaz](http://go.microsoft.com/fwlink/?LinkId=229142) das permissões.

## <a name="next-steps"></a>Passos seguintes
[Securing your SQL Database (Proteger a sua Base de Dados SQL)](sql-database-security.md)

[Creating a Table \(Tutorial\) (Criar uma Tabela [Tutorial])](https://msdn.microsoft.com/library/ms365315.aspx)

[Inserting and Updating Data in a Table \(Tutorial\) (Inserir e Atualizar Dados numa Tabela [Tutorial])](https://msdn.microsoft.com/library/ms365309.aspx)

[Reading the Data in a Table \(Tutorial\) (Ler os Dados numa Tabela [Tutorial])](https://msdn.microsoft.com/library/ms365310.aspx)

[Creating views and stored procedures (Criar vistas e procedimentos armazenados)](https://msdn.microsoft.com/library/ms365311.aspx)

[Granting Access to a Database Object (Conceder Acesso a um Objeto de Base de Dados)](https://msdn.microsoft.com/library/ms365327.aspx)

## <a name="additional-resources"></a>Recursos adicionais
[Securing your SQL Database (Proteger a sua Base de Dados SQL)](sql-database-security.md)

[Security Center for SQL Server Database Engine and Azure SQL Database (Centro de Segurança do Motor de Base de Dados do SQL Server e da Base de Dados SQL do Azure)](https://msdn.microsoft.com/library/bb510589.aspx) 




<!--HONumber=Dec16_HO3-->


