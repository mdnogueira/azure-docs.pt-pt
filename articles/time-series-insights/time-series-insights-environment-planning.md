---
title: "Planear a escala do seu ambiente de informações de séries de tempo do Azure | Microsoft Docs"
description: "Este artigo descreve como a seguir as melhores práticas quando planear um ambiente de informações de séries de tempo do Azure, incluindo a capacidade de armazenamento, retenção de dados, capacidade de entrada e de monitorização."
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 5fb158ba162dd199f419f9568de08a7a18c833dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planear o ambiente de informações de séries de tempo do Azure

Este artigo descreve como planear o ambiente de informações de séries de tempo do Azure com base no seu taxa de entrada esperado e os requisitos de retenção de dados.

## <a name="best-practices"></a>Melhores práticas

Para obter uma introdução das informações de séries de tempo, é melhor se sabe que quantidade de dados pretende push por minuto, bem como tempo de que necessita armazenar os dados.  

Para mais informações sobre a capacidade e de retenção para ambos os SKUs de informações de série de tempo, consulte [Insights de séries de tempo de preços](https://azure.microsoft.com/pricing/details/time-series-insights/).

Tenha em consideração os seguintes atributos para o plano de melhor o ambiente para o sucesso de longo prazo: 
- Capacidade de armazenamento
- Período de retenção de dados
- Capacidade de entrada 

## <a name="understand-storage-capacity"></a>Compreender a capacidade de armazenamento
Por predefinição, o Insights de séries de tempo retém dados com base na quantidade de armazenamento aprovisionou (unidades vezes quantidade de armazenamento por unidade) e de entrada.

## <a name="understand-data-retention"></a>Compreender a retenção de dados
Pode configurar o ambiente de tempo série Insights **período de retenção de dados** definição, a ativação de retenção até 400 dias.  Informações de séries de tempo tem dois modos, um otimizado para garantir o seu ambiente tem os dados mais atualizados (por predefinição) e outro que otimiza para assegurar a retenção limites são cumpridos, onde entrada está em pausa se a capacidade de armazenamento geral para o atingiu o ambiente.  Pode ajustar a retenção e alternar entre os dois modos de página de configuração do ambiente no portal do Azure.

Pode configurar um máximo de dias 400 de retenção de dados no seu ambiente de informações de séries de tempo.

## <a name="configure-data-retention"></a>Configurar a retenção de dados

1. No [portal do Azure](https://portal.azure.com), selecione o seu ambiente de informações de séries de tempo.

2. No **página de ambiente de informações de séries de tempo**, sob o **definições** cabeçalho, selecione **configurar**. 

3. No **período de retenção de dados (em dias)** box, introduza um valor de 1 para 400.

   ![Configurar a retenção](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Compreender a capacidade de entrada

Outra área para se focarem em para planeamento é capacidade de entrada, o que é um derivativo de atribuição por minuto. 

Numa perspetiva de limitação, um pacote de dados ingressed com um tamanho de pacotes de 32 KB é tratado como 32 eventos, cada um tamanho de 1 KB. O tamanho máximo do evento permitido é de 32 KB; pacotes de dados superiores a 32 KB serão truncados.

A tabela seguinte resume a capacidade de entrada para cada SKU:

|SKU  |Contagem de eventos por mês, por unidade  |Tamanho de eventos por mês, por unidade  |Contagem de eventos por minuto, por unidade  | Tamanho por minuto, por unidade   |
|---------|---------|---------|---------|---------|
|S1     |   30 milhões     |  30 GB     |  700    |  700 KB   |
|S2     |   milhões de 300    |   300 GB   | 7,000   | 7.000 KB  |

Pode aumentar a capacidade de uma S1 ou S2 SKU 10 unidades num ambiente único. Não é possível migrar a partir de um ambiente de S1 para um S2 ou de um ambiente de S2 para um S1. 

A capacidade de entrada, primeiro deve determinar a entrada total que requerem numa base por mês. Em seguida, determine quais o por minuto necessidades são, que é onde limitação e latência desempenham um papel.

Se tiver um pico de pedidos na sua entrada de dados lasting inferior a 24 horas, informações de séries de tempo pode "catch-up" a uma taxa de entrada de 2 x as taxas de listados acima. 

Por exemplo, se tiver uma única S1 SKU e os dados de entrada uma taxa de 700 eventos por minuto e o pico de pedidos para inferior a 1 hora, uma taxa de eventos de 1400 ou menos, não haveria nenhum latência considerável ao seu ambiente. No entanto, se exceder 1400 eventos por minuto durante mais de uma hora, provavelmente seria sofrem latência para dados que são visualizadas e disponíveis para consulta no seu ambiente. 

Não pode saber antecipadamente quantidade de dados pretende push. Neste caso, pode encontrar a telemetria dados [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e [Event Hubs do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) no portal do Azure. Esta telemetria pode ajudá-lo a determinar como aprovisionar o seu ambiente. Utilize o **métricas** página no portal do Azure para a origem de evento correspondentes ver a telemetria. Se compreender as métricas de origem do evento, pode planear com mais eficiência e aprovisionar o seu ambiente de informações de séries de tempo.

## <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

- O confirme a capacidade de entrada é superior a taxa média de por minuto e de que o seu ambiente é suficientemente grande para processar a entrada prevista equivalente ao 2x a capacidade para inferior a 1 hora.

- Se ocorrerem picos de entrada que última durante um período superior a 1 hora, utilize a taxa de pico de pedidos como a média e aprovisionar um ambiente com capacidade para processar a taxa de pico de pedidos.
 
## <a name="mitigate-throttling-and-latency"></a>Mitigar limitação e latência

Para obter informações sobre como evitar limitação e latência, consulte [mitigar latência e limitação](time-series-insights-environment-mitigate-latency.md). 

## <a name="next-steps"></a>Passos seguintes
- [Como adicionar uma origem de evento do Hub de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Como adicionar uma origem de evento do IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md)
