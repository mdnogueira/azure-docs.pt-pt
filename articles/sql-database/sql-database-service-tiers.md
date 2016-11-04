---
title: 'Desempenho e opções da Base de Dados SQL: camadas de serviço | Microsoft Docs'
description: Compare o desempenho da Base de Dados SQL e as funcionalidades de continuidade do negócio das camadas de serviços para balancear o custo e a capacidade, à medida que aumenta de dimensão.
keywords: opções da base de dados, desempenho da base de dados
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler

ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/10/2016
ms.author: carlrab

---
# Opções e desempenho da Base de Dados SQL: compreender o que está disponível em cada camada de serviço
A [Base de Dados SQL do Azure](sql-database-technical-overview.md) oferece três camadas de serviço com vários níveis de desempenho para processar diferentes cargas de trabalho. Cada nível de desempenho fornece um conjunto cada vez maior de recursos concebidos para proporcionar um débito cada vez mais elevado. Pode gerir cada base de dados na sua própria [camada de serviço](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) com o seu próprio nível de desempenho. Também pode gerir várias bases de dados num [conjunto elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) com um conjunto partilhado de recursos. Os recursos disponíveis para bases de dados autónomas são expressos em termos de Unidades de Transação da Base de Dados (DTUs) e para conjuntos elásticos em termos de DTUs elásticas ou eDTUs. Para obter mais informações sobre DTUs e eDTUs, veja [O que é uma DTU](sql-database-what-is-a-DTU.md). 

Em ambos os casos, as camadas de serviço incluem **Básico**, **Standard** e **Premium**. As opções de base de dados nestas camadas são semelhantes às de bases de dados autónomas e conjuntos elásticos, mas existem considerações adicionais para os conjuntos elásticos. Este artigo fornece detalhes sobre camadas de serviços para bases de dados autónomas e conjuntos elásticos.

## Camadas de serviços e opções de base de dados
As camadas de serviços Básica, Standard e Premium têm um SLA de 99,99% de tempo de atividade e oferecem desempenho previsível, opções de continuidade do negócio flexíveis, funcionalidades de segurança e faturação por hora. A tabela seguinte fornece exemplos de camadas mais adequadas para cargas de trabalho de aplicações diferentes.

| Camada de serviços | Cargas de trabalho de destino |
| --- | --- |
| **Básica** |Mais adequada para uma base de dados pequena, suportando normalmente uma única operação ativa num determinado momento. Os exemplos incluem bases de dados utilizadas para desenvolvimento ou testes, ou aplicações de dimensionamento pequeno raramente utilizadas. |
| **Standard** |A opção prática para a maioria das aplicações em nuvem, suportando várias consultas em simultâneo. Os exemplos incluem aplicações de grupo de trabalho ou Web. |
| **Premium** |Concebida para elevado volume transacional, suporta muitos utilizadores em simultâneo e requer o nível mais elevado de capacidades de continuidade do negócio. Os exemplos são bases de dados que suportam aplicações fundamentais. |

> [!NOTE]
> As edições Web e Business foram extinguidas. Leia as [FAQ sobre o Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/), se pretender continuar a utilizar edições Web e Business.
> 
> 

## Camadas de serviços de bases de dados autónomas e níveis de desempenho
Para bases de dados autónomas, existem vários níveis de desempenho dentro de cada camada de serviços. Tem a flexibilidade de escolher o nível que melhor se adequa às exigências da sua carga de trabalho. Se precisar de aumentar ou reduzir verticalmente, pode alterar facilmente as camadas da base de dados. Consulte [Alterar Camadas de Serviços de Bases de Dados e Níveis de Desempenho](sql-database-scale-up.md), para detalhes.

As características de desempenho aqui listadas aplicam-se às bases de dados criadas com [SQL Database V12](sql-database-v12-whats-new.md). Independentemente do número de bases de dados alojadas, a sua base de dados obtém um conjunto de recursos garantido, e as características de desempenho esperadas da base de dados não são afetadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Capacidades e limites da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 
> 

## Camadas de serviços do conjunto elástico e o desempenho nas eDTUs
Além de criar e dimensionar uma base de dados autónoma, também tem a opção de gerir várias bases de dados dentro de um [conjunto elástico](sql-database-elastic-pool.md). Todas as bases de dados num conjunto elástico partilham um conjunto comum de recursos. As características de desempenho são avaliadas por *Unidades de Transação de Bases de Dados elásticas* (eDTUs). Tal como nas bases de dados autónomas, os conjuntos são fornecidos em três camadas de serviços: **Básica**, **Standard** e **Premium**. Para os conjuntos, estas três camadas de serviços definem também os limites de desempenho global e várias funcionalidades.

