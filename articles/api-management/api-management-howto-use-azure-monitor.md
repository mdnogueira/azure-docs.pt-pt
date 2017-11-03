---
title: "Monitorizar a gestão de API com a monitorização do Azure | Microsoft Docs"
description: "Saiba como monitorizar o serviço de API Management do Azure utilizando o Monitor do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-api-management-with-azure-monitor"></a>Gestão de API do monitor com a monitorização do Azure
Monitor do Azure é um serviço Azure que fornece uma única origem para a monitorização de todos os seus recursos do Azure. Com a monitorização do Azure, pode visualizar, consultar, encaminhar, arquivar e executar ações no métricas e registos proveniente de recursos do Azure, tais como a API Management. 

O vídeo seguinte mostra como monitorizar a gestão de API utilizando o Monitor do Azure. Para obter mais informações sobre o Monitor do Azure, consulte [introdução ao Azure Monitor]. 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>Métricas
Gestão de API atualmente emite cinco métricas e planeamos adicionar mais no futuro. Estas métricas são emitidas a cada minuto, dando-lhe quase em tempo real visibilidade sobre o estado e estado de funcionamento das suas APIs. Segue-se um resumo das métricas:
* Total de pedidos de Gateway: o número de API pedidos no período. 
* Pedidos de Gateway com êxito: o número de pedidos de API que recebeu com êxito códigos de resposta HTTP, incluindo 304, 307 e algo mais pequeno do que 301 (por exemplo, 200). 
* Pedidos falhados do Gateway: o número de pedidos de API que receberam errados códigos de resposta HTTP, incluindo 400 e algo maior do que 500.
* Pedidos de Gateway não autorizados: o número de pedidos de API que foi recebido, incluindo 401, 403 e 429 códigos de resposta HTTP. 
* Outros pedidos de Gateway: o número de pedidos de API que receberam códigos de resposta HTTP que não pertencem a qualquer uma das categorias anteriores (por exemplo, 418).

Pode aceder a métricas no seu serviço de API Management, ou as métricas de acesso de todos os seus recursos do Azure no Monitor do Azure. Para ver as métricas no seu serviço de gestão de API:
1. Abra o portal do Azure.
2. Aceda ao seu serviço de API Management.
3. Clique em **métricas**.

![Painel de métricas][metrics-blade]

Para obter mais informações sobre como utilizar as métricas, consulte [descrição geral de métricas].

## <a name="activity-logs"></a>Registos de Atividade
Registos de atividade fornecem informações aprofundadas as operações que foram executadas no seu serviços de gestão de API. Era anteriormente conhecido como "registos de auditoria" ou "registos operacionais". Utilizar registos de atividade, poderá determinar o "o que, quem e quando" para quaisquer operações (PUT, POST, DELETE) efetuadas nos seus serviços de gestão de API de escrita. 

> [!NOTE]
> Registos de atividade inclui as operações de leitura (GET) ou operações executadas no Portal clássico do publicador ou com as APIs de gestão original.

Pode aceder a registos de atividade no seu serviço de API Management ou aceder a registos de todos os seus recursos do Azure no Monitor do Azure. Para ver a atividade regista no seu serviço de gestão de API:
1. Abra o portal do Azure.
2. Aceda ao seu serviço de API Management.
3. Clique em **registo de atividade**.

![Painel de registos de atividade][activity-logs-blade]

Para obter mais informações sobre como utilizar as métricas, consulte [registos de descrição geral da atividade].

## <a name="alerts"></a>Alertas
Pode configurar para receber alertas com base nos registos de métricas e atividade. Monitor do Azure permite-lhe configurar um alerta para fazer o seguinte quando aciona:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma aplicação de lógica do Azure

Pode configurar regras de alerta no seu serviço de API Management, ou no Monitor do Azure. Configurá-las na API Management: 
1. Abra o portal do Azure.
2. Aceda ao seu serviço de API Management.
3. Clique em **regras de alerta**.

![Painel de regras de alertas][alert-rules-blade]

Para obter mais informações sobre como utilizar alertas, consulte [descrição geral dos alertas].

## <a name="diagnostic-logs"></a>Registos de Diagnóstico
Os registos de diagnóstico fornecem informações avançadas sobre operações e erros que são importantes para auditoria, bem como para fins de resolução de problemas. Registos de diagnóstico diferem dos registos de atividade. Registos de atividade fornecem informações sobre as operações que foram executadas no seus recursos do Azure. Registos de diagnóstico fornecem informações aprofundadas operations que o seu recurso efetuadas em si.

A API Management fornece atualmente diagnóstico registos (hora a hora em lotes) sobre API individuais pedem com cada entrada ter a estrutura seguinte:

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

Pode aceder a registos de diagnóstico no seu serviço de API Management ou aceder a registos de todos os seus recursos do Azure no Monitor do Azure. Para ver os registos de diagnóstico no seu serviço de gestão de API:
1. Abra o portal do Azure.
2. Aceda ao seu serviço de API Management.
3. Clique em **registo de diagnóstico**.

![Painel de registos de diagnóstico][diagnostic-logs-blade]

Para obter mais informações sobre como utilizar as métricas, consulte [descrição geral de registos de diagnóstico].

## <a name="next-step"></a>Passo seguinte

* [introdução ao Azure Monitor]
* [descrição geral de métricas]
* [registos de descrição geral da atividade]
* [descrição geral de registos de diagnóstico]
* [descrição geral dos alertas]

[introdução ao Azure Monitor]: ../monitoring-and-diagnostics/monitoring-get-started.md
[descrição geral de métricas]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[registos de descrição geral da atividade]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[descrição geral de registos de diagnóstico]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[descrição geral dos alertas]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png
