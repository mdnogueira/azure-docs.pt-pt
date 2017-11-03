---
title: "Base de dados SQL do Azure - otimização automática | Microsoft Docs"
description: Base de dados SQL do Azure analisa a consulta SQL e feita automaticamente a carga de trabalho do utilizador.
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 10f8cca8e519b28f0fe29605419b860f73e04a87
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>A otimização automática na SQL Database do Azure

Base de dados SQL do Azure é um serviço completamente gerido dados que monitoriza as consultas que são executadas na base de dados e automaticamente melhora o desempenho da carga de trabalho da base de dados. Base de dados SQL do Azure tem um incorporado [otimização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) mecanismo intelligence que pode otimizar e melhorar o desempenho das consultas através da adaptação dinamicamente a base de dados para a carga de trabalho automaticamente. A otimização automática na SQL Database do Azure pode ser uma das funcionalidades mais importantes que pode ativar na SQL Database do Azure para otimizar o desempenho das consultas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>Opções de otimização automáticas

[A otimização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) as opções disponíveis na SQL Database do Azure são:
 1. **CREATE INDEX** que identifica os índices que podem melhorar o desempenho da sua carga de trabalho, cria os índices e verifica que melhorar o desempenho das consultas.
 2. **DROP INDEX** que identifica os índices redundantes e duplicados e índices que não foram utilizados no longo período de tempo.
 3. **PLANO de correção de REGRESSÃO** que identifica as consultas SQL que estão a utilizar o plano de execução que são mais lentas do que anterior bom plano e utiliza o último conhecido bom plano em vez do plano regressed.

Base de dados SQL do Azure identifica **CREATE INDEX**, **DROP INDEX**, e **planear a correção de REGRESSÃO** recomendações que podem otimizar a base de dados e mostra-las no Azure Portal. Encontrar mais informações sobre a identificação dos índices que devem ser alteradas em [encontrar recomendações do índice no portal do Azure](sql-database-advisor-portal.md). Ou pode aplicar manualmente recomendações utilizando o portal ou pode deixar a base de dados do SQL Azure automaticamente aplicar recomendações, monitorizar a carga de trabalho após a alteração e certifique-se de que a recomendação melhorada o desempenho da sua carga de trabalho.

Automático opções de otimização pode ser independentemente ativado ou desativado por base de dados, ou podem ser configuradas no servidor lógico e aplicadas em cada base de dados que herda as definições do servidor. Configurar [otimização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) opções no servidor e herdar as definições nas bases de dados no servidor, é recomendado método para configurar a otimização automática porque simplifica a gestão das opções de otimização automáticas num grande número de bases de dados.

Consulte este artigo para obter os passos para [ativar a otimização automática](sql-database-automatic-tuning-enable.md) no portal do Azure.

## <a name="next-steps"></a>Passos seguintes

- Para ativar a otimização automática da base de dados do Azure SQL e permitir que a funcionalidade de otimização automática gerir totalmente a carga de trabalho, consulte [ativar a otimização automática](sql-database-automatic-tuning-enable.md).
- Para utilizar a otimização manual, pode rever [otimização recomendações no portal do Azure](sql-database-advisor-portal.md) e aplicar manualmente aqueles que melhoram o desempenho das consultas.
- Saiba mais sobre intelligence incorporado que tunes o [SQL Database do Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Leia mais sobre [otimização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) na SQL Database do Azure e SQL Server de 2017.
