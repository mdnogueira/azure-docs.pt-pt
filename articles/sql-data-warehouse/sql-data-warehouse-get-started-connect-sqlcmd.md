<properties
   pageTitle="Consulta do Azure SQL Data Warehouse (sqlcmd) | Microsoft Azure"
   description="Consultar o Azure SQL Data Warehouse com o Utilitário de Linha de Comandos sqlcmd."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/22/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Consulta do Azure SQL Data Warehouse (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Estas instruções utilizam o Utilitário de Linha de Comando sqlcmd para consultar um Azure SQL Data Warehouse.  

## Pré-requisitos

Para seguir este tutorial, é necessário:

-  [sqlcmd.exe][]. Para transferir, consulte [Microsoft Command Line Utilities 11 for SQL Server (Microsoft Command Line Utilities 11 para o SQL Server)][], que também poderá requerer o [Controlador ODBC 11 da Microsoft para o SQL Server do Windows][].

## 1. Ligar

Para começar a utilizar o sqlcmd, abra a linha de comandos e escreva **sqlcmd**, seguido da cadeia de ligação para a sua base de dados SQL Data Warehouse. A cadeia de ligação terá de seguir os parâmetros necessários:

+ **Server (-S):** servidor sob a forma `<`Nome do Servidor`>`. database.windows.net
+ **Database (-d):** nome da base de dados.
+ **User (-U):** utilizador do servidor sob a forma `<`Utilizador`>`
+ **Password (-P):** palavra-passe associada ao utilizador.
+ **Enable Quoted Identifiers (-I):** os identificadores delimitado por aspas têm de estar ativados para ligar a uma instância do SQL Data Warehouse.

Por exemplo, a cadeia de ligação poderá ter o seguinte aspeto:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] A opção -I, que permite identificadores delimitados, é atualmente necessária para ligar ao SQL Data Warehouse.

## 2. Consulta

Após a ligação, pode emitir quaisquer instruções Transact-SQL suportadas na instância.  Neste exemplo, as consultas são submetidas no modo interativo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Os exemplos que se seguem mostram como pode executar consultas no modo batch através da opção -Q ou ao ligar o seu SQL ao sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## Passos seguintes

Consulte a [documentação do sqlcmd][sqlcmd.exe] para obter mais informações detalhadas sobre as opções disponíveis no sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd.exe]: https://msdn.microsoft.com/library/ms162773.aspx
[Controlador ODBC 11 da Microsoft para o SQL Server do Windows]: https://www.microsoft.com/download/details.aspx?id=36434
[Microsoft Command Line Utilities 11 for SQL Server (Microsoft Command Line Utilities 11 para o SQL Server)]: http://go.microsoft.com/fwlink/?LinkId=321501
[Portal do Azure]: https://portal.azure.com

<!--Other Web references-->



<!--HONumber=Aug16_HO1-->


