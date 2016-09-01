<properties
    pageTitle="O que é a Base de Dados SQL? Introdução à Base de Dados SQL | Microsoft Azure"
    description="Obtenha uma introdução à Base de Dados SQL: detalhes técnicos e capacidades do sistema de gestão de bases de dados relacionais (RDBMS) da Microsoft na nuvem."
    keywords="introdução à sql, introdução sql, o que é a base de dados sql"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# O que é a Base de Dados SQL? Introdução à Base de Dados SQL

A Base de Dados SQL é um serviço de base de dados relacional na nuvem baseado no motor Microsoft SQL Server líder de mercado, com capacidades fundamentais. A Base de Dados SQL proporciona um desempenho previsível, escalabilidade sem períodos de indisponibilidade, continuidade do negócio e proteção de dados — tudo quase sem administração. Pode concentrar-se no desenvolvimento rápido de aplicações e acelerar o seu tempo de colocação no mercado, em vez de gerir máquinas virtuais e infraestruturas. Como se baseia no motor do [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), a Base de Dados SQL suporta as ferramentas, bibliotecas e APIs existentes do SQL Server, o que facilita a transição e a expansão para a nuvem.

Este artigo é uma introdução aos conceitos e às funcionalidades essenciais da Base de Dados SQL relacionados com desempenho, escalabilidade e capacidade de gestão, com ligações para explorar detalhes. Se estiver pronto para começar, pode [Criar a sua primeira base de dados SQL](sql-database-get-started.md) ou [Criar um conjunto de bases de dados elásticas](sql-database-elastic-pool-create-portal.md) em minutos. Se pretender uma descrição mais aprofundada, veja este vídeo de 30 minutos.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## Ajuste o desempenho e dimensione a capacidade sem períodos de indisponibilidade

As bases de dados SQL estão disponíveis nos *escalões de serviço* Básico, Standard e Premium. Cada escalão de serviço oferece [diferentes níveis de desempenho e capacidades](sql-database-service-tiers.md) para suportar desde cargas de trabalho leves até pesadas. Pode criar a primeira aplicação numa base de dados pequena por alguns euros por mês e, em seguida, [alterar o escalão de serviço](sql-database-scale-up.md) manualmente ou programaticamente em qualquer altura à medida que a sua aplicação se torna viral em todo o mundo, sem períodos de indisponibilidade para a sua aplicação ou para os seus clientes.

Para muitas empresas e aplicações, ser capaz de criar bases de dados e aumentar e reduzir o desempenho de uma base de dados individual a pedido é suficiente, sobretudo se os padrões de utilização forem relativamente previsíveis. No entanto, se tiver padrões de utilização imprevisíveis, pode tornar-se difícil gerir os custos e o seu modelo de negócio.

Os [conjuntos elásticos](sql-database-elastic-pool.md) na Base de Dados SQL resolvem este problema. O conceito é simples. O utilizador aloca o desempenho a um conjunto e paga pelo desempenho coletivo do conjunto em vez do desempenho de bases de dados individuais. Não precisa de aumentar ou reduzir o desempenho da base de dados. As bases de dados no conjunto, denominadas *bases de dados elásticas*, aumentam e reduzem verticalmente de forma automática para responder à procura. As bases de dados elásticas consomem, mas não excedem os limites do conjunto, pelo que os seus custos mantêm-se previsíveis, mesmo que a utilização das bases de dados não. Além disso, pode [adicionar e remover bases de dados do conjunto](sql-database-elastic-pool-manage-portal.md), dimensionando a sua aplicação de um punhado de bases de dados para milhares, tudo dentro de um orçamento controlado por si. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Seja qual for a sua opção —individual ou elástica — não fica preso a ela. Pode misturar bases de dados individuais com conjuntos de bases de dados elásticas e alterar os escalões de serviço de bases de dados individuais e conjuntos para criar estruturas inovadoras. Além disso, com a capacidade e a o alcance do Azure, pode combinar os serviços do Azure com a Base de Dados SQL para satisfazer as necessidades de design da sua aplicação moderna e única, aumentar as eficiências de custos e recursos, e conseguir novas oportunidades de negócio.

