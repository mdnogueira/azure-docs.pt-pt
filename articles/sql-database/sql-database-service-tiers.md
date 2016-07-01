<properties
    pageTitle="Desempenho e opções da Base de Dados SQL: camadas de serviço | Microsoft Azure"
    description="Compare o desempenho da Base de Dados SQL e as funcionalidades de continuidade do negócio das camadas de serviços para balancear o custo e a capacidade, à medida que aumenta de dimensão."
    keywords="database options,database performance"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="05/13/2016"
    ms.author="carlrab"/>

# Opções e desempenho da Base de Dados SQL: compreender o que está disponível em cada camada de serviço

[Base de dados SQL do Azure](sql-database-technical-overview.md) tem várias camadas de serviços para processar cargas de trabalho diferentes. Pode alterar camadas de serviços em qualquer altura, sem períodos de indisponibilidade para a aplicação. Também pode [criar uma base de dados individual](sql-database-get-started.md) com características e preços definidos. Ou pode gerir várias bases de dados ao [criar um conjunto de bases de dados elásticas](sql-database-elastic-pool-create-portal.md). Em ambos os casos, as camadas incluem **Básica**, **Standard** e **Premium**. As opções de base de dados nestas camadas são semelhantes às de bases de dados individuais e conjuntos elásticos, mas existem considerações adicionais para os conjuntos elásticos. Este artigo fornece detalhes sobre camadas de serviços para bases de dados individuais e bases de dados elásticas.

## Camadas de serviços e opções de base de dados
As camadas de serviços Básica, Standard e Premium têm um SLA de 99,99% de tempo de atividade e oferecem desempenho previsível, opções de continuidade do negócio flexíveis, funcionalidades de segurança e faturação por hora. A tabela seguinte fornece exemplos de camadas mais adequadas para cargas de trabalho de aplicações diferentes.

| Camada de serviços | Cargas de trabalho de destino |
|---|---|
| **Básica** | Mais adequada para uma base de dados pequena, suportando normalmente uma única operação ativa num determinado momento. Os exemplos incluem bases de dados utilizadas para desenvolvimento ou testes, ou aplicações de dimensionamento pequeno raramente utilizadas. |
| **Standard** | A opção prática para a maioria das aplicações em nuvem, suportando várias consultas em simultâneo. Os exemplos incluem aplicações de grupo de trabalho ou Web. |
| **Premium** | Concebida para elevado volume transacional, suporta um grande número de utilizadores em simultâneo e requer o nível mais elevado de capacidades de continuidade do negócio. Os exemplos são bases de dados que suportam aplicações fundamentais. |

>[AZURE.NOTE] As edições Web e Business foram extinguidas. Leia [Perguntas Frequentes sobre o Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/), se pretender continuar a utilizar edições Web e Business.

## Camadas de serviços de bases de dados individuais e níveis de desempenho
Para bases de dados individuais, existem vários níveis de desempenho dentro de cada camada de serviços. Tem a flexibilidade de escolher o nível que melhor se adequa às exigências da sua carga de trabalho. Se precisar de aumentar ou reduzir verticalmente, pode alterar facilmente as camadas da base de dados, **sem períodos de indisponibilidade para a aplicação.** Consulte [Alterar Camadas de Serviços de Bases de Dados e Níveis de Desempenho](sql-database-scale-up.md), para detalhes.

As características de desempenho aqui listadas aplicam-se às bases de dados criadas com [SQL Database V12](sql-database-v12-whats-new.md). Em situações em que o hardware subjacente no Azure aloja várias bases de dados, a base de dados continua a obter um conjunto de recursos garantido, e as características de desempenho esperadas da base de dados não são afetadas.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

Para uma melhor compreensão das DTUs, consulte a [secção DTU](#understanding-dtus) neste tópico.

>[AZURE.NOTE] Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Capacidades e limites da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## Camadas de serviços do conjunto elástico e o desempenho nas eDTUs
Além de criar e dimensionar uma base de dados individual, também tem a opção de gerir várias bases de dados dentro de um [conjunto elástico](sql-database-elastic-pool.md). Todas as bases de dados num conjunto elástico partilham um conjunto comum de recursos. As características de desempenho são avaliadas por *Unidades de Transação de Bases de Dados elásticas* (eDTUs). Tal como nas bases de dados individuais, os conjuntos são fornecidos em três camadas de serviços: **Básica**, **Standard** e **Premium**. Para os conjuntos, estas três camadas de serviços definem também os limites de desempenho global e várias funcionalidades.

Os conjuntos permitem que bases de dados elásticas partilhem e consumam recursos DTU sem necessidade de atribuir um nível de desempenho específico às bases de dados no conjunto. Por exemplo, uma base de dados individual num conjunto Standard pode utilizar desde 0 eDTUs até à eDTU máxima de base de dados que definir quando configurar o conjunto. Isto permite que várias bases de dados com diferentes cargas de trabalho utilizem eficazmente os recursos eDTU disponíveis para o conjunto completo. Consulte [Considerações sobre preços e o desempenho de um conjunto elástico](sql-database-elastic-pool-guidance.md), para detalhes.

A tabela seguinte descreve as características de camadas de serviços de conjunto.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada base de dados dentro de um conjunto também respeita as características de base de dados individual para essa camada. Por exemplo, o conjunto Básico tem um limite de sessões máximo por conjunto de 4800-28800, mas uma base de dados individual dentro desse conjunto tem um limite de base de dados de 300 sessões (o limite para uma base de dados Básica individual, conforme especificado na secção anterior).

## Compreender as DTUs

[AZURE.INCLUDE [SQL DB DTU description](../../includes/sql-database-understanding-dtus.md)]

## Passos seguintes
- Saber mais sobre os preços para estas camadas em [Preços de Bases de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Saiba os detalhes de [conjuntos de bases de dados elásticas](sql-database-elastic-pool-guidance.md) e as [considerações sobre preços e o desempenho de conjuntos de bases de dados elásticas](sql-database-elastic-pool-guidance.md).
- Saiba como [Monitorizar, gerir e redimensionar conjuntos elásticos](sql-database-elastic-pool-manage-portal.md) e [Monitorizar o desempenho de bases de dados individuais](sql-database-single-database-monitor.md).
- Agora que sabe mais sobre as camadas de base de dados SQL, experimente com uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e saiba [como criar a sua primeira base de dados SQL](sql-database-get-started.md).



<!--HONumber=Jun16_HO2-->


