---
title: Proteger uma base de dados no SQL Data Warehouse | Microsoft Docs
description: "Sugestões para proteger uma base de dados no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 8fa2f5ca-4cf5-4418-99a2-4dc745799850
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: 6ea45c40bc428282faf24b4a08f8b0d345adb3fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger uma base de dados no armazém de dados do SQL Server
> [!div class="op_single_selector"]
> * [Descrição geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Este artigo explica as noções básicas de proteger a base de dados do Azure SQL Data Warehouse. Em particular, este artigo irá começar com recursos para limitar o acesso, proteger os dados e monitorização de atividades numa base de dados.

## <a name="connection-security"></a>Segurança da Ligação
A Segurança da Ligação diz respeito à forma como restringe e protege as ligações à sua base de dados através de regras de firewall e de encriptação da ligação.

As regras de firewall são utilizadas pelo servidor e pela base de dados para rejeitar as tentativas de ligação de endereços IP que não estejam explicitamente na lista de permissões. Para permitir ligações a partir da sua aplicação ou o endereço IP público do computador de cliente, tem primeiro de criar uma regra de firewall ao nível do servidor utilizando o Portal do Azure, a REST API ou o PowerShell. Como melhor prática, deve restringir o máximo possível os intervalos de endereços IP permitidos na firewall do servidor.  Para aceder ao Azure SQL Data Warehouse do seu computador local, certifique-se de que a firewall na sua rede e o computador local permite a comunicação de saída na porta TCP 1433.  Para obter mais informações, consulte [firewall da SQL Database do Azure][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule], e [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

As ligações ao seu armazém de dados do SQL Server estão encriptadas por predefinição.  Definições de ligação de modificação para desativar a encriptação são ignoradas.

## <a name="authentication"></a>Autenticação
A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. Atualmente, o armazém de dados do SQL Server suporta autenticação do SQL Server com um nome de utilizador e palavra-passe, bem como um Azure Active Directory. 

Quando criou o servidor lógico para a sua base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Utilizar estas credenciais, pode autenticar-se qualquer base de dados nesse servidor como o proprietário da base de dados, ou "dbo" através da autenticação do SQL Server.

No entanto, como melhor prática, os utilizadores da sua organização devem utilizar uma conta diferente para autenticar. Desta forma pode limitar as permissões concedidas à aplicação e reduzir os riscos de atividade maliciosa no caso de código da aplicação é vulnerável a um ataque de injeção de SQL. 

Para criar um utilizador autenticado do SQL Server, ligue para o **mestre** no seu servidor com início de sessão de administração do servidor de base de dados e criar um novo início de sessão do servidor.  Além disso, é boa ideia criar um utilizador na base de dados mestra para utilizadores do Azure SQL Data Warehouse. Criar um utilizador no mestre permite ao utilizador iniciar sessão utilizando ferramentas como SSMS sem especificar um nome de base de dados.  Também permite-lhe utilizar o Explorador de objeto para ver todas as bases de dados num SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, ligar a sua **base de dados do SQL Data Warehouse** com o início de sessão de administração do servidor e criar um utilizador de base de dados com base no início de sessão do servidor que acabou de criar.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Se um utilizador realizar operações adicionais, tais como criar inícios de sessão ou criar novas bases de dados, também terá de ser atribuídos ao `Loginmanager` e `dbmanager` funções na base de dados mestra. Para obter mais informações sobre estas funções adicionais e a autenticação numa base de dados do SQL Server, consulte [gerir bases de dados e inícios de sessão SQL Database do Azure][Managing databases and logins in Azure SQL Database].  Para obter mais detalhes sobre o Azure AD para o SQL Data Warehouse, consulte [ligar ao SQL dados do armazém por utilizar o Azure autenticação do Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorização
Autorização refere-se para o que pode fazer dentro de uma base de dados do Azure SQL Data Warehouse e isto é controlado pelas permissões e as associações de função da sua conta de utilizador. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. O Azure SQL Data Warehouse faz com que esta seja mais fácil gerir com as funções de T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação.

Existem formas de limitar ainda mais o que um utilizador pode fazer com a Base de Dados SQL do Azure:

* Granulares [permissões] [ Permissions] permitem-lhe controlo as operações que pode em colunas individuais, tabelas, vistas, procedimentos e outros objetos na base de dados. Utilize permissões granulares para ter a maioria dos controlo e conceder as permissões mínimas necessárias. O sistema de permissões granulares é um pouco complicado e irá exigir algumas prático utilizar de forma eficiente.
* [Funções de base de dados] [ Database roles] diferentes db_datareader e db_datawriter podem ser utilizados para criar contas de utilizador de aplicação mais potentes ou menos poderosas contas de gestão. As funções de base de dados fixa incorporadas fornecem uma forma fácil para conceder permissões, mas podem resultar em conceder mais permissões que são necessários.
* [Procedimentos armazenados] [ Stored procedures] pode ser utilizado para limitar as ações que podem ser executadas na base de dados.

A gestão de bases de dados e servidores lógicos no Portal Clássico do Azure ou com a API do Azure Resource Manager é controlada pelas atribuições de função da sua conta de utilizador do portal. Para obter mais informações sobre este tópico, consulte [controlo de acesso baseado em funções no Portal do Azure][Role-based access control in Azure Portal].

## <a name="encryption"></a>Encriptação
Do Azure SQL Data Warehouse transparente dados encriptação (TDE) ajuda a proteger contra a ameaça de atividade maliciosa efetuando em tempo real encriptação e desencriptação de dados inativos.  Ao encriptar a base de dados, sem necessidade de alterações às suas aplicações encriptados de cópias de segurança associadas e ficheiros de registo de transações. TDE encripta o armazenamento de uma base de dados completa através de uma chave simétrica chamada a chave de encriptação da base de dados. Base de dados SQL a chave de encriptação da base de dados está protegida por um certificado de servidor incorporada. O certificado de servidor incorporada é exclusivo para cada servidor de base de dados SQL. Microsoft roda automaticamente estes certificados, pelo menos, todos os 90 dias. O algoritmo de encriptação utilizado pelo SQL Data Warehouse é AES 256. Para obter uma descrição geral de TDE, consulte [encriptação transparente de dados][Transparent Data Encryption].

Pode encriptar a sua base de dados ao utilizar o [Portal do Azure] [ Encryption with Portal] ou [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Passos seguintes
Para obter detalhes e exemplos sobre a ligação ao SQL Data Warehouse com protocolos diferentes, consulte [ligar ao SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure Portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