Mas como pode comparar o desempenho relativo de bases de dados e conjuntos de bases de dados? Como pode saber qual é o ponto de paragem certo quando aumenta ou reduz verticalmente o desempenho? A resposta é a Unidade de Transação de Base de Dados (DTU) para bases de dados individuais e a DTU elástica (eDTU) para bases de dados elásticas e conjuntos de bases de dados. Consulte o artigo [Opções e desempenho da Base de Dados SQL: compreender o que está disponível em casa escalão de serviço](sql-database-service-tiers.md) para obter mais detalhes.

## Mantenha a sua aplicação e o seu negócio operacionais

O contrato de nível de serviço [(SLA)](http://azure.microsoft.com/support/legal/sla/) do Azure líder da indústria, que garante 99,99% de disponibilidade, com tecnologia de uma rede global de datacenters geridos pela Microsoft, ajuda a manter a sua aplicação operacional 24 horas por dia, sete dias por semana. Com todas as bases de dados SQL, pode tirar partido da proteção de dados e tolerância a falhas incorporadas que, de outra forma, teria de conceber, comprar, criar e gerir. Ainda assim, consoante as exigências do seu negócio, poderá precisar de camadas adicionais de proteção para garantir a recuperação rápida da sua aplicação e do seu negócio em caso de desastre, erro ou outro incidente. Com a Base de Dados SQL, cada escalão de serviço oferece um menu diferente de funcionalidades que pode utilizar para ficar operacional e manter-se dessa forma. Pode utilizar o restauro para um ponto anterior no tempo para repor um estado anterior de uma base de dados, até 35 dias. Além disso, se o datacenter que aloja as bases de dados sofrer um período de indisponibilidade, pode efetuar a ativação pós-falha para réplicas de bases de dados numa região diferente. Em alternativa, pode utilizar réplicas para atualizações ou reposicionamento para regiões diferentes.

![Georreplicação da Base de Dados SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Consulte o artigo [Continuidade do Negócio](sql-database-business-continuity.md) para obter detalhes sobre as diferentes funcionalidades de continuidade do negócio disponíveis para diferentes escalões de serviço.

## Proteja os seus dados
O SQL Server tem um tradição de segurança de dados sólida, que a Base de Dados SQL mantém com funcionalidades que limitam o acesso, protegem os dados e ajudam a monitorizar a atividade. Consulte o artigo [Proteger a Base de Dados SQL](sql-database-security.md) para ver um resumo rápido das opções de segurança de que dispõe na Base de Dados SQL. Consulte o [Centro de Segurança do Motor de Base de Dados do SQL Server e da Base de Dados SQL](https://msdn.microsoft.com/library/bb510589), para ficar com uma perspetiva mais abrangente das funcionalidades de segurança. Visite também o [Centro de Fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/security/) para obter informações sobre a segurança da plataforma do Azure.

## Passos seguintes
Agora que já leu uma introdução à Base de Dados SQL e respondeu à pergunta "O que é a Base de Dados SQL?", está pronto para:

- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para ver comparações de preços e calculadoras de bases de dados individuais e bases de dados elásticas.
- Saber mais sobre [conjuntos elásticos](sql-database-elastic-pool.md).
- Começar por [criar a sua primeira base de dados](sql-database-get-started.md).
- [Ligar e consultar com SSMS](sql-database-connect-query-ssms.md)
- Criar a sua primeira aplicação em C#, Java, Node.js, PHP, Python ou Ruby: [Bibliotecas de ligações para a Base de Dados SQL e o SQL Server](sql-database-libraries.md)
- Ver um índice de títulos e descrições de [Todos os tópicos do serviço Base de Dados SQL do Azure](sql-database-index-all-articles.md).



<!--HONumber=ago16_HO4-->


