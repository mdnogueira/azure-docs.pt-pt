---
title: Monitorizar e melhorar o desempenho - SQL Database do Azure | Microsoft Docs
description: "A base de dados do SQL do Azure fornece ferramentas de desempenho para o ajudar a identificar áreas que podem melhorar o desempenho de consulta atual."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: a60b75ac-cf27-4d73-8322-ee4d4c448aa2
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 07/19/2016
ms.author: sstein
ms.openlocfilehash: 49b24619372d41ff25f815ca493ca7e6ce69ef9c
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="monitor-and-improve-performance"></a>Monitorizar e melhorar o desempenho
Base de dados SQL do Azure identifica potenciais problemas na sua base de dados e recomenda ações que podem melhorar o desempenho da sua carga de trabalho, fornecendo as ações de otimização inteligentes e recomendações.

Para rever o desempenho de base de dados, utilize o **desempenho** mosaico na página de descrição geral ou navegue para baixo para "Suporte + resolução de problemas" secção:

   ![Vista de desempenho](./media/sql-database-performance/entries.png)

No "Suporte + resolução de problemas" secção, pode utilizar as seguintes páginas:


1. [Descrição geral do desempenho](#performance-overview) para monitorizar o desempenho da base de dados. 
2. [Recomendações de desempenho](#performance-recommendations) encontrar recomendações de desempenho que podem melhorar o desempenho da sua carga de trabalho.
3. [Query Performance Insight](#query-performance-insight) para localizar o recurso superior consultas de consumo.
4. [A otimização automática](#automatic-tuning) para permitir que a base de dados do SQL Azure otimizar automaticamente a base de dados.

## <a name="performance-overview"></a>Descrição geral do desempenho
Esta vista fornece um resumo de desempenho da sua base de dados e ajuda-o com um desempenho Otimização e resolução de problemas. 

![Desempenho](./media/sql-database-performance/performance.png)

* O **recomendações** mosaico fornece uma repartição da otimização recomendações para a base de dados (recomendações de topo três são apresentadas se existem mais). Ao clicar neste mosaico leva-o para  **[recomendações de desempenho](#performance-recommendations)**. 
* O **otimização atividade** mosaico fornece um resumo em curso e concluída otimização ações para a base de dados, dando-lhe uma vista rápida para o histórico de atividade de otimização. Ao clicar neste mosaico leva-o para a vista histórico otimização completa da base de dados.
* O **otimização automática** mosaico mostra o [otimização automática da configuração](sql-database-automatic-tuning-enable.md) da base de dados (otimização opções que são automaticamente aplicadas à base de dados). Ao clicar neste mosaico, abre-se a caixa de diálogo de configuração de automatização.
* O **consultas de base de dados** mosaico mostra o resumo do desempenho de consulta da base de dados (geral DTU utilização e principais recursos consultas de consumo). Ao clicar neste mosaico leva-o para  **[Query Performance Insight](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Recomendações de desempenho
Esta página fornece inteligente [otimização recomendações](sql-database-advisor.md) que pode melhorar o desempenho da base de dados. Os seguintes tipos de recomendações são apresentados nesta página:

* Recomendações no quais índices para criar ou remover.
* Recomendações quando são identificados problemas de esquema na base de dados.
* Recomendações ao consultas podem beneficiar do consultas parametrizadas.

![Desempenho](./media/sql-database-performance/recommendations.png)

Também pode encontrar histórico completo de otimização de ações que foram aplicadas no passado.

Saiba como localizar um aplicar as recomendações de desempenho no [localizar e aplicar as recomendações de desempenho](sql-database-advisor-portal.md) artigo.

## <a name="automatic-tuning"></a>Ajuste automático
Bases de dados SQL do Azure automaticamente pode otimizar o desempenho de base de dados através da aplicação [recomendações de desempenho](sql-database-advisor.md). Para obter mais informações, leia [artigo otimização automático](sql-database-automatic-tuning.md). Para ativá-la, leia [como ativar a otimização automática](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](sql-database-query-performance.md) permite-lhe gaste menos tempo a resolução de problemas de desempenho de base de dados ao fornecer:

* Mais aprofundadas sobre o seu consumo de recursos (DTU) de bases de dados. 
* A CPU superior consumir consultas, que potencialmente podem ser otimizadas para um melhor desempenho. 
* A capacidade para desagregar os detalhes de uma consulta. 

  ![dashboard de desempenho](./media/sql-database-query-performance/performance.png)

Encontrar mais informações sobre esta página no artigo  **[como utilizar o Query Performance Insight](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Recursos adicionais
* [Guia de desempenho de base de dados SQL do Azure das bases de dados](sql-database-performance-guidance.md)
* [Quando deve ser utilizado um conjunto elástico?](sql-database-elastic-pool-guidance.md)

