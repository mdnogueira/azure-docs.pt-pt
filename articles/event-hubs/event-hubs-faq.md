---
title: Event Hubs do Azure FAQ | Microsoft Docs
description: Os Hubs de eventos do Azure perguntas mais frequentes (FAQ)
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: c4faa071c4f2401fe3e852e787e3b7d4da0c7d44
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-frequently-asked-questions"></a>Os Event Hubs perguntas mais frequentes

## <a name="general"></a>Geral

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>O que é a diferença entre camadas Standard e Hubs de eventos básicos?

O escalão Standard de Event Hubs do Azure fornece funcionalidades para além de que está disponível na camada básica. As seguintes funcionalidades estão incluídas com o padrão:
* Mais retenção de eventos
* Ligações mediadas adicionais, com um custo de mais do que o número incluído excedida
* Mais do que um único grupo de consumidores
* [Captura](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

Para obter mais informações sobre preços camadas, incluindo dedicado de Hubs de eventos, consulte o [detalhes de preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Quais são as unidades de débito dos Event Hubs?

Selecionar explicitamente unidades de débito dos Event Hubs, através do portal do Azure ou modelos de Gestor de recursos de Hubs de eventos. Unidades de débito se aplicam a todos os event hubs, um espaço de nomes de Event Hubs e cada unidade de débito entitles o espaço de nomes para as seguintes capacidades:

* Segurança 1 MB por segundo de eventos de entrada (eventos enviados para um hub de eventos), mas não existem mais de 1000 eventos de entrada, operações de gestão ou controlo de chamadas à API por segundo.
* Até 2 MB por segundo de eventos de saída (eventos consumidos a partir de um hub de eventos).
* Até 84 GB de armazenamento de eventos (suficiente para o período de retenção de 24 horas predefinido).

Unidades de débito dos Hubs de eventos são faturadas numa base horária, com base no número máximo de unidades selecionado durante a hora especificada. Pode automaticamente [aumentar as número unidades de débito](event-hubs-auto-inflate.md) à medida que aumenta a utilização.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Como os limites de unidade de débito dos Event Hubs são impostos?
Se o débito de entrada total ou a taxa de eventos de entrada total em todos os event hubs num espaço de nomes exceder os allowances de unidade de débito agregado, os remetentes são limitados e irão recebem erros que possam indicar que a quota de entrada foi excedida.

Se o débito de saída total ou a taxa de saída total do evento em todos os event hubs num espaço de nomes exceder os allowances de unidade de débito agregado, recetores estão limitadas e irão recebem erros que possam indicar que a quota de saída foi excedida. As quotas de entrada e de saída são impostas em separado, para que não remetente pode fazer com que o consumo de eventos para mais lentos, nem pode um recetor impedir eventos enviados para um hub de eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Existe um limite em relação ao número de unidades de débito que podem ser selecionadas?
Há uma quota de predefinição de 20 unidades de débito por espaço de nomes. Pode pedir uma quota maior de unidades de débito de apresentação de um pedido de suporte. Além do limite de unidade de 20 débito, os pacotes estão disponíveis em 20 e 100 unidades de débito. Tenha em atenção que utilizar mais de 20 unidades de débito remove a capacidade de alterar o número de unidades de débito sem apresentação de um pedido de suporte.

Utilizar o [Auto-inflate](event-hubs-auto-inflate.md) funcionalidade, pode aumentar automaticamente as número as unidades de débito à medida que aumenta a utilização.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Pode utilizar uma ligação AMQP única para enviar e receber vários dos event hubs?
Sim, desde que todos os hubs de eventos estão no mesmo espaço de nomes.

### <a name="what-is-the-maximum-retention-period-for-events"></a>O que é o período de retenção máximo para os eventos?
Atualmente, o escalão padrão de Hubs de eventos suporta um período de retenção máximo de 7 dias. Tenha em atenção que os event hubs não destinam-se como um arquivo de dados permanente. Períodos de retenção superiores a 24 horas destinam-se cenários em que é conveniente de reprodução de um fluxo de eventos para os mesmos sistemas; Por exemplo, para preparar ou certifique-se de um modelo de machine learning nos dados existentes. Se precisar de mensagens retenção para além de 7 dias, permitindo [capturar os Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) no seu evento hub obtém os dados do seu hub de eventos para a conta de armazenamento ou a conta de serviço do Azure Data Lake à sua escolha. Ativar a captura incorreu cobrada uma taxa com base na sua unidade de débito adquiridas.

### <a name="where-is-azure-event-hubs-available"></a>Onde Event Hubs do Azure está disponível?
Os Hubs de eventos do Azure está disponível em todas as regiões do Azure suportadas. Para obter uma lista, visite o [regiões do Azure](https://azure.microsoft.com/regions/) página.  

## <a name="best-practices"></a>Melhores práticas

### <a name="how-many-partitions-do-i-need"></a>O número de partições que preciso?
. Tenha em atenção que a partição contagem num hub de eventos não pode ser modificada após a configuração. Com que em mente, é importante considerar sobre quantos partições precisa antes de começar a utilizar. 

Hubs de eventos foi concebida para permitir que um leitor de única partição por grupo de consumidores. Na maioria dos casos de utilização, a predefinição de quatro partições é suficiente. Se estiver à procura de dimensionar o processamento de eventos, poderá considerar adicionar partições adicionais. Não há nenhum limite de débito específico numa partição, no entanto, o débito agregado no seu espaço de nomes é limitado pelo número de unidades de débito. Como aumentar o número de unidades de débito no seu espaço de nomes, poderá pretender partições adicionais para permitir que os leitores simultâneos alcançar os seus próprios débito máximo.

No entanto, se tiver um modelo no qual a aplicação tem uma afinidade para uma partição específica, o aumento do número de partições não pode ser qualquer vantagem para si. Para obter mais informações, consulte [disponibilidade e consistência](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre os preços?
Para obter informações completas sobre os preços de Hubs de eventos, consulte o [detalhes de preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>É cobrada uma taxa de retenção de eventos de Hubs de eventos para mais de 24 horas?
O escalão Standard do Event Hubs permitem a retenção de mensagem mais de 24 horas, para um máximo de 7 dias de períodos. Se o tamanho do número total de eventos armazenados exceder o allowance de armazenamento para o número selecionado de unidades de débito (84 GB por unidade de débito), o tamanho que exceda o allowance é cobrado à taxa de armazenamento de Blobs do Azure publicada. O allowance de armazenamento em cada unidade de débito abrange todos os custos de armazenamento para períodos de retenção de 24 horas (predefinição), mesmo se a unidade de débito é utilizada a cópia de segurança para o allowance de entrada máxima.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como é que o tamanho de armazenamento dos Event Hubs é calculado e cobrado?
O tamanho total de todos os eventos armazenados, incluindo quaisquer overhead interno para cabeçalhos de evento ou em estruturas de armazenamento de disco em todos os event hubs, é medido ao longo do dia. No final do dia, é calculado o tamanho de armazenamento máximo. O diário allowance de armazenamento é calculado com base no número mínimo de unidades de débito que foram selecionados durante o dia (cada unidade de débito fornece um allowance de 84 GB). Se o tamanho total exceder o calculada allowance armazenamento diária, o armazenamento em excesso é faturado com taxas de armazenamento de Blobs do Azure (no **armazenamento localmente redundante** taxa).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como são calculados os eventos de entrada de Event Hubs?
Cada evento enviado para um hub de eventos conta como uma mensagem sujeito a faturação. Um *eventos de entrada* está definido como uma unidade de dados que é menor ou igual a 64 KB. Qualquer evento que é menor ou igual a 64 KB de tamanho é considerado um evento sujeito a faturação. Se o evento é superior a 64 KB, o número de eventos sujeito a faturação é calculado, de acordo com o tamanho de evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o hub de eventos é faturado como um evento, mas uma mensagem de 96 KB enviada para o hub de eventos é faturada como dois eventos.

Eventos consumidos a partir de um hub de eventos, como, bem como as operações de gestão e chamadas de controlo, tais como pontos de verificação, não são contabilizadas como eventos de entrada faturável, mas acumular até o allowance de unidade de débito.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Ligação mediadas são aplicáveis encargos para os Event Hubs?
São aplicáveis encargos de ligação apenas quando é utilizado o protocolo AMQP. Não existem sem custos de ligação para o envio de eventos utilizando HTTP, independentemente do número de envio de sistemas ou dispositivos. Se planeia utilizar AMQP (por exemplo, para alcançar mais eficiente do evento de transmissão em fluxo ou para ativar a comunicação bidirecional no comando de IoT e cenários de controlo), consulte o [informações sobre os preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/) página para obter detalhes sobre quantos as ligações são incluídas em cada camada de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como é faturada a Captura dos Hubs de Eventos?
Captura é ativada quando nenhum hub de eventos no espaço de nomes tem a opção de captura ativada. Captura de Hubs de eventos é faturada numa base horária por unidade de débito adquiridas. Como o número de unidade de débito aumentado ou diminuído, capturar os Hubs de eventos faturação reflete estas alterações em incrementos de hora completa. Para obter mais informações sobre faturação capturar os Hubs de eventos, consulte [informações sobre os preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Serão posso cobrado para a conta de armazenamento que para capturar os Hubs de eventos selecionados?
Captura utiliza uma conta de armazenamento que fornecem quando ativado no hub de eventos. Como esta é a sua conta do storage, quaisquer alterações para esta configuração são faturadas à sua subscrição do Azure.

## <a name="quotas"></a>Quotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existem quaisquer quotas associados com os Event Hubs?
Para obter uma lista de todas as quotas de Event Hubs, consulte [quotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelo Event Hubs e respetivas ações sugeridas?
Para obter uma lista de exceções de Hubs de eventos possíveis, consulte [descrição geral de exceções](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Registos de diagnósticos
Os Event Hubs suportam dois tipos de [registos de diagnóstico](event-hubs-diagnostic-logs.md) -captura de registos de erros e registos operacionais - que são representados no json e podem ser ativados através do portal do Azure.

### <a name="support-and-sla"></a>Suporte e SLA
O suporte técnico para os Event Hubs está disponível através de [fóruns da Comunidade](https://social.msdn.microsoft.com/forums/azure/home). O suporte de gestão da faturação e subscrição é fornecido sem custos.

Para saber mais sobre o nosso SLA, consulte o [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/) página.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Inflate automática do Event Hubs](event-hubs-auto-inflate.md)
