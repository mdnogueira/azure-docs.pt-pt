---
title: Carregar dados do ficheiro CSV para a Base de Dados SQL do Azure (bcp) | Microsoft Docs
description: "Para um tamanho de dados de pequena dimensão, utilize o bcp para importar dados para a Base de Dados SQL do Azure."
services: sql-database
documentationcenter: NA
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 875f9b8d-f1a1-4895-b717-f45570fb7f80
ms.service: sql-database
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/13/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8d6e201ba5fb8283d883272e0cb2b8e7c11f43e5


---
# <a name="load-data-from-csv-into-azure-sql-data-warehouse-flat-files"></a>Carregar dados do ficheiro CSV para o SQL Data Warehouse (ficheiros simples)
Pode utilizar o utilitário da linha de comandos do bcp para importar dados a partir de um ficheiro CSV para uma Base de Dados SQL do Azure.

## <a name="before-you-begin"></a>Antes de começar
### <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Uma base de dados e um servidor lógico da Base de Dados SQL do Azure
* Ter instalado o utilitário bcp de linha de comandos
* Ter instalado o utilitário sqlcmd de linha de comandos

Pode transferir os utilitários bcp e sqlcmd a partir do [Centro de Transferências da Microsoft][Centro de Transferências da Microsoft].

### <a name="data-in-ascii-or-utf16-format"></a>Dados no formato ASCII ou UTF-16
Se estiver a experimentar este tutorial com os seus dados, estes têm de utilizar a codificação ASCII ou UTF-16, uma vez que o bcp não suporta UTF-8. 

## <a name="1-create-a-destination-table"></a>1. Criar uma tabela de destino
Defina uma tabela na Base de Dados SQL como a tabela de destino. As colunas na tabela têm de corresponder aos dados em cada linha do ficheiro de dados.

Para criar uma tabela, abra uma linha de comandos e utilize sqlcmd.exe para executar o seguinte comando:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
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

## <a name="next-steps"></a>Passos seguintes
Para migrar uma base de dados do SQL Server, veja [SQL Server database migration (Migração da base de dados do SQL Server)](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Sintaxe CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de Transferências da Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Nov16_HO2-->


