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
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 48e8eb91a5febcc1109bee3404bb534bd0391f88
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configurar e utilizar a análise de registos (OMS) com uma aplicação de SaaS de base de dados do SQL Azure multi-inquilino

Neste tutorial, configurar e utilizar *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* para conjuntos elásticos e bases de dados de monitorização. Este tutorial baseia-se a [tutorial de gestão e monitorização do desempenho](saas-dbpertenant-performance-monitoring.md). Mostra como utilizar *Log Analytics* para melhorar a monitorização e alertas fornecido no portal do Azure. Análise de registos é adequado para a monitorização e alertas na escala porque suporta centenas de agrupamentos e centenas de milhares de bases de dados. Também fornece uma solução de monitorização única que pode integrar a monitorização de diferentes aplicações e serviços do Azure, em várias subscrições do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics (OMS)
> * Utilizar o Log Analytics para monitorizar conjuntos e bases de dados

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação de Wingtip bilhetes SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de Wingtip bilhetes SaaS da base de dados por inquilino](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Veja o [Tutorial de Monitorização e Gestão do Desempenho](saas-dbpertenant-performance-monitoring.md) para ver um debate sobre padrões e cenários SaaS e como afetam os requisitos numa solução de monitorização.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Monitorizar e gerir o desempenho com o Log Analytics (OMS)

Na Base de Dados SQL, a monitorização e os alertas estão disponíveis nas bases de dados e nos conjuntos. Estes alertas e monitorização incorporados, específicos dos recursos, são convenientes quando existirem números baixos de recursos, mas são menos adequados para a monitorização de grandes instalações ou para fornecer uma vista unificada nos diferentes recursos e subscrições.

Para cenários de volume elevado, pode ser utilizado o Log Analytics. Este é um serviço do Azure separado que fornece análises sobre os registos de diagnóstico emitidos e a telemetria recolhida na área de trabalho do Log Analytics, que pode recolher telemetria de muitos serviços e ser utilizado para consultar e definir alertas. O Log Analytics fornece uma linguagem de consulta incorporada e ferramentas de visualização de dados que permitem análises operacionais e visualização de dados. A solução de análise do SQL Server fornece vários predefinido conjunto elástico e base de dados de monitorização e alertas, vistas e consultas e permite-lhe adicionar as suas próprias consultas ad-hoc e guardá-las conforme necessário. O OMS também fornece um estruturador de vistas personalizado.

As áreas de trabalho do Log Analytics e as soluções de análise podem ser abertas no portal do Azure e no OMS. O portal do Azure é o mais recente ponto de acesso, mas pode ficar atrás em relação ao portal do OMS em algumas áreas.

### <a name="create-data-by-starting-the-load-generator"></a>Criar dados ao iniciar o gerador de carga 

1. No **ISE do PowerShell**, abra **demonstração PerformanceMonitoringAndManagement.ps1**. Mantenha este script aberto, uma vez que poderá querer executar vários cenários de geração de carga durante este tutorial.
1. Se tiver menos de cinco inquilinos, aprovisionar um lote de inquilinos para fornecer um mais interessante monitorização contexto:
   1. Defina **$DemoScenario = 1,** **Aprovisionar um lote de inquilinos**
   1. Para executar o script, prima **F5**.

1. Definir **$DemoScenario** = 2, **carga normal intensidade de gerar (approx. 40 DTU)**.
1. Para executar o script, prima **F5**.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicação Wingtip bilhetes SaaS da base de dados por inquilino

Os scripts de base de dados do Wingtip bilhetes SaaS multi-inquilino e o código fonte da aplicação, estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja o [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalar e configurar o Log Analytics e a solução Análise de SQL do Azure

O Log Analytics é um serviço em separado que tem de ser configurado. O Log Analytics recolhe dados de registo, telemetria e métricas numa área de trabalho do Log Analytics. Uma área de trabalho é um recurso, tal como os outros recursos no Azure, e tem de ser criada. Enquanto a área de trabalho não precisa de ser criada no mesmo grupo de recursos como a aplicação (ou aplicações) que está a monitorizar, muitas vezes, isto faz mais sentido. Para a aplicação de Wingtip bilhetes SaaS da base de dados por inquilino SaaS, isto permite que a área de trabalho, ao eliminar o grupo de recursos, facilmente ser eliminadas com a aplicação.

1. No **ISE do PowerShell**, abra... \\Learning módulos\\monitorização de desempenho e gestão\\Iniciar análise\\*demonstração LogAnalytics.ps1*.
1. Para executar o script, prima **F5**.

Nesta fase, deve ser capaz de análise de registos de abrir o portal do Azure (ou o portal do OMS). Demora alguns minutos para que a telemetria recolhida na área de trabalho de análise de registos e ficar visível. Mais tempo deixar o sistema de recolha de dados mais interessante a experiência é. Agora é o momento indicado para tomar um café. Só tem de verificar se o gerador de carga ainda está em execução!


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Utilizar o Log Analytics e a Solução de Análise de SQL para monitorizar conjuntos e bases de dados


Neste exercício, abra o portal do OMS e de análise de registo para ver a telemetria a ser recolhida para as bases de dados e agrupamentos.

1. Navegue para o [portal do Azure](https://portal.azure.com) e abra o Log Analytics ao clicar em Mais serviços e, em seguida, procure o Log Analytics:

   ![abrir o Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecione a área de trabalho com o nome *wtploganalytics-&lt;UTILIZADOR&gt;*.

1. Selecione **Descrição Geral** para abrir a solução Log Analytics no portal do Azure.
   ![ligação da descrição geral](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Pode demorar alguns minutos antes da solução está ativa. Seja paciente!

1. Clique no mosaico Análise de SQL do Azure para o abrir.

    ![descrição geral](media/saas-dbpertenant-log-analytics/overview.png)

    ![análise](media/saas-dbpertenant-log-analytics/analytics.png)

1. A vista no painel Solução tem deslocação lateral, com a sua própria barra de deslocamento na parte inferior (atualize o painel, se necessário).

1. Explore as várias vistas ao clicar nelas ou nos recursos individuais para abrir o explorador detalhado, onde pode utilizar o controlo de deslize temporal na parte superior esquerda ou clicar na barra vertical para se concentrar numa fatia temporal mais pequena. Com esta vista, pode selecionar as bases de dados individuais ou os conjuntos para se concentrar em recursos específicos:

    ![gráfico](media/saas-dbpertenant-log-analytics/chart.png)

1. De volta ao painel Solução, se se deslocar para extremidade direita, verá algumas consultas guardadas que pode clicar para abrir e explorar. Pode experimentar modificar estes e guardar quaisquer interessantes consultas produzir, o que pode, em seguida, volte a abrir e utilizar com outros recursos.

1. De volta ao painel da área de trabalho do Log Analytics, selecione Portal do OMS para abrir aí a solução.

    ![oms](media/saas-dbpertenant-log-analytics/oms.png)

1. No portal do OMS, pode configurar alertas. Clique na parte de alertas da vista DTU da base de dados.

1. Na vista Pesquisa de Registos apresentada, verá um gráfico de barras das métricas que estão a ser representadas.

    ![pesquisa de registos](media/saas-dbpertenant-log-analytics/log-search.png)

1. Se clicar no alerta na barra de ferramentas, será capaz de ver a configuração do alerta e pode alterá-la.

    ![adicionar regra de alerta](media/saas-dbpertenant-log-analytics/add-alert.png)

A monitorização e os alertas no Log Analytics e no OMS baseiam-se em consultas sobre os dados na área de trabalho, ao contrário dos alertas em cada painel de recursos, que são específicos dos recursos. Assim, pode definir um alerta que procura em todas as bases de dados, em vez de definir um por base de dados. Em alternativa, pode também escrever um alerta que utiliza uma consulta composta em vários tipos de recursos. As consultas são apenas limitadas pelos dados disponíveis na área de trabalho.

O Log Analytics da Base de Dados SQL é cobrado com base no volume de dados na área de trabalho. Neste tutorial, criou uma área de trabalho Gratuita, limitada a 500 MB por dia. Assim que esse limite for atingido, os dados já não serão adicionados à área de trabalho.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics (OMS)
> * Utilizar o Log Analytics para monitorizar conjuntos e bases de dados

[Tutorial de análise de inquilinos](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que criar após a implementação de aplicação Wingtip bilhetes SaaS da base de dados por inquilino inicial](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Log Analytics do Azure](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
