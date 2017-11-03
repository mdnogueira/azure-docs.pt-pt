---
title: Configurar alertas de consultas no Stream Analytics | Microsoft Docs
description: "Análise de fluxo de compreensão de alerta"
keywords: configurar alertas
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/26/2017
ms.author: samacha
ms.openlocfilehash: cdbd7da36f3eb2795fc4548915ebbf18200ca44d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para tarefas do Azure Stream Analytics
## <a name="introduction-monitor-page"></a>Introdução: Página de Monitor
Pode configurar alertas para acionar um alerta quando uma métrica atinge uma condição que especificar. Por exemplo, pode configurar um alerta para uma condição como o seguinte:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Regras pode ser configuradas nas métricas através do portal, ou podem ser configuradas [programaticamente](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre dados de registos de operações.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no portal do Azure
1. No portal do Azure, abra a tarefa de Stream Analytics que pretende criar um alerta para. 

2. No **tarefa** painel, clique em de **monitorização** secção.  

3. No **métrica** painel, clique em de **Adicionar alerta** comando.

      ![Configuração do portal do Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Introduza um nome e uma descrição.

5. Utilize os seletores para definir a condição em que o alerta será enviado.

6. Fornece informações sobre onde deve passar o alerta.

      ![Configurar um alerta para uma tarefa de análise de transmissão em fluxo do Azure](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Para obter mais detalhes sobre como configurar alertas no portal do Azure, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-get-started.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

