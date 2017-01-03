---
title: "O que é um conjunto elástico do Azure? | Microsoft Docs"
description: "Gerir centenas ou milhares de bases de dados com um conjunto. Um preço de um conjunto de unidades de desempenho pode ser distribuído por um conjunto. Mover bases de dados para dentro ou para fora à vontade."
keywords: "base de dados elástica, bases de dados sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 12/14/2016
ms.author: CarlRabeler
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: c3757dadb09ba070b30820a46007a9c82490d8f2


---
# <a name="what-is-an-azure-elastic-pool"></a>O que é um conjunto elástico do Azure?
Os conjuntos elásticos de bases de dados SQL fornecem uma solução económica e simples para gerir os objetivos de desempenho de várias bases de dados que tenham padrões de utilização extremamente variáveis e imprevisíveis.

> [!NOTE]
> Os conjuntos elásticos estão em disponibilidade geral (GA) em todas as regiões do Azure, exceto na Índia Ocidental, onde se encontra, de momento, em pré-visualização.  A GA dos conjuntos elásticos nesta região vai ocorrer assim que possível.
>
>

## <a name="how-it-works"></a>Como funciona
Um padrão de aplicação SaaS comum é o modelo de base de dados de inquilino único: a cada cliente é atribuída a sua própria base de dados. Cada cliente (base de dados) tem requisitos de recursos imprevisíveis em termos de memória, E/S e CPU. Com estes altos e baixos de procura, como pode atribuir recursos de forma eficiente e rentável? Tradicionalmente, tem duas opções: (1) aprovisionar excessivamente recursos com base na utilização máxima e pagar em excesso ou (2) aprovisionar insuficientemente para economizar custos, em detrimento do desempenho e da satisfação do cliente durante os picos. Os conjuntos elásticos resolvem este problema ao garantir que as bases de dados recebem os recursos de desempenho de que precisam e quando precisam. Fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>
>

Na Base de Dados SQL, a medida relativa da capacidade de uma base de dados de gerir exigências de recursos é expressa em Unidades de Transação de Base de Dados (DTUs) para bases de dados individuais e DTUs elásticas (eDTUs) para bases de dados elásticas num conjunto elástico. Veja [Introdução à Base de Dados SQL](sql-database-technical-overview.md) para saber mais sobre DTUs e eDTUs.

A um conjunto é atribuído um número definido de eDTUs, por um preço definido. Dentro do conjunto, é dada às bases de dados individuais a flexibilidade para se dimensionarem automaticamente dentro de parâmetros definidos. Uma base de dados sobrecarregada pode consumir mais eDTUs para responder às necessidades. As bases de dados sujeitas a cargas mais leves consomem menos e as bases de dados que não estão sujeitas a qualquer carga não consomem eDTUs. O aprovisionamento de recursos para o conjunto completo e não para bases de dados individuais simplifica as tarefas de gestão. Além disso, tem um orçamento previsível para o conjunto.

Podem ser adicionais mais eDTUs a um conjunto existente sem qualquer período de indisponibilidade da base de dados ou sem impacto nas bases de dados do conjunto elástico. Do mesmo modo, se as eDTUs adicionais já não forem necessárias, podem ser removidas de um conjunto existente em qualquer momento.

Além disso, pode adicionar ou subtrair bases de dados ao conjunto. Se uma base de dados estiver a subutilizar recursos de forma previsível, remova-a.

## <a name="which-databases-go-in-a-pool"></a>Que bases de dados são adequadas a um conjunto?
![Bases de dados SQL que partilham eDTUs num conjunto elástico.][1]

As bases de dados que são excelentes candidatos para conjuntos elásticos têm, normalmente, períodos de atividade e outros períodos de inatividade. No exemplo acima, pode ver a atividade de uma base de dados individual, 4 bases de dados e, por último, um conjunto elástico com 20 bases de dados. As bases de dados com atividade variável ao longo do tempo são excelentes candidatas para conjuntos elásticos, porque não estão todas ativas ao mesmo tempo e podem partilhar eDTUs. Nem todas as bases de dados se enquadram neste padrão. As bases de dados com uma exigência de recursos mais constante adequam-se melhor aos escalões de serviço Básico, Standard e Premium, onde os recursos são atribuídos individualmente.

[Considerações sobre preço e desempenho de um conjunto elástico](sql-database-elastic-pool-guidance.md).

## <a name="edtu-and-storage-limits-for-elastic-pools-and-elastic-databases"></a>eDTU e limites de armazenamento para conjuntos elásticos e bases de dados elásticas

A tabela seguinte descreve as caraterísticas dos conjuntos elásticos Básico, Standard e Premium.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Se todas as DTUs de um conjunto elástico forem utilizadas, cada base de dados no conjunto recebe uma quantidade igual de recursos para processar consultas.  O serviço Base de Dados SQL fornece equidade de partilha de recursos entre bases de dados, garantindo frações iguais de tempo de computação. A equidade de partilha de recursos de um conjunto elástico é adicional a qualquer quantidade de recursos garantido de outro modo a cada base de dados quando o mínimo de DTUs por base de dados está definido como um valor diferente de zero.

## <a name="elastic-pool-and-elastic-database-properties"></a>Propriedades de conjuntos elásticos e de bases de dados elásticas

As tabelas seguintes descrevem os limites dos conjuntos elásticos e das bases de dados elásticas.

