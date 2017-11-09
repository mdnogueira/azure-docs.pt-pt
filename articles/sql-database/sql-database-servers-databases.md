---
title: Criar e gerir servidores SQL do Azure e as bases de dados | Microsoft Docs
description: Saiba mais sobre conceitos de base de dados e de servidor da SQL Database do Azure e sobre como criar e gerir servidores e bases de dados.
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 469db4f3faf12cbd778f18b7bc74ec6b86b412c7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Criar e gerir servidores SQL Database do Azure e as bases de dados

Uma base de dados SQL do Azure é uma base de dados gerida no Microsoft Azure, que é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com um conjunto definido de [recursos de computação e armazenamento para cargas de trabalho diferentes](sql-database-service-tiers.md). Uma base de dados SQL do Azure está associado um servidor lógico da SQL Database do Azure, que é criado numa região do Azure específica. 

## <a name="an-azure-sql-database-can-be-a-single-pooled-or-partitioned-database"></a>Uma base de dados SQL do Azure pode ser uma base de dados único, agrupado ou particionada

Pode ser uma base de dados SQL do Azure:

- A [base de dados individual](sql-database-single-database-resources.md) com o seu próprio conjunto de recursos
- Parte de um [conjunto elástico](sql-database-elastic-pool.md) que partilham um conjunto de recursos
- Parte de um [conjunto aumentado horizontalmente de bases de dados partilhadas](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), que podem ser bases de dados individuais ou agrupadas
- Parte de um conjunto de bases de dados que participam num [padrão de estrutura de SaaS multi-inquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md), e cujas bases de dados podem ser individuais ou agrupadas (ou ambas) 

> [!TIP]
> Para nomes de bases de dados válidos, veja [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). 
>
 
- O agrupamento de base de dados predefinido utilizado pela Base de Dados SQL do Microsoft Azure é **SQL_LATIN1_GENERAL_CP1_CI_AS**, onde **LATIN1_GENERAL** é inglês (Estados Unidos), **CP1** é o código de página 1252, **CI** é sensível a maiúsculas e minúsculas e **AS** é sensível a acentos. Para obter mais informações sobre como definir o agrupamento, veja [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).
- Base de dados do Microsoft Azure SQL suporta a versão de cliente de protocolo do tabular data stream (recebido) 7.3 ou posterior.
- Ligações de TCP/IP só são permitidas.

## <a name="what-is-an-azure-sql-logical-server"></a>O que é um servidor lógico SQL do Azure?

Um servidor lógico age como um ponto de administração central para várias bases de dados, incluindo [conjuntos elásticos](sql-database-elastic-pool.md) [inícios de sessão](sql-database-manage-logins.md), [regras de firewall](sql-database-firewall-configure.md), [auditoria regras](sql-database-auditing.md), [as políticas de deteção da ameaça](sql-database-threat-detection.md), e [grupos de ativação pós-falha](sql-database-geo-replication-overview.md). Pode ser um servidor lógico numa região diferente ao respetivo grupo de recursos. O servidor lógico tem de existir antes de poder criar a base de dados SQL do Azure. Todas as bases de dados num servidor são criados na mesma região que o servidor lógico. 


> [!IMPORTANT]
> Na Base de Dados SQL, um servidor é uma construção lógica distinta de uma instância do SQL Server com a qual poderá estar familiarizado no mundo no local. Especificamente, o serviço Base de Dados SQL não dá qualquer garantia relativamente à localização das bases de dados em relação aos respetivos servidores lógicos, nem expõe qualquer acesso de nível de instância ou funcionalidades.
> 

Quando cria um servidor lógico, forneça um servidor de conta de início de sessão e palavra-passe que tenha direitos administrativos para a base de dados mestra nesse servidor e todas as bases de dados criadas nesse servidor. Esta conta inicial é uma conta de início de sessão do SQL Server. Base de dados SQL do Azure suporta a autenticação do SQL Server e a autenticação do Active Directory do Azure para a autenticação. Para obter informações sobre os inícios de sessão e autenticação, consulte [gerir bases de dados e inícios de sessão na base de dados do Azure SQL](sql-database-manage-logins.md). A autenticação do Windows não é suportada. 

> [!TIP]
> Para nomes de grupo e o servidor de recurso válido, consulte [regras e as restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).
>

Um servidor lógico da Base de Dados do Azure:

