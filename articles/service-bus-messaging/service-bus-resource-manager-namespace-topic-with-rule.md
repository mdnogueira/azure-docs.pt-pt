---
title: "Criar subscrição de tópico do Service Bus do Azure e regra utilizando o modelo Azure Resource Manager | Microsoft Docs"
description: "Criar um espaço de nomes do Service Bus com o tópico, subscrição e regra utilizando o modelo Azure Resource Manager"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/10/2017
ms.author: sethm;shvija
ms.openlocfilehash: 976c7b425dd17f8ed38f18b6ffa50b4368ab44b3
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes do Service Bus com o tópico, subscrição e a regra através de um modelo Azure Resource Manager

Este artigo mostra como utilizar um modelo Azure Resource Manager que cria um espaço de nomes do Service Bus com um tópico, subscrição e regra (filtro). O artigo explica como especificar quais os recursos são implementados e como definir os parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Criar modelos do Azure Resource Manager).

Para obter mais informações sobre práticas e padrões de convenções de nomenclatura de recursos do Azure, consulte [recomendado convenções de nomenclatura para recursos do Azure][Recommended naming conventions for Azure resources].

Para o modelo completo, consulte o [espaço de nomes do Service Bus com o tópico, subscrição e regra] [ Service Bus namespace with topic, subscription, and rule] modelo.

> [!NOTE]
> Os modelos Azure Resource Manager seguintes estão disponíveis para transferência e implementação.
> 
> * [Criar um espaço de nomes de barramento de serviço com a regra de autorização e de fila](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nomes de barramento de serviço com a fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
> 
> Para verificar os modelos mais recentes, visite o [modelos de início rápido do Azure] [ Azure Quickstart Templates] Galeria e pesquisa para o Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com este modelo, implemente um espaço de nomes do Service Bus com o tópico, subscrição e a regra (filtro).

[Tópicos do Service Bus e subscrições](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) proporcionar uma forma de um-para-muitos de comunicação, um *publicar/subscrever* padrão. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si, trocam antes mensagens através de tópico que funciona como um intermediário. Uma subscrição para um tópico é semelhante uma fila virtual que recebe cópias de mensagens em fila que foram enviadas para o tópico. Um filtro de subscrição permite-lhe especificar quais as mensagens enviadas para um tópico deve aparecer dentro de uma subscrição de tópico específica.

## <a name="what-are-rules-filters"></a>O que são regras (filtros)?

Em vários cenários, as mensagens com características específicas tem de ser processadas de formas diferentes. Para ativar este processamento personalizado, pode configurar subscrições para localizar mensagens de que tem as propriedades específicas e, em seguida, efetuar as modificações para essas propriedades. Embora as subscrições do Service Bus ver todas as mensagens enviadas para o tópico, só poderá copiar um subconjunto dessas mensagens para a fila virtual da subscrição. Isto é conseguido utilizando filtros de subscrição. Para obter mais informações sobre regras (filtros), consulte [regras e ações](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, deve definir os parâmetros para os valores que pretenda especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Deverá definir um parâmetro para esses valores que variam com base no projeto ou no ambiente que está a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do espaço de nomes de barramento de serviço para criar.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
O nome do tópico criado no espaço de nomes de barramento de serviço.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
O nome da subscrição criado no espaço de nomes de barramento de serviço.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
O nome do rule(filter) criado no espaço de nomes de barramento de serviço.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
A versão de API do Service Bus do modelo.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Recursos a implementar
Cria um espaço de nomes do Service Bus padrão do tipo **mensagens**, tópico e subscrição e as regras.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Passos seguintes
Agora que já tenha criado e implementado recursos através do Azure Resource Manager, saiba como gerir estes recursos visualizando nestes artigos:

* [Gerir o Service Bus do Azure](service-bus-management-libraries.md)
* [Gerir o barramento de serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir recursos do Service Bus com o Explorador de barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