Os conjuntos permitem que as bases de dados partilhem e consumam recursos de DTU sem ser necessário atribuir um nível de desempenho específico a cada base de dados no conjunto. Por exemplo, uma base de dados autónoma num conjunto Standard pode utilizar desde 0 eDTUs até ao número máximo de eDTUs de base de dados que definir quando configurar o conjunto. Os conjuntos permitem que várias bases de dados com diferentes cargas de trabalho utilizem eficazmente os recursos eDTU disponíveis para o conjunto completo. Consulte [Considerações sobre preços e o desempenho de um conjunto elástico](sql-database-elastic-pool-guidance.md), para detalhes.

A tabela seguinte descreve as características de camadas de serviços de conjunto.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada base de dados dentro de um conjunto também respeita as características de base de dados autónoma para essa camada. Por exemplo, o conjunto Básico tem um limite máximo de sessões por conjunto de 4 800–28 800, mas uma base de dados individual dentro de um conjunto Básico tem um limite de base de dados de 300 sessões.

## Escolher uma camada de serviços
Para decidir qual a camada de serviços a utilizar, comece por determinar se a base de dados deverá ser uma base de dados autónoma ou se fará parte de um conjunto elástico. 

### Escolher uma camada de serviços para uma base de dados autónoma
Para decidir qual a camada de serviços a utilizar para uma base de dados autónoma, comece por determinar as funcionalidades da base de dados de que precisa para escolher a sua edição da Base de Dados SQL:

* Tamanho da base de dados (2 GB no máximo para Basic, 250 GB no máximo para Standard e 500 GB a 1 TB no máximo para Premium, consoante o nível de desempenho)
* Período de retenção de cópias de segurança da base de dados (7 dias para Básico, 35 dias para Standard e 35 dias para Premium)

Depois de ter determinado a edição da Base de Dados SQL, estará pronto para determinar o nível de desempenho para a base de dados (o número de DTUs). Pode adivinhar e, em seguida, [aumentar ou reduzir vertical e dinamicamente](sql-database-scale-up.md) com base na experiência real. Pode ainda utilizar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para se obter uma estimativa do número de DTUs necessários. 

### Escolher uma camada de serviços para um conjunto de bases de dados elásticas.
Para decidir qual a camada de serviços a utilizar para um conjunto de bases de dados elásticas, comece por determinar as funcionalidades da base de dados de que precisa para escolher a camada de serviço para o seu conjunto.

* Tamanho da base de dados (2 GB para Básico, 250 GB para Standard e 500 GB para Premium)
* Período de retenção de cópias de segurança da base de dados (7 dias para Básico, 35 dias para Standard e 35 dias para Premium)
* Número de bases de dados por conjunto (400 para Básico, 400 para Standard e 50 para Premium)
* Armazenamento máximo por conjunto (117 GB para Básico, 1200 para Standard e 750 para Premium)

Depois de ter determinado a camada de serviços para o seu conjunto, estará pronto para determinar o nível de desempenho para o conjunto (eDTUs). Pode adivinhar e, em seguida, [aumentar ou reduzir vertical e dinamicamente](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) com base na experiência real. Pode utilizar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para obter uma estimativa do número de DTUs necessários para cada base de dados dentro de um conjunto, para o ajudar a determinar o limite superior do conjunto.

## Passos seguintes
* Saber mais sobre os preços para estas camadas em [Preços de Bases de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
* Saiba os detalhes de [conjuntos elásticos](sql-database-elastic-pool-guidance.md) e as [considerações sobre preços e desempenho de conjuntos elásticos](sql-database-elastic-pool-guidance.md).
* Saiba como [Monitorizar, gerir e redimensionar conjuntos elásticos](sql-database-elastic-pool-manage-portal.md) e [Monitorizar o desempenho de bases de dados autónomas](sql-database-single-database-monitor.md).
* Agora que sabe mais sobre as camadas de base de dados SQL, experimente com uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e saiba [como criar a sua primeira base de dados SQL](sql-database-get-started.md).

## Recursos adicionais
Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!--HONumber=Sep16_HO3-->


