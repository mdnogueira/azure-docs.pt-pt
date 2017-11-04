---
title: "Descrição geral dos alertas no Microsoft Azure e o Monitor do Azure | Microsoft Docs"
description: "Alertas permitem-lhe monitorizar métricas de recurso do Azure, eventos ou os registos e ser notificado quando for cumprida uma condição que especificar."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afa863e2a900d4f823b77453d92f034db7d5a93f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-alerts-in-microsoft-azure"></a>O que são alertas no Microsoft Azure?
Este artigo descreve várias origens de alertas no Microsoft Azure, que está os fins de alertas, os benefícios e como começar a utilizá-los. -La especificamente aplica-se a monitorização do Azure, mas disponibiliza apontadores para outros serviços, bem como os alertas. Alertas oferecem um método de monitorização no Azure que permite-lhe configurar condições sobre dados e ficar notificado quando as condições corresponderem aos dados monitorização mais recentes.

## <a name="taxonomy-of-azure-alerts"></a>Taxonomia de alertas do Azure
Azure utiliza os seguintes termos para descrever as respetivas funções e de alertas:
* **Alerta** -uma definição de critérios (um ou mais regras ou condições) que torna-se ativado quando cumpridos.
* **Active Directory** -o estado quando os critérios definidos por um alerta são cumpridos.
* **Resolvido** -o estado quando os critérios definidos por um alerta já não for cumprida após ter anteriormente foram cumpridos.
* **Notificação** - a ação tomada baseada em termina a sessão de um alerta se tornar Active Directory.
* **Ação** -uma chamada específica enviada para um recetor de uma notificação (por exemplo, relacionada um endereço ou publicação para um URL do webhook). As notificações, normalmente, podem acionar várias ações.

## <a name="alerts-in-different-azure-services"></a>Alertas nas diferentes serviços do Azure
Alertas estão disponíveis no Azure vários serviços de monitorização. Para obter informações sobre como e quando utiliza estes serviços [consulte este artigo](./monitoring-overview.md). Aqui está uma repartição dos tipos de alertas disponível através do Azure:

