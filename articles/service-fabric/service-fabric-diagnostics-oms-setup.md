---
title: "Azure Service Fabric - configurar a monitorização com o OMS Log Analytics | Microsoft Docs"
description: Saiba como configurar o OMS para visualizar e analisar eventos para monitorizar os clusters de Service Fabric do Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 32c09f06cea97024437e7e339407d344194a14ae
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>Configurar a análise de registos do OMS para um cluster

Pode configurar uma área de trabalho do OMS através do Azure Resource Manager ou do Azure Marketplace. Utilize o anterior quando pretender manter um modelo de implementação para utilização futura. Implementar através do Marketplace é mais fácil se já tiver um cluster implementado com diagnóstico ativado.

> [!NOTE]
> Tem de ter o diagnóstico ativado para o cluster para ver o cluster / eventos de nível de plataforma para poder configurar com êxito o OMS para monitorizar o seu cluster.

## <a name="deploying-oms-using-a-resource-manager-template"></a>Implementar OMS através de um modelo do Resource Manager

Quando implementar um cluster com um modelo do Resource Manager, o modelo deve criar uma nova área de trabalho OMS, adicione a solução de recursos de infraestrutura de serviço e configurá-lo para ler os dados das tabelas de armazenamento adequado.

[Aqui](https://azure.microsoft.com/resources/templates/service-fabric-oms/) é um modelo de exemplo que pode utilizar e modificar de acordo com os requisitos. Modelos mais que dão-lhe diferentes opções para configurar uma área de trabalho do OMS pode ser encontrado em [modelos de Service Fabric e OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

As alterações principais são os seguintes:

1. Adicionar `omsWorkspaceName` e `omsRegion` para os parâmetros. Isto significa que a adicionar o fragmento seguinte para parâmetros definidos na sua *Template* ficheiro. Pode modificar os valores predefinidos, como julgar. Também deve adicionar os dois parâmetros novos na sua *Parameters. JSON* para definir os respetivos valores para a implementação de recursos:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    O `omsRegion` valores têm de estar em conformidade com um conjunto específico de valores. Deverá escolher aquele que está mais próximo para a implementação do cluster.

2. Se vai enviar os registos da aplicação à OMS, confirme que o `applicationDiagnosticsStorageAccountType` e `applicationDiagnosticsStorageAccountName` são incluídos como parâmetros no modelo. Se não estiverem, adicione-o para a secção de variáveis desta forma e editar os respetivos valores conforme necessário. Também pode inclui-los como parâmetros, se desejar, seguindo o formato utilizado acima.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]",
    ```

3. Adicione a solução de Service Fabric OMS variáveis do modelo:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric",
    ```

4. Adicionar o seguinte ao final da secção de recursos, após o qual o recurso de cluster do Service Fabric está declarado.

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```

5. Implemente o modelo como uma atualização do Gestor de recursos para o cluster. Isto é feito utilizando o `New-AzureRmResourceGroupDeployment` API no módulo AzureRM PowerShell. Um comando de exemplo seria:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    O Azure Resource Manager será capaz de detetar que se trata de uma atualização para um recurso existente. Processar apenas as alterações entre o modelo de implementação existente a ocasionar e o novo modelo fornecido.

## <a name="deploying-oms-using-azure-marketplace"></a>Implementar OMS através do Azure Marketplace

Se preferir adicionar uma área de trabalho do OMS depois de implementar um cluster, aceda a Azure Marketplace (no Portal) e procure *"Serviço de análise de recursos de infraestrutura"*.

1. Clique em **novo** no menu de navegação esquerdo. 

2. Procurar *serviço análise de recursos de infraestrutura*. Clique no recurso que aparece.

3. Clique em **criar**

    ![Análise de SF OMS no Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Na janela de criação de análise de recursos de infraestrutura de serviço, clique em **Selecione uma área de trabalho** para o *área de trabalho OMS* campo e, em seguida, **criar uma nova área de trabalho**. Preencha as entradas necessárias - o único requisito aqui é que a subscrição para o cluster do Service Fabric e a área de trabalho do OMS deve ser o mesmo. Depois das suas entradas tem sido validadas, a sua área de trabalho do OMS começarão a implementar. Isto deverá demorar apenas alguns minutos.

5. Quando terminar, clique em **criar** novamente na parte inferior da janela de criação de análise de recursos de infraestrutura de serviço. Certifique-se de que a nova área de trabalho aparece em *área de trabalho OMS*. Isto irá adicionar a solução para a área de trabalho que acabou de criar.

6. A área de trabalho ainda tem de estar ligado dos dados de diagnóstico provenientes do seu cluster. Navegue para o grupo de recursos que criou da solução de análise de recursos de infraestrutura de serviço. Deverá ver uma *ServiceFabric (\<nameOfOMSWorkspace\>)*. Clique na solução de para navegar para a página de descrição geral, a partir de onde pode alterar as definições de solução, definições da área de trabalho e navegue para o portal do OMS.

7. No menu de navegação esquerdo, clique em **registos de contas de armazenamento**, em *origens de dados da área de trabalho*.

8. No *os registos da conta de armazenamento* página, clique em **adicionar** na parte superior para adicionar registos do cluster para a área de trabalho.

9. Clique em **conta de armazenamento** para adicionar a conta apropriada criada no seu cluster. Se utilizou o nome predefinido, a conta de armazenamento com o nome *sfdg\<resourceGroupName\>*. Também pode confirmar isto, verificando o modelo Azure Resource Manager utilizado para implementar o cluster, ao verificar o valor utilizado para o `applicationDiagnosticsStorageAccountName`. Também poderá ter de se deslocar para baixo e clique em **carregar mais** se o nome não for apresentada. Clique no nome da conta de armazenamento correta cópias de segurança para o selecionar.

10. Em seguida, terá de especificar o *tipo de dados*, que deve ser definido como **eventos de recursos de infraestrutura de serviço**.

11. O *origem* automaticamente deve ser definido como *WADServiceFabric\*EventTable*.

12. Clique em **OK** para ligar a sua área de trabalho para registos do seu cluster.

    ![Adicionar os registos da conta de armazenamento ao OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A conta deve são agora apresentadas como parte da sua *os registos da conta de armazenamento* em origens de dados da sua área de trabalho.

Com esta agora adicionou a solução de análise de recursos de infraestrutura de serviço numa área de trabalho de análise de registos do OMS que agora está corretamente ligada à plataforma do cluster e a tabela de registo de aplicação. Pode adicionar origens adicionais para a área de trabalho da mesma forma.

## <a name="next-steps"></a>Passos Seguintes
* [Implementar o agente do OMS](service-fabric-diagnostics-oms-agent.md) para os nós para recolher os contadores de desempenho e recolher estatísticas de docker registos e para os contentores
* Obter familiarized com o [de registo de pesquisa e consultar](../log-analytics/log-analytics-log-searches.md) funcionalidades disponibilizadas como parte da análise de registos
* [Utilize o estruturador de vistas para criar vistas personalizadas no Log Analytics](../log-analytics/log-analytics-view-designer.md)
