---
title: "Monitorização e a otimização de desempenho - SQL Database do Azure | Microsoft Docs"
description: "Sugestões de otimização na SQL Database do Azure através de avaliação e melhoramento do desempenho."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "Otimização de otimização de desempenho de sql sugestões, a otimização de desempenho de base de dados de desempenho de SQL otimização de desempenho de base de dados do SQL Server"
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: v-shysun
ms.openlocfilehash: 1791c56f86ee1997177daa95638c4f14068f8115
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="monitoring-and-performance-tuning"></a>Monitorização e a otimização de desempenho

Base de dados SQL do Azure é gerida automaticamente e serviço flexível de dados, onde pode facilmente monitorizar a utilização, adicionar ou remover recursos (CPU, memória, e/s), localize as recomendações que podem melhorar o desempenho da base de dados, ou permitir que o se adaptam a sua carga de trabalho de base de dados e Otimize o desempenho automaticamente.

Este artigo fornece uma descrição geral da monitorização e as opções disponíveis na SQL Database do Azure de otimização do desempenho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Monitorização e resolução de problemas de desempenho de base de dados

Base de dados SQL do Azure permite-lhe monitorizar a utilização da base de dados e consultas que poderão causar problemas de desempenho de identificar facilmente. Pode monitorizar o desempenho de base de dados utilizando a vistas de sistema ou o portal do Azure. Tem as seguintes opções de monitorização e resolução de problemas de desempenho de base de dados:

1. No [portal do Azure](https://portal.azure.com), clique em **bases de dados SQL**, selecione a base de dados e, em seguida, utilize o gráfico de monitorização para procurar recursos aproximar-se os seus máximo. Consumo de DTU é apresentado por predefinição. Clique em **editar** para alterar o intervalo de tempo e os valores apresentados.
2. Utilize [Query Performance Insight](sql-database-query-performance.md) para identificar as consultas que passam o máximo partido dos recursos.
3. Pode utilizar vistas de gestão dinâmica (DMVs), eventos expandidos (`XEvents`) e o arquivo de consultas no SSMS ao obter parâmetros de desempenho em tempo real.

Consulte o [tópico de documentação de orientação de desempenho](sql-database-performance-guidance.md) para localizar as técnicas que pode utilizar para melhorar o desempenho de SQL Database do Azure se identificar algum problema com estes relatórios ou vistas.

> [!IMPORTANT] 
> É recomendado utilizar sempre a versão mais recente do Management Studio, para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Otimizar a base de dados para melhorar o desempenho

Base de dados SQL do Azure permite-lhe identificar oportunidades para melhorar e otimizar o desempenho de consulta sem alterar recursos revendo [recomendações de otimização do desempenho](sql-database-advisor.md). Os índices em falta e as consultas mal otimizadas são razões comuns para um desempenho fraco da base de dados. Pode aplicar estas recomendações otimização para melhorar o desempenho da sua carga de trabalho.
Pode também permitem SQL database do Azure para [automaticamente otimizar o desempenho das suas consultas](sql-database-automatic-tuning.md) aplicando identificado todas as recomendações e verificar a melhorar o desempenho da base de dados. Pode utilizar as seguintes opções para melhorar o desempenho da base de dados:

1. Utilize [SQL Database Advisor](sql-database-advisor-portal.md) para ver as recomendações para criação e remover índices, parameterizing consultas e corrigir problemas de esquema.
2. [Ativar a otimização automática](sql-database-automatic-tuning-enable.md) e permita que o Azure SQL da base de dados automaticamente problemas de desempenho de correção identificada.

## <a name="improving-database-performance-with-more-resources"></a>Melhorar o desempenho da base de dados com mais recursos

Por fim, se existem quaisquer itens passíveis de ação que podem melhorar o desempenho da base de dados, pode alterar a quantidade de recursos disponíveis no SQL Database do Azure. Pode atribuir mais recursos alterando o [camada de serviço](sql-database-service-tiers.md) de uma base de dados autónoma ou aumentar as eDTUs de um conjunto elástico em qualquer altura.
1. Para bases de dados autónomo, pode [alterar camadas de serviço](sql-database-service-tiers.md) a pedido para melhorar o desempenho de base de dados.
2. Para várias bases de dados, considere a utilização [conjuntos elásticos](sql-database-elastic-pool-guidance.md) Dimensionar automaticamente recursos.

## <a name="tune-and-refactor-application-or-database-code"></a>Otimizar e aplicação refactorize ou um código de base de dados

Pode alterar o código de aplicação mais ideal, utilizar a base de dados, alterar os índices, forçar planos ou utilizar sugestões para adaptar manualmente a base de dados para a carga de trabalho. Localizar algumas orientações e sugestões de Otimização e conversão de código no manual de [tópico de documentação de orientação de desempenho](sql-database-performance-guidance.md) artigo.


## <a name="next-steps"></a>Passos seguintes

- Para ativar a otimização automática da base de dados do Azure SQL e permitir que a funcionalidade de otimização automática gerir totalmente a carga de trabalho, consulte [ativar a otimização automática](sql-database-automatic-tuning-enable.md).
- Para utilizar a otimização manual, pode rever [otimização recomendações no portal do Azure](sql-database-advisor-portal.md) e aplicar manualmente aqueles que melhoram o desempenho das consultas.
- Alterar recursos que estão disponíveis na base de dados ao alterar [escalões de serviço do SQL Database do Azure](sql-database-performance-guidance.md)
