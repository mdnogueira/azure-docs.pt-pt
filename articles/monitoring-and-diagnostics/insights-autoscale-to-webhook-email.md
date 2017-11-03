---
title: "Utilize ações de dimensionamento automático para enviar correio eletrónico e webhook notificações de alerta. | Microsoft Docs"
description: "Ver como utilizar as ações de dimensionamento automático URLs da web ou enviar notificações por correio eletrónico no Monitor do Azure. "
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: eb9a4c98-0894-488c-8ee8-5df0065d094f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: ancav
ms.openlocfilehash: 16caf14028494800e9259f0296c292b606d0210a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Utilize ações de dimensionamento automático para enviar correio eletrónico e webhook notificações de alertas no Monitor do Azure
Este artigo mostra como configurar acionadores para que possa chamar URLs específicos na web ou enviar mensagens de correio eletrónico com base nas ações de dimensionamento automático no Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks permitem-lhe encaminhar as notificações de alerta do Azure para outros sistemas de notificações de pós-processamento ou personalizados. Por exemplo, de encaminhamento de alerta para serviços que podem processar um pedido web recebido a enviar do que SMS, o registo de erros, notificar a equipa utilizando chat ou mensagens de serviços, etc. O URI de webhook tem de ser um ponto final HTTP ou HTTPS válido.

## <a name="email"></a>E-mail
E-mail pode ser enviado para qualquer endereço de correio eletrónico válido. Os administradores e coadministradores da subscrição que está a executar a regra também serão notificados.

## <a name="cloud-services-and-web-apps"></a>Serviços em nuvem e de aplicações Web
Que pode optar ativamente por participar sessão do portal do Azure para serviços em nuvem e de Farms de servidores (aplicações Web).

* Escolha o **Dimensionar por** métrica.

![Dimensionar por](./media/insights-autoscale-to-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquina virtual
Para mais recente máquinas virtuais criadas com o Resource Manager (conjuntos de dimensionamento de Máquina Virtual), pode configurar esta opção utilizando a REST API, modelos do Resource Manager, PowerShell e CLI. Uma interface de portal ainda não está disponível.
Quando utilizar o modelo de REST API ou do Resource Manager, inclua o elemento de notificações com as seguintes opções.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
| Campo | Obrigatório? | Descrição |
| --- | --- | --- |
| operação |sim |o valor tem de ser "Horizontal" |
| sendToSubscriptionAdministrator |sim |valor deve ser "verdadeiro" ou "false" |
| sendToSubscriptionCoAdministrators |sim |valor deve ser "verdadeiro" ou "false" |
| customEmails |sim |o valor pode ser [] nulo ou uma matriz de cadeia de mensagens de correio eletrónico |
| Webhooks |sim |valor pode ser nula ou válida Uri |
| serviceUri |sim |um Uri de https válido |
| propriedades |sim |valor tem de ser vazio {} ou pode contêm pares chave-valor |

## <a name="authentication-in-webhooks"></a>Autenticação no webhooks
O webhook pode autenticar através da autenticação baseada em tokens, onde guardar o webhook URI com um ID de token como um parâmetro de consulta. Por exemplo, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Esquema de payload de webhook de notificação de dimensionamento automático
Quando a notificação de dimensionamento automático é gerada, os metadados seguintes está incluído no payload do webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Campo | Obrigatório? | Descrição |
| --- | --- | --- |
| status |sim |O estado que indica que foi gerada uma ação de dimensionamento automático |
| operação |sim |Para um aumento de instâncias, será "Aumentar horizontalmente" e para um decréscimo no instâncias, será "Escala em" |
| Contexto |sim |O contexto de ação de dimensionamento automático |
| carimbo de data/hora |sim |Carimbo de hora quando a ação de dimensionamento automático foi acionada |
| ID |Sim |Gestor de recursos do ID de definição de dimensionamento automático |
| nome |Sim |O nome da definição de dimensionamento automático |
| Detalhes |Sim |Explicação da ação que o serviço de dimensionamento automático demorou e a alteração na contagem de instância |
| subscriptionId |Sim |ID de subscrição do recurso de destino que está a ser escalado |
| resourceGroupName |Sim |Nome do grupo de recursos do recurso de destino que está a ser escalado |
| resourceName |Sim |Nome do recurso de destino que está a ser escalado |
| resourceType |Sim |Os três valores suportados: "microsoft.classiccompute/domainnames/slots/roles" - funções do serviço em nuvem, "microsoft.compute/virtualmachinescalesets" - conjuntos de dimensionamento de Máquina Virtual e "Microsoft.Web/serverfarms -" aplicação Web |
| resourceId |Sim |Gestor de recursos do ID do recurso de destino que está a ser escalado |
| portalLink |Sim |Ligação de portal do Azure para a página de resumo do recurso de destino |
| oldCapacity |Sim |A atual contagem de instâncias (antiga) quando o dimensionamento automático demorou uma ação de dimensionamento |
| newCapacity |Sim |A nova contagem de instâncias que o dimensionamento automático ampliada o recurso a |
| Propriedades |Não |Opcional. Conjunto de < chave, valor > pares (por exemplo, dicionário < String, String >). O campo de propriedades é opcional. Numa interface de utilizador personalizada ou fluxo de trabalho de aplicação com base de lógica, pode introduzir as chaves e valores que podem ser transmitidos com o payload. Uma maneira alternativa para passar as propriedades personalizadas para a chamada de webhook saída consiste em utilizar o webhook URI propriamente dito (como parâmetros de consulta) |
