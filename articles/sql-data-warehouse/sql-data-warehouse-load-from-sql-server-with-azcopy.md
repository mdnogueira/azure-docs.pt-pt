---
title: Carregar dados do SQL Server para o Azure SQL Data Warehouse (PolyBase) | Microsoft Docs
description: Utiliza o bcp para exportar dados do SQL Server para ficheiros simples, o AZCopy para importar dados para o armazenamento de blobs do Azure e o PolyBase para incorporar os dados no Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 21b4cc704e271ac220fd606305f8f97c9b2593bb
ms.contentlocale: pt-pt
ms.lasthandoff: 03/21/2017

---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Carregar dados do SQL Server para o Azure SQL Data Warehouse (AZCopy)
Utilize os utilitários bcp e AZCopy de linha de comandos para carregar dados do SQL Server para o armazenamento de blobs do Azure. Em seguida, utilize o PolyBase ou o Azure Data Factory para carregar os dados para o Azure SQL Data Warehouse. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Uma base de dados SQL Data Warehouse
* Ter instalado o utilitário bcp de linha de comandos
* Ter instalado o utilitário SQLCMD de linha de comandos

> [!NOTE]
> Pode transferir os utilitários bcp e sqlcmd a partir do [Centro de Transferências da Microsoft][Microsoft Download Center].
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>Importar dados para o SQL Data Warehouse
Neste tutorial, irá criar uma tabela no Azure SQL Data Warehouse e importar dados para a tabela.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Passo 1: criar uma tabela no Azure SQL Data Warehouse
A partir de uma linha de comandos, utilize o sqlcmd para executar a consulta seguinte, para criar uma tabela na sua instância:

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

> [!NOTE]
> Veja [Table Overview (Descrição Geral da Tabela)][Table Overview] ou a [sintaxe CREATE TABLE][CREATE TABLE syntax] para obter mais informações sobre como criar uma tabela no SQL Data Warehouse e as opções disponíveis na cláusula WITH.
> 
> 

### <a name="step-2-create-a-source-data-file"></a>Passo 2: criar um ficheiro de dados de origem
Abra o Bloco de Notas e copie as seguintes linhas de dados para um novo ficheiro de texto e, em seguida, guarde este ficheiro no diretório temporário local, C:\Temp\DimDate2.txt.

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

> [!NOTE]
> É importante não esquecer que bcp.exe não suporta a codificação UTF-8 de ficheiros. Utilize ficheiros ASCII ou ficheiros codificados com UTF-16 ao utilizar bcp.exe.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Passo 3: ligar e importar os dados
Ao utilizar o bcp, pode ligar e importar os dados com o seguinte comando, substituindo os valores conforme adequado:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Pode verificar os dados que foram carregados, executando a seguinte consulta com o sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Isto deverá devolver os seguintes resultados:

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

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Passo 4: criar Estatísticas nos dados recentemente carregados
O Azure SQL Data Warehouse ainda não suporta a criação ou atualização automática de estatísticas. Para obter o melhor desempenho das consultas, é importante que sejam criadas estatísticas em todas as colunas de todas as tabelas após o primeiro carregamento ou após a ocorrência de quaisquer alterações substanciais nos dados. Para obter uma explicação detalhada das estatísticas, veja o tópico [Statistics (Estatísticas)][Statistics] no grupo de tópicos Programar. Segue-se um breve exemplo de como criar estatísticas para a tabela carregada neste exemplo

Execute as seguintes instruções CREATE STATISTICS a partir de uma linha de comandos sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportar dados do SQL Data Warehouse
Neste tutorial, irá criar um ficheiro de dados a partir de uma tabela do SQL Data Warehouse. Vamos exportar os dados que criámos acima para um novo ficheiro de dados denominado DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Passo 1: exportar os dados
Ao utilizar o utilitário bcp, pode ligar e exportar dados com o seguinte comando, substituindo os valores conforme adequado:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Pode verificar se os dados foram exportados corretamente ao abrir o novo ficheiro. Os dados no ficheiro devem corresponder ao texto abaixo:

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

> [!NOTE]
> Devido à natureza dos sistemas distribuídos, a ordem dos dados poderá não ser a mesma nas Bases de Dados SQL Data Warehouse. Outra opção consiste em utilizar a função **queryout** do bcp para escrever um extrato de consulta, em vez de exportar a tabela inteira.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para obter uma descrição geral do carregamento, veja [Load data into SQL Data Warehouse (Carregar dados para o SQL Data Warehouse)][Load data into SQL Data Warehouse].
Para obter mais sugestões de desenvolvimento, veja [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)][SQL Data Warehouse development overview].

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

