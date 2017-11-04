---
title: "Utilizar a API de REST de alerta de análise de registo do OMS"
description: "A API de REST de alerta de análise do registo permite-lhe criar e gerir alertas na análise de registos que faz parte do Operations Management Suite (OMS).  Este artigo fornece detalhes sobre a API e vários exemplos para efetuar operações diferentes."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ce72ffef4394bf3bbe39fa420c4fcaa965ae35c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Criar e gerir regras de alertas na análise de registos com a REST API
A API de REST de alerta de análise do registo permite-lhe criar e gerir alertas no Operations Management Suite (OMS).  Este artigo fornece detalhes sobre a API e vários exemplos para efetuar operações diferentes.

A API de REST de pesquisa de análise do registo é RESTful e pode ser acedida através da API de REST do Azure Resource Manager. Este documento irá encontrar exemplos em que a API é acedida a partir de uma linha de comandos do PowerShell utilizando [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comandos de código aberto que simplifica a invocar a API do Azure Resource Manager. A utilização de ARMClient e do PowerShell é uma das muitas opções para aceder à API de pesquisa de análise do registo. Com estas ferramentas podem utilizar o Gestor de recursos API RESTful do Azure para efetuar chamadas a áreas de trabalho do OMS e executar comandos de pesquisa dentro delas. A API irá enviar os resultados da pesquisa para si no formato JSON, permitindo-lhe utilizar os resultados da pesquisa de várias maneiras diferentes através de programação.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas só podem ser criados com uma procura guardada na análise de registos.  Pode consultar o [API de REST do registo de pesquisa](log-analytics-log-search-api.md) para obter mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa guardada pode ter uma ou mais agendas. A agenda define a frequência a pesquisa está a executar e o intervalo de tempo durante o qual é identificado os critérios.
As agendas de tem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| intervalo |Frequência de pesquisa é executada. Medido em minutos. |
| QueryTimeSpan |O intervalo de tempo durante o qual é avaliada os critérios. Tem de ser igual ou superior ao intervalo. Medido em minutos. |
| Versão |A versão de API que está a ser utilizada.  Atualmente, este deve ser sempre definido para 1. |

Por exemplo, considere uma consulta de eventos com um intervalo de 15 minutos e um Timespan de 30 minutos. Neste caso, a consulta deverá ser executada a cada 15 minutos e, seria possível acionar a um alerta se os critérios continuaram a resolver para verdadeiro por um intervalo de 30 minutos.

### <a name="retrieving-schedules"></a>A obter agendas
Utilize o método Get para obter todas as agendas de uma procura guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Utilize o método Get com um ID de agenda para obter uma determinada agenda para uma pesquisa guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Segue-se uma resposta de amostra de uma agenda.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Criar uma agenda
Utilize o método Put com um ID exclusivo de agendamento para criar uma nova agenda.  Tenha em atenção que as duas agendas não podem ter o mesmo ID mesmo que se encontrem associados com diferentes das procuras guardadas.  Quando criar uma agenda na consola do OMS, um GUID é criado para o ID de agenda.

> [!NOTE]
> O nome para pesquisas guardadas todos, agendas e criadas com a API de análise do registo de ações tem de ser em minúsculas.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Editar uma agenda
Utilize o método Put com um ID de agenda existente para a mesma pesquisa guardada para modificar essa agenda.  O corpo do pedido tem de incluir o etag da agenda.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Agendas de eliminação
Utilize o método Delete com um ID de agenda para eliminar uma agenda.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Ações
Uma agenda pode ter várias ações. Uma ação pode definir uma ou mais processos para efetuar como enviar uma mensagem ou iniciar um runbook ou pode definir um limiar que determina quando os resultados de uma procura correspondem alguns critérios.  Algumas ações vai definir ambos para que os processos são efetuados quando é cumprido o limiar.

Todas as ações de ter as propriedades na tabela seguinte.  Diferentes tipos de alertas têm diferentes propriedades adicionais que são descritas abaixo.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |Tipo de ação.  Atualmente, os valores possíveis são alerta e o Webhook. |
| Nome |Nome a apresentar para o alerta. |
| Versão |A versão de API que está a ser utilizada.  Atualmente, este deve ser sempre definido para 1. |

### <a name="retrieving-actions"></a>A obter ações
Utilize o método Get para obter todas as ações para uma agenda.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilize o método Get com o ID de ação para obter uma ação específica para uma agenda.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Criação ou edição de ações
Utilize o método Put com um ID de ação que é exclusivo para a agenda para criar uma ação de novo.  Quando cria uma ação na consola do OMS, um GUID é para o ID de ação.

> [!NOTE]
> O nome para pesquisas guardadas todos, agendas e criadas com a API de análise do registo de ações tem de ser em minúsculas.

Utilize o método Put com um ID de ação existente para a mesma pesquisa guardada para modificar essa agenda.  O corpo do pedido tem de incluir o etag da agenda.

O formato do pedido para criar uma nova ação varia consoante o tipo de ação para que estes exemplos são fornecidos nas secções abaixo.

### <a name="deleting-actions"></a>Eliminar ações
Utilize o método Delete com o ID de ação para eliminar uma ação.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Ações de alerta
Uma agenda deve ter um e apenas uma ação de alerta.  Ações de alerta de ter um ou mais das secções na seguinte tabela.  Cada um é descrita mais detalhadamente abaixo.

| Section | Descrição |
|:--- |:--- |
| Limiar |Critérios de quando é executada a ação. |
| EmailNotification |Envie correio para vários destinatários. |
| Remediação |Inicie um runbook na automatização do Azure para tentar corrigir o problema identificado. |

#### <a name="thresholds"></a>Limiares
Uma ação de alerta deve ter um e apenas um limiar.  Quando os resultados de uma procura guardada coincidir com o limiar de uma ação associada essa procura, quaisquer outros processos nessa ação são executados.  Uma ação também pode conter apenas um limiar para que possa ser utilizado com as ações de outros tipos que não contenham limiares.

Limiares de ter as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| operador |Operador de comparação de limiar. <br> gt = maior que <br> lt = inferior a |
| Valor |Valor para o limiar. |

Por exemplo, considere uma consulta de eventos com um intervalo de 15 minutos, Timespan de 30 minutos e um limiar de maior que 10. Neste caso, a consulta deverá ser executada a cada 15 minutos e seria possível acionar um alerta se devolveu 10 eventos que foram criados ao longo de um intervalo de 30 minutos.

Segue-se uma resposta de amostra de uma ação com apenas um limiar.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Utilize o método Put com um ID exclusivo de ação para criar uma nova ação de limiar para uma agenda.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Utilize o método Put com um ID de ação existente para modificar uma ação de limiar para uma agenda.  O corpo do pedido tem de incluir o etag da ação.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notificação por e-mail
Notificações por e-mail enviam correio para um ou mais destinatários.  Incluem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Destinatários |Lista de endereços de correio. |
| Assunto |O assunto do correio. |
| Anexo |Anexos não são atualmente suportados, pelo que este terão sempre um valor de "None". |

Segue-se uma resposta de amostra de uma ação de notificação de correio eletrónico com um limiar.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Utilize o método Put com um ID exclusivo de ação para criar uma nova ação de correio eletrónico para uma agenda.  O exemplo seguinte cria uma notificação por e-mail com um limiar para que o correio eletrónico é enviado quando os resultados da pesquisa guardada excedem o limiar.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Utilize o método Put com um ID de ação existente para modificar uma ação de correio eletrónico para uma agenda.  O corpo do pedido tem de incluir o etag da ação.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Ações de remediação
Remediações iniciar um runbook na automatização do Azure que tentam corrigir o problema identificado pelo alerta.  Tem de criar um webhook para o runbook utilizado na ação de remediação e, em seguida, especifique o URI na propriedade WebhookUri.  Ao criar esta ação utilizando a consola do OMS, é criado automaticamente um novo webhook para o runbook.

Remediações incluem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| RunbookName |Nome do runbook. Isto deve corresponder ao runbook publicado na conta de automatização configurado na solução de automatização na sua área de trabalho do OMS. |
| WebhookUri |URI do webhook. |
| Expiração |A data de expiração e hora do webhook.  Se o webhook não tiver uma expiração, em seguida, isto pode ser qualquer data futura válida. |

Segue-se uma resposta de amostra de uma ação de remediação com um limiar.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Utilize o método Put com um ID exclusivo de ação para criar uma nova ação de remediação para uma agenda.  O exemplo seguinte cria uma remediação com um limiar para que o runbook é iniciado quando os resultados da pesquisa guardada excedem o limiar.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Utilize o método Put com um ID de ação existente para modificar uma ação de remediação para uma agenda.  O corpo do pedido tem de incluir o etag da ação.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exemplo
Segue-se um exemplo completo para criar um novo alerta de e-mail.  Esta ação cria uma nova agenda juntamente com uma ação que contém um limiar e e-mail.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Ações de Webhook
As ações de Webhook iniciar um processo ao chamar um URL e, opcionalmente, fornecer um payload de envio.  Estes são semelhantes às ações de remediação, exceto se destinam para webhooks que pode invocar processos que não sejam runbooks de automatização do Azure.  Também fornecem a opção adicional de fornecer um payload a entregar o processo remoto.

As ações de Webhook não dispõe de um limiar, mas em vez disso, devem ser adicionadas a uma agenda que tem uma ação com um limiar de alerta.  Pode adicionar várias ações de Webhook todas serem executadas quando é cumprido o limiar.

As ações de Webhook incluem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| WebhookUri |O assunto do correio. |
| CustomPayload |Payload personalizado sejam enviados para o webhook.  O formato dependerá que o webhook está à espera. |

Segue-se uma resposta de exemplo para a ação de webhook e uma ação de alerta associada com um limiar.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Criar ou editar uma ação do webhook
Utilize o método Put com um ID exclusivo de ação para criar uma nova ação de webhook para uma agenda.  O exemplo seguinte cria uma ação de Webhook e uma ação de alerta com um limiar para que o webhook será acionado quando os resultados da pesquisa guardada excedem o limiar.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Utilize o método Put com um ID de ação existente para modificar uma ação do webhook para uma agenda.  O corpo do pedido tem de incluir o etag da ação.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Passos seguintes
* Utilize o [API REST para efetuar pesquisas de registo](log-analytics-log-search-api.md) na análise de registos.

