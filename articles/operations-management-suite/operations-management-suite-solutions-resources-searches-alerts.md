---
title: "Guardar pesquisas e os alertas em soluções do OMS | Microsoft Docs"
description: "Soluções na OMS incluem, geralmente, as pesquisas guardadas na análise de registos para analisar os dados recolhidos pela solução.  Estes podem também definir alertas para notificar o utilizador ou automaticamente tomar medidas em resposta a um problema crítico.  Este artigo descreve como definir a análise de registos guardar pesquisas e os alertas num modelo do Resource Manager pode ser incluídos em soluções de gestão."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b2388626dd68ea1911cdfb3d6a84e70f6bf3cc6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-oms-management-solution-preview"></a>A adição de análise de registos guardar pesquisas e os alertas à solução de gestão do OMS (pré-visualização)

> [!NOTE]
> Esta documentação é de preliminar para criar soluções de gestão no OMS que estão atualmente em pré-visualização. Nenhum esquema descrita abaixo está sujeita a alterações.   


[As soluções de gestão no OMS](operations-management-suite-solutions.md) normalmente incluirá a [pesquisas guardadas](../log-analytics/log-analytics-log-searches.md) na análise de registos para analisar os dados recolhidos pela solução.  Pode também definirem [alertas](../log-analytics/log-analytics-alerts.md) para notificar o utilizador ou automaticamente tomar medidas em resposta a um problema crítico.  Este artigo descreve como definir a análise de registos pesquisas guardadas e alertas num [modelo de gestão de recursos](../resource-manager-template-walkthrough.md) pelo que pode ser incluídos numa [soluções de gestão](operations-management-suite-solutions-creating.md).

> [!NOTE]
> Os exemplos neste artigo utilizam parâmetros e variáveis que são necessários ou comuns para soluções de gestão e descrito em [criar soluções de gestão no Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)  

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já está familiarizado com a [criar uma solução de gestão](operations-management-suite-solutions-creating.md) e a estrutura de um [modelo do Resource Manager](../resource-group-authoring-templates.md) e ficheiro de solução.


## <a name="log-analytics-workspace"></a>Área de trabalho de análise de registo
Todos os recursos na análise de registos estão contidos num [área de trabalho](../log-analytics/log-analytics-manage-access.md).  Conforme descrito em [OMS área de trabalho e a conta de automatização](operations-management-suite-solutions.md#oms-workspace-and-automation-account), a área de trabalho não está incluída na solução de gestão, mas tem de existir antes da solução está instalada.  Se não estiver disponível, em seguida, a solução de instalação irá falhar.

O nome da área de trabalho é nome cada recurso de análise de registos.  Isto é feito na solução com o **área de trabalho** parâmetro como no seguinte exemplo de um recurso de savedsearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versão de API de análise do registo
Todos os recursos de análise de registos definidos num modelo do Resource Manager tem uma propriedade **apiVersion** que define a versão da API, deve utilizar o recurso.  Esta versão é diferente para recursos que utilizam o [legado e o idioma de consulta atualizado](../log-analytics/log-analytics-log-search-upgrade.md).  

 A seguinte tabela especifica as versões de API de análise do registo para áreas de trabalho de legado e atualizadas e uma consulta de exemplo para especificar a sintaxe diferente para cada. 

| Versão da área de trabalho | Versão de API | Consulta de exemplo |
|:---|:---|:---|
| V1 (Legado)   | 2015-11-01-preview | Tipo = EventLevelName de evento = erro             |
| v2 (atualizado) | 2017-03-15-preview | Evento &#124; onde EventLevelName = = "Erro"  |

Tenha em atenção o seguinte para o qual as áreas de trabalho são suportadas por versões diferentes.

- Os modelos que utilizam o idioma de consulta legado podem ser instalados numa área de trabalho atualizada ou legada.  Se instalou numa área de trabalho atualizada, as consultas são convertidas no momento para o novo idioma quando são executados pelo utilizador.
- Os modelos que utilizam o idioma de consulta atualizado só podem ser instalados numa área de trabalho atualizada.


## <a name="saved-searches"></a>Pesquisas guardadas
Incluir [pesquisas guardadas](../log-analytics/log-analytics-log-searches.md) numa solução para permitir aos utilizadores consultar os dados recolhidos pela sua solução.  Guardar pesquisas são apresentados em **Favoritos** no portal do OMS e **pesquisas guardadas** no portal do Azure.  Uma pesquisa guardada também é necessária para cada alerta.   

[Análise de registos guardar pesquisa](../log-analytics/log-analytics-log-searches.md) recursos tem um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches` e ter a seguinte estrutura.  Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }



Cada propriedade de uma procura guardada são descritas na seguinte tabela. 

| Propriedade | Descrição |
|:--- |:--- |
| categoria | A categoria para a pesquisa guardada.  As pesquisas guardadas na mesma solução frequentemente irão partilhar uma única categoria para são agrupados em conjunto na consola do. |
| DisplayName | Nome a apresentar para a pesquisa guardada no portal. |
| consulta | Consulta seja executada. |

> [!NOTE]
> Terá de utilizar os carateres de escape da consulta se este incluir carateres que poderão ser interpretados como JSON.  Por exemplo, se a consulta foi **tipo: AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, devem ser escrita no ficheiro de solução como **OperationName de tipo: AzureActivity:\" Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alertas
[Alertas de análise de registo](../log-analytics/log-analytics-alerts.md) são criados pelas regras de alertas que executam uma procura guardada num intervalo regular.  Se os resultados de correspondência de consulta aos critérios especificados, é criado um registo de alerta e uma ou mais ações são executadas.  

Regras de alerta numa solução de gestão são efetuadas cópias de segurança dos seguintes três recursos diferentes.

- **Pesquisa guardada.**  Define a pesquisa de registo que é executada.  Várias regras de alerta podem partilhar uma procura guardada único.
- **Agenda.**  Define a frequência a pesquisa de registo é executada.  Cada regra de alerta tem um e apenas um agendamento.
- **Ação de alerta.**  Cada regra de alerta é um recurso de ação com um tipo de **alerta** que define os detalhes do alerta, tais como os critérios quando é criado um registo de alerta e a gravidade do alerta.  O recurso de ação, opcionalmente, vai definir uma resposta de correio e o runbook.
- **Ação do Webhook (opcional).**  Se a regra de alerta chama um webhook, em seguida, necessita de um recurso de ação adicional com um tipo de **Webhook**.    

Guardar pesquisa recursos são descritos acima.  Os outros recursos são descritos abaixo.


### <a name="schedule-resource"></a>Recurso de agenda

Uma pesquisa guardada pode ter uma ou mais agendas com cada agenda que representa uma regra de alerta separada. A agenda define a frequência a pesquisa está a ser executada e o intervalo de tempo durante o qual os dados são obtidos.  Recursos de agendamento tem um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` e ter a seguinte estrutura. Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 


    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }



As propriedades de recursos de agenda são descritas na seguinte tabela.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| ativado       | Sim | Especifica se o alerta é ativado quando é criado. |
| intervalo      | Sim | Frequência de consulta é executada em minutos. |
| QueryTimeSpan | Sim | Período de tempo em minutos durante o qual a avaliar os resultados. |

O recurso de agenda deve dependem a pesquisa guardada para que é criado antes da agenda.


### <a name="actions"></a>Ações
Existem dois tipos de recursos da ação especificado pelo **tipo** propriedade.  Uma agenda requer um **alerta** ação que define os detalhes da regra de alerta que ações são efetuadas quando é criado um alerta.  Podem também incluir um **Webhook** ação se deve ser chamado um webhook do alerta.  

Recursos da ação tem um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

#### <a name="alert-actions"></a>Ações de alerta

Cada agenda tem um **alerta** ação.  Isto define os detalhes do alerta e, opcionalmente, as ações de notificação e remediação.  Uma notificação envia um e-mail para um ou vários endereços.  Uma remediação inicia um runbook na automatização do Azure para tentar corrigir o problema detetado.

Ações de alerta tem a seguinte estrutura.  Isto inclui os parâmetros e variáveis comuns para que possam copie e cole o fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro. 



    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
        ],
        "properties": {
            "etag": "*",
            "type": "Alert",
            "name": "[variables('Alert').Name]",
            "description": "[variables('Alert').Description]",
            "severity": "[variables('Alert').Severity]",
            "threshold": {
                "operator": "[variables('Alert').Threshold.Operator]",
                "value": "[variables('Alert').Threshold.Value]",
                "metricsTrigger": {
                    "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                    "operator": "[variables('Alert').Trigger.Operator]",
                    "value": "[variables('Alert').Trigger.Value]"
                },
            },
            "emailNotification": {
                "recipients": [
                    "[variables('Alert').Recipients]"
                ],
                "subject": "[variables('Alert').Subject]"
            },
            "remediation": {
                "runbookName": "[variables('Alert').Remedition.RunbookName]",
                "webhookUri": "[variables('Alert').Remedition.WebhookUri]"
            }
        }
    }

As propriedades de recursos da ação de alerta são descritas nas tabelas seguintes.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| Tipo | Sim | Tipo de ação.  Este é **alerta** para ações de alerta. |
| Nome | Sim | Nome a apresentar para o alerta.  Este é o nome que é apresentado na consola para a regra de alerta. |
| Descrição | Não | Descrição opcional do alerta. |
| Gravidade | Sim | Gravidade do alerta registo entre os valores seguintes:<br><br> **Crítico**<br>**Aviso**<br>**Informativo** |


##### <a name="threshold"></a>Limiar
Esta secção é necessária.  Define as propriedades para o limiar de alerta.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| operador | Sim | Operador de comparação entre os valores seguintes:<br><br>**gt = maior<br>lt = inferior a** |
| Valor | Sim | O valor a comparar os resultados. |


##### <a name="metricstrigger"></a>MetricsTrigger
Esta secção é opcional.  Incluí-la para um alerta de métrica de medida.

