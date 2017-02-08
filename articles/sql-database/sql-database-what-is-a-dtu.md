---
title: "Base de Dados SQL: O que é uma DTU? | Microsoft Docs"
description: "Compreender o que é uma unidade de transação da Base de Dados SQL."
keywords: "opções da base de dados, desempenho da base de dados"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3d619f5d6959594ee9b139d611d7e45390a40c55
ms.openlocfilehash: d9b0eaa48d71f3ecf0a23f3bddb3c777c98afea7


---
# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Explicar as Unidades de Transação da Base de Dados (DTUs) e as Unidades de Transação da Base de Dados Elástica (eDTUs)
Este artigo explica as Unidades de Transação da Base de Dados (DTUs) e as Unidades de Transação da Base de Dados Elástica (eDTUs) e o que acontece quando atinge o limite máximo de DTUs ou eDTUs.  

## <a name="what-are-database-transaction-units-dtus"></a>O que são Unidades de Transação da Base de Dados (DTUs)
Uma DTU é uma unidade de medida dos recursos cuja disponibilidade é garantida para uma base de dados SQL do Azure SQL única a um nível de desempenho específico dentro de uma [camada de serviço de base de dados única](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels). Uma DTU é uma medida combinada de CPU, memória e E/S de dados e E/S de registos de transações num rácio determinado por uma carga de trabalho de referência do OLTP, concebida para ser típica de cargas de trabalho do OLTP do mundo real. Duplicar as DTUs aumentando o nível de desempenho de uma base de dados equivale a duplicar o conjunto de recursos disponíveis nessa base de dados. Por exemplo, uma base de dados Premium P11 com 1750 DTUs fornece 350 x mais potência de computação de DTUs do que uma base de dados básica com cinco DTUs. Para compreender a metodologia por detrás da carga de trabalho de referência do OLTP utilizada para determinar a combinação de DTU, veja [Descrição geral de referência da Base de Dados SQL](sql-database-benchmark-overview.md).

