<properties
   pageTitle="Introdução: ligar ao Azure SQL Data Warehouse | Microsoft Azure"
   description="Introdução à ligação ao SQL Data Warehouse e execução de algumas consultas."
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
   ms.date="05/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Ligar e consultar com SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)


Estas instruções mostram como ligar e consultar uma base de dados Azure SQL Data Warehouse em apenas alguns minutos, utilizando o utilitário sqlcmd.exe. Nestas instruções, irá:

+ Instalar software de pré-requisitos
+ Ligar a uma base de dados que contém a base de dados de exemplo AdventureWorksDW
+ Executar uma consulta na base de dados de exemplo  

## Pré-requisitos

+ Para transferir [sqlcmd.exe][], consulte [Microsoft Command Line Utilities 11 para SQL Server][].

## Obter o seu nome de servidor completamente qualificado do SQL do Azure

Para ligar à base de dados, é necessário o nome completo do servidor (***servername**.database.windows.net*) que contém a base de dados à qual pretende ligar.

1. Aceda ao [Portal do Azure][].
2. Navegue para a base de dados à qual pretende ligar.
3. Localize o nome de servidor completo (iremos utilizá-lo nos passos abaixo):

![][1]


## Ligar ao SQL Data Warehouse com sqlcmd

Para ligar a uma instância específica do SQL Data Warehouse ao utilizar o sqlcmd, terá de abrir a linha de comandos e introduzir **sqlcmd**, seguido da cadeia de ligação para a base de dados SQL Data Warehouse. A cadeia de ligação terá de seguir os parâmetros necessários:

+ **Server (-S):** servidor sob a forma `<`Nome do Servidor`>`. database.windows.net
+ **Database (-d):** nome da base de dados.
+ **User (-U):** utilizador do servidor sob a forma `<`Utilizador`>`
+ **Password (-P):** palavra-passe associada ao utilizador.
+ **Enable Quoted Identifiers (-I):** os identificadores delimitado por aspas têm de estar ativados para ligar a uma instância do SQL Data Warehouse.

Por conseguinte, para ligar a uma instância do SQL Data Warehouse, introduziria o seguinte:

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Executar consultas de exemplo

Após a ligação, pode emitir quaisquer instruções Transact-SQL suportadas na instância.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Para informações adicionais sobre o sqlcmd, consulte a [documentação do sqlcmd][sqlcmd.exe].


## Passos seguintes

Agora que pode ligar e consultar, experimente [ligar com o PowerBI][].

Para configurar o ambiente para a autenticação do Windows, consulte [Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory][].

<!--Articles-->
[Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[ligar com o PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/en-us/library/ms162773.aspx
[Microsoft Command Line Utilities 11 para SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Portal do Azure]: https://portal.azure.com

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png



<!--HONumber=Jun16_HO2-->


