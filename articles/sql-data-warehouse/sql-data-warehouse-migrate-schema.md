---
title: Migrar o esquema para o SQL Data Warehouse | Microsoft Docs
description: "Sugestões para migrar o esquema para o Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: 07ca2321852e276502187e768177e7e82bdfd080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migrar as esquemas para o SQL Data Warehouse
Orientações para migrar as esquemas SQL ao SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planear a migração de esquema

Quando planear uma migração, consulte o [descrição geral da tabela] [ table overview] para se familiarizar com considerações de design de tabela, por exemplo estatísticas, distribuição, a criação de partições e indexação.  Também lista alguns [tabela funcionalidades não suportadas] [ unsupported table features] e as respetivas soluções.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Utilizar esquemas definido pelo utilizador para consolidar as bases de dados

A carga de trabalho existente provavelmente tem mais do que uma base de dados. Por exemplo, um armazém de dados do SQL Server pode incluir uma base de dados de teste, uma base de dados do armazém de dados e algumas bases de dados do data mart. Nesta topologia, cada base de dados é executado como uma carga de trabalho separada com as políticas de segurança separados.

Por outro lado, o SQL Data Warehouse executa a carga de trabalho de armazém de dados completo dentro de uma base de dados. Cruzada da base de dados não são permitidas associações. Por conseguinte, o SQL Data Warehouse espera todas as tabelas utilizadas ao armazém de dados armazenados dentro de uma base de dados.

Recomendamos que utilize esquemas definido pelo utilizador para consolidar a carga de trabalho existente para uma base de dados. Para obter exemplos, consulte [esquemas definido pelo utilizador](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Utilize os tipos de dados compatível
Modificar os tipos de dados para ser compatível com o SQL Data Warehouse. Para obter uma lista dos tipos de dados suportados e não suportados, consulte [tipos de dados][data types]. Este tópico fornece soluções para os tipos não suportados. Também fornece uma consulta para identificar os tipos de existentes que não são suportados no SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimizar o tamanho de linha
Para melhor desempenho, estará a minimizar o comprimento de linha das suas tabelas. Uma vez que os comprimentos de linhas mais pequeno levar para um melhor desempenho, utilize os tipos de dados mais pequeno que funcionam para os seus dados. 

Para a largura da linha de tabela, o PolyBase tem um limite de 1 MB.  Se pretender carregar dados para o SQL Data Warehouse com o PolyBase, Atualize as tabelas ter larguras de linha máximo de menos de 1 MB. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Especifique a opção de distribuição
O SQL Data Warehouse é um sistema de base de dados distribuída. Cada tabela é distribuída ou replicada em todos os nós de computação. Há uma opção de tabela que lhe permite especificar como distribuir os dados. As opções são round robin, replicados, ou distribuído de hash. Cada um tem os profissionais de TI e contras. Se não especificar a opção de distribuição, o SQL Data Warehouse irá utilizar round robin como predefinição.

- Round robin é a predefinição. É mais simples utilizar e carrega dados tão rápidos como possíveis, mas as associações necessitarão de movimento de dados que abrandar o desempenho das consultas.
- Replicadas armazena uma cópia da tabela em cada nó de computação. As tabelas replicadas são performant porque não necessitam de movimento de dados para associações e agregações. Necessita de armazenamento adicional e, por conseguinte, funciona melhor para tabelas mais pequenas.
- Hash distribuído, distribui as linhas de todos os nós através de uma função de hash. As tabelas hash distribuída estão centro do SQL Data Warehouse desde que foram concebidos para fornecer o desempenho de consulta elevado em tabelas grandes. Esta opção requer alguns planeamento selecionar a coluna melhor em que pretende distribuir os dados. No entanto, se não escolher a melhor coluna pela primeira vez, pode facilmente novamente distribuir os dados de uma coluna diferente. 

Para escolher a melhor opção de distribuição para cada tabela, consulte [distribuídas tabelas](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>Passos seguintes
Depois de ter migrado com êxito o seu esquema de base de dados ao SQL Data Warehouse, avance para um dos seguintes artigos:

* [Migrar os dados][Migrate your data]
* [Migrar o seu código][Migrate your code]

Para obter mais informações sobre as melhores práticas do SQL Data Warehouse, consulte o [melhores práticas] [ best practices] artigo.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
