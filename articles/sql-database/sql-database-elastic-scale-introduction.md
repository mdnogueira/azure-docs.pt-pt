---
title: Aumentar horizontalmente com a SQL Database do Azure | Microsoft Docs
description: "Software como um programadores de serviço (SaaS) pode facilmente criar bases de dados elásticas, dimensionáveis na nuvem utilizando estas ferramentas"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: d15a2e3f-5adf-41f0-95fa-4b945448e184
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: ddove
ms.openlocfilehash: a99607a0a57087c313d1718ff0b77af3637e1fa9
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="scaling-out-with-azure-sql-database"></a>Aumentar horizontalmente com a Base de Dados SQL do Azure
Pode ampliar facilmente bases de dados SQL do Azure utilizando o **bases de dados elásticas** ferramentas. Estas ferramentas e funcionalidades permitem-lhe utilizar os recursos de base de dados de **SQL Database do Azure** para criar soluções para cargas de trabalho transacionais e especialmente Software como aplicações de serviço (SaaS). Funcionalidades de base de dados elásticas são compostas pelo:

* [Biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md): A biblioteca de cliente é uma funcionalidade que permite-lhe criar e manter a bases de dados.  Consulte [começar a utilizar as ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md).
* [Ferramenta de intercalação de divisão de bases de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md): move dados entre bases de dados em partição horizontal. Isto é útil para mover dados de uma base de dados do multi-inquilino para uma base de dados de inquilino único (ou vice-versa). Consulte [tutorial da ferramenta de intercalação de divisão de base de dados elástica](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [As tarefas de base de dados elásticas](sql-database-elastic-jobs-overview.md) (pré-visualização): utilizar tarefas para gerir um grande número de bases de dados SQL do Azure. Facilmente execute operações administrativas como alterações de esquema, gestão de credenciais, as atualizações de dados de referência, recolha de dados de desempenho ou a coleção de telemetria de inquilino (cliente) através de tarefas.
* [Consulta de base de dados elástica](sql-database-elastic-query-overview.md) (pré-visualização): permite-lhe executar uma consulta de Transact-SQL que abrange várias bases de dados. Isto permite que a ligação ao Reporting Services ferramentas, como o Excel, PowerBI, Tableau, etc.
* [Transações elásticas](sql-database-elastic-transactions-overview.md): esta funcionalidade permite-lhe executar transações que abrangem várias bases de dados na base de dados do Azure SQL. Transações da base de dados elásticas estão disponíveis para aplicações de .NET através do ADO .NET e integrar com a utilização de experiência de programação familiar de [System.Transaction classes](https://msdn.microsoft.com/library/system.transactions.aspx).

O gráfico seguinte mostra uma arquitetura que inclui o **funcionalidades da base de dados elástica** em relação a uma coleção de bases de dados.

Este gráfico, cores da base de dados representam o esquemas. Bases de dados com o mesmo cor partilham o mesmo esquema.

1. Um conjunto de **bases de dados SQL do Azure** estão alojados no Azure através de arquitetura de fragmentação.
2. O **biblioteca de clientes de base de dados elástica** é utilizado para gerir um conjunto de partições horizontais.
3. Um subconjunto das bases de dados são colocados um **conjunto elástico**. (Consulte [o que é um agrupamento?](sql-database-elastic-pool.md)).
4. Um **tarefa da base de dados elástica** é executada ad hoc ou agendadas scripts T-SQL relativamente a todas as bases de dados.
5. O **ferramenta de intercalação de divisão** é utilizada para mover dados de um ID de partição horizontal para outro.
6. O **consulta de base de dados elástica** permite-lhe escrever uma consulta que abrange todas as bases de dados no conjunto de partições horizontais.
7. **Transações elásticas** permitem a execução de transações que abrangem várias bases de dados. 

![Ferramentas de Bases de Dados Elásticas][1]

## <a name="why-use-the-tools"></a>Porquê utilizar as ferramentas?
Alcançar a elasticidade e escala para aplicações em nuvem foi simples para as VMs e armazenamento de BLOBs - simplesmente adicionar ou subtrair unidades ou aumentar a energia. Mas permaneceu um desafio de monitorização de estado de processamento de dados nas bases de dados relacionais. Desafios emerged nos seguintes cenários:

* A crescer e reduzir a capacidade para a parte da base de dados relacional da sua carga de trabalho.
* Gestão de hotspots que podem surgir que afetam um subconjunto específico de dados - por exemplo, um fim-cliente ocupado (inquilino).

Tradicionalmente, cenários nestes tem sido resolvidos investindo em servidores de base de dados de grande escala para suportar a aplicação. No entanto, esta opção é limitada na nuvem em que todo o processamento ocorre no hardware do produto predefinidas. Em vez disso, a distribuição de dados e processamento em muitas bases de dados de forma idêntica structured (um padrão de escalamento horizontal conhecido como "fragmentação") fornece uma alternativa à tradicionais vertical abordagens, tanto em termos de custo e a elasticidade.

## <a name="horizontal-and-vertical-scaling"></a>Dimensionamento horizontal e vertical
A figura seguinte mostra as dimensões horizontais e verticais do dimensionamento, que são as formas básicas que podem ser escalados para as bases de dados elásticas.

![Horizontal versus de escalamento horizontal vertical.][2]

Dimensionamento horizontal refere-se à adição ou remoção de bases de dados para ajustar o desempenho de capacidade ou geral. Isto também é denominado "dimensionamento" out. Fragmentação, na qual os dados estão particionados através de uma coleção de bases de dados de forma idêntica structured, é uma forma comum para implementar o dimensionamento horizontal.  

Dimensionamento vertical refere-se para aumentar ou diminuir o nível de desempenho de uma base de dados individuais — Isto também é conhecido como "como aumentar verticalmente."

A maioria das aplicações de base de dados de escala da nuvem utilizam uma combinação destes dois estratégias. Por exemplo, um Software como uma aplicação de serviço pode utilizar para aprovisionar novos clientes de fim de dimensionamento horizontal e vertical de dimensionamento para permitir que a base de dados de cada fim-cliente aumentar ou diminuir os recursos conforme necessário, a carga de trabalho.

* Dimensionamento horizontal gerido utilizando o [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md).
* Dimensionamento vertical é realizada através de cmdlets do Azure PowerShell para alterar o escalão de serviço, ou colocando a bases de dados num agrupamento elástico.

## <a name="sharding"></a>Fragmentação
*Fragmentação* é uma técnica para distribuir grandes quantidades de dados de forma idêntica estruturados através de um número de bases de dados independentes. É especialmente popular com os programadores de nuvem criar Software como um ofertas de serviço (SAAS) para clientes finais ou as empresas. Estes clientes finais são, muitas vezes, referidos como "inquilinos". Fragmentação poderão ser necessária para diversas razões:  

* A quantidade total de dados é demasiado grande para caber nas restrições de uma base de dados
* O débito de transação da carga de trabalho geral excede as capacidades de uma base de dados
* Os inquilinos podem requerem isolamento de físico uns dos outros, pelo que são necessários bases de dados separadas para cada inquilino
* Secções diferentes de uma base de dados poderão ter de residir em diversas localizações geográficas para compatibilidade, desempenho ou motivos geopolíticos.

Outros cenários, como a ingestão de dados dos dispositivos distribuídos, a fragmentação pode ser utilizada para preencher um conjunto de bases de dados que se encontram organizados temporariamente. Por exemplo, uma base de dados individual pode ser dedicado para cada dia ou de uma semana. Nesse caso, a chave de fragmentação pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas em partição horizontal) e consultas de obtenção de informações para um intervalo de datas devem ser encaminhadas através da aplicação para o subconjunto de bases de dados que abrangem o intervalo em questão.

Fragmentação funciona melhor quando todas as transações de uma aplicação podem ser restringida a um valor único de uma chave de fragmentação. Que assegura que todas as transações são locais para uma base de dados específica.

## <a name="multi-tenant-and-single-tenant"></a>Multi-inquilino e de inquilino único
Algumas aplicações usam a abordagem mais simples de criação de uma base de dados separada para cada inquilino. Este é o **padrão de fragmentação de inquilino único** que fornece isolamento, capacidade de cópia de segurança/restauro, recursos e dimensionamento granularidade do inquilino. Com a fragmentação de inquilino único, cada base de dados está associado a um valor de ID de inquilino específico (ou valor de chave de cliente), mas essa chave não tem sempre de ser presente em dados propriamente ditos. Responsabilidade da aplicação para encaminhar cada pedido para a base de dados adequada - e a biblioteca de clientes pode simplificar isto.

![Inquilino único versus multi-inquilino][4]

Outros cenários do pacote de vários inquilinos em conjunto para bases de dados, em vez de isolá-las em bases de dados separadas. Este é típica **padrão de fragmentação do multi-inquilino** - e pode ser controlada pelo facto da que uma aplicação gere grande número de inquilinos pequenos. No fragmentação do multi-inquilino, as linhas nas tabelas de base de dados todos os concebidas para executar uma chave que identifica o ID do inquilino ou chave de fragmentação. Novamente, a camada de aplicação é responsável por encaminhamento de pedido de um inquilino na base de dados adequada e isto pode ser suportado pela biblioteca de clientes de base de dados elásticas. Além disso, a segurança ao nível da linha pode ser utilizado para filtrar as linhas que pode aceder a cada inquilino - para obter detalhes, consulte [aplicações de multi-inquilinos com segurança ao nível da linha e ferramentas de base de dados elástica](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuir dados entre bases de dados pode ser necessária, com o padrão de fragmentação do multi-inquilino e este processo é facilitado pela ferramenta de intercalação de divisão de bases de dados elásticas. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Mover dados a partir de vários inquilinos único bases de dados
Ao criar uma aplicação SaaS, é normal para oferecer potenciais clientes que uma versão de avaliação do software. Neste caso, é económica para utilizar uma base de dados do multi-inquilino de dados. No entanto, quando um prospect torna-se um cliente, uma base de dados único inquilino é melhor uma vez que fornece um melhor desempenho. Se o cliente tinha criado dados durante o período de avaliação, utilize o [ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md) para mover os dados de multi-inquilino para a nova base de dados único inquilino.

## <a name="next-steps"></a>Passos seguintes
Para uma aplicação de exemplo que demonstra a biblioteca de clientes, consulte [começar a utilizar as ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md).

Para converter as bases de dados existentes para utilizar as ferramentas, consulte [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md).

Para ver as especificações do conjunto elástico, consulte [considerações sobre preço e desempenho de um conjunto elástico](sql-database-elastic-pool.md), ou criar um novo conjunto com [conjuntos elásticos](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