### <a name="limits-for-elastic-pools"></a>Limites para conjuntos elásticos
| Propriedade | Descrição |
|:--- |:--- |
| Camada de serviços |Básico, Standard ou Premium. A camada de serviço determina o intervalo nos limites de desempenho e armazenamento que podem ser configurados, bem como opções de continuidade do negócio. Cada base de dados de um conjunto tem a mesma camada de serviço que o conjunto. "Camada de serviço" é também referida como "edição". |
| eDTUs por conjunto |O número máximo de eDTUs que podem ser partilhadas pelas bases de dados no conjunto. O total de eDTUs utilizadas pelas bases de dados no conjunto não pode exceder este limite no mesmo ponto no tempo. |
| Armazenamento máximo por conjunto (GB) |A quantidade máxima de armazenamento em GBs que pode ser partilhada pelas bases de dados no conjunto. O armazenamento total utilizado pelas bases de dados no conjunto não pode exceder este limite. Este limite é determinado pelas eDTUs por conjunto. Se este limite for excedido, todas as bases de dados passam a ser só de leitura. |
| Número máximo de bases de dados por conjunto |O número máximo de bases de dados permitidas por conjunto. |
| Máximo de trabalhadores simultâneos por conjunto |O número máximo de trabalhos simultâneos (pedidos) disponíveis para todas as bases de dados no conjunto. |
| Máximo de inícios de sessão simultâneos por conjunto |O número máximo de inícios de sessão simultâneos para todas as bases de dados no conjunto. |
| Máximo de sessões simultâneas por conjunto |O número máximo de sessões disponíveis para todas as bases de dados no conjunto. |

### <a name="limits-for-elastic-databases"></a>Limites para bases de dados elásticas
| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por base de dados |O número máximo de eDTUs que qualquer base de dados no conjunto pode utilizar, caso estejam disponíveis com base na utilização por outras bases de dados no conjunto.  O número máximo de eDTUs por base de dados não é uma garantia de recurso para uma base de dados.  Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. Defina um número máximo de eDTUs suficientemente elevado para processar picos na utilização de base de dados. É esperado algum grau de consolidação excessiva, uma vez que, geralmente, o conjunto assume padrões de utilização a frio e a quente para bases de dados em que todas as bases de dados não atingem o pico em simultâneo. Por exemplo, suponha que o pico de utilização por base de dados é 20 eDTUs e apenas 20% das 100 bases de dados no conjunto atingem o pico ao mesmo tempo.  Se o número máximo de eDTUs por base de dados estiver definido como 20 eDTUs, é razoável sobreconsolidar o conjunto em 5 vezes e definir o número de eDTUs por conjunto como 400. |
| Mínimo de eDTUs por base de dados |O número mínimo de eDTUs que é garantido a qualquer base de dados no conjunto.  Esta definição é uma definição global que se aplica a todas as bases de dados no conjunto. O mínimo de eDTUs por base de dados pode ser definido como 0, que é também o valor predefinido. Esta propriedade é definida como qualquer valor entre 0 e a utilização média de eDTUs por base de dados. O produto do número de bases de dados no conjunto e o número mínimo de eDTUs por base de dados não pode exceder as eDTUs por conjunto.  Por exemplo, se um conjunto tiver 20 bases de dados e o número mínimo de eDTUs por base de dados definido como 10 eDTUs, as eDTUs por conjunto têm de ser, pelo menos, 200 eDTUs. |
| Armazenamento máximo por base de dados (GB) |O armazenamento máximo para uma base de dados num conjunto. As bases de dados elásticas partilham o armazenamento do conjunto, pelo que o armazenamento da base de dados é limitado ao armazenamento mais pequeno do conjunto restante e ao armazenamento máximo por base de dados. |

## <a name="elastic-database-jobs"></a>Tarefas de base de dados elástica
Com um conjunto, as tarefas de gestão são simplificadas através da execução de scripts em **[tarefas elásticas](sql-database-elastic-jobs-overview.md)**. Uma tarefa de base de dados elástica elimina a maior parte da monotonia associada a grandes números de bases de dados. Para começar, veja [Introdução às tarefas de Base de Dados Elástica](sql-database-elastic-jobs-getting-started.md).

Para obter mais informações sobre outras ferramentas de base de dados elástica, veja [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>Funcionalidades de continuidade de negócio para bases de dados num conjunto
As bases de dados elásticas suportam geralmente as mesmas [funcionalidades de continuidade de negócio](sql-database-business-continuity.md) que estão disponíveis para as bases de dados únicas.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
O Restauro para um ponto anterior no tempo utiliza cópias de segurança automáticas da base de dados para recuperar uma base de dados num conjunto para um ponto específico no tempo. Veja [Restauro para um ponto anterior no tempo](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Restauro geográfico
O Restauro geográfico fornece a opção de recuperação predefinida quando uma base de dados está indisponível devido a um incidente na região onde a base de dados está alojada. Veja [Restaurar uma Base de Dados SQL do Azure ou fazer a ativação pós-falha para uma secundária](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>Georreplicação Ativa
Para aplicações que têm requisitos de recuperação mais agressivos do que aqueles que o Restauro geográfico pode oferecer, configure a Georreplicação ativa com o [portal do Azure](sql-database-geo-replication-portal.md), o [PowerShell](sql-database-geo-replication-powershell.md) ou o [Transact-SQL](sql-database-geo-replication-transact-sql.md).

## <a name="additional-resources"></a>Recursos adicionais
* [Curso de vídeo da Microsoft Virtual Academy sobre as capacidades das bases de dados elásticas](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png



<!--HONumber=Dec16_HO5-->


