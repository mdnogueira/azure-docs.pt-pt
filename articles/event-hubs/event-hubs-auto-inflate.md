---
title: "Dimensionar automaticamente unidades de débito de Event Hubs do Azure | Microsoft Docs"
description: "Ativar o inflate de automaticamente um espaço de nomes para dimensionar automaticamente unidades de débito"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 1cd31e0866ee6088483f88e8f80d01f75764c771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Dimensionar automaticamente unidades de débito de Event Hubs do Azure

Os Hubs de eventos do Azure é uma plataforma de transmissão em fluxo de dados altamente dimensionáveis. Como tal, a clientes de Event Hubs, muitas vezes, aumentam a respetiva utilização depois de integração para o serviço. Essa aumenta requerem aumentar as unidades de débito predeterminado dimensionar os Event Hubs e processar taxas de transferência maior. O *Auto-inflate* funcionalidade dos Event Hubs automaticamente ajusta-se o número de unidades de débito para satisfazer as necessidades de utilização. Aumentar as unidades de débito impede cenários, na qual a limitação:

* Taxas de entrada de dados excedem as unidades de débito do conjunto.
* Taxas de pedidos de saída de dados excedem as unidades de débito do conjunto.

## <a name="how-auto-inflate-works"></a>Como funciona o inflate de automática

Tráfego de Hubs de eventos é controlado pelas unidades de débito. Uma única unidade de débito permite 1 MB por segundo de entrada e de duas vezes que quantidade de saída. Hubs de eventos Standard pode ser configurados com 1-20 unidades de débito. Auto-inflate permite-lhe começar por algo pequeno com as unidades de débito necessário mínimo. Em seguida, a funcionalidade ajusta automaticamente para o limite máximo de unidades de débito que precisa, consoante o aumento no tráfego. Auto-inflate fornece as seguintes vantagens:

- Um mecanismo de dimensionamento eficiente para começar por algo pequeno e dimensione à medida que aumentam a.
- Dimensione automaticamente para o limite superior especificado sem problemas de limitação.
- Mais controlam sobre o dimensionamento, como pode controlar quando e quanto à escala.

## <a name="enable-auto-inflate-on-a-namespace"></a>Ativar o inflate de automaticamente um espaço de nomes

Pode ativar ou desativar a inflate de automática de mensagens em fila um espaço de nomes utilizando um dos seguintes métodos:

1. O [portal do Azure](https://portal.azure.com).
2. Um modelo Azure Resource Manager.

### <a name="enable-auto-inflate-through-the-portal"></a>Ativar automaticamente-inflate através do portal

Pode ativar a funcionalidade de Auto-inflate um espaço de nomes ao criar um espaço de nomes de Event Hubs:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Com esta opção ativada, pode começar por algo pequeno nas suas unidades de débito e dimensione à medida que a utilização da necessidades aumento. O limite superior inflation não afeta os preços, que depende do número de unidades de débito utilizada por hora.

Também pode ativar inflate automática utilizando o **escala** opção no painel de definições no portal:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Ativar Inflate automático utilizando um modelo Azure Resource Manager

Pode ativar inflate de automática durante uma implementação de modelo Azure Resource Manager. Por exemplo, definir o `isAutoInflateEnabled` propriedade **verdadeiro** e defina `maximumThroughputUnits` para 10.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Para o modelo completo, consulte o [criar Hubs de eventos espaço de nomes e ativar inflate](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) modelo no GitHub.

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
