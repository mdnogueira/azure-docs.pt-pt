---
title: "Descrição geral das regras de firewall da Base de Dados SQL | Microsoft Docs"
description: "Saiba como configurar uma firewall de base de dados SQL com regras de firewall ao nível do servidor e da base de dados para gerir o acesso."
keywords: firewall de base de dados
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/23/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2f24c1c43300a3c5035fbb4e90c3d13f73c1bc74
ms.openlocfilehash: a0c4cfb127934f86a7ce272c1aec2fd01331e17c


---
# <a name="overview-of-azure-sql-database-firewall-rules"></a>Descrição geral das regras de firewall da Base de Dados SQL do Azure 
> [!div class="op_single_selector"]
> * [Descrição geral](sql-database-firewall-configure.md)
> * [Portal do Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

A Base de Dados SQL do Microsoft Azure disponibiliza um serviço de bases de dados relacionais para o Azure e outras aplicações baseadas na Internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor e da base de dados.

* **Regras de firewall ao nível do servidor:** estas regras permitem que os clientes acedam a todo o seu servidor do SQL Azure, ou seja, todas as bases de dados dentro do mesmo servidor lógico. Estas regras são armazenadas na base de dados **mestra**. A configuração das regras de firewall ao nível do servidor pode ser feita no portal ou através de instruções de Transact-SQL. Para criar regras de firewall ao nível do servidor com o portal do Azure ou com o PowerShell, tem de ser o proprietário da subscrição ou um contribuinte da subscrição. Para criar uma regra de firewall ao nível do servidor através de Transact-SQL, deve ligar à instância da Base de dados SQL como o início de sessão principal ao nível do servidor ou como administrador do Azure Active Directory (o que significa que uma regra de firewall ao nível do servidor tem primeiro de ser criada por um utilizador com permissões ao nível do Azure).
* **Regras de firewall ao nível da base de dados:** estas regras permitem que os clientes acedam a bases de dados individuais no seu servidor da Base de Dados SQL do Azure. Pode criar estas regras para cada base de dados, as quais são armazenadas nas bases de dados individuais. (Pode criar regras de firewall ao nível da base de dados para a base de dados **mestra**.) Estas regras podem ser úteis para restringir o acesso a determinadas bases de dados (seguras) no mesmo servidor lógico. As regras de firewall ao nível da base de dados só podem ser configuradas com instruções Transact-SQL.

   > [!NOTE]
   > Para obter um tutorial que demonstre a utilização de firewalls ao nível da base de dados, veja [SQL Database tutorial: SQL Server authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules (Tutorial da Base de Dados SQL: autenticação do SQL Server, inícios de sessão e contas de utilizador, funções de base de dados, permissões, regras de firewall ao nível do servidor e regras de firewall ao nível da base de dados)](sql-database-control-access-sql-authentication-get-started.md).
   >

**Recomendação:** a Microsoft recomenda a utilização de regras de firewall ao nível da base de dados sempre que possível, para melhorar a segurança e tornar a sua base de dados mais portátil. Utilize as regras de firewall ao nível do servidor para os administradores e se tiver muitas bases de dados com os mesmos requisitos de acesso e não quer perder tempo a configurar individualmente cada uma.

> [!Note]
> Para obter informações sobre bases de dados portáteis no contexto de continuidade do negócio, veja [Authentication requirements for disa ster recovery (Requisitos de autenticação para a recuperação após desastre)](sql-database-geo-replication-security-config.md).
>

## <a name="firewall-overview"></a>Descrição geral das firewalls
Inicialmente, todos os acessos Transact-SQL ao servidor SQL Azure são bloqueados pela firewall. Para começar a utilizar o servidor do SQL Azure, tem de aceder ao portal do Azure e especificar uma ou mais regras de firewall ao nível do servidor que permitam o acesso àquele. Utilize as regras de firewall para especificar quais os intervalos de endereços IP da Internet permitidos e se as aplicações do Azure podem ou não tentar ligar ao servidor do SQL Azure.

Para conceder acesso seletivamente a apenas uma das bases de dados no seu servidor do SQL Azure, tem de criar uma regra ao nível da base de dados para essa base de dados. Especifique um intervalo de endereços IP para a regra de firewall da base de dados que esteja para lá do intervalo de endereços IP especificado na regra de firewall ao nível do servidor e confirme que o endereço IP do cliente está compreendido no intervalo especificado na regra ao nível da base de dados.

As tentativas de ligação a partir da Internet e do Azure têm, primeiro, de passar pela firewall antes de chegarem ao servidor do SQL Azure ou à base de dados SQL, conforme mostrado no diagrama seguinte:

   ![Diagrama com descrição da configuração da firewall.][1]

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
Quando um computador tenta ligar ao servidor da base de dados a partir da Internet, a firewall começa por verificar o endereço IP de origem do pedido em relação ao conjunto completo de regras de firewall:

* Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, é concedida ligação ao servidor da Base de Dados SQL do Azure.
* Se o endereço IP do pedido não estiver dentro de um dos intervalos especificados na regra de firewall ao nível do servidor, são verificadas as regras de firewall ao nível da base de dados. Se o endereço IP do pedido estiver dentro de um dos intervalos especificados nas regras de firewall ao nível da base de dados, é concedida ligação apenas à base de dados que tem uma regra ao nível de base de dados correspondente.
* Se o endereço IP do pedido não estiver dentro dos intervalos especificados em qualquer uma das regras ao nível do servidor ou da base de dados, o pedido de ligação falha.

> [!NOTE]
> Para aceder à Base de Dados SQL do Azure a partir do seu computador local, confirme que a firewall na sua rede e no seu computador local permite comunicação de saída na porta TCP 1433.
> 

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que as aplicações do Azure se liguem ao servidor do SQL Azure, as ligações do Azure têm de estar ativadas. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Se a tentativa de ligação não for permitida, o pedido não chega ao servidor da Base de Dados SQL do Azure.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

> [!NOTE]
>  Para obter mais informações, veja a secção **SQL Database: Outside vs inside (Base de Dados SQL: exterior vs interior)** de [Ports beyond 1433 for ADO.NET 4.5 and SQL Database (Portas além da 1433 para ADO.NET 4.5 e Base de Dados SQL)](sql-database-develop-direct-route-ports-adonet-v12.md)
>  

## <a name="creating-the-first-server-level-firewall-rule"></a>Criar a primeira regra de firewall ao nível do servidor
A primeira definição de firewall ao nível do servidor pode ser criada através do [portal do Azure](https://portal.azure.com/) ou programaticamente com a API REST ou o Azure PowerShell. As regras seguintes podem ser criadas e geridas com estes métodos e com o Transact-SQL. Para melhorar o desempenho, as regras de firewall ao nível do servidor são colocadas em cache temporariamente ao nível da base de dados. Para atualizar a cache, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Para obter mais informações sobre regras de firewall ao nível do servidor, veja [How to: Configure an Azure SQL server firewall using the Azure portal (Como: Configurar uma firewall ao nível do servidor do SQL Azure com o portal do Azure)](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Criar regras de firewall ao nível da base de dados
Depois de configurar a primeira firewall ao nível do servidor, poderá ser útil restringir o acesso a determinadas bases de dados. Se especificar um intervalo de endereços IP na regra de firewall ao nível da base de dados que esteja fora do intervalo especificado na regra de firewall ao nível do servidor, só os clientes que tiverem endereços IP no intervalo ao nível da base de dados podem aceder à base de dados. Pode ter um máximo de 128 regras de firewall ao nível da base de dados para uma base de dados. As regras de firewall ao nível da base de dados para bases de dados mestras e de utilizador podem ser criadas e geridas através do Transact-SQL. Para obter mais informações sobre como configurar as regras de firewall ao nível da base de dados, veja [sp_set_database_firewall_rule (Azure SQL Databases) (Bases de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Para além do portal do Azure, as regras de firewall podem ser geridas programaticamente com Transact-SQL, a API REST e o Azure PowerShell. As tabelas seguintes descrevem o conjunto de comandos disponíveis para cada método.

### <a name="transact-sql"></a>Transact-SQL
| Vista de Catálogo ou Procedimento Armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Servidor |Apresenta as regras de firewall ao nível do servidor atuais |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Servidor |Cria ou atualiza as regras de firewall ao nível do servidor |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Servidor |Remove as regras de firewall ao nível do servidor |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Base de Dados |Apresenta as regras de firewall ao nível da base de dados atuais |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Base de Dados |Cria ou atualiza as regras de firewall ao nível da base de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bases de Dados |Remove as regras de firewall ao nível da base de dados |

### <a name="rest-api"></a>API REST
| API | Nível | Descrição |
| --- | --- | --- |
| [Listar Regras de Firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx) |Servidor |Apresenta as regras de firewall ao nível do servidor atuais |
| [Criar Regra de Firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) |Servidor |Cria ou atualiza as regras de firewall ao nível do servidor |
| [Definir Regra de Firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx) |Servidor |Atualiza as propriedades de uma regra de firewall ao nível do servidor existente |
| [Eliminar Regra de Firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) |Servidor |Remove as regras de firewall ao nível do servidor |

### <a name="azure-powershell"></a>Azure PowerShell
| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) |Servidor |Devolve as regras de firewall ao nível do servidor atuais |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) |Servidor |Cria uma regra de firewall ao nível do servidor nova |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) |Servidor |Atualiza as propriedades de uma regra de firewall ao nível do servidor existente |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) |Servidor |Remove as regras de firewall ao nível do servidor |

