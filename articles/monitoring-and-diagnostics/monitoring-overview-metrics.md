---
title: "Descrição geral das métricas no Microsoft Azure | Microsoft Docs"
description: "Descrição geral das métricas e a sua utilização no Microsoft Azure"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: johnkem
ms.openlocfilehash: eb519aab87c13e8836bf1d41992812762f0cd737
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Descrição geral das métricas no Microsoft Azure
Este artigo descreve quais são as métricas no Microsoft Azure, os seus benefícios e como começar a utilizá-los.  

## <a name="what-are-metrics"></a>Quais são as métricas?
Monitor do Azure permite-lhe consumir telemetria ganhar visibilidade sobre o desempenho e estado de funcionamento das suas cargas de trabalho no Azure. O tipo de dados de telemetria do Azure mais importante é as métricas (também denominadas de contadores de desempenho) emitidas pelo Azure mais recursos. Monitor do Azure fornece várias formas de configurar e consumir estas métricas de monitorização e resolução de problemas.

## <a name="what-can-you-do-with-metrics"></a>O que fazer com a métrica?
As métricas são uma origem de telemetria importante e permitem-lhe efetuar as seguintes tarefas:

* **Monitorizar o desempenho** do seu recurso (por exemplo, uma aplicação VM, o Web site ou lógica) ao representar a métricas num gráfico portal e que o gráfico a um dashboard de afixação.
* **Seja notificado de um problema** que tem impacto no desempenho do seu recurso quando uma métrica atravesse um determinado limiar.
* **Configurar as ações automatizadas**, tais como o dimensionamento automático de um recurso ou acionando um runbook quando uma métrica atravesse um determinado limiar.
* **Efetuar análises avançadas** ou relatórios sobre tendências de desempenho ou a utilização do seu recurso.
* **Arquivo** o histórico de desempenho ou o estado de funcionamento do seu recurso **para compatibilidade ou auditoria** fins.

## <a name="what-are-the-characteristics-of-metrics"></a>Quais são as características de métricas?
Métricas de ter as seguintes características:

* Todas as métricas tem **frequência de um minuto**. Recebe um valor métrico de cada minuto do seu recurso, dando-lhe quase em tempo real visibilidade sobre o estado e estado de funcionamento dos seus recursos.
* As métricas são **disponíveis imediatamente**. Não precisa de optar ativamente por participar no ou configurar diagnósticos adicionais.
* Pode aceder ao **30 dias do histórico** para cada métrica. Pode ver rapidamente as tendências recentes e mensais no desempenho ou do Estado de funcionamento dos seus recursos.
* Alguns métricas podem ter atributos de pares nome-valor chamados **dimensões**. Estas permitem-lhe segmentar ainda mais e explorar uma métrica de forma mais significativa.

Também pode:

* Configurar uma métrica **alerta regra que envia uma notificação ou demora automatizada ação** quando a métrica atravesse o limiar que definiu. Dimensionamento automático é uma ação automática especial que permite-lhe aumentar horizontalmente o recurso para satisfazer os pedidos recebidos ou carrega no seu Web site ou recursos informáticos. Pode configurar uma regra de definição de dimensionamento automático para aumentar ou reduzir com base numa métrica de cruzamento entre um limiar.

* **Rota** todas as métricas do Application Insights ou análise de registos (OMS) para ativar a análise instantânea, pesquisa e alertas personalizados em dados de métricas dos seus recursos. Também pode transmitir métricas para um Hub de eventos, permitindo-lhe encaminhá-las para o Azure Stream Analytics ou para aplicações personalizadas para análise de quase em tempo real. Configurar o Hub de eventos com as definições de diagnóstico de transmissão em fluxo.

* **As métricas para o armazenamento de arquivo** de retenção mais longa ou utilizá-las para relatórios offline. Pode encaminhar as métricas para o armazenamento de Blobs do Azure quando configurar as definições de diagnóstico para o seu recurso.

* Facilmente detetar, o acesso, e **ver todas as métricas** através do portal do Azure, quando seleciona um recurso e desenhar as métricas num gráfico.

* **Consumir** as métricas através das novas APIs de REST do Monitor do Azure.

