---
title: "Implementar e atualizar aplicações e serviços com o Azure Resource Manager | Microsoft Docs"
description: "Saiba como implementar aplicações e serviços para um cluster de Service Fabric utilizando um modelo Azure Resource Manager."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: dekapur
ms.openlocfilehash: 0ffa1f33c41ceb9f8cdd4c8c9e46f06efa4f89a7
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Gerir aplicações e serviços como recursos do Azure Resource Manager

Pode implementar aplicações e serviços em cluster do Service Fabric através do Azure Resource Manager. Isto significa que, em vez de implementar e gerir aplicações através do PowerShell ou o CLI depois de ter de aguardar que o cluster para estar pronto, pode agora express aplicações e serviços no JSON e implementá-las no mesmo modelo do Resource Manager do seu cluster. O processo de registo de aplicação, aprovisionamento e implementação de todos os acontece num único passo.

Esta é a forma recomendada para a implementação de qualquer configuração, governação ou aplicações de gestão de cluster que necessita no seu cluster. Isto inclui o [Patch Orchestration aplicação](service-fabric-patch-orchestration-application.md), Watchdogs ou aplicações que precisam de estar em execução no seu cluster antes de outros serviços ou aplicações são implementados. 

Quando aplicável, gerir as suas aplicações como recursos do Resource Manager para melhorar a:
* Registo de auditoria: o Gestor de recursos auditorias de cada operação e mantém um detalhadas *registo de atividade* que podem ajudar a analisar as modificações feitas a estas aplicações e o cluster.
* O controlo de acesso baseado em funções (RBAC): Gerir o acesso aos clusters, bem como as aplicações implementadas no cluster pode ser efetuado através do mesmo modelo do Resource Manager.
* O Azure Resource Manager (através do portal do Azure) torna-se um uma-stop-cafetaria para gerir o cluster e as implementações de aplicações críticos.

O fragmento seguinte mostra os diferentes tipos de recursos que podem ser geridos através de um modelo:

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Adicionar uma nova aplicação ao seu modelo do Resource Manager

1. Prepare o modelo de Gestor de recursos do cluster para a implementação. Consulte [criar um cluster do Service Fabric com o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter mais informações sobre este.
2. Pensa sobre algumas das aplicações que planeia implementar no cluster. Existem os que será sempre executado que outras aplicações podem demorar dependências? Planear implementar qualquer governação de cluster ou aplicações de configuração? Estes tipos de aplicações melhor geridos através de um modelo do Resource Manager, tal como explicado acima. 
3. Depois de ter figured saída que aplicações pretende que sejam implementados desta forma, as aplicações tem de ser compactadas, zipado e colocada numa partilha de ficheiros. A partilha tem de estar acessível através de um ponto final de REST do Azure Resource Manager para consumir durante a implementação.
4. O modelo do Resource Manager, abaixo da declaração de cluster, descreve as propriedades de cada aplicação. Estas propriedades incluem a contagem de instâncias ou de réplica e quaisquer cadeias de dependência entre os recursos (outros serviços ou aplicações). Para obter uma lista de propriedades completos, consulte o [REST API Swagger Spec](https://github.com/Azure/azure-rest-api-specs/blob/current/specification/servicefabric/resource-manager/Microsoft.ServiceFabric/2017-07-01-preview/servicefabric.json). Tenha em atenção que isto não substitui a aplicação ou serviço manifestos, mas em vez disso descreve algumas das novidades no-los como parte do modelo de Gestor de recursos do cluster. Segue-se um modelo de exemplo que inclui a implementar um serviço sem estado *Service1* e um serviço com estado *Service2* como parte da *Application1*:

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only"
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name"
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version"
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file"
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The application name"
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Service1",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The service type name"
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Service2",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The service type name"
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
  }
  ```

  > [!NOTE] 
  > O *apiVersion* deve ser definido como `"2017-07-01-preview"`. Também é possível implementar este modelo independentemente do cluster, desde que o cluster já foi implementado.

5. Implemente! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Gerir uma aplicação existente através do Gestor de recursos

Se o cluster já está operacional e obter algumas aplicações que pretende gerir recursos do Gestor de recursos já são implementados no mesmo, em vez de remoção das aplicações e reimplementação-los, pode utilizar uma chamada PUT com as APIs do mesmas para que as aplicações confirmada como recursos do Resource Manager. 


## <a name="next-steps"></a>Passos seguintes

* Utilize o [CLI de recursos de infraestrutura de serviço](service-fabric-cli.md) ou [PowerShell](service-fabric-deploy-remove-applications.md) para implementar outras aplicações em cluster. 
* [Atualize o cluster do Service Fabric](service-fabric-cluster-upgrade.md)

