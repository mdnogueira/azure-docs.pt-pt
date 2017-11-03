---
title: "Monitorização e diagnóstico para contentores do Windows no Azure Service Fabric | Microsoft Docs"
description: "Configurar a monitorização e diagnóstico para o contentor de Windows orquestradas no Service Fabric do Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 16c9926d44f972d2b38028cb6ab1420de6b60533
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-windows-containers-on-service-fabric-using-oms"></a>Monitorizar os contentores do Windows no Service Fabric com o OMS

Isto faz parte três de um tutorial e explica-lhe configurar OMS para monitorizar os contentores de Windows orquestrados no Service Fabric.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o OMS para o cluster do Service Fabric
> * Utilize uma área de trabalho do OMS para ver e consultar os registos de contentores e nós
> * Configurar o agente do OMS para recolher contentor e métricas de nó

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, deve:
- Tem um cluster no Azure, ou [criar um com este tutorial](service-fabric-tutorial-create-cluster-azure-ps.md)
- [Implementar uma aplicação ao mesmo](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-oms-with-your-cluster-in-the-resource-manager-template"></a>Configurar o OMS com o cluster no modelo do Resource Manager

No caso que utilizou o [modelo fornecido](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) na primeira parte deste tutorial, deve incluir as seguintes adições a um modelo genérico do Service Fabric do Azure Resource Manager. No caso as maiúsculas e minúsculas que tem um cluster do seu próprio que pretender para estar configurada para monitorização de contentores com o OMS:
* Efetue as seguintes alterações ao seu modelo do Resource Manager.
* Implementar com o PowerShell para atualizar o cluster [implementar o modelo](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm). O Azure Resource Manager realiza a que o recurso existe, pelo que irá implementá-la como uma atualização.

### <a name="adding-oms-to-your-cluster-template"></a>Adicionar OMS ao seu modelo de cluster

Efetue as seguintes alterações no seu *Template*:

1. Adicionar a localização de área de trabalho do OMS e atribua o nome para o *parâmetros* secção:
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
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

    Para alterar o valor utilizado para a adicionar os mesmos parâmetros para o *template.parameters.json* e alterar os valores utilizados não existe.

2. Adicione o nome de solução e a solução para a sua *variáveis*: 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Adicione o OMS Microsoft Monitoring Agent como uma extensão da máquina virtual. Localizar o recurso de conjuntos de dimensionamento de máquina virtual: *recursos* > *"apiVersion": "[variables('vmssApiVersion')]"*. Sob o *propriedades* > *virtualMachineProfile* > *extensionProfile* > *extensões*, adicione a seguinte descrição de extensão sob o *ServiceFabricNode* extensão: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Adicione a área de trabalho do OMS como um recurso individual. No *recursos*, após o dimensionamento da máquina virtual define recursos, adicione o seguinte:
    
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
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Aqui](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) é um modelo de exemplo (parte utilizado num neste tutorial) que tem todas estas alterações que pode referenciar conforme necessário. Estas alterações irão adicionar uma área de trabalho de análise de registos do OMS para o grupo de recursos. A área de trabalho será configurada para recolher eventos de plataforma do Service Fabric das tabelas de armazenamento configuradas com o [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) agente. O agente do OMS (Microsoft Monitoring Agent) também foi adicionado a cada nó do cluster como uma extensão de máquina virtual - Isto significa que à medida que o cluster, o agente é automaticamente configurado em cada máquina e estabelecer ligação com ele cópias de segurança para o mesmo espaço de trabalho.

Implemente o modelo com as novas alterações para atualizar o cluster atual. Deverá ver os recursos do OMS no seu grupo de recursos quando este tiver sido concluída. Quando o cluster estiver pronto, implemente a aplicação ao mesmo. No próximo passo, vamos configurar os contentores de monitorização.

## <a name="add-the-container-monitoring-solution-to-your-oms-workspace"></a>Adicionar a solução de monitorização do contentor à sua área de trabalho do OMS

Para configurar a solução de contentor na sua área de trabalho, procure *solução de monitorização do contentor* e crie um recurso de contentores (sob a monitorização + gestão categoria).

