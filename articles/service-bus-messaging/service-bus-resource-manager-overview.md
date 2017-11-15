---
title: Criar recursos do Service Bus do Azure utilizando modelos do Resource Manager | Microsoft Docs
description: "Utilizar modelos Azure Resource Manager para automatizar a criação de recursos do Service Bus"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/10/2017
ms.author: sethm
ms.openlocfilehash: 0ceeb138a7432e51cabe2597c680cb01ea9eac4a
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Criar recursos de Service Bus através de modelos Azure Resource Manager

Este artigo descreve como criar e implementar os recursos de Service Bus através de modelos Azure Resource Manager, PowerShell e o fornecedor de recursos do Service Bus.

Modelos Azure Resource Manager ajudam-na definir os recursos para implementar uma solução de e para especificar os parâmetros e variáveis que permitem introduzir os valores para os diferentes ambientes. O modelo é escrito no JSON e é composto expressões que pode utilizar para construir valores para a sua implementação. Para obter informações detalhadas sobre como escrever modelos Azure Resource Manager e ver um debate do formato modelo, consulte [estrutura e a sintaxe do modelos Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Os exemplos neste artigo mostram como utilizar o Azure Resource Manager para criar um espaço de nomes do Service Bus e a entidade de mensagens (fila). Para outros exemplos de modelo, visite o [Galeria de modelos de início rápido do Azure] [ Azure Quickstart Templates gallery] e procure **Service Bus**.
>
>

## <a name="service-bus-resource-manager-templates"></a>Modelos do Resource Manager do Service Bus

Estes modelos de Gestor de recursos do Service Bus do Azure estão disponíveis para transferência e implementação. Clique nas hiperligações seguintes para obter detalhes sobre cada um deles, com hiperligações para os modelos no GitHub:

* [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
* [Criar um espaço de nomes de barramento de serviço com a fila](service-bus-resource-manager-namespace-queue.md)
* [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
* [Criar um espaço de nomes de barramento de serviço com a regra de autorização e de fila](service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um espaço de nomes do Service Bus com o tópico, subscrição e a regra](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Implementar com o PowerShell

O procedimento seguinte descreve como utilizar o PowerShell para implementar um modelo Azure Resource Manager que cria um espaço de nomes do Service Bus do escalão Standard e uma fila dentro desse espaço de nomes. Neste exemplo é baseado no [criar um espaço de nomes do Service Bus com fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) modelo. O fluxo de trabalho aproximado é o seguinte:

1. Instale o PowerShell.
2. Crie o modelo e (opcionalmente) um ficheiro de parâmetros.
3. No PowerShell, inicie sessão sua conta do Azure.
4. Se não existir, crie um novo grupo de recursos.
5. Teste a implementação.
6. Se assim o desejar, defina o modo de implementação.
7. Implemente o modelo.

Para obter informações completas sobre a implementação de modelos Azure Resource Manager, consulte [implementar recursos com modelos Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalar o PowerShell

Instalar o Azure PowerShell, seguindo as instruções em [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Criar um modelo

Clone ou copiar o [201-servicebus-criar-fila](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) modelo a partir do GitHub:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Criar um ficheiro de parâmetros (opcional)

Para utilizar um ficheiro de parâmetros opcionais, copie o [201-servicebus-criar-fila](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) ficheiro. Substitua o valor de `serviceBusNamespaceName` com o nome do espaço de nomes de barramento de serviço que pretende criar esta implementação e substitua o valor de `serviceBusQueueName` com o nome da fila que pretende criar.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Para obter mais informações, consulte o [parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) artigo.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Inicie sessão no Azure e definir a subscrição do Azure

A partir da linha de comandos do PowerShell, execute o seguinte comando:

```powershell
Login-AzureRmAccount
```

Lhe for pedido que inicie sessão na sua conta do Azure. Depois de iniciar sessão, execute o seguinte comando para ver as subscrições disponíveis:

```powershell
Get-AzureRMSubscription
```

Este comando devolve uma lista de subscrições do Azure disponíveis. Escolha uma subscrição para a sessão atual, executando o seguinte comando. Substitua `<YourSubscriptionId>` com o GUID da subscrição do Azure que pretende utilizar:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Se não tiver um recurso existente de grupo, crie um novo grupo de recursos com o * * New-AzureRmResourceGroup * * comando. Forneça o nome do grupo de recursos e localização que pretende utilizar. Por exemplo:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se tiver êxito, é apresentado um resumo do novo grupo de recursos.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testar a implementação

Validar a sua implementação, executando o `Test-AzureRmResourceGroupDeployment` cmdlet. Quando a implementação de teste, fornece os parâmetros exatamente tal como faria ao executar a implementação.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Criar a implementação

Para criar a nova implementação, execute o `New-AzureRmResourceGroupDeployment` cmdlet e forneça os parâmetros necessários quando lhe for pedido. Os parâmetros incluem um nome para a sua implementação, o nome do seu grupo de recursos e o caminho ou URL para o ficheiro de modelo. Se o **modo** parâmetro não for especificado, o valor predefinido de **Incremental** é utilizado. Para obter mais informações, consulte [Incremental e completas implementações](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

O seguinte comando pede-lhe três parâmetros na janela do PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar um ficheiro de parâmetros em vez disso, utilize o seguinte comando:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Também pode utilizar parâmetros inline ao executar o cmdlet de implementação. O comando é o seguinte:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar um [concluída](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) implementação, defina o **modo** parâmetro para **concluída**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Verificar a implementação
Se os recursos são implementados com êxito, é apresentado um resumo da implementação na janela do PowerShell:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Passos seguintes
Agora viu o fluxo de trabalho básico e comandos para implementar um modelo Azure Resource Manager. Para obter informações mais detalhadas, visite as seguintes ligações:

* [Descrição geral do Azure Resource Manager][Azure Resource Manager overview]
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Criar modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