> [!NOTE]
> As alterações às definições da firewall podem demorar até cinco minutos a serem aplicadas.
> 
> 

## <a name="troubleshooting-the-database-firewall"></a>Resolver problemas da firewall de base de dados
Se o comportamento do acesso ao serviço Base de Dados SQL do Microsoft Azure não for o esperado, considere o seguinte:

* **Configuração da firewall local:** antes de o computador poder aceder à Base de Dados SQL do Azure, poderá ter de criar uma exceção de firewall no computador para a porta TCP 1433. Se estiver a fazer ligações dentro do limite da cloud do Azure, poderá ter de abrir portas adicionais. Para obter mais informações, veja a secção **V12 of SQL Database: Outside vs inside (V12 da Base de Dados SQL: exterior vs interior)** de [Ports beyond 1433 for ADO.NET 4.5 and SQL Database V12 (Portas além de 1433 para ADO.NET 4.5 e Base de Dados SQL V12)](sql-database-develop-direct-route-ports-adonet-v12.md).
* **Tradução de endereços de rede (NAT):** devido a NAT, o endereço IP utilizado pelo seu computador para ligar à Base de Dados SQL do Azure poderá ser diferente do endereço IP mostrado nas definições de configuração de IP do computador. Para ver o endereço IP que o computador está a utilizar para ligar ao Azure, inicie sessão no portal e navegue para o separador **Configurar** no servidor que aloja a base de dados. Na secção **Endereços IP permitidos**, é apresentado o **Endereço IP de Cliente Atual**. Clique em **Adicionar** aos **endereços IP Permitidos** para permitir que este computador aceda ao servidor.
* **As alterações à lista de permissões ainda não foram aplicadas:** as alterações à configuração da firewall da Base de Dados SQL do Azure poderão demorar até cinco minutos a serem aplicadas.
* **O início de sessão não está autorizado ou foi utilizada uma palavra-passe incorreta:** se um início de sessão não tiver permissões no servidor da Base de Dados SQL do Azure ou a palavra-passe utilizada estiver incorreta, a ligação ao servidor da Base de Dados SQL do Azure é negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias. Para obter mais informações sobre a preparação dos inícios de sessão, veja Managing Databases, Logins, and Users in Azure SQL Database (Gerir Bases de Dados, Inícios de Sessão e Utilizadores na Base de Dados SQL do Azure).
* **Endereço IP dinâmico:** se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das soluções seguintes.
  
  * Peça ao seu Fornecedor de Serviços Internet (ISP) o intervalo de endereços IP que foi atribuído aos seus computadores clientes que acedem ao servidor da Base de Dados SQL do Azure e, depois, adicione esse intervalo como regra de firewall.
  * Em alternativa, obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP como regras de firewall.

