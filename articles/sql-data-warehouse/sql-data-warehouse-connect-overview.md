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
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Ligar ao Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Descrição geral](sql-data-warehouse-connect-overview.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Controladores](sql-data-warehouse-connection-strings.md)

Descrição geral da ligação ao Azure SQL Data Warehouse. 

## Detetar a cadeia de ligação a partir do portal

O seu SQL Data Warehouse está associado a um servidor SQL do Azure. Para ligar, precisa do nome completamente qualificado do servidor.  Por exemplo, **myserver**.database.windows.net.

Descubra o nome de servidor completamente qualificado:

1. Aceda ao [Portal do Azure][].
2. Clique em **bases de dados SQL** e clique na base de dados à qual pretende ligar. Este exemplo utiliza a base de dados de exemplo AdventureWorksDW.
3. Localize o nome do servidor completo.

    ![Nome de servidor completo][1]

## Definições de ligação

O SQL Data Warehouse uniformiza algumas definições durante a ligação e a criação do objeto. Estas definições não podem ser substituídas.

| Definição de base de dados       | Valor                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | ON                           |
| [QUOTED_IDENTIFIERS][] | ON                           |
| [DATEFORMAT][]         | mdy                          |
| [DATEFIRST][]          | 7                            |

## Monitorizar ligações e consultas

Assim que for feita uma ligação e estabelecida uma sessão, estará pronto para escrever e submeter consultas ao SQL Data Warehouse.  Para saber como monitorizar sessões e consultas, veja [Monitorizar a carga de trabalho com DMVs][].

## Passos seguintes

Para começar a consultar o armazém de dados com o Visual Studio e outras aplicações, veja [Query with Visual Studio (Consulta com o Visual Studio)][]. 

<!--Articles-->
[Query with Visual Studio (Consulta com o Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Monitorizar a carga de trabalho com DMVs]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png





<!--HONumber=ago16_HO5-->


