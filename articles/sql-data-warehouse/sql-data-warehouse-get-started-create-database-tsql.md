<properties
   pageTitle="Criar um SQL Data Warehouse com TSQL | Microsoft Azure"
   description="Saiba como criar um Azure SQL Data Warehouse com TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar uma base de dados SQL Data Warehouse, utilizando Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo irá mostrar-lhe como criar um SQL Data Warehouse com T-SQL.

## Pré-requisitos

Para começar, irá precisar do seguinte: 

- **Conta Azure**: aceda a [Versão de Avaliação Gratuita do Azure][] ou [Créditos do MSDN Azure][] para criar uma conta.
- **Azure SQL Server**: consulte [Create an Azure SQL Database logical server with the Azure Portal (Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure)][] ou [Create an Azure SQL Database logical server with PowerShell (Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell)][] para obter mais detalhes.
- **Grupo de recursos**: utilize o mesmo grupo de recursos do Azure SQL Server ou veja [como criar um grupo de recursos][].
- **Ambiente para executar T-SQL**: pode utilizar o [Visual Studio][Instalar o Visual Studio e SSDT], [sqlcmd][] ou [SSMS][] para executar o T-SQL.

> [AZURE.NOTE] A criação de um novo SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Consulte [Preços do SQL Data Warehouse][], para mais detalhes sobre preços.

## Criar uma base de dados com o Visual Studio

Se não estiver familiarizado com o Visual Studio, veja o artigo [Consultar o Azure SQL Data Warehouse (Visual Studio)][].  Para começar, abra o SQL Server Object Explorer no Visual Studio e ligue ao servidor que alojará a base de dados SQL Data Warehouse.  Assim que estiver ligado, pode criar um SQL Data Warehouse, executando o seguinte comando SQL na base de dados **mestra**.  Este comando irá criar a base de dados MySqlDwDb com um Objetivo de Serviço de DW400 e permitir que a base de dados aumente o tamanho máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Criar uma base de dados com sqlcmd

Em alternativa, pode executar o mesmo comando com sqlcmd, executando o seguinte numa linha de comandos.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

O `MAXSIZE` pode estar entre 250 GB e 240 TB.  O `SERVICE_OBJECTIVE` pode estar entre DW100 e DW2000 [DWU][].  Para obter uma lista de todos os valores válidos, consulte a documentação MSDN para [CREATE DATABASE][].  O MAXSIZE e SERVICE_OBJECTIVE também podem ser alterados com um comando T-SQL [ALTER DATABASE][].  Deve ter cuidado ao alterar o SERVICE_OBJECTIVE, pois isto causa um reinício dos serviços que cancelará todas as consultas em curso.  A alteração de MAXSIZE não reinicia os serviços, pois é apenas uma operação simples de metadados.

## Passos seguintes

Após terminar o aprovisionamento do SQL Data Warehouse,poderá querer experimentar [carregar os dados de exemplo][] ou verificar como [programar][], [carregar][], ou [migrar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[como criar um SQL Data Warehouse a partir do portal do Azure]: sql-data-warehouse-get-started-provision.md
[Consultar o Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrar]: sql-data-warehouse-overview-migrate.md
[programar]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar os dados de exemplo]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL Database logical server with the Azure Portal (Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure)]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell (Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell)]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group
[Instalar o Visual Studio e SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do MSDN Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=ago16_HO4-->


