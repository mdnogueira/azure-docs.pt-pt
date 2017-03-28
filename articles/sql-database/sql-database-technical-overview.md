---
title: "O que é o serviço Base de Dados SQL do Azure? | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "introdução à sql, introdução sql, o que é a base de dados sql"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: c505844cc2b7c745a1106b3c446833fb206ca98a
ms.lasthandoff: 03/17/2017

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>O que é a Base de Dados SQL? Introdução à Base de Dados SQL
A Base de Dados SQL é um serviço de bases de dados relacionais na cloud da Microsoft baseado no motor Microsoft SQL Server líder de mercado e com capacidade para processar cargas de trabalho críticas para missões. A Base de Dados SQL proporciona um desempenho previsível em vários níveis de serviço, escalabilidade dinâmica sem períodos de indisponibilidade, continuidade de negócio incorporada e proteção de dados — tudo quase sem administração. Estas capacidades permitem-lhe concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de alocar tempo e recursos valiosos para a gestão de máquinas virtuais e de infraestruturas. Uma vez que se baseia no motor do [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), a Base de Dados SQL suporta as ferramentas, bibliotecas e APIs existentes do SQL Server. Como resultado, é mais fácil desenvolver soluções novas, mover as suas soluções do SQL Server existentes e alargá-las para a cloud da Microsoft sem ter de aprender competências novas.

Este artigo é uma introdução aos conceitos e às funcionalidades essenciais da Base de Dados SQL relacionados com desempenho, escalabilidade e capacidade de gestão, com ligações para explorar detalhes. Veja estes guias de introdução para começar:
 - [Criar uma base de dados SQL no portal do Azure](sql-database-get-started-portal.md)  
 - [Criar uma base de dados SQL com a CLI do Azure](sql-database-get-started-cli.md)
 - [Criar uma base de dados SQL utilizando o PowerShell](sql-database-get-started-powershell.md)

