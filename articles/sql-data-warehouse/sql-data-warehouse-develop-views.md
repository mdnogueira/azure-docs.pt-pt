---
title: Utilizar vistas de T-SQL no Azure SQL Data Warehouse | Microsoft Docs
description: "Sugestões para utilizar as vistas de Transact-SQL no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: d2a03be810bd7f792876607ec735eb578b65a3b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="views-in-sql-data-warehouse"></a>Vistas do armazém de dados do SQL Server
As vistas são particularmente útil no armazém de dados do SQL Server. Podem ser utilizadas num número de diferentes formas para melhorar a qualidade da sua solução.  Este artigo realça alguns exemplos de como enriquecer a sua solução com vistas, bem como as limitações que precisam de ser considerados.

> [!NOTE]
> Sintaxe para `CREATE VIEW` não é abordada neste artigo. Consulte o [Criar vista] [ CREATE VIEW] artigo no MSDN para estas informações de referência.
> 
> 

## <a name="architectural-abstraction"></a>Abstração da arquitetura
É um padrão de aplicação muito comum voltar a criar tabelas criar tabela AS SELECIONE (CTAS) seguido de um objeto mudar o nome padrão enfrenta carregar dados a utilizar.

O exemplo abaixo adiciona registos data novo a uma dimensão de data. Tenha em atenção a como um novo tabble, DimDate_New, é criado pela primeira vez e, em seguida, mudar o nome para substituir a versão original da tabela.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

No entanto, esta abordagem pode resultar em tabelas de apresentação e disappearing da vista de um utilizador, bem como as mensagens de erro de "tabela não existe". Vistas podem ser utilizadas para fornecer aos utilizadores com uma camada de apresentação consistente, embora os objetos subjacentes são mudados. Ao fornecer aos utilizadores aceder aos dados através de um vistas, significa que os utilizadores não precisam de ter visibilidade das tabelas subjacentes. Isto fornece uma experiência de utilizador consistente enquanto garantir que o armazém de dados designers pode evoluir o modelo de dados e maximizar o desempenho utilizando CTAS durante o processo de carregamento de dados.    

## <a name="performance-optimization"></a>Otimização do desempenho
Vistas também podem ser utilizadas para impor as associações entre as tabelas com otimização de desempenho. Por exemplo, uma vista pode incorporar uma chave de distribuição redundante como parte dos critérios de joining para minimizar o movimento de dados.  Outra vantagem de uma vista pode ser para forçar uma consulta específica ou sugestão de associação. Utilizar vistas desta forma, garante que associações são sempre executadas de forma ideal, evitando a necessidade dos utilizadores memorizem a construção correta para as respetivas associações.

## <a name="limitations"></a>Limitações
As vistas do armazém de dados do SQL Server são apenas metadados.  Por conseguinte não estão disponíveis as seguintes opções:

* Não há nenhuma opção de enlace de esquema
* Tabelas base não podem ser atualizadas através da vista
* Não não possível criar vistas através de tabelas temporárias
* Não são suportadas para o expandir / sugestões NOEXPAND
* Não existem nenhum vistas indexadas no SQL Data Warehouse

## <a name="next-steps"></a>Passos seguintes
Para obter mais sugestões de desenvolvimento, veja [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)][SQL Data Warehouse development overview].
Para `CREATE VIEW` sintaxe consulte [Criar vista][CREATE VIEW].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
