---
title: Carregar dados de exemplo para o SQL Data Warehouse | Microsoft Docs
description: Carregar dados de exemplo para o SQL Data Warehouse
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 1e0df958a2f18fe1e988168918e5cfd293f84e64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-sample-data-into-sql-data-warehouse"></a>Carregar dados de exemplo para o SQL Data Warehouse
Siga estes passos simples para carregar e consultar a base de dados de exemplo do Adventure Works. Estes scripts primeiro utilizam o sqlcmd para executar o SQL Server que irá criar tabelas e vistas. Depois de criar tabelas, os scripts irão utilizar o bcp para carregar dados.  Se ainda não tiver sqlcmd e bcp instalado, siga estas ligações para [instalar bcp] [ install bcp] e [instalar sqlcmd][install sqlcmd].

## <a name="load-sample-data"></a>Carregar dados de exemplo
1. Transferir o [Scripts de exemplo Adventure Works para SQL Data Warehouse] [ Adventure Works Sample Scripts for SQL Data Warehouse] ficheiro zip.
2. Extraia os ficheiros do zip transferido para um diretório no seu computador local.
3. Editar os ficheiros extraídos aw_create.bat e defina as seguintes variáveis encontradas na parte superior do ficheiro.  Certifique-se a sair sem espaços em branco entre o "=" e o parâmetro.  Seguem-se exemplos de como poderão ver as suas edições.
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. A partir de uma linha de comandos do Windows, execute o aw_create.bat editado.  Lembre-se de que estão no diretório onde guardou a versão de aw_create.bat editada.
   Este script será...
   
   * Remover Adventure Works tabelas ou vistas que já existem na base de dados
   * Criar as tabelas do Adventure Works e vistas
   * Carregar a tabela cada Adventure Works com o bcp
   * Validar o número de linha para cada tabela Adventure Works
   * Recolher estatísticas em cada coluna para cada tabela Adventure Works

## <a name="query-sample-data"></a>Dados de exemplo de consulta
Assim que tiver carregar alguns dados de exemplo para o SQL Data Warehouse, pode rapidamente executar algumas consultas.  Para executar uma consulta, ligar à base de dados Adventure Works recentemente criado no armazém de dados do SQL do Azure utilizando o Visual Studio e SSDT, conforme descrito no [consulta com o Visual Studio] [ query with Visual Studio] documento.

Exemplo de instrução select simple para obter todas as informações de funcionários:

```sql
SELECT * FROM DimEmployee;
```

Exemplo de uma consulta mais complexa utilizar construções como GROUP BY para ver a quantidade total de todas as vendas em cada dia:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Exemplo de um com uma cláusula WHERE para filtrar as ordens de antes de uma determinada data, SELECIONE:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Armazém de dados SQL suporta quase todas as construções de T-SQL que suporta o SQL Server.  Todas as diferenças estão documentadas na nossa [migre código] [ migrate code] documentação.

## <a name="next-steps"></a>Passos seguintes
Agora que já teve oportunidade de experimentar algumas consultas com dados de exemplo, verificar como [desenvolver][develop], [carregar][load], ou [migrar] [ migrate] ao SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[query with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrate code]: sql-data-warehouse-migrate-code.md
[install bcp]: sql-data-warehouse-load-with-bcp.md
[install sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works Sample Scripts for SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
