---
title: Carregar dados do SQL Server para o Azure SQL Data Warehouse (bcp) | Microsoft Docs
description: "Para um tamanho de dados de pequena dimensão, utilize o bcp para exportar dados do SQL Server para ficheiros simples e importar os dados diretamente para o Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: f87d8d7c-8276-43c5-90e7-d4ccc0e3a224
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: dae7b5f7456f4ec0daf60d55f9c38b780896ff83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Carregar dados do SQL Server para o Azure SQL Data Warehouse (ficheiros simples)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Para conjuntos de dados pequenos, pode utilizar o utilitário bcp da linha de comandos para exportar dados do SQL Server e, em seguida, carregá-los diretamente para o Azure SQL Data Warehouse.

Neste tutorial, irá utilizar o bcp para:

* Exportar uma tabela do SQL Server, utilizando o comando de saída do bcp (ou criar um ficheiro de exemplo simples)
* Importe a tabela de um ficheiro simples para o SQL Data Warehouse.
* Crie estatísticas sobre os dados carregados.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="before-you-begin"></a>Antes de começar
### <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Uma base de dados SQL Data Warehouse
* Ter instalado o utilitário bcp de linha de comandos
* Ter instalado o utilitário sqlcmd de linha de comandos

Pode transferir os utilitários bcp e sqlcmd a partir do [Centro de Transferências da Microsoft][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Dados no formato ASCII ou UTF-16
Se estiver a experimentar este tutorial com os seus dados, estes têm de utilizar a codificação ASCII ou UTF-16, uma vez que o bcp não suporta UTF-8. 

O PolyBase suporta UTF-8, mas ainda não suporta UTF-16. Tenha em atenção que, se pretender combinar o bcp com o PolyBase, terá de transformar os dados para UTF-8 depois de serem exportados do SQL Server. 

## <a name="1-create-a-destination-table"></a>1. Criar uma tabela de destino
Defina uma tabela no SQL Data Warehouse que será a tabela de destino para o carregamento. As colunas na tabela têm de corresponder aos dados em cada linha do ficheiro de dados.

Para criar uma tabela, abra uma linha de comandos e utilize sqlcmd.exe para executar o seguinte comando:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```


## <a name="2-create-a-source-data-file"></a>2. Criar um ficheiro de dados de origem
Abra o Bloco de Notas e copie as seguintes linhas de dados para um novo ficheiro de texto e, em seguida, guarde este ficheiro no diretório temporário local, C:\Temp\DimDate2.txt. Estes dados estão no formato ASCII.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Opcional) Para exportar os dados a partir de uma base de dados SQL Server, abra uma linha de comandos e execute o seguinte comando. Substitua TableName, ServerName, DatabaseName, Username e Password pelas suas informações.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. Carregar os dados
Para carregar os dados, abra uma linha de comandos e execute o seguinte comando, substituindo os valores para o Nome do Servidor, nome da Base de Dados, Nome de Utilizador e Palavra-passe pelas suas informações.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Utilize este comando para verificar se os dados foram carregados corretamente

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Os resultados devem ter o seguinte aspeto:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="4-create-statistics"></a>4. Criar estatísticas
O SQL Data Warehouse ainda não suporta a criação ou atualização automática de estatísticas. Para obter o melhor desempenho das consultas, é importante criar estatísticas em todas as colunas de todas as tabelas após o primeiro carregamento ou após a ocorrência de quaisquer alterações substanciais nos dados. Para obter uma explicação detalhada das estatísticas, consulte [estatísticas][Statistics]. 

Execute o seguinte comando para criar estatísticas na tabela recentemente carregada.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. Exportar dados do SQL Data Warehouse
Pode experimentar exportar os dados que acabou de carregar de volta para o SQL Data Warehouse.  O comando para exportar é exatamente igual à exportação do SQL Server.

No entanto, existe uma diferença nos resultados. Uma vez que os dados ficam armazenados nas localizações distribuídas no SQL Data Warehouse, ao exportar dados, cada nó de Computação escreve os respetivos dados no ficheiro de saída. É provável que a ordem dos dados no ficheiro de saída seja diferente da ordem dos dados no ficheiro de entrada.

### <a name="export-a-table-and-compare-exported-results"></a>Exportar uma tabela e comparar os resultados exportados
Para ver os dados exportados, abra uma linha de comandos e execute este comando, utilizando os seus parâmetros. ServerName é o nome do seu SQL Server lógico do Azure.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Pode verificar se os dados foram exportados corretamente ao abrir o novo ficheiro. Os dados no ficheiro devem corresponder ao texto abaixo, mas provavelmente estarão ordenados por uma ordem diferente:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="export-the-results-of-a-query"></a>Exportar os resultados de uma consulta
Pode utilizar a função **queryout** do bcp para exportar os resultados de uma consulta, em vez de exportar a tabela inteira. 

## <a name="next-steps"></a>Passos seguintes
Para obter uma descrição geral do carregamento, veja [Load data into SQL Data Warehouse (Carregar dados para o SQL Data Warehouse)][Load data into SQL Data Warehouse].
Para obter mais sugestões de desenvolvimento, veja [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)][SQL Data Warehouse development overview].
Consulte [descrição geral da tabela] [ Table Overview] ou [sintaxe CREATE TABLE] [ CREATE TABLE syntax] para obter mais informações sobre como criar uma tabela no SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
