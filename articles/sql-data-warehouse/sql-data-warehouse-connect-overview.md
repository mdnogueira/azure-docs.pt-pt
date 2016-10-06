<properties
   pageTitle="Ligar ao Azure SQL Data Warehouse | Microsoft Azure"
   description="Como encontrar o nome do servidor e a cadeia de ligação do Azure SQL Data Warehouse"
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
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>


# Ligar ao Azure SQL Data Warehouse

Este artigo ajuda-o a ligar-se ao SQL Data Warehouse pela primeira vez.

## Encontrar o nome do servidor

O primeiro passo para ligar ao SQL Data Warehouse é saber como localizar o nome do servidor.  Por exemplo, o nome do servidor no exemplo seguinte é sample.database.windows.net. Descubra o nome de servidor completamente qualificado:

1. Aceda ao [Portal do Azure][].
2. Clique em **Bases de Dados SQL** 
3. Clique na base de dados à qual se quer ligar.
4. Localize o nome do servidor completo.

    ![Nome de servidor completo][1]

## Controladores e cadeias de ligação suportados

O Azure SQL Data Warehouse suporta [ADO.NET][], [ODBC][], [PHP][] e [JDBC][]. Clique num dos controladores anteriores para encontrar a versão mais recente e a documentação. Para gerar automaticamente a cadeia de ligação para o controlador que está a utilizar a partir do portal do Azure, pode clicar em **Mostrar cadeias de ligação de bases de dados**, no exemplo anterior.  Seguem-se também alguns exemplos do aspeto de uma cadeia de ligação para cada controlador.

> [AZURE.NOTE] Considere definir o tempo limite da ligação como 300 segundos, para permitir que a ligação sobreviva a curtos períodos de indisponibilidade.

### Exemplo de cadeia de ligação de ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Exemplo de cadeia de ligação de ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Exemplo de cadeia de ligação de PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Exemplo de cadeia de ligação de JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Definições de ligação

O SQL Data Warehouse uniformiza algumas definições durante a ligação e a criação do objeto. Estas definições não podem ser substituídas e incluem:

| Definição de base de dados       | Valor                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | ON                           |
| [QUOTED_IDENTIFIERS][] | ON                           |
| [DATEFORMAT][]         | mdy                          |
| [DATEFIRST][]          | 7                            |

## Passos seguintes

Para ligar e consultar com o Visual Studio, veja [Query with Visual Studio (Consulta com o Visual Studio)][]. Para saber mais sobre as opções de autenticação, veja [Authentication to Azure SQL Data Warehouse (Autenticação no Azure SQL Data Warehouse)][].

<!--Articles-->
[Query with Visual Studio (Consulta com o Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse (Autenticação no Azure SQL Data Warehouse)]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png





<!--HONumber=Sep16_HO4-->


