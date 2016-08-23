<properties
   pageTitle="Utilizar o bcp para carregar os dados para o SQL Data Warehouse | Microsoft Azure"
   description="Saiba o que é o bcp e como utilizá-lo para cenários de armazenamento de dados."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="mausher;barbkess;sonyama"/>


# Carregar dados com o bcp

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp][]** é um utilitário da linha de comandos para carregamento em massa, que permite copiar dados entre o SQL Server, ficheiros de dados e o SQL Data Warehouse. Utilize o bcp para importar um grande número de linhas para tabelas do SQL Data Warehouse ou exportar dados de tabelas do SQL Server para ficheiros de dados. Exceto quando utilizado com a opção de queryout, o bcp não necessita de nenhum conhecimento de Transact-SQL.

O bcp é uma forma rápida e fácil de mover conjuntos de dados mais pequenos para dentro e fora de uma base de dados SQL Data Warehouse. A quantidade exata de dados recomendada para carregamento/extração através do bcp dependerá da ligação de rede ao centro de dados do Azure.  Geralmente, as tabelas de dimensões podem ser prontamente carregadas e extraídas com o bcp. No entanto, não é recomendada a utilização do bcp para carregar ou extrair grandes volumes de dados.  O Polybase é a ferramenta recomendada para carregar e extrair grandes volumes de dados, pois efetua uma melhor tarefa ao tirar partido da arquitetura de processamento paralelo em massa do SQL Data Warehouse.

O bcp permite:

- Utilizar um utilitário de linha de comandos simples para carregar dados para o SQL Data Warehouse.
- Utilizar um utilitário de linha de comandos simples para extrair dados do SQL Data Warehouse.

Este tutorial irá mostrar-lhe como:

- Importar dados para uma tabela com o comando de entrada do bcp
- Exportar dados de uma tabela com o comando de saída do bcp

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Pré-requisitos

Para seguir este tutorial, é necessário:

- Uma base de dados SQL Data Warehouse
- Ter instalado o utilitário bcp de linha de comandos
- Ter instalado o utilitário SQLCMD de linha de comandos

>[AZURE.NOTE] Pode transferir os utilitários bcp e sqlcmd a partir do [Centro de Transferências da Microsoft][].

## Importar dados para o SQL Data Warehouse

Neste tutorial, irá criar uma tabela no Azure SQL Data Warehouse e importar dados para a tabela.

### Passo 1: criar uma tabela no Azure SQL Data Warehouse

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

>[AZURE.NOTE] Consulte [Descrição Geral da Tabela][] ou a [sintaxe CREATE TABLE][] para obter mais informações sobre como criar uma tabela no SQL Data Warehouse e as opções disponíveis na cláusula WITH.

### Passo 2: criar um ficheiro de dados de origem

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

> [AZURE.NOTE] É importante não esquecer que bcp.exe não suporta a codificação UTF-8 de ficheiros. Utilize ficheiros ASCII ou ficheiros codificados com UTF-16 ao utilizar bcp.exe.

### Passo 3: ligar e importar os dados
Ao utilizar o bcp, pode ligar e importar os dados com o seguinte comando, substituindo os valores conforme adequado:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Pode verificar os dados que foram carregados, executando a seguinte consulta com o sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Isto deverá devolver os seguintes resultados:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### Passo 4: criar Estatísticas nos dados recentemente carregados

O Azure SQL Data Warehouse ainda não suporta a criação ou atualização automática de estatísticas. Para obter o melhor desempenho das consultas, é importante que sejam criadas estatísticas em todas as colunas de todas as tabelas após o primeiro carregamento ou após a ocorrência de quaisquer alterações substanciais nos dados. Para obter uma explicação detalhada das estatísticas, consulte o tópico [Estatísticas][] no grupo de tópicos Desenvolver. Segue-se um breve exemplo de como criar estatísticas para a tabela carregada neste exemplo

Execute as seguintes instruções CREATE STATISTICS a partir de uma linha de comandos sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## Exportar dados do SQL Data Warehouse
Neste tutorial, irá criar um ficheiro de dados a partir de uma tabela do SQL Data Warehouse. Vamos exportar os dados que criámos acima para um novo ficheiro de dados denominado DimDate2_export.txt.

### Passo 1: exportar os dados

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

>[AZURE.NOTE] Devido à natureza dos sistemas distribuídos, a ordem dos dados poderá não ser a mesma nas Bases de Dados SQL Data Warehouse. Outra opção consiste em utilizar a função **queryout** do bcp para escrever um extrato de consulta, em vez de exportar a tabela inteira.

## Passos seguintes
Para uma descrição geral do carregamento, consulte [Carregar dados para o SQL Data Warehouse][].
Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Carregar dados para o SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Descrição geral do desenvolvimento no SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Descrição Geral da Tabela]: ./sql-data-warehouse-tables-overview.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[sintaxe CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de Transferências da Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Aug16_HO1-->


