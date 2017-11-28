---
title: "Exemplo de aplicação multi-inquilino de base de dados SQL do Azure - Wingtip SaaS | Microsoft Docs"
description: "Aprender utilizando um exemplo de aplicação de multi-inquilino que utiliza o SQL Database do Azure, o exemplo de Wingtip SaaS"
keywords: tutorial de base de dados sql
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: d17c361d2249cc95be78cde143925251ad65db44
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Introdução a um exemplo de aplicação de SaaS de multi-inquilino de base de dados SQL

O *Wingtip SaaS* aplicação é uma aplicação de multi-inquilino de exemplo que demonstra vantagens exclusivas da base de dados SQL. A aplicação utiliza uma base de dados por inquilino, o padrão da aplicação SaaS, para servir vários inquilinos. A aplicação foi concebida para demonstram as funcionalidades da SQL Database do Azure que ativar cenários de SaaS, incluindo vários padrões de conceção e gestão de SaaS. Para obter rapidamente e em execução, a aplicação Wingtip SaaS implementa em menos de cinco minutos!

Scripts de gestão e código de origem de aplicação estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja o [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio.

## <a name="application-architecture"></a>Arquitetura da aplicação

A aplicação Wingtip SaaS utiliza o modelo de base de dados por inquilino e utiliza conjuntos elásticos SQL para maximizar a eficiência. Para o aprovisionamento e os inquilinos de mapeamento para os seus dados, é utilizada uma base de dados do catálogo. As principais aplicação Wingtip SaaS, utiliza um conjunto com três inquilinos de exemplo, para além da base de dados do catálogo. Concluir muitas do SaaS Wingtip tutoriais resultam em suplementos para a implementação inicial, ao introduzir bases de dados de análise, entre bases de dados gestão de esquema, etc.


![Arquitetura de SaaS Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Ao percorrer os tutoriais e trabalhar com a aplicação, é importante focar-se nos padrões de SaaS, como se relacionam com a camada de dados. Por outras palavras, concentre-se na camada de dados e não em analisar em excesso a aplicação em si. Noções sobre a implementação destas SaaS padrões é a chave para implementar estes padrões nas suas aplicações, ao considerar quaisquer modificações necessárias para necessidades comerciais específicas.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais do SQL Server da base de dados Wingtip SaaS

Depois de implementar a aplicação, explore os tutoriais seguintes que criar após a implementação inicial. Estes tutoriais explorar padrões comuns da SaaS que tirar partido das funcionalidades integradas de base de dados SQL, o SQL Data Warehouse e outros serviços do Azure. Tutoriais incluem scripts do PowerShell, com explicações detalhadas que simplificar significativamente a compreender e implementar os padrões de gestão de SaaS mesmos nas suas aplicações.


| Tutorial | Descrição |
|:--|:--|
| [Orientações e sugestões de exemplo de aplicação SaaS do SQL Database do Azure multi-inquilino](saas-tenancy-wingtip-app-guidance-tips.md) | **COMECE POR AQUI!** Transferir e executar scripts do PowerShell para preparar as partes da aplicação. |
|[Implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)|  Implementar e explorar a aplicação Wingtip SaaS à sua subscrição do Azure. |
|[Aprovisionamento e o catálogo de inquilinos](saas-dbpertenant-provision-and-catalog.md)| Saiba como a aplicação se liga ao utilizar uma base de dados do catálogo de inquilinos e como o catálogo de mapas de inquilinos para os seus dados. |
|[Monitorizar e gerir o desempenho](saas-dbpertenant-performance-monitoring.md)| Saiba como utilizar funcionalidades de monitorização da base de dados do SQL Server e como configurar alertas quando os limiares de desempenho for excedidos. |
|[Monitor com a análise de registos (OMS)](saas-dbpertenant-log-analytics.md) | Saiba como utilizar [Log Analytics](../log-analytics/log-analytics-overview.md) monitorizar grandes quantidades de recursos, entre vários conjuntos. |
|[Restaurar um inquilino único](saas-dbpertenant-restore-single-tenant.md)| Saiba como restaurar uma base de dados do inquilino para um ponto anterior no tempo. Passos para restaurar uma base de dados paralelas, deixando a base de dados existente do inquilino online, também são incluídos. |
|[Gerir o esquema de inquilino](saas-tenancy-schema-management.md)| Saiba como atualizar o esquema e atualizar os dados de referência, em todos os inquilinos Wingtip SaaS. |
|[Executar a análise de ad-hoc](saas-tenancy-adhoc-analytics.md) | Criar uma base de dados de análise do ad-hoc e executar consultas distribuídas em tempo real em todos os inquilinos.  |
|[Executar a análise de inquilino](saas-tenancy-tenant-analytics.md) | Extrair dados do inquilino para um armazém de dados ou base de dados de análise para executar consultas de análise offline. |


## <a name="next-steps"></a>Passos seguintes

- [Orientações e sugestões de exemplo de aplicação SaaS do SQL Database do Azure multi-inquilino](saas-tenancy-wingtip-app-guidance-tips.md)

- [Implementar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
