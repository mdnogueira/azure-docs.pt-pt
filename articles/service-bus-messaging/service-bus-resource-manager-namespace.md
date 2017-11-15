---
title: "Criar espaço de nomes do Service Bus através de um modelo Azure Resource Manager | Microsoft Docs"
description: "Utilize o modelo Azure Resource Manager para criar um espaço de nomes do Service Bus"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/07/2017
ms.author: sethm;shvija
ms.openlocfilehash: c8a42638c79a8a53f80102fc344eccb521e4c1c5
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes do Service Bus através de um modelo Azure Resource Manager

Este artigo descreve como utilizar um modelo Azure Resource Manager que cria um espaço de nomes de barramento de serviço do tipo **mensagens** com um SKU Standard. O artigo também define os parâmetros especificados para a execução da implementação. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Criar modelos do Azure Resource Manager).

Para o modelo completo, consulte o [modelo de espaço de nomes do Service Bus] [ Service Bus namespace template] no GitHub.

> [!NOTE]
> Os modelos Azure Resource Manager seguintes estão disponíveis para transferência e implementação. 
> 
> * [Criar um espaço de nomes de barramento de serviço com a fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
> * [Criar um espaço de nomes de barramento de serviço com a regra de autorização e de fila](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nomes do Service Bus com o tópico, subscrição e a regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite o [modelos de início rápido do Azure] [ Azure Quickstart Templates] Galeria e pesquisa para o Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>O que irá implementar?
Com este modelo, implementa um espaço de nomes de barramento de serviço com um [Standard ou Premium](https://azure.microsoft.com/pricing/details/service-bus/) SKU.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Deverá definir um parâmetro para esses valores que variam com base no projeto ou no ambiente que está a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

Este modelo define os seguintes parâmetros:

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

### <a name="servicebussku"></a>serviceBusSKU
O nome do Service Bus [SKU](https://azure.microsoft.com/pricing/details/service-bus/) criar.

```json
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

O modelo define os valores que são permitidos para este parâmetro (Standard ou Premium). Se for especificado nenhum valor, o Gestor de recursos atribui um valor predefinido (Standard).

Para obter mais informações sobre preços do Service Bus, consulte [Service Bus preços e faturação][Service Bus pricing and billing].

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
### <a name="service-bus-namespace"></a>Espaço de nomes do Service Bus
Cria um espaço de nomes do Service Bus padrão do tipo **mensagens**.

```json
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>CLI do Azure
```azurecli
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Passos seguintes
Agora que já tenha criado e implementado recursos através do Azure Resource Manager, saiba como gerir estes recursos através da leitura nestes artigos:

* [Gerir o barramento de serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir recursos do Service Bus com o Explorador de barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
