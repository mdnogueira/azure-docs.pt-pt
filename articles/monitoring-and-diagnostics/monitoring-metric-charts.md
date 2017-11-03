---
title: "Explorador de métricas de Monitor do Azure | Microsoft Docs"
description: "Saiba mais sobre as novas funcionalidades no Explorador de métricas de Monitor do Azure"
author: vgorbenko
manager: Victor.Mushkatin
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.openlocfilehash: 537dd6d64fe49093dd73d8040cde5a9153a7bd5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-metrics-explorer"></a>Explorador de métricas de Monitor do Azure

Este procedimentos descreve a próxima geração métricas de Monitor de Azure charting experiência que está atualmente em pré-visualização pública. A nova experiência suporta gráficos de composição para multidimensionais métricas e métricas básicas com nenhuma dimensões. Pode desenhar gráficos sobreposição métricas de tipos de recursos diferente, vários grupos de recursos e subscrições. Os gráficos de métricas multidimensionais podem ser personalizados por aplicar os filtros de dimensão, bem como de agrupamento. Qualquer gráfico, incluindo gráficos personalizados pode afixar nos dashboards.

Se está a procurar informações sobre a experiência de antiga que só suporta métricas básicas com nenhuma dimensões, consulte a secção denominada "Métricas através do portal de acesso" no [guia de descrição geral do Microsoft Azure métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="what-is-azure-monitor-metrics-explorer"></a>O que é o Explorador de métricas de Monitor do Azure?

Explorador de métricas de Monitor do Azure é um componente do portal do Microsoft Azure que permite o desenho de gráficos, visualmente correlacionando tendências e investigar picos e dips valores dos métricas. Explorador de métricas é um ponto de partida essencial para investigar vários desempenho e disponibilidade problemas relacionados com as suas aplicações e infraestrutura alojada no Azure ou monitorizado por serviços de Monitor do Azure. 

## <a name="what-are-metrics-in-azure"></a>Quais são as métricas no Azure?

As métricas no Microsoft Azure são a série de valores de medida e contagens que são recolhidas e armazenadas ao longo do tempo. Existem métricas standard (ou "plataforma") e métricas personalizadas. As métricas padrão são que lhe são fornecidas pela plataforma do Azure em si. Métricas padrão refletem as estatísticas de utilização e estado de funcionamento dos seus recursos Azure. Enquanto métricas personalizadas são enviadas para o Azure pelas suas aplicações utilizando o [API do Application Insights para eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Métricas personalizadas são armazenadas nos recursos do Application Insights, juntamente com outras métricas específicas da aplicação.

## <a name="what-are-multi-dimensional-metrics"></a>Quais são as métricas multidimensionais?

Agora, muitos dos recursos do Azure expõem multidimensionais métricas. Estas métricas de controlam várias séries de valores para uma ou várias dimensões com nome. Por exemplo, uma métrica "espaço disponível no disco" pode ter uma dimensão chamada "Unidade" com valores "C:", "D:", que permita a visualização de qualquer espaço em disco disponível em todas as unidades ou para cada unidade individualmente. 

O exemplo abaixo ilustra os dois conjuntos de dados para uma métrica hipotético chamado "Débito de rede". O primeiro conjunto de dados tem não foram dimensões. O conjunto de dados segundo mostra os valores com duas dimensões, "Endereço IP" e "Direção":

### <a name="network-throughput"></a>Débito de rede
(Esta métrica tem não foram dimensões)

 |Timestamp        | Valor métrico de | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 kbps | 
   | 8/9/2017 8:15 | 1,141.4 kbps |
   | 8/9/2017 8:16 | 1,110.2 kbps |

Esta métrica não dimensional pode apenas uma pergunta básica de resposta, como "qual era a minha débito de rede num determinado momento?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Débito de rede + duas dimensões ("IP" e "Direção")

| Timestamp          | Dimensão "IP" | Dimensão "Direção" | Valor métrico de| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Direção = "Enviar"    | 646.5 kbps |
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Direção = "Receber" | 420.1 kbps |
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Direção = "Enviar"    | 150.0 kbps | 
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Direção = "Receber" | 115.2 kbps |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Direção = "Enviar"    | 515.2 kbps |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Direção = "Receber" | 371.1 kbps |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Direção = "Enviar"    | 155.0 kbps |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Direção = "Receber" | 100.1 kbps |

Esta métrica pode responder a perguntas como "qual era o débito de rede para cada endereço IP?" e "quantidade de dados foi enviada recebido?" Métricas multidimensionais transportem valor analítico e de diagnóstico adicional em comparação comparado a métrica não dimensional. 

## <a name="how-do-i-create-a-new-chart"></a>Como criar um novo gráfico?

   > [!NOTE]
   > Algumas das funcionalidades de experiência de métricas antiga ainda não estão disponíveis no Explorador de métricas de novo. Enquanto a nova experiência de pré-visualização, pode continuar a utilizar a vista de métricas (não dimensional) antiga do Monitor do Azure. 

1. Abra o portal do Azure
2. Navegue para o novo **Monitor** separador e, em seguida, selecione **métricas (pré-visualização)**.

   ![Imagem de pré-visualização de métricas](./media/monitoring-metric-charts/001.png)

3. O **Seletor métrica** será automaticamente abrir por si. Escolha um recurso na lista para ver o respetiva métricas associada. Apenas os recursos com métricas são apresentados na lista.

   ![Imagem de pré-visualização de métricas](./media/monitoring-metric-charts/002.png)

   > [!NOTE]
   >Se tiver mais do que uma subscrição do Azure, o Explorador de métricas obtém os recursos em todas as subscrições são selecionadas nas definições do Portal -> Filtrar por lista de subscrições. Para alterá-lo, clique no ícone de equipamento de definições do Portal na parte superior do ecrã e selecione as subscrições às quais pretende utilizar.

4. Para alguns tipos de recursos (ou seja, contas de armazenamento e máquinas virtuais), antes de selecionar uma métrica tem de escolher um **Sub serviço**. Cada serviço sub acarreta o próprio conjunto de métricas que são relevantes para este serviço sub apenas e não para outros serviços sub.

   Por exemplo, cada Storage do Azure tem métricas para subservices "", "Ficheiros", "Filas" tabelas e "Blobs, que são todas as partes da conta de armazenamento". No entanto, a métrica de "contagem de mensagens de fila" é naturalmente aplicável para o subservice "Fila" e não para quaisquer outros subservices de conta de armazenamento.

   ![Imagem de pré-visualização de métricas](./media/monitoring-metric-charts/003.png)

5. Selecione uma métrica da lista. Se souber o nome parcial da métrica que quiser, pode começar a escrever-a para ver uma lista filtrada de métricas disponíveis:

   ![Imagem de pré-visualização de métricas](./media/monitoring-metric-charts/004.png)

6. Depois de selecionar uma métrica, o gráfico irá compor com a agregação predefinida para a métrica selecionada. Neste momento, apenas pode clicar away do **Seletor de métricas** para fechá-lo. Também, opcionalmente, pode mudar o gráfico para uma agregação diferente. Algumas das métricas, de mudança de agregação permite-lhe escolher qual o valor que pretende incluir no gráfico. Por exemplo, pode alternar entre os valores mínimo e máximos médio. 

7. Ao clicar no ícone de adicionar métrica ![ícone de métrica](./media/monitoring-metric-charts/icon001.png) e repetir os passos 3 a 6 pode adicionar mais métricas no mesmo gráfico.

   > [!NOTE]
   > Normalmente, não pretender ter métricas com diferentes unidades de medida (ou seja, "milissegundos" e "quilobytes") ou com uma escala significativamente diferente num gráfico. Em vez disso, considere a utilização de vários gráficos. Clique no botão Adicionar gráfico para criar vários gráficos no Explorador de métricas.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Como se aplicar filtros para os gráficos?

Pode aplicar os filtros para os gráficos que mostram as métricas de dimensões. Por exemplo, se a métrica de "Contagem de transações" tem uma dimensão, "Tipo de resposta", que indica se a resposta de transações com êxito ou falha, em seguida, filtragem nesta dimensão de seria desenhar uma linha de gráfico para apenas com êxito (ou apenas falha) transações. 

### <a name="to-add-a-filter"></a>Para adicionar um filtro:

1. Clique no ícone de adicionar filtro ![ícone de filtro](./media/monitoring-metric-charts/icon002.png) acima do gráfico

2. Selecione a dimensão (propriedade) que pretende filtrar

   ![imagem de métrica](./media/monitoring-metric-charts/006.png)

3. Selecione os valores de dimensão que pretende incluir quando instala o desenho do gráfico (Este exemplo mostra filtrar as transações de armazenamento com êxito):

   ![imagem de métrica](./media/monitoring-metric-charts/007.png)

4. Depois de selecionar os valores de filtro, clique na direção oposta ao Seletor de filtro de para fechá-lo. O gráfico mostra agora falharam quantas transações de armazenamento:

   ![imagem de métrica](./media/monitoring-metric-charts/008.png)

5. Pode repetir os passos 1-4 para aplicar vários filtros para os mesmos gráficos.

## <a name="how-do-i-segment-a-chart"></a>O segmento de um gráfico?

Pode dividir uma métrica pela dimensão para visualizar de forma diferentes segmentos da comparar métrica contra entre si e identificar os segmentos de uma dimensão distantes. 

### <a name="to-segment-a-chart"></a>Para segmentar um gráfico:

1. Clique no ícone de adicionar agrupamento  ![imagem de métrica](./media/monitoring-metric-charts/icon003.png) acima do gráfico.
 
   > [!NOTE]
   > Pode ter vários filtros, mas apenas um agrupamento em qualquer gráfico único.

2. Escolha uma dimensão no qual pretende segmentar o gráfico: 

   ![imagem de métrica](./media/monitoring-metric-charts/010.png)

   O gráfico agora mostra agora várias linhas, um para cada segmento da dimensão:

   ![imagem de métrica](./media/monitoring-metric-charts/012.png)

3. Clique em away do **Seletor agrupamento** para fechá-lo.

   > [!NOTE]
   > Utilize filtragem e de agrupamento na mesma dimensão para ocultar os segmentos que são irrelevantes para o seu cenário e facilitar a leitura de gráficos.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Como posso afixar gráficos para dashboards?

Depois de configurar os gráficos, poderá querer adicioná-lo para os dashboards, para que possa vê-la novamente, possivelmente no contexto de outra telemetria de monitorização, ou partilhar com a sua equipa. 

Para afixar um gráfico configurado a um dashboard:

Depois de configurar o gráfico, clique em de **gráfico ações** menu no lado direito principais área do gráfico e clique em **afixar ao dashboard**.

   ![imagem de métrica](./media/monitoring-metric-charts/013.png)

## <a name="next-steps"></a>Passos seguintes

  Leitura [criar dashboards personalizados do KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) para saber mais sobre as melhores práticas para criar dashboards acionáveis com a métrica.