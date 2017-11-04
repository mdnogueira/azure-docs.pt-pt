---
title: "Consumir dados de monitorização do Azure | Microsoft Docs"
description: "Saiba mais sobre todas as monitorização origens de dados disponíveis no Azure hoje."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2017
ms.author: johnkem
ms.openlocfilehash: c7075c2e1a2500eca1d0aa9b3a797e8a0e903ede
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="consume-monitoring-data-from-azure"></a>Consumir dados de monitorização do Azure

Na plataforma do Azure, podemos colocarem em conjunto dados de monitorização num único local com o Monitor do Azure de pipeline mas praticamente reconhecer que atualmente não todos os dados de monitorização ainda estão disponíveis nesse pipeline. Neste artigo, vamos resumirá as várias formas através de programação podem aceder a dados de monitorização de serviços do Azure.

## <a name="options-for-data-consumption"></a>Opções para consumo de dados

| Tipo de dados | Categoria | Serviços suportados | Métodos de acesso |
| --- | --- | --- | --- |
| Métricas de nível de plataforma do Azure do Monitor | Métricas | [Ver lista aqui](monitoring-supported-metrics.md) | <ul><li>**API de REST:** [API de métrica de Monitor do Azure](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Hub de blob ou evento de armazenamento:** [definições de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Métricas de SO convidado (ex de computação. contadores de desempenho) | Métricas | [Windows](../virtual-machines-dotnet-diagnostics.md) e máquinas virtuais do Linux (v2), [serviços em nuvem](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela de armazenamento ou blob:** [diagnóstico do Windows ou Linux Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Hub de eventos:** [diagnóstico do Windows Azure](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Métricas personalizadas ou da aplicação | Métricas | Qualquer aplicação equipada com Application Insights | <ul><li>**API de REST:** [Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| Métricas do Storage | Métricas | Storage do Azure | <ul><li>**Tabela de armazenamento:** [análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dados de faturação | Métricas | Todos os serviços do Azure | <ul><li>**API de REST:** [APIs de RateCard e utilização de recursos do Azure](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Registo de Atividades | Eventos | Todos os serviços do Azure | <ul><li>**API de REST:** [eventos de monitorização do Azure API](https://docs.microsoft.com/rest/api/monitor/events)</li><li>**Hub de blob ou evento de armazenamento:** [perfil de registo](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Registos de diagnóstico de Monitor do Azure | Eventos | [Ver lista aqui](monitoring-diagnostic-logs-schema.md) | <ul><li>**Hub de blob ou evento de armazenamento:** [definições de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Registos de SO convidado (ex de computação. IIS, ETW, auditáveis) | Eventos | [Windows](../virtual-machines-dotnet-diagnostics.md) e máquinas virtuais do Linux (v2), [serviços em nuvem](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela de armazenamento ou blob:** [diagnóstico do Windows ou Linux Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Hub de eventos:** [diagnóstico do Windows Azure](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Registos do serviço de aplicações | Eventos | Serviços aplicacionais | <ul><li>**Ficheiro, tabela ou o armazenamento de BLOBs:** [Web de diagnóstico de aplicações](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Registos de armazenamento | Eventos | Storage do Azure | <ul><li>**Tabela de armazenamento:** [análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertas do Centro de segurança | Eventos | Centro de Segurança do Azure | <ul><li>**API de REST:** [alertas de segurança](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Relatórios do Active Directory | Eventos | Azure Active Directory | <ul><li>**API de REST:** [gráfico do Azure Active Directory API](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Estado de recursos de centro de segurança | Estado | [Recursos todos suportados](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**API de REST:** [Estados de segurança](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Estado de Funcionamento de Recursos | Estado | Serviços suportados | <ul><li>**API de REST:** [REST API do Estado de funcionamento do recurso](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alertas de métricas de Monitor do Azure | Notificações | [Ver lista aqui](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [alertas métricas do Azure](insights-webhooks-alerts.md)</li></ul> |
| Alertas de registo de atividade do Monitor do Azure | Notificações | Todos os serviços do Azure | <ul><li>**Webhook:** alertas de registo de atividade do Azure</li></ul> |
| Notificações de dimensionamento automático | Notificações | [Ver lista aqui](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [esquema de payload de webhook de notificação de dimensionamento automático](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Alertas de consulta de pesquisa de registo do OMS | Notificações | Análise de registos do OMS | <ul><li>**Webhook:** [alertas de análise de registos](../log-analytics/log-analytics-alerts-actions.md#webhook-actions)</li></ul> |
| Alertas de métricos do Application Insights | Notificações | Application Insights | <ul><li>**Webhook:** [alertas do Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |
| Testes web do Application Insights | Notificações | Application Insights | <ul><li>**Webhook:** [alertas do Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [métricas de Monitor do Azure](monitoring-overview-metrics.md)
- Saiba mais sobre [o registo de atividade do Azure](monitoring-overview-activity-logs.md)
- Saiba mais sobre [registos de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
