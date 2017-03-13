---
title: Criar um SQL Data Warehouse com TSQL | Microsoft Docs
description: Saiba como criar um Azure SQL Data Warehouse com TSQL
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 836d72e32e54ecef9691b55214766a1fc3ff9701
ms.lasthandoff: 12/06/2016


---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Criar uma base de dados SQL Data Warehouse, utilizando Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Este artigo mostra-lhe como criar um SQL Data Warehouse com T-SQL.

## <a name="prerequisites"></a>Pré-requisitos
Para começar, precisa do seguinte:

* **Conta Azure**: aceda a [Versão de Avaliação Gratuita do Azure][Azure Free Trial] ou [Créditos do MSDN Azure][MSDN Azure Credits] para criar uma conta.
* **Azure SQL Server**: consulte [Create an Azure SQL Database logical server with the Azure Portal (Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure)][Create an Azure SQL Database logical server with the Azure Portal] ou [Create an Azure SQL Database logical server with PowerShell (Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell)][Create an Azure SQL Database logical server with PowerShell] para obter mais detalhes.
* **Grupo de recursos**: utilize o mesmo grupo de recursos do Azure SQL Server ou veja [como criar um grupo de recursos][how to create a resource group].
* **Ambiente para executar T-SQL**: pode utilizar o [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] ou [SSMS][SSMS] para executar o T-SQL.

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Consulte [SQL Data Warehouse pricing (Preços do SQL Data Warehouse)][SQL Data Warehouse pricing], para mais detalhes sobre preços.
>
>

## <a name="create-a-database-with-visual-studio"></a>Criar uma base de dados com o Visual Studio
Se não estiver familiarizado com o Visual Studio, veja o artigo [Consultar o Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)].  Para começar, abra o SQL Server Object Explorer no Visual Studio e ligue ao servidor que alojará a base de dados SQL Data Warehouse.  Assim que estiver ligado, pode criar um SQL Data Warehouse, executando o seguinte comando SQL na base de dados **mestra**.  Este comando cria a base de dados MySqlDwDb com um Objetivo de Serviço de DW400 e permite que esta aumente de tamanho, para um máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Criar uma base de dados com sqlcmd
Em alternativa, pode executar o mesmo comando com sqlcmd ao executar o seguinte numa linha de comandos.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

O agrupamento predefinido quando não é especificado é COLLATE SQL_Latin1_General_CP1_CI_AS.  O `MAXSIZE` pode estar entre 250 GB e 240 TB.  O `SERVICE_OBJECTIVE` pode estar entre DW100 e DW2000 [DWU][DWU].  Para obter uma lista de todos os valores válidos, consulte a documentação MSDN para [CREATE DATABASE][CREATE DATABASE].  O MAXSIZE e SERVICE_OBJECTIVE podem ser alterados com um comando T-SQL [ALTER DATABASE][ALTER DATABASE].  O agrupamento da base de dados não pode ser alterado após a criação.   Deve ter cuidado ao alterar o SERVICE_OBJECTIVE, pois a alteração da DWU causa o reinício dos serviços, o que cancela todas as consultas em curso.  A alteração de MAXSIZE não reinicia os serviços, pois é apenas uma operação simples de metadados.

## <a name="next-steps"></a>Passos seguintes
Após terminar o aprovisionamento do SQL Data Warehouse,poderá querer experimentar [carregar os dados de exemplo][load sample data] ou verificar como [programar][develop], [carregar][load], ou [migrar][migrate].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

