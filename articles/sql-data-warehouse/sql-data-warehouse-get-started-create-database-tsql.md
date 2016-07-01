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
   ms.date="06/04/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar uma base de dados SQL Data Warehouse, utilizando Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra como criar uma base de dados SQL Data Warehouse com Transact-SQL (T-SQL).

## Pré-requisitos
Antes de começar, certifique-se de que tem os seguintes pré-requisitos.

- **Conta Azure**: consulte [Versão de Avaliação Gratuita do Azure][] ou [Créditos do MSDN Azure][] para criar uma conta.
- **V12 Azure SQL Server**: consulte [Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure][] ou [Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell][].
- **Nome do grupo de recursos**: utilize o mesmo grupo de recursos do V12 Azure SQL Server ou consulte [grupos de recursos][] para criar um novo grupo de recursos.
- **Visual Studio com SQL Server Data Tools**: para instruções de instalação, consulte [Instalar o Visual Studio e SSDT][].

> [AZURE.NOTE] A criação de um novo SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Consulte [Preços do SQL Data Warehouse][], para mais detalhes sobre preços.

## Criar uma base de dados com o Visual Studio

Se não estiver familiarizado com o Visual Studio, consulte [Ligar ao SQL Data Warehouse com o Visual Studio][].  Para começar, abra o SQL Server Object Explorer no Visual Studio e ligue ao servidor que alojará a base de dados SQL Data Warehouse.  Assim que estiver ligado, pode criar um SQL Data Warehouse, executando o seguinte comando SQL na base de dados **mestra**.  Este comando irá criar a base de dados MySqlDwDb com um Objetivo de Serviço de DW400 e permitir que a base de dados aumente o tamanho máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Criar uma base de dados com sqlcmd

Em alternativa, pode executar o mesmo comando com sqlcmd, executando o seguinte numa linha de comandos.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Os parâmetros **MAXSIZE** e **SERVICE_OBJECTIVE** especificam o espaço máximo que a base de dados pode utilizar no disco e os recursos de computação atribuídos à sua instância do Armazém de Dados.  O Objetivo de Serviço é, essencialmente, uma alocação de CPU e memória que dimensiona de forma linear com o tamanho de DWU.  

O MAXSIZE pode estar entre 250 GB e 240 TB.  O Objetivo de Serviço pode estar entre DW100 e DW2000.  Para obter uma lista completa de todos os valores válidos para MAXSIZE e SERVICE_OBJECTIVE, consulte a documentação de MSDN para [CREATE DATABASE][].  O MAXSIZE e SERVICE_OBJECTIVE também podem ser alterados com um comando T-SQL [ALTER DATABASE][].  Deve ter cuidado ao alterar o SERVICE_OBJECTIVE, pois isto causa um reinício dos serviços que cancelará todas as consultas em curso.  A alteração de MAXSIZE não reinicia os serviços, pois é apenas uma operação simples de metadados.

## Passos seguintes
Após terminar o aprovisionamento do SQL Data Warehouse,poderá querer experimentar [carregar os dados de exemplo][] ou verificar como [programar][], [carregar][], ou [migrar][].

<!--Article references-->
[como criar um SQL Data Warehouse a partir do portal do Azure]: ./sql-data-warehouse-get-started-provision.md
[Ligar ao SQL Data Warehouse com o Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-overview-load.md
[carregar os dados de exemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[grupos de recursos]: ../azure-portal/resource-group-portal.md
[Instalar o Visual Studio e SSDT]: ./sql-data-warehouse-install-visual-studio.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do MSDN Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Jun16_HO2-->


