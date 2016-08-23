<properties
   pageTitle="Ligar ao Azure SQL Data Warehouse | Microsoft Azure"
   description="Descrição geral da ligação para ligar ao Azure SQL Data Warehouse"
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
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# Ligar ao Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Descrição geral](sql-data-warehouse-connect-overview.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Controladores](sql-data-warehouse-connection-strings.md)

Descrição geral da ligação ao Azure SQL Data Warehouse. 

## Detetar a cadeia de ligação a partir do portal

O seu SQL Data Warehouse está associado a um servidor SQL do Azure. Para ligar, precisa do nome completamente qualificado do servidor (**servername**.database.windows.net*).

Descubra o nome de servidor completamente qualificado:

1. Aceda ao [Portal do Azure][].
2. Clique em **bases de dados SQL** e clique na base de dados à qual pretende ligar. Este exemplo utiliza a base de dados de exemplo AdventureWorksDW.
3. Localize o nome do servidor completo.

    ![Nome de servidor completo][1]

## Definições de ligação
O SQL Data Warehouse uniformiza algumas definições durante a ligação e a criação do objeto. Estas definições não podem ser substituídas.

| Definição de base de dados   | Valor                        |
| :----------------- | :--------------------------- |
| ANSI_NULLS         | ON                           |
| QUOTED_IDENTIFIERS | ON                           |
| NO_COUNT           | OFF                          |
| DATEFORMAT         | mdy                          |
| DATEFIRST          | 7                            |
| Agrupamento de Base de Dados | SQL_Latin1_General_CP1_CI_AS |

## Sessões e pedidos
Assim que for feita uma ligação e estabelecida uma sessão, estará pronto para escrever e submeter consultas ao SQL Data Warehouse.

Cada consulta será representada por um ou mais identificadores de pedido. Todas as consultas submetidas nessa ligação fazem parte de uma única sessão e, por isso, serão representadas por um ID de sessão único.

No entanto, uma vez que o SQL Data Warehouse é um sistema MPP (Processamento Paralelo em Massa) distribuído, os identificadores de sessão e pedido são expostos de forma um pouco diferente relativamente ao SQL Server.

As sessões e pedidos são representados logicamente pelos seus respetivos identificadores.

| Identificador | Valor de exemplo |
| :--------- | :------------ |
| ID de sessão | SID123456     |
| ID do pedido | QID123456     |

Tenha em atenção que o ID de sessão tem como prefixo SID, a abreviatura para ID de sessão, e os pedidos têm como prefixo QID, que é a abreviatura para ID de consulta.

Irá precisar desta informação para ajudar a identificar a sua consulta ao monitorizar o desempenho das consultas. Pode monitorizar o desempenho das consultas através do [Portal do Azure] ou das vistas de gestão dinâmica.

Esta consulta identifica a sua sessão atual.

```sql
SELECT SESSION_ID()
;
```

Para ver todas as consultas que estão em execução ou que foram recentemente executadas no armazém de dados, pode utilizar o exemplo seguinte. Este procedimento cria uma vista e, em seguida, executa-a.

```sql
CREATE VIEW dbo.vSessionRequests
AS
SELECT   s.[session_id]                                 AS Session_ID
        ,s.[status]                                     AS Session_Status
        ,s.[login_name]                                 AS Session_LoginName
        ,s.[login_time]                                 AS Session_LoginTime
        ,r.[request_id]                                 AS Request_ID
        ,r.[status]                                     AS Request_Status
        ,r.[submit_time]                                AS Request_SubmitTime
        ,r.[start_time]                                 AS Request_StartTime
        ,r.[end_compile_time]                           AS Request_EndCompileTime
        ,r.[end_time]                                   AS Request_EndTime
        ,r.[total_elapsed_time]                         AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])        AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])   AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])     AS Request_ExecDuration_ms
        ,[label]                                        AS Request_QueryLabel
        ,[command]                                      AS Request_Command
        ,[database_id]                                  AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s  ON  r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;

SELECT * FROM dbo.vSessionRequests;
```

## Passos seguintes

Para começar a consultar o armazém de dados com o Visual Studio e outras aplicações, veja [Query with Visual Studio (Consulta com o Visual Studio)][].


<!--Arcticles-->

[Query with Visual Studio (Consulta com o Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png





<!--HONumber=Aug16_HO1-->


