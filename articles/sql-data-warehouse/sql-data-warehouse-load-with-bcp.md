---
title: Utilizar o bcp para carregar os dados para o SQL Data Warehouse | Microsoft Docs
description: "Saiba o que é o bcp e como utilizá-lo para cenários de armazenamento de dados."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 7596eac10fdf53380d85128265430ce07b551fe3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-with-bcp"></a>Carregar dados com o bcp
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

O **[bcp][bcp]** é um utilitário de linha de comandos para carregamento em massa, que lhe permite copiar dados entre o SQL Server, ficheiros de dados e o SQL Data Warehouse. Utilize o bcp para importar um grande número de linhas para tabelas do SQL Data Warehouse ou exportar dados de tabelas do SQL Server para ficheiros de dados. Exceto quando utilizado com a opção de queryout, o bcp não necessita de nenhum conhecimento de Transact-SQL.

O bcp é uma forma rápida e fácil de mover conjuntos de dados mais pequenos para dentro e fora de uma base de dados SQL Data Warehouse. A quantidade exata de dados recomendada para carregamento/extração através do bcp dependerá da ligação de rede ao centro de dados do Azure.  Geralmente, as tabelas de dimensões podem ser prontamente carregadas e extraídas com o bcp. No entanto, não é recomendada a utilização do bcp para carregar ou extrair grandes volumes de dados.  O Polybase é a ferramenta recomendada para carregar e extrair grandes volumes de dados, pois efetua uma melhor tarefa ao tirar partido da arquitetura de processamento paralelo em massa do SQL Data Warehouse.

O bcp permite:

* Utilizar um utilitário de linha de comandos simples para carregar dados para o SQL Data Warehouse.
* Utilizar um utilitário de linha de comandos simples para extrair dados do SQL Data Warehouse.

Este tutorial irá mostrar-lhe como:

* Importar dados para uma tabela com o comando de entrada do bcp
* Exportar dados de uma tabela com o comando de saída do bcp

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

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
