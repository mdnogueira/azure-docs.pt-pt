---
title: "Registos de diagnóstico do Azure Service Bus | Microsoft Docs"
description: "Saiba como configurar registos de diagnóstico para o Service Bus no Azure."
keywords: 
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/16/2017
ms.author: sethm
ms.openlocfilehash: 2bf65b7c5b0518da59e767db18fe6f4193e0ab6e
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="service-bus-diagnostic-logs"></a>Registos de diagnóstico do Service Bus

Pode ver dois tipos de registos do Service Bus do Azure:
* **[Registos de atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Estes registos contêm informações sobre operações executadas numa tarefa. Os registos estão sempre ativados.
* **[Os registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Pode configurar os registos de diagnóstico para informações mais completas sobre tudo o que acontece dentro de uma tarefa. Registos de diagnóstico abrange atividades desde o momento em que é criada a tarefa até que a tarefa é eliminada, incluindo atualizações e as atividades que ocorrem enquanto a tarefa está em execução.

## <a name="turn-on-diagnostic-logs"></a>Ativar registos de diagnóstico

Registos de diagnóstico estão desativados por predefinição. Para ativar os registos de diagnóstico, execute os seguintes passos:

1.  No [portal do Azure](https://portal.azure.com), em **monitorização + gestão**, clique em **registos de diagnóstico**.

    ![navegação do painel para os registos de diagnóstico](./media/service-bus-diagnostic-logs/image1.png)

2. Clique no recurso que pretende monitorizar.  

3.  Clique em **ative os diagnósticos**.

    ![Ativar registos de diagnóstico](./media/service-bus-diagnostic-logs/image2.png)

4.  Para **estado**, clique em **no**.

    ![alterar os registos de diagnóstico de estado](./media/service-bus-diagnostic-logs/image3.png)

5.  Definir o destino de arquivo que pretende; Por exemplo, uma conta de armazenamento, um hub de eventos ou Log Analytics do Azure.

6.  Guarde as novas definições de diagnóstico.

Novas definições entram em vigor no cerca de 10 minutos. Depois disso, os registos são apresentados no destino arquivo configurado, no **registos de diagnóstico** painel.

Para obter mais informações sobre a configuração de diagnósticos, consulte o [descrição geral dos registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Esquema de registos de diagnóstico

Todos os registos são armazenados no formato de JavaScript Object Notation (JSON). Cada entrada tem campos de cadeia que utilizam o formato descrito na secção seguinte.

## <a name="operational-logs-schema"></a>Esquema de registos operacionais

Iniciar sessão a **OperationalLogs** categoria capturar o que acontece durante as operações de Service Bus. Especificamente, estes registos capturam o tipo de operação, incluindo a criação da fila, os recursos utilizados e o estado da operação.

Cadeias JSON do registo operacional incluem elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
ActivityId | ID interno, utilizado para o controlo
EventName | Nome da operação           
resourceId | ID de recurso do Azure Resource Manager
SubscriptionId | ID da subscrição
EventTimeString | Tempo de operação
EventProperties | Propriedades de operação
Estado | Estado da operação
Autor da chamada | Autor da chamada da operação (Azure portal ou de gestão do cliente)
categoria | OperationalLogs

Eis um exemplo de um registo operacional cadeia JSON:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Passos seguintes

Visite as seguintes ligações para saber mais acerca do Service Bus:

* [Introdução ao Service Bus](service-bus-messaging-overview.md)
* [Introdução ao Service Bus](service-bus-dotnet-get-started-with-queues.md)
