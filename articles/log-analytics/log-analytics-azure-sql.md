---
title: "Solução de análise de SQL do Azure no Log Analytics | Microsoft Docs"
description: "A solução de análise do SQL Azure ajuda-o a gerir as bases de dados SQL do Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: magoedte;banders
ms.openlocfilehash: 957b8b9aca0ae27362923c87c7a0161cf2c052b7
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Monitorizar a base de dados do SQL do Azure através da análise do SQL do Azure (pré-visualização) no Log Analytics

![Símbolo de análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

A solução de análise de SQL do Azure no Log Analytics do Azure recolhe e visualiza importantes métricas de desempenho de SQL Azure. Ao utilizar as métricas que recolher com a solução, pode criar regras personalizadas de monitorização e alertas. Pode monitorizar a SQL Database do Azure e métricas de conjunto elástico em vários subscrições do Azure e elástico agrupamentos e visualizá-los. A solução também ajuda a identificar problemas em cada camada de pilha de aplicação.  Utiliza [métricas de diagnóstico do Azure](log-analytics-azure-storage.md) juntamente com as vistas de análise de registos para apresentar dados sobre todas as suas bases de dados SQL do Azure e conjuntos elásticos numa única área de trabalho do Log Analytics.

Atualmente, esta solução de pré-visualização suporta até 150.000 bases de dados de SQL do Azure e 5000 conjuntos elásticos SQL por área de trabalho.

A solução de análise de SQL do Azure, tal como outros disponível para análise de registos, ajuda a monitorizar e receber notificações sobre o estado de funcionamento dos seus recursos do Azure, neste caso, SQL Database do Azure. Base de dados do Microsoft Azure SQL é um serviço de base de dados relacional dimensionável, que fornece capacidades de SQL Server-semelhantes familiares para aplicações em execução na nuvem do Azure. Análise de registos ajuda-o a recolher, correlacionar e visualizar dados estruturados e não estruturados.

Para obter uma descrição prática utilizando a solução de análise de SQL do Azure e para cenários de utilização normal, veja o vídeo incorporado:
          
> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Origens ligadas

A solução de análise do Azure SQL não utilize agentes para ligar ao serviço análise de registos.

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não | Agentes diretos do Windows não são utilizados pela solução. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | Direcionar os agentes não são utilizados pela solução de Linux. |
| [Grupo de gestão do SCOM](log-analytics-om-agents.md) | Não | Uma ligação direta do agente do SCOM para análise de registos não é utilizada pela solução. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | Análise de registos não ler os dados a partir de uma conta de armazenamento. |
| [Diagnóstico do Azure](log-analytics-azure-storage.md) | Sim | Dados de métricas e registo do Azure são enviados para análise de registos diretamente pelo Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, pode criar um para [livre](https://azure.microsoft.com/free/).
- Uma área de trabalho de análise de registos. Pode utilizar um existente ou pode [criar um novo](log-analytics-quick-create-workspace.md) antes de começar a utilizar esta solução.
- Ativar o diagnóstico do Azure para as bases de dados SQL do Azure e os conjuntos elásticos e [configurá-los para enviar os seus dados para análise de registos](../sql-database/sql-database-metrics-diag-logging.md).

## <a name="configuration"></a>Configuração

Execute os seguintes passos para adicionar a solução de análise de SQL do Azure a sua área de trabalho.

1. Adicionar a solução de análise de SQL do Azure a sua área de trabalho de [do Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).
2. No portal do Azure, clique em **novo** (o símbolo +), em seguida, na lista de recursos, selecione **monitorização + gestão**.  
    ![Monitorização + Gestão](./media/log-analytics-azure-sql/monitoring-management.png)
3. No **monitorização + gestão** lista clique **ver todos os**.
4. No **recomendado** lista, clique em **mais** e, em seguida, na lista nova, localizar **análise de SQL do Azure (pré-visualização)** e, em seguida, selecioná-lo.  
    ![Solução de análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. No **análise de SQL do Azure (pré-visualização)** painel, clique em **criar**.  
    ![Criar](./media/log-analytics-azure-sql/portal-create.png)
6. No **criar nova solução** painel, selecione a área de trabalho que pretende adicionar a solução para e, em seguida, clique em **criar**.  
    ![Adicione a área de trabalho](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Para configurar várias subscrições do Azure

Para suportar várias subscrições, utilize o script do PowerShell do [registo de métricas de recurso de ativar o Azure com o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Forneça o ID de recurso da área de trabalho como um parâmetro ao executar o script para enviar dados de diagnóstico de recursos de uma subscrição do Azure para uma área de trabalho outra subscrição do Azure.

**Exemplo**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Utilizar a solução

>[!NOTE]
> Atualize a sua análise de registos para obter a versão mais recente da análise de SQL do Azure.
>

Quando adiciona a solução para a sua área de trabalho, o mosaico de análise de SQL do Azure é adicionado à sua área de trabalho e é apresentado na descrição geral. O mosaico mostra o número de bases de dados SQL do Azure e conjuntos elásticos SQL do Azure que está ligada a solução.

![Mosaico de análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Visualizar dados de análise de SQL do Azure

Clique em de **análise do Azure SQL** mosaico para abrir o dashboard de análise de SQL do Azure. O dashboard inclui a descrição geral de todas as bases de dados que são monitorizados através de diferentes perspetivas. Para diferentes perspetivas funcione, tem de ativar métricas adequadas ou registos nos seus recursos do SQL Server para transmissão em fluxo a área de trabalho do Log Analytics do Azure. 

![Descrição geral da análise SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer uma dos mosaicos, abre-se de um relatório de desagregação em perspetiva específica. Depois de perspetiva estiver selecionada, é aberta desagregação de relatório.

![Tempos limite de análise SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-timeouts.png)

Cada perspetiva, fornece resumos na subscrição, o servidor, o conjunto elástico e o nível de base de dados. Além disso, cada perspetiva mostra o relatório de específica de perspetiva à direita. Selecionar a subscrição, o servidor, o conjunto ou base de dados da lista continua a desagregação para baixo.

| Perspetiva | Descrição |
| --- | --- |
| Recurso por tipo | Perspetiva contagens de todos os recursos monitorizados. Desagregar fornece o resumo das métricas DTU e GB. |
| Informações | Fornece hierárquica desagregar para Insights inteligente. Saiba mais sobre as informações inteligentes. |
| Erros | Fornece desagregar hierárquica erros de SQL que ocorreram nas bases de dados. |
| Tempos limite | Fornece hierárquica desagregar para tempos limite SQL que ocorreram nas bases de dados. |
| Blockings | Fornece hierárquica desagregar para blockings SQL que ocorreram nas bases de dados. |
| Tem de aguardar de base de dados | Fornece hierárquica desagregar para estatísticas de espera SQL no nível da base de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |
| duração de consulta | Fornece hierárquica desagregar para as estatísticas de execução da consulta como a utilização da CPU, duração de consulta, utilização de registo e/s e utilização de dados e/s. |
| Tem de aguardar de consulta | Fornece hierárquica desagregar para as estatísticas de espera de consulta por categoria de espera. |

### <a name="intelligent-insights-report"></a>Relatório de Insights inteligente

Base de dados SQL do Azure [Insights inteligente](../sql-database/sql-database-intelligent-insights.md) permite-lhe determinar o que acontece com o desempenho de base de dados. Todas as informações inteligente recolhidos podem ser visualizadas e acedidas através de perspetiva Insights.

![Informações de análise SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Relatórios de conjunto elástico e base de dados

Conjuntos elásticos e bases de dados têm os seus próprios relatórios específicos que mostram todos os dados recolhidos para o recurso no tempo especificado.

![Base de dados de análise SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Agrupamento elástico de análise SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Através da perspetiva de aguarda de consulta e duração de consulta, pode correlacionar o desempenho de qualquer consulta através do relatório de consulta. Este relatório compara o desempenho de consulta em bases de dados diferentes e torna mais fácil identificar bases de dados que executar a consulta selecionada bem versus aqueles que são lenta.

![Consultas de análises SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

Pode facilmente criar alertas com os dados provenientes de recursos de SQL Database do Azure. Seguem-se alguns útil [pesquisa registo](log-analytics-log-searches.md) consultas que pode utilizar para alertas:

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]


*DTU elevado na base de dados SQL do Azure*

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

*Elevada DTU num agrupamento elástico de bases de dados SQL do Azure*

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

Pode utilizar estas consultas baseadas no alerta para o alertar limiares específicos para a SQL Database do Azure e conjuntos elásticos. Para configurar um alerta para a sua área de trabalho do OMS:

#### <a name="to-configure-an-alert-for-your-workspace"></a>Para configurar um alerta para a sua área de trabalho

1. Vá para o [portal do OMS](http://mms.microsoft.com/) e iniciar sessão.
2. Abra a área de trabalho que tiver configurado para a solução.
3. Na página Descrição geral, clique o **análise de SQL do Azure (pré-visualização)** mosaico.
4. Execute uma das consultas de exemplo.
5. No registo de pesquisa, clique em **alerta**.  
![criar o alerta na pesquisa](./media/log-analytics-azure-sql/create-alert01.png)
6. No **Adicionar regra de alerta** página, configure as propriedades adequadas e os limiares específicos que pretende e, em seguida, clique em **guardar**.  
![Adicionar regra de alerta](./media/log-analytics-azure-sql/create-alert02.png)

## <a name="next-steps"></a>Passos seguintes

- Utilize [pesquisas de registo](log-analytics-log-searches.md) na análise de registos para ver os dados detalhados de SQL do Azure.
- [Criar os seus próprios dashboards](log-analytics-dashboards.md) mostrar os dados de SQL do Azure.
- [Criar alertas](log-analytics-alerts.md) quando ocorrem eventos específicos de SQL do Azure.
