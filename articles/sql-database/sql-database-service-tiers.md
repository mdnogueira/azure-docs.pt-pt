---
title: "Desempenho da Base de Dados SQL: camadas de serviço | Microsoft Docs"
description: "Compare as camadas de serviços da Base de Dados SQL."
keywords: "opções da base de dados, desempenho da base de dados"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/09/2016
ms.author: carlrab; janeng
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: ed598cfdaf5bebc1cf92894ba1f6c79f268ef3c3


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opções e desempenho da Base de Dados SQL: compreender o que está disponível em cada camada de serviço
A [Base de Dados SQL do Azure](sql-database-technical-overview.md) oferece três camadas de serviço, **Básico**, **Standard** e **Premium**, com vários níveis de desempenho para processar diferentes cargas de trabalho. Os níveis de desempenho superiores oferecem recursos crescentes concebidos para proporcionar um débito cada vez mais elevado. Pode alterar os [escalões de serviço e os níveis de desempenho dinamicamente](sql-database-scale-up.md) sem período de indisponibilidade. As camadas de serviços Básica, Standard e Premium têm um SLA de 99,99% de tempo de atividade, opções de continuidade do negócio flexíveis, funcionalidades de segurança e faturação por hora. 

Pode criar bases de dados autónomas com recursos dedicados no [nível de desempenho](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) selecionado. Também pode gerir várias bases de dados num [conjunto elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) no qual os recursos são partilhados entre bases de dados. Os recursos disponíveis para bases de dados autónomas são expressos em termos de Unidades de Transação da Base de Dados (DTUs) e para conjuntos elásticos em termos de DTUs elásticas (eDTUs). Para obter mais informações sobre DTUs e eDTUs, veja [O que é uma DTU?](sql-database-what-is-a-dtu.md). 

Em ambos os casos, as camadas de serviço incluem **Básico**, **Standard** e **Premium**. 

## <a name="choosing-a-service-tier"></a>Escolher uma camada de serviços
A tabela seguinte fornece exemplos de camadas mais adequadas para cargas de trabalho de aplicações diferentes.

| Camada de serviços | Cargas de trabalho de destino |
| :--- | --- |
| **Básica** | Mais adequada para uma base de dados pequena, suportando normalmente uma única operação ativa num determinado momento. Os exemplos incluem bases de dados utilizadas para desenvolvimento ou testes, ou aplicações de dimensionamento pequeno raramente utilizadas. |
| **Standard** |A opção go-to (ir) para aplicações na cloud com requisitos de desempenho E/S entre baixos e médios suporta várias consultas em simultâneo. Os exemplos incluem aplicações de grupo de trabalho ou Web. |
| **Premium** | Concebido para elevado volume transacional com requisitos de desempenho E/S elevados, suporta muitos utilizadores em simultâneo. Os exemplos são bases de dados que suportam aplicações fundamentais. |

Primeiro decida se pretende executar uma base de dados autónoma ou se pretende agrupar bases de dados que partilham recursos. Reveja as [considerações sobre o conjunto elástico](sql-database-elastic-pool-guidance.md). Para decidir sobre uma camada de serviços, comece por determinar as funcionalidades mínimas da base de dados de que necessita:

* Tamanho máximo da base de dados para bases de dados individuais (2 GB no máximo para Basic, 250 GB no máximo para Standard e 500 GB a 1 TB no máximo para Premium, em níveis de desempenho máximos)
* Armazenamento total máximo no caso de um conjunto elástico (117 GB para o Básico, 1200 para o Standard e 750 para o Premium)
* Número máximo de bases de dados por conjunto (400 para o Básico, 400 para o Standard e 50 para o Premium)
* Período de retenção de cópias de segurança da base de dados (7 dias para o Básico, 35 dias para o Standard e para o Premium)

Depois de ter determinado a camada de serviços mínima, estará pronto para determinar o nível de desempenho para a base de dados (o número de DTUs). Os níveis de desempenho S2 e S3 standard são, em muitos casos, um bom ponto de partida. Para bases de dados com requisitos de CPU ou E/S elevados, os níveis de desempenho Premium são o ponto inicial acertado. O Premium oferece mais CPU e começa com 10x mais E/S comparativamente com o nível de desempenho mais elevado do Standard.

Após escolher inicialmente um nível de desempenho pode, em seguida, aumentar ou reduzir verticalmente e de forma dinâmica a [base de dados individual](sql-database-scale-up.md) ou o [conjunto elástico](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) com base na experiência real. Para cenários de migração, pode ainda utilizar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para se obter uma estimativa do número de DTUs necessários. 

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Camadas de serviços de bases de dados autónomas e níveis de desempenho
Para bases de dados autónomas, existem vários níveis de desempenho dentro de cada camada de serviços. Tem a flexibilidade de escolher o nível que melhor se adequa às exigências da sua carga de trabalho. Se precisar de aumentar ou reduzir verticalmente, pode alterar facilmente as camadas da base de dados. Consulte [Alterar Camadas de Serviços de Bases de Dados e Níveis de Desempenho](sql-database-scale-up.md), para detalhes.

As características de desempenho aqui listadas aplicam-se às bases de dados criadas com [SQL Database V12](sql-database-v12-whats-new.md). Independentemente do número de bases de dados alojadas, a sua base de dados obtém um conjunto de recursos garantido, e as características de desempenho esperadas da base de dados não são afetadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Capacidades e limites da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Camadas de serviços do conjunto elástico e o desempenho nas eDTUs

Os conjuntos permitem que as bases de dados partilhem e consumam recursos de eDTU sem ser necessário atribuir um nível de desempenho específico a cada base de dados no conjunto. Por exemplo, uma base de dados autónoma num conjunto Standard pode utilizar desde 0 eDTUs até ao número máximo de eDTUs de base de dados que definir quando configurar o conjunto. Os conjuntos permitem que várias bases de dados com diferentes cargas de trabalho utilizem eficazmente os recursos eDTU disponíveis para o conjunto completo. Consulte [Considerações sobre preços e o desempenho de um conjunto elástico](sql-database-elastic-pool-guidance.md), para detalhes.

A tabela seguinte descreve as características de camadas de serviços de conjunto.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada base de dados dentro de um conjunto também respeita as características de base de dados autónoma para essa camada. Por exemplo, o conjunto Básico tem um limite máximo de sessões por conjunto de 4 800–28 800, mas uma base de dados individual dentro de um conjunto Básico tem um limite de base de dados de 300 sessões.

## <a name="next-steps"></a>Passos seguintes

* Saiba os detalhes de [conjuntos elásticos](sql-database-elastic-pool-guidance.md) e as [considerações sobre preços e desempenho de conjuntos elásticos](sql-database-elastic-pool-guidance.md).
* Saiba como [Monitorizar, gerir e redimensionar conjuntos elásticos](sql-database-elastic-pool-manage-portal.md) e [Monitorizar o desempenho de bases de dados autónomas](sql-database-single-database-monitor.md).
* Agora que sabe mais sobre as camadas de base de dados SQL, experimente com uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e saiba [como criar a sua primeira base de dados SQL](sql-database-get-started.md).




<!--HONumber=Dec16_HO2-->


