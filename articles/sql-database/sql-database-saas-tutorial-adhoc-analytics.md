---
title: "Executar consultas de análise ad-hoc em várias bases de dados SQL do Azure | Microsoft Docs"
description: "Executar consultas de análise ad-hoc em várias bases de dados numa aplicação multi-inquilino"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: dd41e7f1f131f6c18e03d2434982c3d681342b8b
ms.contentlocale: pt-pt
ms.lasthandoff: 05/12/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>Executar consultas de análise ad hoc em todos os inquilinos do WTP

Neste tutorial, vai criar uma base de dados de análise ad hoc e executar várias consultas em todos os inquilinos. Estas consultas podem extrair informações escondidas nos dados operacionais quotidianos da aplicação WTP.

Para executar consultas de análise ad hoc (em vários inquilinos), a aplicação WTP utiliza a [Consulta Elástica](sql-database-elastic-query-overview.md), juntamente com uma base de dados de análise.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Implementar uma base de dados de análise ad hoc
> * Executar consultas distribuídas em todas as bases de dados do inquilino



Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação WTP está implementada. Para implementar em menos de cinco minutos, veja [Deploy and explore the WTP SaaS application](sql-database-saas-tutorial.md) (Implementar e explorar a aplicação SaaS WTP)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>Padrão de Análise Ad Hoc

Uma das grandes possibilidades das aplicações SaaS consiste em utilizar grandes quantidades de dados de inquilinos que tem armazenados centralmente na cloud. Utilize estes dados para obter informações sobre o funcionamento e a utilização das suas aplicações, dos seus inquilinos, respetivos utilizadores e respetivos comportamentos e preferências. As informações obtidas podem levar ao desenvolvimento de funcionalidades, a melhorias na usabilidade e a outros investimentos nas suas aplicações e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Um abordagem passa por utilizar a Consulta Elástica, que permite a consulta ad hoc num conjunto distribuído de bases de dados com esquema comum. A Consulta Elástica utiliza uma base de dados *head* individual, na qual são definidas tabelas externas que espelham tabelas nas bases de dados distribuídas (inquilino). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. A Consulta Elástica utiliza o mapa de fragmentação da base de dados de catálogo para fornecer a localização das bases de dados inquilinas. A configuração e a consulta são muito simples com a utilização de T-SQL normal e suporta a consulta ad hoc a partir de ferramentas como o Power BI e o Excel.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts e o código de origem da aplicação Wingtip Tickets estão disponíveis no repositório do GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os ficheiros dos scripts estão localizados na pasta [Módulos de Aprendizagem](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Transfira a pasta **Módulos de Aprendizagem** para o computador local, mantendo a estrutura das pastas.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Implementar a base de dados utilizada para consultas de análise ad hoc

Este exercício implementa a base de dados Ad-hoc Analytics que contém o esquema utilizado para emitir consultas ad hoc que abrangem todas as bases de dados do inquilino.

1. Abra ...\\Módulos de Aprendizagem\\Análise Operacional\\Análise Ad Hoc \\*Demo-AdhocAnalytics.ps1* no *ISE do PowerShell* e defina os valores seguintes:
   * **$DemoScenario** = 2, **Implementar a base de dados Analytics Ad-hoc**.

1. Prima **F5** para executar o script e criar uma nova base de dados SQL para a análise ad hoc e adicione-a ao catálogo do WTP. São adicionadas as tabelas TicketPurchases, Tickets e Venues como tabelas externas, as quais podem ser consultadas.
   Não há problema se encontrar o aviso *O servidor RPC está indisponível* aqui.


Tem agora a base de dados *adhocanalytics*, que pode ser utilizada para executar consultas distribuídas e recolher informações de todos os inquilinos.

## <a name="run-ad-hoc-analytics-queries"></a>Executar consultas de análise ad hoc

Este exercício executa consultas de análise ad hoc para desvendar informações dos inquilinos da aplicação WTP.

1. Abra ...\\Módulos de Aprendizagem\\Análise Operacional\\Análises Ad Hoc\\*Demo-AdhocAnalyticsQueries.sql* no SSMS.
1. Confirme que está ligado à base de dados **adhocanalytics**.
1. Selecione a consulta individual que pretende executar e prima **F5**:

    ![consulta](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Implementar uma base de dados de análise ad hoc
> * Executar consultas distribuídas em todas as bases de dados do inquilino

[Tutorial do Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados após a implementação inicial da aplicação Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Consulta Elástica](sql-database-elastic-query-overview.md)