- É criado numa subscrição do Azure, mas pode ser movido com os recursos nele contidos para outra subscrição
- É o recurso principal para bases de dados, conjuntos elásticos e armazéns de dados
- Fornece um espaço de nomes para armazéns de dados, conjuntos elásticos e bases de dados
- É um contentor lógico com semântica de duração strong - eliminar um servidor e elimina as bases de dados contidas, conjuntos elásticos e armazéns de dados
- Em que participou no [controlo de acesso do Azure baseada em funções (RBAC)](/active-directory/role-based-access-control-what-is) -armazéns de dados dentro de um servidor, conjuntos elásticos e bases de dados herdarem direitos de acesso do servidor
- É um elemento de ordem de elevado de identidade dos armazéns de dados para o recurso do Azure, conjuntos elásticos e bases de dados do fins de gestão (consulte o esquema de URL para as bases de dados e agrupamentos)
- Coloca recursos numa região
- Fornece um ponto final de ligação para o acesso à base de dados (<serverName>.database.windows.net)
- Fornece acesso a metadados relativamente aos recursos contidos através de DMVs, estabelecendo ligação a uma base de dados mestra 
- Fornece o âmbito de políticas de gestão que aplicar a suas bases de dados - inícios de sessão, firewall, auditorias, ameaça deteção, etc. 
- É restringido por uma quota dentro da subscrição principal (seis servidores por subscrição por predefinição - [Consulte subscrição limita aqui](../azure-subscription-service-limits.md))
- Fornece o âmbito de quota da base de dados e da quota de DTU para os recursos que nele contidos (por exemplo, a 45,000 DTU)
- É o âmbito do controlo de versões de capacidades ativadas nos recursos nele contidos 
- Os inícios de sessão principais ao nível do servidor podem gerir todas as bases de dados num servidor
- Pode conter inícios de sessão semelhantes àqueles nas instâncias do SQL Server no local, aos quais é concedido acesso a uma ou mais bases de dados no servidor e aos quais podem ser concedidos direitos administrativos limitados. Para obter mais informações, veja [Inícios de sessão](sql-database-manage-logins.md).

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Bases de dados do SQL do Azure protegidos por firewall de base de dados SQL