> [!NOTE]
> Alertas de medida métrica estão atualmente em pré-visualização pública. 

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| TriggerCondition | Sim | Especifica se o limiar de número total de falhas ou falhas consecutivas entre os valores seguintes:<br><br>**Total<br>consecutivas** |
| operador | Sim | Operador de comparação entre os valores seguintes:<br><br>**gt = maior<br>lt = inferior a** |
| Valor | Sim | Número de vezes que os critérios têm de ser cumpridos para acionar o alerta. |

##### <a name="throttling"></a>Limitação
Esta secção é opcional.  Inclua esta secção se pretende suprimir os alertas da mesma regra algum período de tempo depois de criar um alerta.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| DurationInMinutes | Sim, se a limitação de elemento incluído | Número de minutos para suprimir alertas depois da mesma regra de alerta ser criado. |

##### <a name="emailnotification"></a>EmailNotification
 Esta secção é opcional incluí-la se pretender que o alerta para enviar correio para um ou mais destinatários.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| Destinatários | Sim | Lista delimitada por vírgulas de endereços de e-mail para enviar notificações quando é criado um alerta, tal como no exemplo seguinte.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Assunto | Sim | Linha de assunto de correio. |
| Anexo | Não | Anexos não são atualmente suportados.  Se este elemento está incluído, deve ser **nenhum**. |


##### <a name="remediation"></a>Remediação
Esta secção é opcional incluí-la se pretender que um runbook para começar em resposta ao alerta. |

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| RunbookName | Sim | Nome do runbook para iniciar. |
| WebhookUri | Sim | URI de webhook para o runbook. |
| Expiração | Não | Data e hora de expiração a remediação. |

#### <a name="webhook-actions"></a>Ações de Webhook

As ações de Webhook iniciar um processo ao chamar um URL e, opcionalmente, fornecer um payload de envio. Estes são semelhantes às ações de remediação, exceto se destinam para webhooks que pode invocar processos que não sejam runbooks de automatização do Azure. Também fornecem a opção adicional de fornecer um payload a entregar o processo remoto.

Se o alerta irá chamar um webhook, em seguida, será necessário um recurso de ação com um tipo de **Webhook** para além de **alerta** recursos da ação.  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

As propriedades de recursos de ação do Webhook são descritas nas tabelas seguintes.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| tipo | Sim | Tipo de ação.  Este é **Webhook** para ações de webhook. |
| nome | Sim | Nome a apresentar para a ação.  Não é apresentado na consola do. |
| wehookUri | Sim | URI para o webhook. |
| CustomPayload | Não | Payload personalizado sejam enviados para o webhook. O formato depende do que o webhook está à espera. |




## <a name="sample"></a>Exemplo

Segue-se um exemplo de uma solução que inclui o que inclui os seguintes recursos:

- Pesquisa guardada
- Agenda
- Ação de alerta
- Ação do Webhook

Este exemplo utiliza [parâmetros de solução padrão](operations-management-suite-solutions-solution-file.md#parameters) variáveis que frequentemente deverá ser utilizadas numa solução, por oposição a valores de codificar nas definições de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0",
        "parameters": {
          "workspaceName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Log Analytics workspace"
            }
          },
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
              "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
              "Interval": 15,
              "TimeSpan": 60
            },
            "MetricsTrigger": {
              "TriggerCondition": "Consecutive",
              "Operator": "gt",
              "Value": 3
            },
            "ThrottleMinutes": 60,
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
              ]
            },
            "plan": {
              "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
              "Version": "[variables('SolutionVersion')]",
              "product": "[variables('ProductName')]",
              "publisher": "[variables('SolutionPublisher')]",
              "promotionCode": ""
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
              "etag": "*",
              "query": "[variables('MySearch').query]",
              "displayName": "[variables('MySearch').displayName]",
              "category": "[variables('MySearch').category]"
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
              "etag": "*",
              "interval": "[variables('MyAlert').Schedule.Interval]",
              "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
              "enabled": true
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Alert",
              "Name": "[variables('MyAlert').DisplayName]",
              "Description": "[variables('MyAlert').Description]",
              "Severity": "[variables('MyAlert').Severity]",
              "Threshold": {
                "Operator": "[variables('MyAlert').ThresholdOperator]",
                "Value": "[variables('MyAlert').ThresholdValue]",
                "MetricsTrigger": {
                  "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                  "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                  "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                }
              },
              "Throttling": {
                "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
              },
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


O ficheiro de parâmetros seguinte fornece valores de exemplos para esta solução.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspacename": {
                "value": "myWorkspace"
            },
            "accountName": {
                "value": "myAccount"
            },
            "workspaceregionId": {
                "value": "East US"
            },
            "regionId": {
                "value": "East US 2"
            },
            "pricingTier": {
                "value": "Free"
            },
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>Passos seguintes
* [Adicionar vistas](operations-management-suite-solutions-resources-views.md) à sua solução de gestão.
* [Adicionar runbooks de automatização e outros recursos](operations-management-suite-solutions-resources-automation.md) à sua solução de gestão.

