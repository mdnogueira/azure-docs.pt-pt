---
title: "Migrar a sua solução ao SQL Data Warehouse | Microsoft Docs"
description: "Orientações de migração para colocar a sua solução de plataforma do Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/27/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 771b9456e66b8a1e41f72340b695b19e2adaf793
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migrar a sua solução ao Azure SQL Data Warehouse
Ver o que está envolvido no migrar uma solução de base de dados existente para o Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>A carga de trabalho de perfil
Antes de migrar, quer ser que determinadas SQL Data Warehouse é a solução certa para a carga de trabalho. O SQL Data Warehouse é um sistema distribuído concebido para efetuar análise de dados de grandes dimensões.  Migrar para o SQL Data Warehouse requer algumas alterações de estrutura que não são demasiado rígido para compreender mas poderão demorar algum tempo a implementar. Se a sua empresa precisar de um armazém de dados de classe empresarial, são as vantagens, mas o esforço do utilizador. No entanto, se não precisa do poder do SQL Data Warehouse, é mais económico para utilizar o SQL Server ou SQL Database do Azure.

Considere utilizar o SQL Data Warehouse quando tiver:
- Ter um ou mais Terabytes de dados
- Pretende executar a análise na grandes quantidades de dados
- Necessita da capacidade de dimensionar a computação e armazenamento 
- Pretende reduzir os custos ao colocar em pausa recursos de computação quando não precisar deles.

Não utilize o SQL Data Warehouse para cargas de trabalho (OLTP) operacionais que tem:
- Frequência alta lê e escreve
- Grande número de singleton seleciona
- Volumes elevados de inserções de linha única
- Linha por linha tem de processar
- Formatos incompatíveis (JSON, XML)


## <a name="plan-the-migration"></a>Planear a migração

Depois de decidir migrar uma solução existente para o SQL Data Warehouse, é importante planear a migração antes de começar a utilizar. 

É um objetivo de planeamento para garantir que os dados, esquemas de tabela e o código são compatíveis com o SQL Data Warehouse. Existem algumas diferenças de compatibilidade para contornar entre o sistema atual e o SQL Data Warehouse. Plus, a migrar grandes quantidades de dados para o tempo de demora do Azure. Um planeamento cuidado expedites colocar os seus dados para o Azure. 

Outro objetivo do planeamento é efetuar ajustes de design para garantir a que sua solução tira partido do desempenho que do armazém de dados do SQL Server foi concebido para fornecer elevada de consulta. Conceber armazéns de dados para a escala apresenta padrões e abordagens tradicionais, por isso, não são sempre o melhor de design diferentes. Embora pode efetuar alguns ajustes de design após a migração, efetuar as alterações mais cedo no processo irá poupar tempo posteriormente.

Para efetuar uma migração com êxito, terá de migrar as esquemas de tabela, o código e os dados. Para obter orientações sobre estes tópicos de migração, consulte:

-  [Migrar as esquemas](sql-data-warehouse-migrate-schema.md)
-  [Migrar o seu código](sql-data-warehouse-migrate-code.md)
-  [Migrar os dados](sql-data-warehouse-migrate-data.md). 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Passos seguintes
O CAT (equipa de Consultadora dos clientes) também tem algumas excelente documentação de orientação do armazém de dados do SQL Server, que publicam através de blogues.  Observe respetivo artigo, [migrar dados para o Azure SQL Data Warehouse na prática] [ Migrating data to Azure SQL Data Warehouse in practice] para orientações adicionais sobre a migração.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
