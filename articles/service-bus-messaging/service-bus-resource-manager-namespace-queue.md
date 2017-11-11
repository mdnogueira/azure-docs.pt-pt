---
title: "Criar espaço de nomes do Service Bus do Azure e fila de espera utilizando o modelo Azure Resource Manager | Microsoft Docs"
description: "Criar um espaço de nomes de barramento de serviço e uma fila com o modelo Azure Resource Manager"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/10/2017
ms.author: sethm;shvija
ms.openlocfilehash: 2ac9bb07a1b690681e0b263eea233cd5373d990b
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes de barramento de serviço e uma fila com um modelo Azure Resource Manager

Este artigo mostra como utilizar um modelo Azure Resource Manager que cria um espaço de nomes de barramento de serviço e uma fila dentro desse espaço de nomes. O artigo explica como especificar quais os recursos são implementados e como definir os parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [modelos Authoring Azure Resource Manager][Authoring Azure Resource Manager templates].

Para o modelo completo, consulte o [modelo de espaço de nomes e a fila do Service Bus] [ Service Bus namespace and queue template] no GitHub.

> [!NOTE]
> Os modelos Azure Resource Manager seguintes estão disponíveis para transferência e implementação.
> 
> * [Criar um espaço de nomes de barramento de serviço com a regra de autorização e de fila](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Criar um espaço de nomes do Service Bus com o tópico, subscrição e a regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite o [modelos de início rápido do Azure] [ Azure Quickstart Templates] Galeria e procure **Service Bus**.
> 
> 

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com este modelo, implemente um espaço de nomes do Service Bus através de uma fila.

[Filas do Service Bus](service-bus-queues-topics-subscriptions.md#queues) oferecem primeiro no, entrega de mensagens First Out (FIFO) para um ou mais consumidores concorrentes.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Deve definir um parâmetro para esses valores que irão variar com base no projeto que está a implementar ou com base no ambiente que esteja a implementar. Não defina parâmetros para valores que irão sempre permaneça igual. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do espaço de nomes de barramento de serviço para criar.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
O nome da fila criada no espaço de nomes de barramento de serviço.

```json
"serviceBusQueueName": {
"type": "string"
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
Cria um espaço de nomes do Service Bus padrão do tipo **mensagens**, através de uma fila.

```json
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Passos seguintes
Agora que já tenha criado e implementado recursos através do Azure Resource Manager, saiba como gerir estes recursos visualizando nestes artigos:

* [Gerir o barramento de serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir recursos do Service Bus com o Explorador de barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