## <a name="next-steps"></a>Passos seguintes
Para obter artigos explicativos sobre como criar regras de firewall ao nível do servidor e da base de dados, veja:

* [Configure Azure SQL Database server-level firewall rules using the Azure portal (Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com o portal do Azure)](sql-database-configure-firewall-settings.md)
* [Configure Azure SQL Database server-level and database-level firewall rules using T-SQL (Configurar regras de firewall ao nível do servidor e da base de dados da Base de Dados SQL do Azure com o T-SQL)](sql-database-configure-firewall-settings-tsql.md)
* [Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com o PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configure Azure SQL Database server-level firewall rules using the REST API (Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com a API REST)](sql-database-configure-firewall-settings-rest.md)

Para ver um tutorial sobre como criar uma base de dados, consulte [Create a SQL database in minutes using the Azure portal (Criar uma Base de Dados SQL em poucos minutos com o Portal do Azure)](sql-database-get-started.md).
Para obter ajuda para ligar a uma base de dados SQL do Azure a partir de aplicações de código aberto ou de terceiros, veja [Client quick-start code samples to SQL Database (Exemplos de código de início rápido de cliente para a Base de Dados SQL)](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para as bases de dados, veja [Manage database access and login security (Gerir acesso da base de dados e segurança do início de sessão)](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionais
* [Proteger a sua base de dados](sql-database-security-overview.md)
* [Centro de Segurança do Motor da Base de Dados do SQL Server e da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png



<!--HONumber=Jan17_HO3-->


