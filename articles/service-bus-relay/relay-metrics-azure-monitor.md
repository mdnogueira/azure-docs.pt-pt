---
title: "Métricas de reencaminhamento do Azure no Monitor do Azure (pré-visualização) | Microsoft Docs"
description: "Utilizar a monitorização do Azure para monitorizar o reencaminhamento do Azure"
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2017
ms.author: sethm
ms.openlocfilehash: 3652e80c20c425570ba90a1f3ce7a3035762a34d
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Métricas de reencaminhamento do Azure no Monitor do Azure (pré-visualização)

Métricas de reencaminhamento do Azure dá-lhe o estado dos recursos na sua subscrição do Azure. Com um vasto conjunto de dados de métricas, pode avaliar o estado de funcionamento geral dos seus recursos de reencaminhamento, não apenas ao nível do espaço de nomes, mas também ao nível da entidade. Estas estatísticas podem ser importantes como ajudam-na monitorizar o estado do reencaminhamento do Azure. Métricas também podem ajudar a resolver problemas de causa raiz sem ser necessário contactar o suporte do Azure.

Monitor do Azure fornece interfaces de utilizador unificadas monitorização através de vários serviços do Azure. Para obter mais informações, consulte [monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e [métricas de obter Monitor do Azure com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) em GitHub.

## <a name="access-metrics"></a>Métricas de acesso

Monitor do Azure fornece várias formas de métricas de acesso. Poderá utilizar qualquer uma das métricas de acesso através de [portal do Azure](https://portal.azure.com), ou utilizar as APIs de Monitor do Azure (REST e .NET) e soluções de análise, tais como operação Management Suite e Hubs de eventos. Para obter mais informações, consulte [métricas de Monitor de Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

As métricas são ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de manter os dados para um período de tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [definições de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) no Monitor do Azure.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar métricas ao longo do tempo no [portal do Azure](https://portal.azure.com). O exemplo seguinte mostra como visualizar os pedidos com êxito e pedidos recebidos ao nível da conta:

![][1]

Também pode aceder métricas diretamente através do espaço de nomes. Para tal, selecione o seu espaço de nomes e, em seguida, clique em **métricas (Peview)**. 

Com base nas métricas que suportam dimensões, tem de filtro com o valor de dimensão pretendido.

## <a name="billing"></a>Faturação

A utilização de métricas no Monitor do Azure é atualmente livre em pré-visualização. No entanto, se utilizar soluções adicionais que ingestão de dados de métricas, é poderá ser-cobrada por estas soluções. Por exemplo, é-lhe faturado o armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Também são faturadas por conjunto de gestão da operação (OMS) se a transmitir dados de métricas para OMS para análise avançada.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Iremos são descontinuar a várias métricas que são movidos sob um nome diferente. Isto pode requerer a atualizar as referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas a passa.

Todos os valores da métrica são enviados para o Monitor de Azure cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores da métrica são apresentados. O intervalo de tempo suportado para todas as métricas de reencaminhamento do Azure é de 1 minuto.

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| ListenerConnections-êxito (pré-visualização) | O número de ligações de serviço de escuta com êxito efetuados para reencaminhamento do Azure durante um período especificado. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ClientError (pré-visualização)|O número de erros de cliente em ligações de serviço de escuta durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ServerError (pré-visualização)|O número de erros de servidor nas ligações de serviço de escuta durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-êxito (pré-visualização)|O número de ligações de remetente bem-sucedida feita durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ClientError (pré-visualização)|O número de erros de cliente em ligações de remetente num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ServerError (pré-visualização)|O número de erros de servidor em ligações de remetente num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-TotalRequests (pré-visualização)|O número total de ligações de serviço de escuta durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-TotalRequests (pré-visualização)|Pedidos de ligação efetuados pelos remetentes durante um período especificado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveConnections (pré-visualização)|O número de ligações ativas num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveListeners (pré-visualização)|O número de serviços de escuta ativos num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerDisconnects (pré-visualização)|O número de serviços de escuta desligados durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderDisconnects (pré-visualização)|O número de remetentes desligados durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="memory-usage-metrics"></a>Métrica de utilização de memória

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|BytesTransferred (pré-visualização)|O número de bytes transferidos durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

Reencaminhamento do Azure suporta as seguintes dimensões para as métricas no Monitor do Azure. A adição de dimensões para as métricas é opcional. Se não adicionar dimensões, métricas são especificadas ao nível do espaço de nomes. 

|Nome da dimensão|Descrição|
| ------------------- | ----------------- |
|EntityName| Reencaminhamento do Azure suporta entidades de mensagens sob o espaço de nomes.|

## <a name="next-steps"></a>Passos seguintes

Consulte o [descrição geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