| Serviço | Tipo de alerta | Serviços suportados | Descrição |
|---|---|---|---|
| Azure Monitor | [Métricas alertas](./insights-alerts-portal.md) | [Métricas suportadas do Monitor do Azure](./monitoring-supported-metrics.md) | Receber uma notificação quando qualquer métrica de nível de plataforma cumpre uma condição específica (por exemplo, numa VM de CPU % é superior a 90 durante os últimos 5 minutos). |
|Azure Monitor | [Quase em tempo real alertas métricas (pré-visualização)](./monitoring-near-real-time-metric-alerts.md)| [Recursos suportados a partir de Monitor do Azure](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | Receber uma notificação mais rapidamente do que a métricas alertas quando um ou mais métricas de nível de plataforma cumprem as condições especificadas (por exemplo, numa VM de CPU % é superior a 90 e rede é maior do que 500 MB para os últimos 5 minutos). |
| Azure Monitor | [Alertas de registo de atividade](./monitoring-activity-log-alerts.md) | Todos os tipos de recursos disponíveis no Gestor de recursos do Azure | Receber uma notificação quando um novo evento no [registo de atividade do Azure](./monitoring-overview-activity-logs.md) corresponde condições específicas (por exemplo, quando uma operação de "Eliminar VM" ocorre em myProductionResourceGroup ou quando um novo evento de estado de funcionamento do serviço com "Ativo" como o é apresentado o estado). |
| Application Insights | [Métricas alertas](../application-insights/app-insights-alerts.md) | Qualquer aplicação instrumentada para enviar dados para o Application Insights | Receber uma notificação quando qualquer métrica de nível de aplicação cumpre uma condição específica (por exemplo, tempo de resposta do servidor é maior do que 2 segundos). |
| Application Insights | [Alertas de teste Web](../application-insights/app-insights-monitor-web-app-availability.md) | Qualquer Web site instrumentada para enviar dados para o Application Insights | Receba uma notificação quando a disponibilidade ou capacidade de resposta de um Web site é inferior a expetativas. |
| Log Analytics | [Alertas de análise do registo](../log-analytics/log-analytics-alerts.md) | Qualquer serviço configurado para enviar dados para análise de registos | Receba uma notificação quando uma consulta de pesquisa de análise de registos através de dados de métrica de e/ou eventos cumpra determinados critérios. |

## <a name="alerts-on-azure-monitor-data"></a>Alertas nos dados de monitorização do Azure
Existem três tipos de alertas retire dados disponível a partir do Monitor do Azure – métricas alertas, quase em tempo real alertas métricas (pré-visualização) e alertas de registo de atividade.

* **Alertas métricas** -este alerta é acionado quando o valor de uma métrica especificado atravesse um limiar que atribuir. O alerta gera uma notificação quando o alerta é "ativado" (quando o limiar for ultrapassado e a condição de alerta for cumprida), bem como quando for "resolvida" (quando o limiar é cruzado novamente e a condição já não for cumprida). Para obter uma lista crescente de métricas disponíveis suportadas pelo monitor do Azure, consulte [lista das métricas suportadas no Monitor de Azure](monitoring-supported-metrics.md).
* **Quase em tempo real alertas métricas (pré-visualização)** - estes alertas são semelhantes aos alertas métricas mas diferem no algumas formas. Firstly, como o nome sugere estes alertas podem acionar em praticamente em tempo real (tão rápido como 1 min). Também suportam várias métricas (atualmente dois) de monitorização.  O alerta gera uma notificação quando o alerta é "ativado" (quando os limiares para cada métrica são ultrapassados ao mesmo tempo e a condição de alerta for cumprida), bem como quando for "resolvida" (quando, pelo menos, uma métrica atravesse o limiar novamente e a condição é nenhuma já está preenchido).

> [!NOTE]
> Quase em tempo real métrica alertas estão atualmente em pré-visualização pública. A experiência de utilizador e a funcionalidade está sujeita a alterações.
>
>

* **Alertas de registo de atividade** -um alerta de registo de transmissão em fluxo que é acionado quando é gerado um evento de registo de atividade que corresponde ao filtrar os critérios que foram atribuídos. Estes alertas tem apenas um Estado "Ativado,", uma vez que o motor de alerta simplesmente aplica-se os critérios de filtro para qualquer novo evento. Estes alertas podem ser utilizados para ser notificado quando ocorre um novo incidente de estado de funcionamento do serviço ou quando um utilizador ou aplicação efetua uma operação na sua subscrição, por exemplo, "Eliminar a máquina virtual."

Para dados de registo de diagnóstico disponíveis através do Monitor do Azure, sugerimos que encaminhar os dados para análise de registos e a utilização de um alerta de análise de registos. O diagrama a seguir resume as origens de dados no Monitor do Azure e, concecionais, como podem alertá retire dados.

![Alertas explicadas](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Como posso receber uma notificação num alerta de Monitor do Azure?
Historicamente, os alertas do Azure a partir de diferentes serviços utilizado os seus próprios métodos de notificação incorporada. Doravante, o Monitor de Azure oferece um agrupamento de notificação reutilizáveis denominado grupos de ação. Grupos de ação especifiquem um conjunto de recetores para uma notificação – qualquer número de endereços de correio eletrónico, números de telefone (para o SMS) ou URLs de webhook - e sempre que um alerta é ativado que referencia o grupo de ação, todos os recetores recebem essa notificação. Isto permite-lhe reutilizar um agrupamento de recetores (por exemplo, a lista de engenheiro chamada no) em muitos objetos de alerta. Atualmente, apenas os alertas de registo de atividade tornar a utilização de grupos de ação, mas estão a funcionar utilizando a ação grupos, bem como vários outros tipos de alerta do Azure.

Grupos de ação suportam notificações através da publicação para um URL do webhook para além dos endereços de e-mail e números SMS. Isto permite a automatização e remediação, por exemplo, utilizando:
    - Runbook da Automatização do Azure
    - Função do Azure
    - Aplicação lógica do Azure
    - um serviço de terceiros

Quase em tempo real métrica alertas (pré-visualização), alertas e o registo de atividade utilizam grupos de ação.

Alertas de métricas não ainda a utilizar grupos de ação. Num alerta métrico individual pode configurar notificações para:
* Envie notificações por e-mail para o administrador de serviço para os coadministradores ou para endereços de e-mail adicionais que especificar.
* Chame um webhook, o que permite iniciar ações adicionais de automatização.

## <a name="next-steps"></a>Passos seguintes
Obter informações sobre regras de alertas e configurá-los utilizando:

* Saiba mais sobre [métricas](monitoring-overview-metrics.md)
* Configurar [métrica alertas através do portal do Azure](insights-alerts-portal.md)
* Configurar [métrica alertas do PowerShell](insights-alerts-powershell.md)
* Configurar [interface de linha de comandos de alertas de métrica (CLI)](insights-alerts-command-line-interface.md)
* Configurar [métrica alertas de monitorização do Azure REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Saiba mais sobre [registo de atividade](monitoring-overview-activity-logs.md)
* Configurar [alertas de registo de atividade através do portal do Azure](monitoring-activity-log-alerts.md)
* Configurar [alertas de registo de atividade através do Gestor de recursos](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Reveja o [esquema de webhook alerta de registo de atividade](monitoring-activity-log-alerts-webhook.md)
* Saiba mais sobre [quase em tempo real métrica alertas](monitoring-near-real-time-metric-alerts.md)
* Saiba mais sobre [notificações de serviço](monitoring-service-notifications.md)
* Saiba mais sobre [grupos de ação](monitoring-action-groups.md)