![Adicionar a solução de contentores](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Quando lhe for pedido para o *área de trabalho OMS*, selecione a área de trabalho que foi criada no seu grupo de recursos e clique em **criar**. Esta ação adiciona uma *solução de monitorização do contentor* à sua área de trabalho, automaticamente fará com que o agente do OMS implementado pelo modelo iniciar a recolha de estatísticas e registos de docker. 

Navegue de volta para o *grupo de recursos*, onde agora, deverá ver a solução de monitorização adicionada recentemente. Se clicar no mesmo, a página de destino deve mostrar o número de imagens do contentor tem de ser executado. 

*Tenha em atenção que executou posso 5 instâncias do meu contentor da fabrikam da [parte dois](service-fabric-host-app-in-a-container.md) do tutorial*

![Página de destino de solução do contentor](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Clicar no **solução de monitorização do contentor** leva-o a um dashboard mais detalhado, que lhe permite navegarem por vários painéis, bem como para executar consultas na análise de registos. 

*Tenha em atenção que, a partir de Setembro de 2017, a solução vai através de algumas atualizações - ignorar quaisquer erros que pode obter sobre eventos Kubernetes à medida que trabalha na integração orchestrators vários para a mesma solução.*

Uma vez que o agente é diretriz dos registos de docker, assume como mostrar *stdout* e *stderr*. Se deslocar para a direita, verá o inventário de imagem do contentor, estado, métricas e consultas de exemplo que é possível executar para obter os dados mais úteis. 

![Dashboard de solução do contentor](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Ao clicar em qualquer nestes painéis leva-o para a consulta de análise de registos que está a gerar o valor apresentado. Alterar a consulta para  *\**  para ver todos os diferentes tipos de registos que estão a ser captados. Aqui, pode consultar ou filtrar para um desempenho de contentor, os registos, ou ver eventos de plataforma do Service Fabric. Os agentes são constantemente também emitir um heartbeat a partir de cada nó, o que pode examinar para se certificar de que ainda a recolha de dados do todas as suas máquinas se a configuração do cluster é alterado.   

![Consulta de contentor](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>Configurar o agente do OMS para recolher os contadores de desempenho

Outra vantagem de utilizar o agente do OMS é a capacidade de alterar os contadores de desempenho que pretende recolher através da experiência de IU do OMS, em vez de ter de configurar o agente de diagnóstico do Azure e não um Gestor de recursos de modelo baseado em atualização de cada vez. Para tal, clique em **Portal do OMS** na página de destino da sua solução de monitorização do contentor (ou recursos de infraestrutura do serviço).

![Portal do OMS](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

Isto irá demorar a sua área de trabalho no portal do OMS, onde pode ver as suas soluções, criar dashboards personalizados, bem como configurar o agente do OMS. 
* Clique em de **roda roda dentada** no canto superior direito do ecrã para abrir o *definições* menu.
* Clique em **origens ligadas** > **servidores Windows** para verificar que tem *5 Windows computadores ligados*.
* Clique em **dados** > **contadores de desempenho do Windows** para procurar e adicionar novos contadores de desempenho. Aqui irá ver uma lista de recomendações da OMS para contadores de desempenho que foi recolher, bem como a opção para procurar outros contadores. Clique em **adicionar os contadores de desempenho selecionados** para começar a recolher as métricas sugeridas.

    ![Contadores de desempenho](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

No portal do Azure, **atualizar** sua solução de monitorização do contentor nuns minutos e deve começar a ver *desempenho computador* dados provenientes de. Isto irá ajudar a compreender a forma como os recursos estão a ser utilizados. Também pode utilizar estas métricas para tomar decisões adequadas sobre dimensionar o seu cluster ou para confirmar se um cluster é balanceamento da carga conforme esperado.

*Nota: Certifique-se que os filtros de hora estão definidos corretamente para que possa consumir estas métricas.* 

![Contadores de desempenho 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar o OMS para o cluster do Service Fabric
> * Utilize uma área de trabalho do OMS para ver e consultar os registos de contentores e nós
> * Configurar o agente do OMS para recolher contentor e métricas de nó

Agora que configurou a monitorização para a sua aplicação de, experimente o seguinte:

* Configure o OMS para um cluster do Linux, seguir passos semelhantes, conforme apresentado acima. Referência [este modelo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) para efetuar alterações no seu modelo do Resource Manager.
* Configurar OMS configurar [automatizada alertas](../log-analytics/log-analytics-alerts.md) para ajudar a detetar e diagnósticos.
* Explorar a lista de Service Fabric de [recomendado contadores de desempenho](service-fabric-diagnostics-event-generation-perf.md) configurar para os clusters.
* Obter familiarized com o [de registo de pesquisa e consultar](../log-analytics/log-analytics-log-searches.md) funcionalidades disponibilizadas como parte da análise de registos.