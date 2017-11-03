---
title: "Configurar webhooks em alertas métricas do Azure | Microsoft Docs"
description: "Reroute alertas do Azure para outros sistemas não do Azure."
author: johnkemnetz
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 8b3ae540-1d19-4f3d-a635-376042f8a5bb
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: johnkem
ms.openlocfilehash: 1a885166e5c71f13da222bfc22b0fc579096c52f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurar um webhook num alerta métrico do Azure
Webhooks permitem-lhe uma notificação de alerta do Azure de rotas para outros sistemas de ações de pós-processamento ou personalizados. Pode utilizar um webhook num alerta para o encaminhar para os serviços que enviar SMS, registo de erros, notificar a equipa através de serviços de chat/mensagens ou fazer qualquer número de outras ações. Este artigo descreve como definir um webhook num alerta métrico do Azure e aspeto o payload para o POST de HTTP para um webhook. Para obter informações sobre a configuração e o esquema para um alerta de registo de atividade do Azure (alerta em eventos), [consulte em vez disso, esta página](insights-auditlog-to-webhook-email.md).

Alertas do Azure HTTP POST alerta conteúdos JSON formato, esquema definida abaixo, para um webhook URI que fornecem ao criar o alerta. Este URI tem de ser um ponto final HTTP ou HTTPS válido. Azure mensagens uma entrada por pedido, quando um alerta é ativado.

## <a name="configuring-webhooks-via-the-portal"></a>Como configurar webhooks através do portal
Pode adicionar ou atualizar o webhook URI no ecrã de alertas de Create/Update no [portal](https://portal.azure.com/).

![Adicionar uma regra de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)

Também pode configurar um alerta para publicar um URI de webhook utilizando o [Cmdlets do PowerShell do Azure](insights-powershell-samples.md#create-metric-alerts), [CLI de várias plataformas](insights-cli-samples.md#work-with-alerts), ou [API REST da Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticar o webhook
O webhook pode autenticar através de autorização baseada em tokens. Guardar o webhook URI está com um ID de token, por exemplo. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema de payload
A operação POST contém a seguinte payload JSON e o esquema para todos os alertas com base em métrica.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Campo | Obrigatório | Fixo conjunto de valores | Notas |
|:--- |:--- |:--- |:--- |
| status |S |"Ativado", "Resolvido" |Estado do alerta baseados nas condições que definiu. |
| Contexto |S | |O contexto do alerta. |
| carimbo de data/hora |S | |A hora em que o alerta foi acionado. |
| ID |S | |Cada regra de alerta tem um id exclusivo. |
| nome |S | |O nome do alerta. |
| descrição |S | |Descrição do alerta. |
| conditionType |S |"Métrica", "Evento de" |São suportados dois tipos de alertas. Um baseada numa condição de métrica e outra com base num evento no registo de atividade. Utilize este valor para verificar se o alerta é baseado num métrica ou eventos. |
| Condição |S | |Os campos específicos para procurar com base no conditionType. |
| metricName |existência de alertas métricas | |O nome da métrica que define o que monitoriza a regra. |
| metricUnit |existência de alertas métricas |"Bytes", "BytesPerSecond", "Count", "CountPerSecond", "Percentagem", "Segundos" |A unidade permitida na métrica. [Permitido valores estão listados aqui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |existência de alertas métricas | |O valor real da métrica que causou o alerta. |
| Limiar |existência de alertas métricas | |O valor de limiar em que o alerta está ativado. |
| windowSize |existência de alertas métricas | |O período de tempo que é utilizado para monitorizar a atividade de alerta com base no limiar. Tem de estar entre 5 minutos e 1 dia. Formato de duração ISO 8601. |
| timeAggregation |existência de alertas métricas |"Médio", "Último", "Máximo", "Mínimo", "None", "Total de" |Como devem ser combinados os dados que são recolhidos ao longo do tempo. O valor predefinido é média. [Permitido valores estão listados aqui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operador |existência de alertas métricas | |O operador utilizado para comparar os dados métricos atuais para o limiar definido. |
| subscriptionId |S | |ID da subscrição do Azure. |
| resourceGroupName |S | |Nome do grupo de recursos para o recurso afetado. |
| resourceName |S | |Nome de recurso do recurso afetado. |
| resourceType |S | |Tipo de recurso do recurso afetado. |
| resourceId |S | |ID de recurso do recurso afetado. |
| resourceRegion |S | |Região ou localização do recurso afetado. |
| portalLink |S | |Ligação direta para a página de resumo do portal de recursos. |
| propriedades |N |Opcional |Conjunto de `<Key, Value>` pares (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. O campo de propriedades é opcional. Num personalizada da IU ou lógica baseada na aplicação fluxo de trabalho, os utilizadores podem introduzir chaves/valores que pode ser transmitidos através do payload. A forma alternativa para passar as propriedades personalizadas para o webhook é através do uri de webhook (como parâmetros de consulta) |

> [!NOTE]
> O campo de propriedades só pode ser definido utilizando o [API REST da Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre alertas do Azure e webhooks as vídeo [integrar alertas do Azure com PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
* [Executar scripts de automatização do Azure (Runbooks) nos alertas do Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Utilize a aplicação lógica para enviar um SMS através do Twilio a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* [Utilize a aplicação lógica para enviar uma mensagem Slack a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* [Utilize a aplicação lógica para enviar uma mensagem para uma fila do Azure a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
