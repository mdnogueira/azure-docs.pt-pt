---
title: "Base de dados de única base de dados SQL do Azure | Microsoft Docs"
description: "Gerir a camada de serviço, o nível de desempenho e a quantidade de storagea para uma base de dados SQL do Azure."
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
ms.openlocfilehash: f2dca5ac40dff077f9e5ce983b15fcb5b2624a14
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Gerir os recursos de uma base de dados na SQL Database do Azure

Com uma base de dados individual, para determinar a quantidade de recursos que requer que a base de dados para processar a carga de trabalho, a camada de serviço, o nível de desempenho e a quantidade de armazenamento requer. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Gerir recursos de base de dados individual no portal do Azure

Para definir ou alterar o escalão de serviço, o nível de desempenho ou a quantidade de armazenamento para uma nova ou existente SQL database do Azure no portal do Azure, abra o **Configurar desempenho** janela para a base de dados clicando **(escalão de preço Dimensionar DTUs)** - conforme mostrado na captura de ecrã seguinte. 

- Definir ou alterar a camada de serviço, selecionando a camada de serviço para a carga de trabalho. 
- Definir ou alterar o nível de desempenho (**DTUs**) dentro de uma camada de serviço utilizando o **DTU** controlo de deslize.
- Definir ou alterar a quantidade de armazenamento para a utilização do nível de desempenho a **armazenamento** controlo de deslize. 

![Configurar o nível de desempenho e o escalão de serviço](./media/sql-database-single-database-resources/change-service-tier.png)

> [!IMPORTANT]
> Reveja [limitações atuais de P11 e P15 bases de dados com o tamanho máximo de 4 TB](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) ao selecionar uma camada de serviço P11 ou P15.
>

## <a name="manage-single-database-resources-using-powershell"></a>Gerir recursos de base de dados único com o PowerShell

Para definir ou alterar camadas de serviços de bases de dados SQL do Azure, níveis de desempenho e a quantidade de armazenamento com o PowerShell, utilize estes cmdlets do PowerShell. Se precisar de instalar ou atualizar o PowerShell, consulte [módulo Azure PowerShell instalar](/powershell/azure/install-azurerm-ps). 

| Cmdlet | Descrição |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Cria uma base de dados |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtém um ou mais bases de dados|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Define as propriedades para uma base de dados ou move de uma base de dados existente para um conjunto elástico. Por exemplo, utilizar o **MaxSizeBytes** propriedade para definir o tamanho máximo para uma base de dados.|


> [!TIP]
> Para um script de exemplo do PowerShell que monitoriza as métricas de desempenho de uma base de dados, dimensiona-lo para um nível de desempenho superior e cria uma regra de alerta das métricas de desempenho, consulte [monitorizar e dimensionar um único SQL da base de dados com o PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Gerir recursos de base de dados individual com a CLI do Azure

Definir ou alterar as bases de dados SQL do Azure escalões de serviço, níveis de desempenho e a quantidade de armazenamento utilizando a CLI do Azure, utilizá-las [SQL Database do Azure CLI](/cli/azure/sql/db) comandos. Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerir conjuntos elásticos SQL, consulte [conjuntos elásticos](sql-database-elastic-pool.md).

| Cmdlet | Descrição |
| --- | --- |
|[Criar AZ sql server-regra de firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Cria uma regra de firewall do servidor|
|[lista de regras de firewall de servidor de sql de AZ](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Apresenta as regras de firewall num servidor|
|[Mostrar de regra de firewall AZ sql server](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Mostra os detalhes de uma regra de firewall|
|[atualização de regra de firewall AZ sql server](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Uma regra de firewall de atualizações|
|[Eliminar regra de firewall do AZ sql server](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Elimina uma regra de firewall|


> [!TIP]
> Para obter um script de exemplo do CLI do Azure que dimensiona de uma única base de dados do SQL do Azure para um nível de desempenho diferente depois de consultar as informações de tamanho da base de dados, consulte [CLI de utilização para monitorizar e dimensionar uma base de dados do SQL Server único](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Gerir recursos de base de dados único com o Transact-SQL

Para definir ou alterar camadas de serviços de bases de dados SQL do Azure, níveis de desempenho e a quantidade de armazenamento com o Transact-SQL, utilize estes comandos de T-SQL. Pode emitir estes comandos no portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), ou qualquer outro programa que pode ligar a um servidor da SQL Database do Azure e passar comandos Transact-SQL. 

| Comando | Descrição |
| --- | --- |
|[Criar base de dados (SQL Database do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria uma nova base de dados. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [Falha de ALTER DATABASE (base de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica uma base de dados SQL do Azure. |
|[sys.database_service_objectives (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devolve o edition (camada de serviço), o objetivo de serviço (escalão de preço) e o nome do conjunto elástico, se existir, para uma base de dados SQL do Azure ou um Azure SQL Data Warehouse. Se tem sessão iniciada base de dados mestra num servidor de base de dados do Azure SQL, devolve informações sobre todas as bases de dados. Para o Azure SQL Data Warehouse, tem de estar ligado à base de dados mestra.|
|[sys.database_usage (SQL Database do Azure)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Apresenta o número, o tipo e a duração das bases de dados num servidor SQL Database do Azure.|

O exemplo seguinte mostra o tamanho máximo para uma base de dados que está a ser alterado utilizando o comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Gerir recursos de base de dados individual, utilizando a API REST

Para definir ou alterar as bases de dados SQL do Azure escalões de serviço, a quantidade de armazenamento e níveis de desempenho, utilizam estes pedidos de REST API.

| Comando | Descrição |
| --- | --- |
|[Bases de dados - criar ou atualizar](/rest/api/sql/databases/createorupdate)|Cria uma nova base de dados ou atualiza uma base de dados existente.|
|[Get - bases de dados](/rest/api/sql/databases/get)|Obtém uma base de dados.|
|[Para obter agrupamento elástico de bases de dados-](/rest/api/sql/databases/getbyelasticpool)|Obtém uma base de dados dentro de um conjunto elástico.|
|[Para obter as bases de dados - recomendado conjunto elástico](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtém uma base de dados dentro de um conjunto elástico recommented.|
|[Bases de dados - lista por agrupamento elástico](/rest/api/sql/databases/listbyelasticpool)|Devolve uma lista de bases de dados num agrupamento elástico.|
|[Bases de dados - lista por conjunto elástico recomendado](/rest/api/sql/databases/listbyrecommendedelasticpool)|Devolve uma lista de bases de dados dentro de um conjunto elástico recommented.|
|[Bases de dados - lista pelo servidor](/rest/api/sql/databases/listbyserver)|Devolve uma lista de bases de dados num servidor.|
|[Bases de dados - atualização](/rest/api/sql/databases/update)|Atualiza a base de dados existente.|



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os escalões de serviço, níveis de desempenho e quantidades de armazenamento, consulte [escalões de serviço](sql-database-service-tiers.md).
- Saiba mais sobre conjuntos elásticos, consulte [conjuntos elásticos](sql-database-elastic-pool.md).
- Saiba mais sobre [subscrição do Azure e limites de serviço, Quotas e restrições](../azure-subscription-service-limits.md)