* **Consulta** métricas utilizando os cmdlets do PowerShell ou a API de REST de várias plataformas.

  ![Encaminhamento de métricas no Monitor do Azure](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Métricas de acesso através do portal
Segue-se um rápido obter instruções sobre como criar um gráfico de métrico utilizando o portal do Azure.

### <a name="to-view-metrics-after-creating-a-resource"></a>Para ver métricas depois de criar um recurso
1. Abra o portal do Azure.
2. Crie um Web site do App Service do Azure.
3. Depois de criar um Web site, vá para o **descrição geral** painel do Web site.
4. Pode ver a nova métrica como um **monitorização** mosaico. Em seguida, pode editar o mosaico e selecionar métricas mais.

   ![Métricas sobre um recurso no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Para aceder a todas as métricas num único local
1. Abra o portal do Azure.
2. Navegue para o novo **Monitor** separador e, em seguida e selecione o **métricas** opção abaixo dele.
3. Selecione a sua subscrição, o grupo de recursos e o nome do recurso na lista pendente.
4. Ver a lista de métricas disponíveis. Em seguida, selecione a métrica que está interessado e desenhá-lo.
5. Pode afixá-lo ao dashboard clicando o pin no canto superior direito.

   ![Aceder a todas as métricas num único local no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Pode aceder ao nível do anfitrião métricas de VMs (baseado em Gestor de recursos do Azure) e conjuntos de dimensionamento de máquina virtual sem qualquer configuração de diagnóstico adicional. Estas métricas de nível do anfitrião novas estão disponíveis para as instâncias de Windows e Linux. Estas métricas não são deve ser confundido com as métricas de SO-ao nível do convidado se tem acesso para quando a ativar o diagnóstico do Azure nas VMs ou dimensionamento da máquina virtual conjuntos. Para saber mais sobre a configuração de diagnósticos, consulte [que é o Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Monitor do Azure também tem uma nova métrica charting experiência disponível na pré-visualização. Esta experiência permite aos utilizadores as métricas de vários recursos de um gráfico de sobreposição. Os utilizadores também podem representar, segmento e filtrar métricas multidimensionais com esta nova métrica charting experiência. Para saber mais [clique aqui](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Métricas de acesso através da API REST
As métricas do Azure podem ser acedidas através das APIs de Monitor do Azure. Existem duas APIs que o ajudam a detetar e aceder métricas:

* Utilize o [definições de métrica de Monitor de Azure REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions) para aceder à lista de métricas e dimensões, que estão disponíveis para um serviço.
* Utilize o [API REST da Azure Monitor métricas](https://docs.microsoft.com/en-us/rest/api/monitor/metrics) segmentar, filtrar e aceder os dados de métricas real.

> [!NOTE]
> Este artigo abrange as métricas através de [nova API com base nas métricas](https://docs.microsoft.com/en-us/rest/api/monitor/) para recursos do Azure. A versão da API para as novas definições de métricas e métricas APIs é 2017-05-01-pré-visualização. As definições de métricas de legado e métricas, podem ser acedidas com a API versão 2014-04-01.
>
>

Para obter instruções mais detalhadas com as APIs de REST de Monitor do Azure, consulte [instruções de API REST da Azure Monitor](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Métricas de exportação
Pode ir para o **as definições de diagnóstico** painel sob o **Monitor** separador e ver as opções de exportação para as métricas. Pode selecionar métricas (e os registos de diagnóstico) para ser encaminhados para o Blob storage, Event Hubs do Azure ou para OMS para casos de utilização que foram mencionados anteriormente neste artigo.

 ![Opções de exportação para as métricas no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)

Pode configurar isto através de modelos do Resource Manager, [PowerShell](insights-powershell-samples.md), [CLI do Azure](insights-cli-samples.md), ou [REST APIs](https://msdn.microsoft.com/library/dn931943.aspx).

## <a name="take-action-on-metrics"></a>Execute as ações nas métricas
Para receber notificações ou executar ações automatizadas dados métricos, pode configurar regras de alerta ou definições de dimensionamento automático.

### <a name="configure-alert-rules"></a>Configurar regras de alertas
Pode configurar regras de alertas nas métricas. Estas regras de alerta podem verificar se uma métrica ultrapassou um determinado limiar. Existem dois capacidades de alertas métricas oferecidas pelo Monitor do Azure.

Alertas de métricas: em seguida, pode notificá-lo por e-mail ou acionados um webhook que pode ser utilizado para executar qualquer script personalizado. Também pode utilizar o webhook para configurar integrações de produto de terceiros.

 ![Métricas e regras de alertas no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

Perto da alertas em tempo real (pré-visualização): estes têm a capacidade de monitorizar várias métricas e limiares, para um recurso e, em seguida, receber notificações através de um [ação grupo](/monitoring-action-groups.md). Mais informações sobre como Leran [quase em tempo real métrica alertas aqui](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>O dimensionamento automático o seu Azure recursos
Alguns recursos do Azure suportam o dimensionamento de saída ou de várias instâncias para processar cargas de trabalho. Aplica-se do dimensionamento automático do serviço de aplicações (aplicações Web), conjuntos de dimensionamento de máquina virtual e os serviços de nuvem do Azure clássico. Pode configurar regras de dimensionamento automático para aumentar a saída ou no quando uma métrica que afeta a sua carga de trabalho atravesse um limiar que especificar. Para obter mais informações, consulte [descrição geral de dimensionamento automático](monitoring-overview-autoscale.md).

 ![Métricas e dimensionamento automático no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Saiba mais sobre serviços suportados e métricas
Pode ver uma lista detalhada de todos os serviços suportados e os respetivos métricas [métricas de Monitor do Azure – métricas suportadas por tipo de recurso](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Passos seguintes
Consulte as ligações ao longo deste artigo. Além disso, saiba mais sobre:  

* [Métricas de comuns de dimensionamento automático](insights-autoscale-common-metrics.md)
* [Como criar regras de alertas](insights-alerts-portal.md)
* [Analisar os registos do armazenamento do Azure com a análise de registos](../log-analytics/log-analytics-azure-storage.md)
