---
title: "Métricas de Event Hubs do Azure no Monitor do Azure (pré-visualização) | Microsoft Docs"
description: "Utilizar a monitorização do Azure para monitorizar os Event Hubs"
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: sethm
ms.openlocfilehash: c3ee6a87592145801167b35ff281c04062e525da
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Métricas de Event Hubs do Azure no Monitor do Azure (pré-visualização)

Métricas de Hubs de eventos dá-lhe o estado dos Event Hubs recursos na sua subscrição do Azure. Com um vasto conjunto de dados de métricas, pode avaliar o estado de funcionamento geral os hubs de eventos, não apenas ao nível do espaço de nomes, mas também ao nível da entidade. Estas estatísticas podem ser importantes como ajudam-na monitorizar o estado dos seus hubs de eventos. Métricas também podem ajudar a resolver problemas de causa raiz sem ser necessário contactar o suporte do Azure.

Monitor do Azure fornece interfaces de utilizador unificadas monitorização através de vários serviços do Azure. Para obter mais informações, consulte [monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e [métricas de obter Monitor do Azure com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) em GitHub.

## <a name="access-metrics"></a>Métricas de acesso

Monitor do Azure fornece várias formas de métricas de acesso. Poderá utilizar qualquer uma das métricas de acesso através de [portal do Azure](https://portal.azure.com), ou utilizar as APIs de Monitor do Azure (REST e .NET) e soluções de análise, tais como operação Management Suite e Hubs de eventos. Para obter mais informações, consulte [métricas de Monitor de Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

As métricas são ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de manter os dados para um período de tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [definições de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) no Monitor do Azure.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar métricas ao longo do tempo no [portal do Azure](https://portal.azure.com). O exemplo seguinte mostra como visualizar os pedidos com êxito e pedidos recebidos ao nível da conta:

![][1]

Também pode aceder métricas diretamente através do espaço de nomes. Para tal, selecione o seu espaço de nomes e, em seguida, clique em **métricas (Peview)**. A apresentação das métricas filtradas ao âmbito do hub de eventos, selecione o hub de eventos e, em seguida, clique em **métricas (pré-visualização)**.

Com base nas métricas que suportam dimensões, tem de filtro com o valor de dimensão pretendido, conforme mostrado no exemplo seguinte:

![][2]

## <a name="billing"></a>Faturação

A utilização de métricas no Monitor do Azure é atualmente livre em pré-visualização. No entanto, se utilizar soluções adicionais que ingestão de dados de métricas, é poderá ser-cobrada por estas soluções. Por exemplo, é-lhe faturado o armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Também são faturadas por conjunto de gestão da operação (OMS) se a transmitir dados de métricas para OMS para análise avançada.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Iremos são descontinuar a várias métricas que são movidos sob um nome diferente. Isto pode requerer a atualizar as referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas a passa.

Todos os valores da métrica são enviados para o Monitor de Azure cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores da métrica são apresentados. O intervalo de tempo suportado para todas as métricas de Event Hubs é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedido

Conta o número de pedidos de operações de gestão e de dados.

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| Pedidos de entrada (pré-visualização) | O número de pedidos efetuados para o serviço de Event Hubs do Azure durante um período especificado. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Pedidos com êxito (pré-visualização)   | O número de pedidos efetuados para o serviço de Event Hubs do Azure durante um período especificado com êxito. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Erros de servidor (pré-visualização) | O número de pedidos não processada devido a um erro no serviço de Event Hubs do Azure durante um período especificado. <br/><br/>Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
|Erros de utilizador (pré-visualização)|O número de pedidos não processada devido a erros de utilizador durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Pedidos otimizados (pré-visualização)|O número de pedidos que foram limitadas porque a utilização da unidade de débito foi excedida.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Quota excedida erros (pré-visualização)|O número de pedidos excedeu a quota disponível. Consulte [neste artigo](event-hubs-quotas.md) para obter mais informações sobre as quotas de Event Hubs.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="throughput-metrics"></a>Métricas de débito

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Pedidos otimizados (pré-visualização)|O número de pedidos que foram limitadas porque a utilização da unidade de débito foi excedida.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="message-metrics"></a>Métricas de mensagem

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens a receber (pré-visualização)|O número de eventos ou as mensagens enviadas para os Event Hubs num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída (pré-visualização)|O número de eventos ou mensagens obtidos a partir do Event Hubs num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes recebidos (pré-visualização)|O número de bytes enviados para o serviço de Event Hubs do Azure durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes enviados (pré-visualização)|O número de bytes obtidos do serviço de Event Hubs do Azure através de um período de tempo especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections (pré-visualização)|O número de ligações ativas um espaço de nomes, bem como uma entidade.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Aberto de ligações (pré-visualização)|O número de ligações abertas.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Ligações fechado (pré-visualização)|O número de ligações fechados.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="event-hubs-capture-metrics"></a>Métricas de capturar os Hubs de eventos

Pode monitorizar as métricas de captura de Hubs de eventos quando ativar a funcionalidade de captura para os hubs de eventos. As métricas seguintes descrevem o que pode monitorizar com captura ativada.

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Capturar o registo de segurança (pré-visualização)|O número de bytes que ainda estão a ser capturadas para o destino escolhido.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens capturadas (pré-visualização)|O número de mensagens ou eventos são capturados para o destino escolhido durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes capturadas (pré-visualização)|O número de bytes que são capturados para o destino escolhido durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

Os Hubs de eventos do Azure suporta as seguintes dimensões para as métricas no Monitor do Azure. A adição de dimensões para as métricas é opcional. Se não adicionar dimensões, métricas são especificadas ao nível do espaço de nomes. 

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|EntityName| Os Event Hubs suporta as entidades de hub de eventos sob o espaço de nomes.|

## <a name="next-steps"></a>Passos seguintes

* Consulte o [descrição geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* [Obter métricas de Monitor do Azure com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) em GitHub. 

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



