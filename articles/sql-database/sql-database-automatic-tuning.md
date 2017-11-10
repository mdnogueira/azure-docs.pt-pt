---
title: "Base de dados SQL do Azure - otimização automática | Microsoft Docs"
description: Base de dados SQL do Azure analisa a consulta SQL e feita automaticamente a carga de trabalho do utilizador.
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: drasumic
editor: danimir
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 11/08/2017
ms.author: jovanpop
ms.openlocfilehash: 34aa035368a0516f9fa1c71c1bda491daf5ab370
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>A otimização automática na SQL Database do Azure

Otimização automática de base de dados do SQL do Azure fornece picos de desempenho e cargas de trabalho estáveis através da utilização Artificial Intelligence de otimização do desempenho contínua.

Otimização automática é um serviço completamente gerido que utiliza intelligence incorporado para monitorizar continuamente consultas executadas numa base de dados e melhora a respetiva desempenho automaticamente. Isto é conseguido através da adaptação dinamicamente a base de dados para as cargas de trabalho de alteração e aplicar recomendações de otimização. A otimização automática aprende horizontalmente de todas as bases de dados no Azure através de Artificial Intelligence e melhora as respetivas ações de otimização dinâmica. Mais tempo uma base de dados do SQL do Azure é executado com a otimização automática no, melhor efetua.

Otimização automática de base de dados do SQL do Azure pode ser uma das funcionalidades mais importantes que pode ativar para fornecer estável e executar cargas de trabalho de pico.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que pode otimização automática fazer por si?

- Otimização de desempenho automatizado de bases de dados do Azure SQL Server
- Verificação automática de ganhos de desempenho
- A reversão automática e a correção automática
- Registo de histórico de otimização
- Otimização de scripts T-SQL de ação para implementações manuais
- Monitorização de desempenho da carga de trabalho proativa
- Ampliar a capacidade de centenas de milhares de bases de dados
- Impacto positivo para recursos de DevOps e o custo total de propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, fiável e comprovado

Operações de otimização aplicadas às bases de dados do Azure SQL são totalmente seguras para o desempenho das cargas de trabalho mais intense. O sistema tem foram concebido com cuidado para não interfere com as cargas de trabalho do utilizador. Recomendações de otimização automáticas são aplicadas apenas nos tempos de uma baixa utilização. O sistema também pode desativar temporariamente operações otimização automáticas para proteger o desempenho da carga de trabalho. Esse caso, "Desativado pelo sistema" mensagem será apresentada no portal do Azure. A otimização automática regards cargas de trabalho com a prioridade mais elevada do recurso.

Mecanismos de otimização automáticos são maduros e tem sido perfected em centenas de milhares de bases de dados em execução no Azure. Operações automáticas de otimização aplicadas são verificadas automaticamente para garantir que não há uma melhoria positiva para o desempenho da carga de trabalho. Recomendações de desempenho regressed são detetadas dinamicamente e retomadas rapidamente reverter. Através do registo de histórico de otimização há um rastreio claro da otimização melhorias efetuadas para cada base de dados do SQL do Azure. 

![Como funciona o trabalho de otimização automático](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Otimização automática de base de dados do SQL do Azure está a partilhar respetiva lógica principal com o motor de otimização automático do SQL Server. Para obter informações técnicas adicionais o mecanismo de intelligence incorporadas, consulte [otimização automática do SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Utilizar a otimização automática

A otimização automática tem de ser ativadas manualmente na sua subscrição. Para ativar a otimização automática através do portal do Azure, consulte [ativar a otimização automática](sql-database-automatic-tuning-enable.md).

A otimização automática pode funcionam de forma autónoma através de aplicar automaticamente recomendações de otimização, incluindo a verificação automática de ganhos de desempenho. 

Para obter mais controlo, pode ser desativado automático de aplicações de otimização de recomendações e otimização recomendações pode ser aplicada manualmente através do portal do Azure. Também é possível utilizar a solução para ver apenas recomendações otimização automáticas e aplicá-las manualmente através de scripts e ferramentas da sua escolha. 

Para uma descrição geral de automática como funciona a Otimização e para cenários de utilização normal, veja o vídeo incorporado:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opções de otimização automáticas

Opções de otimização automáticas disponíveis no SQL Database do Azure são:
 1. **CREATE INDEX** que identifica os índices que podem melhorar o desempenho da sua carga de trabalho, cria os índices e verifica que melhorar o desempenho das consultas.
 2. **DROP INDEX** que identifica os índices redundantes e duplicados e índices que não foram utilizados no longo período de tempo.
 3. **FORCE último bom plano** que identifica as consultas SQL que estão a utilizar o plano de execução que são mais lentas do que anterior bom plano e utiliza o último conhecido bom plano em vez do plano regressed.

Base de dados SQL do Azure identifica **CREATE INDEX**, **DROP INDEX**, e **FORCE último boa planear** recomendações que podem otimizar a base de dados e mostra-las no portal do Azure. Encontrar mais informações sobre a identificação dos índices que devem ser alteradas em [encontrar recomendações do índice no portal do Azure](sql-database-advisor-portal.md). Ou pode aplicar manualmente recomendações utilizando o portal ou pode deixar a base de dados do SQL Azure automaticamente aplicar recomendações, monitorizar a carga de trabalho após a alteração e certifique-se de que a recomendação melhorada o desempenho da sua carga de trabalho.

Automático opções de otimização pode ser independentemente ativado ou desativado por base de dados, ou podem ser configuradas no servidor lógico e aplicadas em cada base de dados que herda as definições do servidor. Configurar opções no servidor de otimização automática e herdar as definições nas bases de dados no servidor, é recomendado um método para configurar a otimização automática porque simplifica a gestão das opções de otimização automáticas num grande número de bases de dados.

## <a name="next-steps"></a>Passos seguintes

- Para ativar a otimização automática da base de dados do Azure SQL Server para gerir a sua carga de trabalho, consulte [ativar a otimização automática](sql-database-automatic-tuning-enable.md).
- Para rever e aplicar automático otimização recomendações manualmente, consulte o artigo [localizar e aplicar as recomendações de desempenho](sql-database-advisor-portal.md).
- Para saber mais sobre intelligence incorporado utilizado na otimização automática, consulte o artigo [Artificial Intelligence tunes bases de dados do Azure SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Para saber mais sobre como automática funciona otimização na SQL Database do Azure e SQL server de 2017, consulte o artigo [otimização automática do SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning).
