---
title: "Barramento de serviço preços e faturação | Microsoft Docs"
description: "Descrição geral da estrutura de preços do Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 8f693bc51fc9635fae4376137e7e573bf74da7cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-pricing-and-billing"></a>Barramento de serviço preços e faturação
Barramento de serviço é oferecido na padrão e [Premium](service-bus-premium-messaging.md) camadas. Pode escolher uma camada de serviço para cada espaço de nomes de serviço de barramento de serviço que criar e esta seleção do escalão aplica-se em todas as entidades criadas dentro desse espaço de nomes.

> [!NOTE]
> Para obter informações detalhadas sobre os preços de barramento de serviço atual, consulte o [página de preços de Service Bus do Azure](https://azure.microsoft.com/pricing/details/service-bus/)e o [FAQ de barramento de serviço](service-bus-faq.md#pricing).
>
>

O Service Bus utiliza as seguintes medidores 2 para filas e tópicos/subscrições:

1. **Operações de mensagens**: definido como chamadas de API nos pontos finais de serviço fila ou um tópico/subscrição. Esta medição substitui as mensagens enviadas ou recebidas como a unidade primária de utilização facturável de filas e tópicos/subscrições.
2. **Mediadas ligações**: definido como o número máximo de ligações persistentes abrir contra filas, tópicos ou subscrições durante um período de amostragem de uma hora especificada. Esta medição só se aplica no escalão Standard, na qual pode abrir ligações adicionais (anteriormente, as ligações foram limitadas a 100 por tópico/fila/subscrição) para uma taxa de nominal por ligação.

O **padrão** camada apresenta preços graduated para operações executadas com as filas e tópicos/subscrições, resultando em descontos baseada no volume de até 80%, o nível de utilização mais elevada. Também é cobrada uma camada Standard base taxa de $10 por mês, o que permite-lhe efetuar operações de 12,5 milhões por mês, sem custos adicionais.

O **Premium** camada fornece isolamento de recursos na camada de CPU e memória para que cada carga de trabalho do cliente seja executado de forma isolada. Este contentor de recursos é designado por *unidade de mensagens*. A cada espaço de nomes premium é atribuído, pelo menos, uma unidade de mensagens. Pode comprar 1, 2 ou 4 unidades de mensagens para cada espaço de nomes Premium do Service Bus. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade, embora a faturação seja realizada com base em tarifas diárias ou de 24 horas. O resultado é um desempenho previsível e repetível da sua solução com base no Service Bus. Este desempenho não só é mais previsível e repetível, como também mais rápido.

Tenha em atenção que a taxa de base do escalão Standard é-lhe cobrada apenas uma vez por mês por subscrição do Azure. Isto significa que depois de criar um espaço de nomes de barramento de serviço de camada única padrão, pode criar tantas padrão espaços de nomes adicionais como pretende que nessa mesma subscrição do Azure, sem incorrer em custos adicionais de base.

O [preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) tabela resume as diferenças funcionais entre as camadas Standard e Premium.

## <a name="messaging-operations"></a>Operações de mensagens
Como parte do novo modelo de preços, faturação de filas e tópicos/subscrições está a mudar. Estas entidades são transição de faturação por mensagem para faturação por operação. Uma operação"" refere-se a qualquer chamada à API efetuada em relação a uma fila ou um tópico/subscrição ponto final de serviço. Isto inclui as operações de estado de gestão, o envio/receção e a sessão.

| Tipo de Operação | Descrição |
| --- | --- |
| Gestão |Crie, ler, atualizar, eliminar (CRUD) contra filas ou tópicos/subscrições. |
| Mensagens |Enviar e receber mensagens com as filas ou tópicos/subscrições. |
| Estado da sessão |Obter ou definir o estado da sessão num tópico/subscrição ou fila. |

Para detalhes de custo, consulte os preços apresentados no [preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) página.

## <a name="brokered-connections"></a>Ligações mediadas
*Mediadas ligações* acomodar padrões de utilização do cliente que envolvem um grande número de "forma permanente ligadas" remetentes por recetores contra filas, tópicos ou subscrições. Os remetentes de forma permanente de ligado/recetores são aqueles que se ligam através de AMQP ou HTTP com um diferente de zero recebem o tempo limite (por exemplo, HTTP longo consulta). Os remetentes HTTP e os recetores com um tempo limite imediato não geram ligações mediadas.

Para quotas de ligação e outros limites de serviço, consulte o [quotas de Service Bus](service-bus-quotas.md) artigo. Para obter mais informações sobre ligações mediadas, consulte o [FAQ](#faq) secção neste artigo.

O escalão Standard remove o limite de ligação mediadas por espaço de nomes e contagens de utilização de agregação de ligação mediadas entre a subscrição do Azure. Para obter mais informações, consulte o [mediadas ligações](https://azure.microsoft.com/pricing/details/service-bus/) tabela.

> [!NOTE]
> 1000 ligações mediadas estão incluídas com o escalão de serviço de mensagens Standard (através de encargos base) e podem ser partilhadas entre todas as filas, tópicos e subscrições dentro da subscrição do Azure associada.
>
>

<br />

> [!NOTE]
> A faturação baseia-se no número máximo de ligações simultâneas e é calculada pro rata à hora com base em 744 horas por mês.
>
>

### <a name="premium-tier"></a>Escalão Premium

As ligações mediadas não são cobradas no escalão Premium.

## <a name="faq"></a>FAQ

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Quais são mediadas ligações e como posso obter cobrado-las?
Uma ligação mediada é definida de uma das seguintes formas:

1. Uma ligação AMQP de um cliente para uma fila de barramento de serviço ou um tópico/subscrição.
2. Uma chamada HTTP para receber uma mensagem de um tópico ou fila do Service Bus com um valor de tempo limite superior a zero.

Custos de barramento de serviço para o número máximo de ligações mediadas simultâneas que excedam a quantidade incluída (1.000 no escalão Standard). Os picos são medidos por hora, calculados pro rata (proporcionalmente) dividindo pelas 744 horas de um mês, e somados ao longo do período de faturação mensal. A quantidade incluída (1000 ligações mediadas por mês) é aplicada no final do período de faturação relativamente à soma dos picos calculados pro rata à hora.

Por exemplo:

1. Cada 10 000 dispositivos liga através de uma ligação AMQP único e recebe comandos a partir de um tópico de barramento de serviço. Os dispositivos enviam eventos de telemetria para um Hub de eventos. Se ligam todos os dispositivos para 12 horas por dia, são aplicáveis encargos de ligação seguinte (para além de quaisquer outros Service Bus tópico encargos): ligações de 10 000 * 12 horas * 31 dias / 744 = 5.000 mediadas ligações. Depois do allowance mensal de 1000 ligações mediadas, seria cobrado para 4000 ligações mediadas, à taxa de $0.03 por ligação mediada para um total de $120.
2. 10.000 dispositivos recebem mensagens de uma fila do Service Bus através de HTTP, especificando um tempo limite de diferente de zero. Se ligam todos os dispositivos para 12 horas todos os dias, verá os encargos de ligação seguinte (para além de outros custos de Service Bus): 10 000 ligações HTTP receber * 12 horas por dia * horas 744/31 dias = 5.000 mediadas ligações.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Aplicam-se custos de ligações mediadas a filas e tópicos/subscrições?
Sim. Não existem sem custos de ligação para o envio de eventos utilizando HTTP, independentemente do número de envio de sistemas ou dispositivos. Receber eventos com HTTP utilizando o limite de tempo maior que zero, por vezes denominado "período de tempo de consulta," gera ligação mediadas encargos. As ligações AMQP geram custos de ligações mediadas independentemente de as ligações estarem a ser utilizadas para envio ou receção. As primeiro 1000 ligações mediadas entre todos os espaços de nomes padrão numa subscrição do Azure são incluídas, sem encargos adicionais (se para além dos encargos base). Porque estes allowances são suficientes para cobrir os vários cenários de mensagens de serviços, os encargos de ligação mediadas normalmente apenas se relevante se planeia utilizar AMQP ou HTTP longa-consulta com um grande número de clientes; Por exemplo, para alcançar mais eficiente do evento de transmissão em fluxo ou ativar a comunicação bidirecional com vários dispositivos ou instâncias de aplicações.

## <a name="next-steps"></a>Passos seguintes
* Para obter detalhes completos sobre os preços do Service Bus, consulte o [página de preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Consulte o [FAQ de barramento de serviço](service-bus-faq.md#pricing) para algumas perguntas mais frequentes comuns acerca do Service bus preços e faturação.

[Azure portal]: https://portal.azure.com
