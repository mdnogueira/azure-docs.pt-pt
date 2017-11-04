---
title: "Utilizar o Log Analytics com uma aplicação multi-inquilino da Base de Dados SQL | Microsoft Docs"
description: "Configurar e utilizar a análise de registos (OMS) com uma aplicação de SaaS de base de dados do SQL Azure multi-inquilino"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: billgib; sstein
ms.openlocfilehash: 43d46e6a31ee05add33da59348a1d180c4078f97
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="setup-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configurar e utilizar a análise de registos (OMS) com uma aplicação de SaaS de base de dados do SQL Azure multi-inquilino

Neste tutorial, configurar e utilizar *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* para conjuntos elásticos e bases de dados de monitorização. Este tutorial baseia-se a [tutorial de gestão e monitorização do desempenho](sql-database-saas-tutorial-performance-monitoring.md). Mostra como utilizar *Log Analytics* para melhorar a monitorização e alertas fornecido no portal do Azure. Análise de registos é adequado para a monitorização e alertas na escala porque suporta centenas de agrupamentos e centenas de milhares de bases de dados. Também fornece uma solução de monitorização única que pode integrar a monitorização de diferentes aplicações e serviços do Azure, em várias subscrições do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics (OMS)
> * Utilizar o Log Analytics para monitorizar conjuntos e bases de dados

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação Wingtip SaaS é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação Wingtip SaaS](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Veja o [Tutorial de Monitorização e Gestão do Desempenho](sql-database-saas-tutorial-performance-monitoring.md) para ver um debate sobre padrões e cenários SaaS e como afetam os requisitos numa solução de monitorização.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Monitorizar e gerir o desempenho com o Log Analytics (OMS)

Na Base de Dados SQL, a monitorização e os alertas estão disponíveis nas bases de dados e nos conjuntos. Estes alertas e monitorização incorporados, específicos dos recursos, são convenientes quando existirem números baixos de recursos, mas são menos adequados para a monitorização de grandes instalações ou para fornecer uma vista unificada nos diferentes recursos e subscrições.

Para cenários de volume elevado, pode ser utilizado o Log Analytics. Este é um serviço do Azure separado que fornece análises sobre os registos de diagnóstico emitidos e a telemetria recolhida na área de trabalho do Log Analytics, que pode recolher telemetria de muitos serviços e ser utilizado para consultar e definir alertas. O Log Analytics fornece uma linguagem de consulta incorporada e ferramentas de visualização de dados que permitem análises operacionais e visualização de dados. A solução de análise do SQL Server fornece vários predefinido conjunto elástico e base de dados de monitorização e alertas, vistas e consultas e permite-lhe adicionar as suas próprias consultas ad-hoc e guardá-las conforme necessário. O OMS também fornece um estruturador de vistas personalizado.

As áreas de trabalho do Log Analytics e as soluções de análise podem ser abertas no portal do Azure e no OMS. O portal do Azure é o mais recente ponto de acesso, mas pode ficar atrás em relação ao portal do OMS em algumas áreas.

### <a name="create-data-by-starting-the-load-generator"></a>Criar dados ao iniciar o gerador de carga 

1. Abra o **Demo-PerformanceMonitoringAndManagement.ps1** no **ISE do PowerShell**. Mantenha este script aberto, uma vez que poderá querer executar vários cenários de geração de carga durante este tutorial.
1. Se tiver menos de cinco inquilinos, aprovisionar um lote de inquilinos para fornecer um mais interessante monitorização contexto:
   1. Defina **$DemoScenario = 1,** **Aprovisionar um lote de inquilinos**
   1. Para executar o script, prima **F5**.

1. Definir **$DemoScenario** = 2, **carga normal intensidade de gerar (approx. 40 DTU)**.
1. Para executar o script, prima **F5**.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts e o código de origem da aplicação Wingtip Tickets estão disponíveis no repositório do GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os ficheiros dos scripts estão localizados na pasta [Módulos de Aprendizagem](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Transfira a pasta **Módulos de Aprendizagem** para o computador local, mantendo a estrutura das pastas.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalar e configurar o Log Analytics e a solução Análise de SQL do Azure

O Log Analytics é um serviço em separado que tem de ser configurado. O Log Analytics recolhe dados de registo, telemetria e métricas numa área de trabalho do Log Analytics. Uma área de trabalho é um recurso, tal como os outros recursos no Azure, e tem de ser criada. Enquanto a área de trabalho não precisa de ser criada no mesmo grupo de recursos como a aplicação (ou aplicações) que está a monitorizar, muitas vezes, isto faz mais sentido. Para a aplicação Wingtip SaaS, isto permite que a área de trabalho facilmente ser eliminadas com a aplicação, basta eliminando o grupo de recursos.

1. Abra ...\\Módulos de Aprendizagem\\Monitorização e Gestão do Desempenho\\Log Analytics\\*Demo-LogAnalytics.ps1* no **ISE do PowerShell**.
1. Para executar o script, prima **F5**.

Nesta fase, deve ser capaz de análise de registos de abrir o portal do Azure (ou o portal do OMS). Demora alguns minutos para que a telemetria recolhida na área de trabalho de análise de registos e ficar visível. Mais tempo deixar o sistema de recolha de dados mais interessante a experiência é. Agora é o momento indicado para tomar um café. Só tem de verificar se o gerador de carga ainda está em execução!


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Utilizar o Log Analytics e a Solução de Análise de SQL para monitorizar conjuntos e bases de dados


Neste exercício, abra o portal do OMS e de análise de registo para ver a telemetria a ser recolhida para as bases de dados e agrupamentos.

1. Navegue para o [portal do Azure](https://portal.azure.com) e abra o Log Analytics ao clicar em Mais serviços e, em seguida, procure o Log Analytics:

   ![abrir o Log Analytics](media/sql-database-saas-tutorial-log-analytics/log-analytics-open.png)

1. Selecione a área de trabalho com o nome *wtploganalytics-&lt;UTILIZADOR&gt;*.

1. Selecione **Descrição Geral** para abrir a solução Log Analytics no portal do Azure.
   ![ligação da descrição geral](media/sql-database-saas-tutorial-log-analytics/click-overview.png)

    **IMPORTANTE**: poderá demorar alguns minutos até a solução ficar ativa. Seja paciente!

1. Clique no mosaico Análise de SQL do Azure para o abrir.

    ![descrição geral](media/sql-database-saas-tutorial-log-analytics/overview.png)

    ![análise](media/sql-database-saas-tutorial-log-analytics/analytics.png)

1. A vista no painel Solução tem deslocação lateral, com a sua própria barra de deslocamento na parte inferior (atualize o painel, se necessário).

1. Explore as várias vistas ao clicar nelas ou nos recursos individuais para abrir o explorador detalhado, onde pode utilizar o controlo de deslize temporal na parte superior esquerda ou clicar na barra vertical para se concentrar numa fatia temporal mais pequena. Com esta vista, pode selecionar as bases de dados individuais ou os conjuntos para se concentrar em recursos específicos:

    ![gráfico](media/sql-database-saas-tutorial-log-analytics/chart.png)

1. De volta ao painel Solução, se se deslocar para extremidade direita, verá algumas consultas guardadas que pode clicar para abrir e explorar. Pode experimentar modificá-las e guardar quaisquer consultas interessantes que produza, as quais pode, em seguida, voltar a abrir e utilizar com outros recursos.

1. De volta ao painel da área de trabalho do Log Analytics, selecione Portal do OMS para abrir aí a solução.

    ![oms](media/sql-database-saas-tutorial-log-analytics/oms.png)

1. No portal do OMS, pode configurar alertas. Clique na parte de alertas da vista DTU da base de dados.

1. Na vista Pesquisa de Registos apresentada, verá um gráfico de barras das métricas que estão a ser representadas.

    ![pesquisa de registos](media/sql-database-saas-tutorial-log-analytics/log-search.png)

1. Se clicar em Alerta na barra de ferramentas, poderá ver a configuração de alertas e alterá-la.

    ![adicionar regra de alerta](media/sql-database-saas-tutorial-log-analytics/add-alert.png)

A monitorização e os alertas no Log Analytics e no OMS baseiam-se em consultas sobre os dados na área de trabalho, ao contrário dos alertas em cada painel de recursos, que são específicos dos recursos. Assim, pode definir um alerta que procura em todas as bases de dados, em vez de definir um por base de dados. Em alternativa, pode também escrever um alerta que utiliza uma consulta composta em vários tipos de recursos. As consultas são apenas limitadas pelos dados disponíveis na área de trabalho.

O Log Analytics da Base de Dados SQL é cobrado com base no volume de dados na área de trabalho. Neste tutorial, criou uma área de trabalho Gratuita, limitada a 500 MB por dia. Assim que esse limite for atingido, os dados já não serão adicionados à área de trabalho.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics (OMS)
> * Utilizar o Log Analytics para monitorizar conjuntos e bases de dados

[Tutorial de análise de inquilinos](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que criar após a implementação de aplicação Wingtip SaaS inicial](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Log Analytics do Azure](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
