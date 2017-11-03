---
title: Diagnosticar e resolver problemas | Microsoft Docs
description: "Este tutorial abrange como diagnosticar e resolver problemas no seu ambiente de informações de séries de tempo"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.openlocfilehash: 4e10a009eb67706d927ece5692134d802094cdf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnosticar e resolver problemas no seu ambiente de informações de séries de tempo

## <a name="i-dont-see-my-data"></a>Não vejo o meu dados
Seguem-se alguns motivos por que motivo não poderá ver os dados no seu ambiente no [portal das informações de séries de tempo de Azure](https://insights.timeseries.azure.com).

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>A origem do evento não tem dados no formato JSON
As informações de séries de tempo do Azure suporta apenas os dados JSON hoje. Para exemplos JSON, consulte [formas de JSON suportado](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>Quando se registou a origem de evento, não fornecer a chave que tenha a permissão necessária
* Para um hub IoT, tem de fornecer a chave que tenha **serviço ligar** permissão.

   ![Permissão de ligação de serviço do hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Conforme mostrado na imagem anterior, qualquer uma das políticas **iothubowner** e **serviço** funcionarão, porque têm **serviço ligar** permissão.
* Para um hub de eventos, terá de fornecer a chave que tenha **escutar** permissão.

   ![Permissão de escuta de hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Conforme mostrado na imagem anterior, qualquer uma das políticas **ler** e **gerir** funcionarão, porque têm **escutar** permissão.

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>O grupo de consumidores fornecido não é exclusivo Insights de séries de tempo
Para um hub IoT ou hub de eventos, durante o registo é necessário especificar o grupo de consumidores que deve ser utilizado para ler os dados. Este grupo de consumidores não tem de ser partilhado. Se for partilhado, o hub de eventos subjacente automaticamente desliga um dos leitores aleatoriamente.

## <a name="i-see-my-data-but-theres-a-lag"></a>Posso ver os meus dados, mas não existe um desfasamento
Eis os motivos por que razão poderá ver dados parciais no seu ambiente no [portal das informações de séries de tempo](https://insights.timeseries.azure.com).

### <a name="your-environment-is-getting-throttled"></a>O ambiente está a obter limitado
O limite de limitação é imposto com base no tipo SKU do ambiente e a capacidade. Todas as origens de eventos no ambiente de partilham esta capacidade. Se a origem do evento para o seu IoT hub ou hub de eventos está a enviar dados que ultrapassem os limites impostos, verá limitação e um atraso.

O diagrama seguinte mostra um ambiente de informações de séries de tempo que tem um SKU de S1 e uma capacidade de 3. Pode eventos de 3 milhões de entrada por dia.

![Capacidade atual da SKU do ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Partem do princípio de que este ambiente está a ingestão relacionadas mensagens de um hub de eventos com a taxa de entrada mostrada no diagrama seguinte:

![Taxa de entrada de exemplo para um hub de eventos](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Como é mostrado no diagrama, a taxa diária de entrada é ~ 67,000 mensagens. Esta taxa traduz-se aproximadamente 46 mensagens cada minuto. Se cada mensagem de hub de eventos é simplificada para um único evento Insights de séries de tempo, este ambiente não verá nenhuma limitação. Se cada mensagem de hub de eventos é simplificada para 100 eventos Insights de séries de tempo, em seguida, 4,600 eventos devem ser ingeridos cada minuto. Ambiente S1 SKU que tem uma capacidade de 3 pode eventos de entrada apenas 2,100 cada minuto (1 milhão de eventos por dia = 700 eventos por minuto em 3 unidades = 2,100 eventos por minuto). Por conseguinte, verá um desfasamento devido à limitação. 

Para uma compreensão como funciona o flattening lógica, consulte [formas de JSON suportado](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Passos recomendados
Para corrigir o desfasamento, aumenta a capacidade SKU do seu ambiente. Para obter mais informações, consulte [como dimensionar o ambiente de tempo série Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>Está a enviar por push dados históricos e fazendo com que a entrada lenta
Se estiver a ligar uma origem de evento existente, é provável que o IoT hub ou hub de eventos já tiver dados no mesmo. Por isso, o ambiente inicia extrair dados a partir do início do período de retenção de mensagens da origem de evento. 

Este comportamento é o comportamento predefinido e não pode ser substituído. Pode interagir com limitação, e pode demorar algum tempo para acompanhar no ingestão de dados históricos.

#### <a name="recommended-steps"></a>Passos recomendados
Para corrigir o desfasamento, siga os passos seguintes:
1. Aumenta a capacidade SKU para o valor máximo permitido (10 neste caso). Depois de aumenta a capacidade, o processo de entrada é iniciado obtendo segurança muito mais rapidamente. Pode visualizar a rapidez, obtendo cópias de segurança, o gráfico de disponibilidade no [portal das informações de séries de tempo](https://insights.timeseries.azure.com). São-lhe cobrados para a capacidade de aumento.
2. Depois do desfasamento é atualizado, diminua a capacidade SKU de volta a taxa de entrada normal.

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>A minha origem de evento *nome da propriedade timestamp* definição não funciona
Certifique-se de que o nome e valor em conformidade com as seguintes regras:
* É o nome da propriedade timestamp _maiúsculas e minúsculas_.
* O valor da propriedade timestamp que provêm da sua origem de evento, como uma cadeia JSON, deve ter o formato _aaaa-MM-ddTHH. FFFFFFFK_. Um exemplo de uma cadeia de tal é "2008-04-12T12:53Z".