Para ajudar a proteger os seus dados, uma [firewall de base de dados SQL](sql-database-firewall-configure.md) impede que todos os acessos ao seu servidor de base de dados ou qualquer uma das suas bases de dados de fora da sua ligação ao servidor diretamente através da ligação de subscrição do Azure. Para ativar a conetividade adicionais, terá [criar um ou mais regras de firewall](sql-database-firewall-configure.md#creating-and-managing-firewall-rules). Para criar e gerir conjuntos elásticos, consulte [conjuntos elásticos](sql-database-elastic-pool.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Gerir servidores SQL do Azure, bases de dados e as firewalls no portal do Azure

Pode criar o grupo de recursos da base de dados SQL do Azure antecedência ou ao criar o próprio servidor. Existem vários métodos para obter a um novo formulário de servidor do SQL Server, ao criar um novo SQL server ou como parte da criação de uma nova base de dados. 

### <a name="create-a-blank-sql-server-logical-server"></a>Criar um SQL server em branco (servidor lógico)

Para criar um servidor (sem uma base de dados) de SQL Database do Azure utilizando o [portal do Azure](https://portal.azure.com), navegue para um formulário de servidor (servidor lógico) do SQL Server em branco.  

### <a name="create-a-blank-or-sample-sql-database"></a>Criar uma base de dados do SQL Server em branco ou de exemplo

Para criar uma base de dados SQL do Azure utilizando o [portal do Azure](https://portal.azure.com), navegue para um formulário de base de dados do SQL Server em branco e forneça as informações pedidas. Pode criar o grupo de recursos e servidor lógico de antecedência ou ao criar a base de dados da base de dados SQL do Azure. Pode criar uma base de dados em branco ou criar uma base de dados de exemplo com base no Adventure Works LT. 

  ![criar base de dados-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Para informações sobre o escalão de preço da base de dados, consulte [escalões de serviço](sql-database-service-tiers.md).
>

### <a name="manage-an-existing-sql-server"></a>Gerir um servidor existente do SQL Server

Para gerir um servidor existente, navegue para o servidor utilizando um número de métodos -, tais como a página de base de dados SQL específica, o **servidores SQL** página, ou o **todos os recursos** página. 

Para gerir uma base de dados existente, navegue para o **bases de dados SQL** página e clique na base de dados que pretende gerir. Captura de ecrã seguinte mostra como começar a definição de uma firewall ao nível do servidor de bases de dados do **descrição geral** página para uma base de dados. 

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Para configurar propriedades de desempenho para uma base de dados, consulte [escalões de serviço](sql-database-service-tiers.md).
>

> [!TIP]
> Para um tutorial de início rápido de portal do Azure, consulte [criar uma base de dados SQL do Azure no portal do Azure](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Gerir servidores SQL do Azure, as bases de dados e firewalls com o PowerShell

Para criar e gerir o servidor SQL do Azure, as bases de dados e firewalls com o Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se precisar de instalar ou atualizar o PowerShell, consulte [módulo Azure PowerShell instalar](/powershell/azure/install-azurerm-ps). Para criar e gerir conjuntos elásticos, consulte [conjuntos elásticos](sql-database-elastic-pool.md).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Cria uma base de dados |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtém um ou mais bases de dados|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Define as propriedades para uma base de dados ou move de uma base de dados existente para um conjunto elástico|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Remove uma base de dados|
|[Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Cria um grupo de recursos]
|[Novo AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Cria um servidor|
|[Get AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Devolve informações sobre servidores|
|[Conjunto AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Modifica as propriedades de um servidor|
|[Remover AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Remove um servidor|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Cria uma regra de firewall ao nível do servidor |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Obtém as regras de firewall para um servidor|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Modifica uma regra de firewall num servidor|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Elimina uma regra de firewall de um servidor.|
| Novo AzureRmSqlServerVirtualNetworkRule | Cria um [ *regra de rede virtual*](sql-database-vnet-service-endpoint-rule-overview.md), com base numa sub-rede que é um ponto final de serviço de rede Virtual. |

> [!TIP]
> Para um tutorial de início rápido do PowerShell, consulte [criar uma base de dados SQL do Azure único através do PowerShell](sql-database-get-started-portal.md). Para scripts de exemplo do PowerShell, consulte [utilize o PowerShell para criar uma base de dados SQL do Azure e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-powershell.md) e [monitorizar e dimensionar um único SQL da base de dados com o PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Gerir servidores SQL do Azure, as bases de dados e firewalls com a CLI do Azure

Para criar e gerir o servidor SQL do Azure, as bases de dados e firewalls com a [CLI do Azure](/cli/azure/overview), utilize o seguinte [SQL Database do Azure CLI](/cli/azure/sql/db) comandos. Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerir conjuntos elásticos, consulte [conjuntos elásticos](sql-database-elastic-pool.md).

| Cmdlet | Descrição |
| --- | --- |
|[Criar BD do sql AZ](/cli/azure/sql/db#az_sql_db_create) |Cria uma base de dados|
|[lista de BD do SQL Server AZ](/cli/azure/sql/db#az_sql_db_list)|Apresenta uma lista de todas as bases de dados e os armazéns de dados num servidor, ou todas as bases de dados num agrupamento elástico|
|[base de dados de sql AZ lista-edições](/cli/azure/sql/db#az_sql_db_list_editions)|Apresenta uma lista de serviços disponíveis objetivos e limites de armazenamento|
|[base de dados de sql AZ lista-utilizações](/cli/azure/sql/db#az_sql_db_list_usages)|Devolve as utilizações de base de dados|
|[Mostrar de BD do SQL Server AZ](/cli/azure/sql/db#az_sql_db_show)|Obtém um armazém de dados ou base de dados|
|[atualização de base de dados do sql AZ](/cli/azure/sql/db#az_sql_db_update)|Atualizações de uma base de dados|
|[eliminação de BD do SQL Server AZ](/cli/azure/sql/db#az_sql_db_delete)|Remove uma base de dados|
|[Criar grupo AZ](/cli/azure/group#az_group_create)|Cria um grupo de recursos|
|[servidor de sql AZ criar](/cli/azure/sql/server#az_sql_server_create)|Cria um servidor|
|[lista de servidores sql AZ](/cli/azure/sql/server#az_sql_server_list)|Apresenta uma lista de servidores|
|[servidor de sql AZ lista-utilizações](/cli/azure/sql/server#az_sql_server_list-usages)|Devolve as utilizações de servidor|
|[Mostrar do AZ sql server](/cli/azure/sql/server#az_sql_server_show)|Obtém um servidor|
|[atualização de servidor de sql AZ](/cli/azure/sql/server#az_sql_server_update)|Um servidor de atualizações|
|[eliminação do AZ sql server](/cli/azure/sql/server#az_sql_server_delete)|Elimina um servidor|
|[Criar AZ sql server-regra de firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Cria uma regra de firewall do servidor|
|[lista de regras de firewall de servidor de sql de AZ](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Apresenta as regras de firewall num servidor|
|[Mostrar de regra de firewall AZ sql server](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Mostra os detalhes de uma regra de firewall|
|[atualização de regra de firewall AZ sql server](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Uma regra de firewall de atualizações|
|[Eliminar regra de firewall do AZ sql server](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Elimina uma regra de firewall|

> [!TIP]
> Para um tutorial de início rápido da CLI do Azure, consulte [criar uma base de dados SQL do Azure único utilizando a CLI do Azure](sql-database-get-started-cli.md). Para scripts de exemplo da CLI do Azure, consulte [CLI de utilização para criar uma base de dados SQL do Azure e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-cli.md) e [CLI de utilização para monitorizar e dimensionar uma base de dados do SQL Server único](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Gerir servidores SQL do Azure, as bases de dados e firewalls com Transact-SQL

Para criar e gerir o servidor SQL do Azure, as bases de dados e firewalls com Transact-SQL, utilize os seguintes comandos de T-SQL. Pode emitir estes comandos no portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), ou qualquer outro programa que pode ligar a um servidor da SQL Database do Azure e passar comandos Transact-SQL. Para gerir conjuntos elásticos, consulte [conjuntos elásticos](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Não é possível criar ou eliminar um servidor com o Transact-SQL.
>

| Comando | Descrição |
| --- | --- |
|[Criar base de dados (SQL Database do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria uma nova base de dados. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [Falha de ALTER DATABASE (base de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica uma base de dados SQL do Azure. |
|[Alterar base de dados (armazém de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modifica um armazém de dados SQL do Azure.|
|[REMOVER a base de dados (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Elimina uma base de dados.|
|[sys.database_service_objectives (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devolve o edition (camada de serviço), o objetivo de serviço (escalão de preço) e o nome do conjunto elástico, se existir, para uma base de dados SQL do Azure ou um Azure SQL Data Warehouse. Se tem sessão iniciada base de dados mestra num servidor de base de dados do Azure SQL, devolve informações sobre todas as bases de dados. Para o Azure SQL Data Warehouse, tem de estar ligado à base de dados mestra.|
|[sys.dm db_resource_stats (SQL Database do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Devolve o consumo de CPU, memória e e/s, para uma base de dados do SQL Database do Azure. Não existe uma linha para cada 15 segundos, mesmo se não houver nenhuma atividade na base de dados.|
|[resource_stats (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Devolve os dados de armazenamento e utilização de CPU para uma base de dados do SQL do Azure. Os dados são recolhidos e agregados em intervalos de cinco minutos.|
|[sys.database_connection_stats (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contém as estatísticas de eventos de conectividade da base de dados de base de dados do SQL Server, que fornece uma descrição geral da base de dados ligação sucessos e falhas. |
|[sys.event_log (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Devolve as ligações de base de dados de SQL Database do Azure com êxito, falhas de ligação e impasses. Pode utilizar estas informações para controlar ou resolver problemas relacionados com a atividade de base de dados com base de dados do SQL Server.|
|[sp_set_firewall_rule (SQL Database do Azure)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Cria ou atualiza as definições de firewall ao nível do servidor para o servidor de base de dados SQL. Este procedimento armazenado só está disponível na base de dados mestra para o início de sessão principal ao nível do servidor. Só é possível criar uma regra de firewall ao nível do servidor com o Transact-SQL após a primeira regra de firewall ao nível do servidor foi criada por um utilizador com permissões ao nível do Azure|
|[sys.firewall_rules (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Devolve informações sobre as definições de firewall ao nível do servidor associado à sua base de dados de SQL do Microsoft Azure.|
|[sp_delete_firewall_rule (SQL Database do Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Remove as definições de firewall ao nível do servidor do seu servidor de base de dados SQL. Este procedimento armazenado só está disponível na base de dados mestra para o início de sessão principal ao nível do servidor.|
|[sp_set_database_firewall_rule (SQL Database do Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Cria ou atualiza as regras de firewall ao nível da base de dados para o seu SQL Database do Azure ou SQL Data Warehouse. Regras de firewall de base de dados podem ser configuradas para a base de dados mestra e para as bases de dados de utilizador na base de dados do SQL Server. Regras de firewall de base de dados são úteis quando utilizar continha os utilizadores de base de dados. |
|[sys.database_firewall_rules (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Devolve informações sobre as definições de firewall ao nível da base de dados associado à sua base de dados de SQL do Microsoft Azure. |
|[sp_delete_database_firewall_rule (SQL Database do Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Remove a definição de firewall ao nível da base de dados do seu SQL Database do Azure ou SQL Data Warehouse. |


> [!TIP]
> Tutorial de início rápido, utilizando o SQL Server Management Studio no Microsoft Windows, consulte [SQL Database do Azure: Utilize o SQL Server Management Studio para ligar e consultar dados](sql-database-connect-query-ssms.md). Para um tutorial de início rápido, utilizando o Visual Studio Code no macOS, Linux ou Windows, consulte [SQL Database do Azure: utilize Visual Studio Code para ligar e consultar dados](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Gerir servidores SQL do Azure, bases de dados e as firewalls utilizando a API REST

Para criar e gerir o Azure SQL server, bases de dados e as firewalls utilizam estes pedidos de REST API.

| Comando | Descrição |
| --- | --- |
|[Servidores – criar ou atualizar](/rest/api/sql/servers/createorupdate)|Cria ou atualiza um novo servidor.|
|[Servidores - eliminar](/rest/api/sql/servers/delete)|Elimina um SQL server.|
|[Servidores - Get](/rest/api/sql/servers/get)|Obtém um servidor.|
|[Servidores - lista](/rest/api/sql/servers/list)|Devolve uma lista de servidores.|
|[Servidores - lista por grupo de recursos](/rest/api/sql/servers/listbyresourcegroup)|Devolve uma lista de servidores num grupo de recursos.|
|[Servidores - atualização](/rest/api/sql/servers/update)|As atualizações de um servidor existente.|
|[Servidores - Sql](/rest/api/sql/servers%20-%20sql)|Determina se um recurso pode ser criado com o nome especificado.|
|[Bases de dados - criar ou atualizar](/rest/api/sql/databases/createorupdate)|Cria uma nova base de dados ou atualiza uma base de dados existente.|
|[Get - bases de dados](/rest/api/sql/databases/get)|Obtém uma base de dados.|
|[Para obter agrupamento elástico de bases de dados-](/rest/api/sql/databases/getbyelasticpool)|Obtém uma base de dados dentro de um conjunto elástico.|
|[Para obter as bases de dados - recomendado conjunto elástico](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtém uma base de dados dentro de um conjunto elástico recommented.|
|[Bases de dados - lista por agrupamento elástico](/rest/api/sql/databases/listbyelasticpool)|Devolve uma lista de bases de dados num agrupamento elástico.|
|[Bases de dados - lista por conjunto elástico recomendado](/rest/api/sql/databases/listbyrecommendedelasticpool)|Devolve uma lista de bases de dados dentro de um conjunto elástico recommented.|
|[Bases de dados - lista pelo servidor](/rest/api/sql/databases/listbyserver)|Devolve uma lista de bases de dados num servidor.|
|[Bases de dados - atualização](/rest/api/sql/databases/update)|Atualiza a base de dados existente.|
|[Firewall regras - criar ou atualizar](/rest/api/sql/firewallrules/createorupdate)|Cria ou atualiza uma regra de firewall.|
|[Regras de firewall - eliminar](/rest/api/sql/firewallrules/delete)|Elimina uma regra de firewall.|
|[Regras de firewall - Get](/rest/api/sql/firewallrules/get)|Obtém uma regra de firewall.|
|[Regras de firewall - lista pelo servidor](/rest/api/sql/firewallrules/listbyserver)|Devolve uma lista de regras de firewall.|

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o agrupamento de bases de dados utilizando conjuntos elásticos, consulte [conjuntos elásticos](sql-database-elastic-pool.md).
- Para obter informações sobre o serviço SQL Database do Azure, consulte [o que é a base de dados SQL?](sql-database-technical-overview.md).
- Para saber mais sobre como migrar uma base de dados do SQL Server para o Azure, consulte [migrar para a SQL Database do Azure](sql-database-cloud-migrate.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md).