Para um conjunto de amostras de CLI do Azure e PowerShell, veja:
 - [Amostras de CLI do Azure para a Base de Dados SQL do Azure](sql-database-cli-samples.md)
 - [Amostras de Azure PowerShell para a Base de Dados SQL do Azure](sql-database-powershell-samples.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste o desempenho e dimensione a capacidade sem períodos de indisponibilidade
O serviço Base de Dados SQL oferece três escalões de serviço - Básico, Standard e Premium. Cada escalão de serviço oferece [diferentes níveis de desempenho e capacidades](sql-database-service-tiers.md) para suportar desde cargas de trabalho leves até pesadas. Pode criar a sua primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, [alterar o escalão de serviço](sql-database-service-tiers.md) manual ou programaticamente em qualquer altura para satisfazer as necessidades da sua solução. Pode fazê-lo sem causar períodos de indisponibilidade para a aplicação e para os seus clientes. A escalabilidade dinâmica permite que a base de dados responda de forma transparente a requisitos de recursos em rápida mutação e permite-lhe pagar apenas pelos recursos de que precisa, quando precisa.

## <a name="elastic-pools-to-maximize-resource-utilization"></a>Conjuntos elásticos para maximizar a utilização de recursos
Para muitas empresas e aplicações, ser capaz de criar bases de dados únicas e aumentar e reduzir o desempenho a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio. Os [conjuntos elásticos](sql-database-elastic-pool.md) foram concebidos para resolver este problema. O conceito é simples. Os recursos de desempenho são alocados a um conjunto em vez de a uma base de dados individual e paga os recursos de desempenho coletivo do conjunto em vez do desempenho da base de dados individual. Com os conjuntos elásticos, não tem de se concentrar em ajustar o desempenho da base de dados à medida que a procura pelos recursos flutua. As bases de dados agrupadas consomem os recursos de desempenho do conjunto elástico conforme necessário. As bases de dados agrupadas consomem os limites do conjunto, mas não os excedem, pelo que os seus custos se mantêm previsíveis, mesmo que a utilização das bases de dados não. Além disso, pode [adicionar e remover bases de dados do conjunto](sql-database-elastic-pool-manage-portal.md), dimensionando a sua aplicação de um punhado de bases de dados para milhares, tudo dentro de um orçamento controlado por si. Por fim, também pode controlar os recursos mínimos e máximos disponíveis para as bases de dados do conjunto, para garantir que nenhuma base de dados do conjunto utiliza todos os recursos e que todas as bases de dados agrupadas têm uma quantidade mínima garantida de recursos. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="blend-single-databases-with-pooled-databases"></a>Combinar bases de dados individuais com bases de dados agrupadas
Qualquer que seja a sua opção — bases de dados individual ou conjuntos elásticos —, não está limitado. Pode misturar bases de dados individuais com conjuntos elásticos e alterar os escalões de serviço das bases de dados individuais e dos conjuntos elásticos rápida e facilmente para os adaptar à sua situação. Além disso, com a capacidade e a o alcance do Azure, pode combinar outros serviços do Azure com a Base de Dados SQL para satisfazer as necessidades de design da sua aplicação única, aumentar as eficiências de custos e recursos e conseguir novas oportunidades de negócio.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas
Mas como pode comparar o desempenho relativo das bases de dados individuais e dos conjuntos elásticos? Como pode saber qual é o ponto de paragem certo quando aumenta ou reduz verticalmente o desempenho? Utilize as ferramentas de [monitorização de desempenho ](sql-database-performance.md) e de [alertas incorporadas](sql-database-insights-alerts-portal.md) em conjunto com as classificações de desempenho baseadas em [Unidades de Transação da Base de Dados (DTUs) de bases de dados individuais e DTUs elásticas (eDTUs) de conjuntos elásticos](sql-database-what-is-a-dtu.md). Com estas ferramentas, pode avaliar rapidamente o impacto de aumentar ou reduzir verticalmente consoante as necessidades de desempenho atuais ou do projeto. Consulte o artigo [Opções e desempenho da Base de Dados SQL: compreender o que está disponível em casa escalão de serviço](sql-database-service-tiers.md) para obter mais detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha a sua aplicação e o seu negócio operacionais
O contrato de nível de serviço [(SLA)](http://azure.microsoft.com/support/legal/sla/) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Em cada base de dados SQL, pode tirar partido da segurança, da tolerância a falhas e da [proteção de dados](sql-database-automated-backups.md) incorporadas, que, de outra forma, teria de comprar ou conceber e criar e gerir. Com Base de dados SQL, cada camada de serviço oferece um conjunto completo de funcionalidades de continuidade de negócio e as opções que pode utilizar para aceder e executar e manter-se dessa forma. Pode utilizar o [restauro para um ponto anterior no tempo](sql-database-recovery-using-backups.md) para repor um estado anterior de uma base de dados, até 35 dias. Pode configurar a [retenção de cópias de segurança de longa duração](sql-database-long-term-retention.md) para armazenar as cópias de segurança num cofre seguro durante um máximo de dez anos. Além disso, se o datacenter que aloja as suas bases de dados sofrer um período de indisponibilidade, pode restaurar bases de dados de [cópias georredundantes de cópias de segurança recentes](sql-database-recovery-using-backups.md). Se for necessário, também pode configurar [réplicas georredundantes legíveis](sql-database-geo-replication-overview.md) numa ou mais regiões para ativação pós-falha rápida em caso de indisponibilidade de um datacenter. Também pode utilizar estas réplicas para um desempenho de leitura mais rápido em regiões geográficas diferentes ou para [atualizações de aplicações sem períodos de indisponibilidade](sql-database-manage-application-rolling-upgrade.md). 

![Georreplicação da Base de Dados SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)

Consulte o artigo [Continuidade do Negócio](sql-database-business-continuity.md) para obter detalhes sobre as diferentes funcionalidades de continuidade do negócio disponíveis para diferentes escalões de serviço.

## <a name="secure-your-data"></a>Proteja os seus dados
O SQL Server tem um tradição de segurança de dados que a Base de Dados SQL mantém, com funcionalidades que limitam o acesso, protegem os dados e ajudam a monitorizar a atividade. Consulte o artigo [Proteger a Base de Dados SQL](sql-database-security-overview.md) para ver um resumo rápido das opções de segurança de que dispõe na Base de Dados SQL. Consulte o [Centro de Segurança do Motor de Base de Dados do SQL Server e da Base de Dados SQL](https://msdn.microsoft.com/library/bb510589), para ficar com uma perspetiva mais abrangente das funcionalidades de segurança. Visite também o [Centro de Fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/security/) para obter informações sobre a segurança da plataforma do Azure.

## <a name="next-steps"></a>Passos seguintes
Agora que já leu uma introdução à Base de Dados SQL e respondeu à pergunta "O que é a Base de Dados SQL?", está pronto para:

* Consultar a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para ver comparações de preços e calculadoras de bases de dados individuais e conjuntos elásticos.
* Saber mais sobre [conjuntos elásticos](sql-database-elastic-pool.md).
* Começar por [criar a sua primeira base de dados](sql-database-get-started.md).
* Criar a sua primeira aplicação em C#, Java, Node.js, PHP, Python ou Ruby: [Bibliotecas de ligações para a Base de Dados SQL e o SQL Server](sql-database-libraries.md)