![Introdução à Base de Dados SQL: DTUs de base de dados individual por camada e nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Pode [alterar as camadas de serviços](sql-database-scale-up.md) em qualquer altura, com um período mínimo de indisponibilidade para a aplicação (em média, menos de quatro segundos). Para muitas empresas e aplicações, ser capaz de criar bases de dados e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Para este cenário, utilize um conjunto elástico com um determinado número de eDTUs.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>O que são Unidades de Transação da Base de Dados Elástica (eDTUs)
Uma eDTU é uma unidade de medida do conjunto de recursos (DTUs) que podem ser partilhados entre um conjunto de bases de dados num servidor SQL do Azure – denominado [conjunto elástico](sql-database-elastic-pool.md). Os conjuntos elásticos fornecem uma solução económica e simples para gerir os objetivos de desempenho de várias bases de dados que tenham padrões de utilização extremamente variáveis e imprevisíveis. Veja [conjuntos elásticos e camadas de serviço](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) para obter mais informações.

![Introdução à Base de Dados SQL: eDTUs por camada e nível](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A um conjunto é atribuído um número definido de eDTUs, por um preço definido. Dentro do conjunto elástico, é dada às bases de dados individuais a flexibilidade para se dimensionarem automaticamente dentro dos limites configurados. Uma base de dados sobrecarregada pode consumir mais eDTUs para responder às necessidades, enquanto as bases de dados sujeitas a cargas mais leves consomem menos. As bases de dados que não estão sujeitas a qualquer carga não consomem eDTUs. O aprovisionamento de recursos para o conjunto completo e não para bases de dados individuais simplifica as tarefas de gestão. Além disso, tem um orçamento previsível para o conjunto.

Podem ser adicionais mais eDTUs a um conjunto existente sem qualquer período de indisponibilidade da base de dados e sem impacto nas bases de dados do conjunto. Do mesmo modo, se as eDTUs adicionais já não forem necessárias, podem ser removidas de um conjunto existente em qualquer momento. Pode adicionar ou subtrair bases de dados ao conjunto, ou limitar a quantidade de eDTUs que uma base de dados pode utilizar quando está sujeita a muita carga, para reservar eDTUs para outras bases de dados. Se uma base de dados estiver a subutilizar recursos de forma previsível, pode removê-la do conjunto e configurá-la como uma base de dados individual com uma quantidade previsível dos recursos de que precisa.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Como posso determinar o número de DTUs necessárias para a minha carga de trabalho?
Se pretender migrar a carga de trabalho de uma máquina virtual do SQL Server ou no local existente para a Base de Dados SQL do Azure, pode utilizar a [Calculadora de DTUs](http://dtucalculator.azurewebsites.net/) para se aproximar do número de DTUs necessárias. No caso da carga de trabalho de uma Base de Dados SQL do Azure existente, pode utilizar [Informações de Desempenho de Consultas de Base de Dados SQL](sql-database-query-performance.md) para compreender o consumo de recursos da sua base de dados (DTUs) para obter um conhecimento mais profundo de como otimizar a sua carga de trabalho. Também pode utilizar a DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) para obter as informações de consumo de recursos da última hora. Em alternativa, a vista de catálogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) também pode ser consultada para obter os mesmos dados dos últimos 14 dias, embora a uma fidelidade inferior das médias de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Como posso saber se poderia beneficiar de um conjunto elástico de recursos?
Os conjuntos são adequados para um grande número de bases de dados com padrões de utilização específicos. Para uma determinada base de dados, este padrão caracteriza-se por uma utilização média baixa com picos de utilização relativamente raros. A Base de Dados SQL avalia automaticamente o histórico de utilização de recursos de bases de dados num servidor de Base de Dados SQL existente e recomenda a configuração de conjunto adequada no portal do Azure. Para obter mais informações, consulte [When should an elastic pool be used? (Quando deve ser utilizado um conjunto elástico?)](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>O que acontece quando atinjo o limite máximo de DTUs
Os níveis de desempenho são calibrados e geridos para fornecerem os recursos necessários para executar a carga de trabalho da base de dados até aos limites máximos permitidos para a camada de serviço/nível de desempenho selecionado. Se a carga de trabalho estiver a chegar aos limites num dos limites de CPU, E/S de Dados ou E/S de Registo, continua a receber os recursos no nível máximo permitido, mas é provável que sinta um aumento da latência nas suas consultas. Estes limites não resultam em erros, mas num abrandamento da carga de trabalho, a menos que o abrandamento se torne tão acentuado que as consultas comecem a exceder o tempo limite. Se estiver a atingir os limites de número de sessões/pedidos (threads de trabalho) de utilizadores em simultâneo máximo permitido, serão apresentados erros específicos. Veja [Limites de recursos da Base de Dados SQL do Azure](sql-database-resource-limits.md) para obter informações sobre o limite dos recursos além da CPU, memória, E/S de dados e E/S de registos de transações.

## <a name="next-steps"></a>Passos seguintes
* Veja [Camada de serviço](sql-database-service-tiers.md) para obter informações sobre as DTUs e eDTUs disponíveis para bases de dados únicas e para conjuntos elásticos.
* Veja [Limites de recursos da Base de Dados SQL do Azure](sql-database-resource-limits.md) para obter informações sobre o limite dos recursos além da CPU, memória, E/S de dados e E/S de registos de transações.
* Veja [SQL Database Query Performance Insight (Informações de Desempenho de Consultas de Base de Dados SQL)](sql-database-query-performance.md) para compreender o seu consumo (de DTUs).
* Veja [Descrição geral de referência da Base de Dados SQL](sql-database-benchmark-overview.md) para compreender a metodologia por detrás da carga de trabalho de referência do OLTP utilizada para determinar a combinação de DTU.



<!--HONumber=Jan17_HO1-->


