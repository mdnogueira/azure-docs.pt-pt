---
title: "Ver os dados de análise Web Apps do Azure | Microsoft Docs"
description: "Pode utilizar a solução de análise de aplicações Web do Azure para obter informações sobre as Web Apps do Azure através da recolha de métricas diferentes em todos os recursos de aplicação Web do Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: banders
ms.openlocfilehash: 9ef26d4b6bfd92925a70b7bbdf8979e287c73445
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Ver dados analíticos métricas em todos os recursos de aplicação Web do Azure

![Símbolo de aplicações Web](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  
A solução de análise de aplicações Web do Azure (pré-visualização) fornece informações sobre a sua [Web Apps do Azure](../app-service/app-service-web-overview.md) recolhendo métricas diferentes em todos os recursos de aplicação Web do Azure. Com a solução, pode analisar e procurar dados métricos do recurso de aplicação web.

Utilizar a solução, pode ver o:

- Principais Web Apps com o tempo de resposta mais elevado
- Número de pedidos entre as aplicações Web, incluindo pedidos com êxito ou falhados
- Principais de Web de aplicações com maior tráfego de entrada e saída
- Planos de serviço superior com elevada utilização da CPU e memória
- Operações de registo de atividade de Web Apps do Azure

## <a name="connected-sources"></a>Origens ligadas

Ao contrário da maioria dos outros soluções de análise de registos, os dados não estão recolhidos para Web Apps do Azure por agentes. Todos os dados utilizados pela solução inclui diretamente a partir do Azure.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não | A solução não recolhe informações de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | A solução não recolhe informações de agentes Linux. |
| [Grupo de gestão do SCOM](log-analytics-om-agents.md) | Não | A solução não recolhe informações de agentes num grupo de gestão do SCOM ligado. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | A solução não não as informações de recolha do armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para aceder a informações de métricas de recursos de aplicação Web do Azure, tem de ter uma subscrição do Azure.

## <a name="configuration"></a>Configuração

Execute os seguintes passos para configurar a solução de análise de aplicações Web do Azure para as áreas de trabalho.

1. Ativar a solução de análise de aplicações Web do Azure de [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).
2. [Ativar o registo de métricas de recurso do Azure para OMS através do PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

A solução de análise de aplicações Web do Azure recolhe dois conjuntos de métricas do Azure:

- Métricas de Web Apps do Azure
  - Memória média conjunto de trabalho
  - Tempo de resposta médio
  - Bytes recebidos/enviados
  - Tempo de CPU
  - Pedidos
  - Memória, conjunto de trabalho
  - Httpxxx
- Métricas de plano de serviço de aplicações
  - Bytes recebidos/enviados
  - Percentagem de CPU
  - Comprimento da fila de disco
  - Comprimento da fila de HTTP
  - Percentagem de memória

Métricas de plano do App Service apenas são recolhidas se estiver a utilizar um plano de serviço dedicada. Isto não se aplica a planos de serviço de aplicações gratuitos ou partilhados.

Se adicionar a solução através do portal do OMS, verá o mosaico seguinte. Terá de [ativar o registo de métricas de recurso do Azure para OMS através do PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

![Efetuar a notificação de avaliação](./media/log-analytics-azure-web-apps-analytics/performing-assessment.png)

Depois de configurar a solução, dados devem começar a fluir a sua área de trabalho em 15 minutos.

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução de análise de aplicações Web do Azure a sua área de trabalho, o **análise de aplicações Web do Azure** mosaico é adicionado ao seu dashboard da descrição geral. Este mosaico mostra uma contagem do número de Web Apps do Azure que a solução tem acesso na sua subscrição do Azure.

![Mosaico de análise de aplicações Web do Azure](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Ver informações de análise de aplicações Web do Azure

Clique em de **análise de aplicações Web do Azure** mosaico para abrir o **análise de aplicações Web do Azure** dashboard. O dashboard inclui os painéis na seguinte tabela. Cada painel lista até dez itens correspondentes aos critérios de nesse painel para o âmbito especificado e o intervalo de tempo. Pode executar uma pesquisa de registo que devolve todos os registos clicando **ver todos os** na parte inferior do painel ou ao clicar no cabeçalho do painel.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| Coluna | Descrição |
| --- | --- |
| Webapps do Azure |   |
| Tendências de pedido de aplicações Web | Mostra um gráfico de linhas da tendência de pedido de aplicações Web para o intervalo de datas que tiver selecionado e mostra uma lista dos pedidos dez web principais. Clique para executar uma pesquisa de registo para o gráfico de linhas<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* (MetricName=Requests OR MetricName=Http*) &#124; measure avg(Average) by MetricName interval 1HOUR</code> <br>Clique num item de pedido web para executar uma pesquisa de registo para a tendência métrica de pedido do web pedido. |
| Tempo de resposta de aplicações Web | Mostra um gráfico de linhas de tempo de resposta de aplicações Web para o intervalo de datas que selecionou. Também mostra uma lista uma lista de topo dez Web resposta de aplicações vezes. Clique em gráfico para executar uma pesquisa de registo para<code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* MetricName="AverageResponseTime" &#124; measure avg(Average) by Resource interval 1HOUR</code><br> Clique numa aplicação Web para executar uma pesquisa de registo devolver tempos de resposta da aplicação Web. |
| Tráfego de aplicações Web | Mostra um gráfico de linhas para o tráfego de aplicações Web, em MB e apresenta uma lista na parte superior no tráfego de aplicações Web. Clique em gráfico para executar uma pesquisa de registo para<code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"*  MetricName=BytesSent OR BytesReceived &#124; measure sum(Average) by Resource interval 1HOUR</code><br> Mostra todas as aplicações Web com o tráfego para o último minuto. Clique numa aplicação Web para executar uma pesquisa de registo que mostra os bytes recebidos e enviados para a aplicação Web. |
| Planos do App Service do Azure |   |
| Planos do App Service com a utilização da CPU &gt; 80% | Mostra o número total de planos de App Service superior a 80% de utilização da CPU e apresenta uma lista de principais 10 planos de App Service através da utilização da CPU. Clique na área total para executar uma pesquisa de registo para<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=CpuPercentage &#124; measure Avg(Average) by Resource</code><br> Mostra uma lista dos seus planos de serviço de aplicações e a utilização média da CPU. Clique num plano do App Service para executar uma pesquisa de registo que mostra a utilização média da CPU. |
| Planos do App Service com a utilização da memória &gt; 80% | Mostra o número total de planos de App Service superior a 80% de utilização da memória e apresenta uma lista de principais 10 planos de App Service através da utilização da memória. Clique na área total para executar uma pesquisa de registo para<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=MemoryPercentage &#124; measure Avg(Average) by Resource</code><br> Mostra uma lista dos seus planos de serviço de aplicações e a respetiva utilização média da memória. Clique num plano do App Service para executar uma pesquisa de registo que mostra a utilização média da memória. |
| Registos de atividade de aplicações Web do Azure |   |
| Auditoria de atividade de aplicações Web do Azure | Mostra o número total de aplicações Web com [registos de atividade](log-analytics-activity.md) e lista as operações de registo de atividade de 10 principais. Clique na área total para executar uma pesquisa de registo para<code>Type=AzureActivity ResourceProvider= "Azure Web Sites" &#124; measure count() by OperationName</code><br> Mostra uma lista das operações de registo de atividade. Clique uma operação de registo de atividade para executar uma pesquisa de registo que apresenta uma lista de registos para a operação. |



### <a name="azure-web-apps"></a>Aplicações Web do Azure

No dashboard, pode desagregar para obter mais informações sobre as métricas de Web Apps. Este primeiro conjunto de mostrar os painéis a tendência dos pedidos de aplicações Web, o número de erros (por exemplo, HTTP404), o tráfego e o tempo de resposta médio ao longo do tempo. Também mostra uma análise detalhada desses métricas para diferentes aplicações Web.

![Painéis de Webapps do Azure](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

É um motivo principal para mostrar os dados para que possa identificar uma aplicação Web com o tempo de resposta elevada e investigar a causa raiz. Um limite do limiar também é aplicado para o ajudar a mais fácil identificar aqueles com problemas.

- As Web Apps mostradas a vermelho têm tempo de resposta superior a 1 segundo.
- As Web Apps mostrados cor de laranja tem um tempo de resposta ou superior segundo 0.7 e menos de 1 segundo.
- As aplicações Web mostradas a verde têm um tempo de resposta inferior a 0,7 segundo.

No registo pesquisa exemplo imagem seguinte, pode ver que o *anugup3* aplicação web tinha um tempo de resposta muito maior do que as outras aplicações web.

![exemplo de pesquisa de registo](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>Planos do App Service

Se estiver a utilizar planos de serviço dedicado, também pode recolher as métricas para os planos de serviço de aplicações. Nesta vista, consulte os planos de serviço de aplicações com elevada utilização de CPU ou memória (&gt; 80%). Mostra também os serviços de aplicação superiores com elevada utilização de memória ou CPU. Da mesma forma, é aplicado um limite do limiar para o ajudar a mais fácil identificar aqueles com problemas.

- Planos do App Service mostrada a vermelho ter uma utilização da CPU/memória superior a 80%.
- Planos de serviço de aplicações mostrados no laranja tem uma utilização da CPU/memória superior a 60% e inferior a 80%.
- Planos do App Service mostrados a verde ter uma utilização da CPU/memória inferior a 60%.

![Painéis planos do App Service do Azure](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Pesquisas de registo de aplicações Web do Azure

O **consultas de lista de populares pesquisa de aplicações de Web do Azure** mostra todos os registos de atividade relacionados para Web Apps, que fornece informações sobre as operações que foram efetuadas nos seus recursos de aplicações Web. Apresenta uma lista também todas as operações relacionadas e o número de vezes que ocorreu.

Utilizar qualquer uma das consultas de pesquisa de registo como um ponto de partida, pode criar facilmente um alerta. Por exemplo, pode querer criar um alerta quando o tempo de resposta médio de uma métrica é superior a cada 1 segundo.

## <a name="next-steps"></a>Passos seguintes

- Criar um [alerta](log-analytics-alerts-creating.md) para métrica específica.
- Utilize [pesquisa registo](log-analytics-log-searches.md) para ver informações detalhadas dos registos de atividade.
