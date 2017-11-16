---
title: "Dimensionar um cluster do serviço de contentor do Azure (AKS) | Microsoft Docs"
description: "Dimensione um cluster do serviço de contentor do Azure (AKS)."
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, Containers, Microsserviços, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: b2fa3ebb7a22b9d19678d45cc50806627ab80e90
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>Dimensionar um cluster do serviço de contentor do Azure (AKS)

É fácil de dimensionar um cluster AKS para um número diferente de nós.  Selecione o número de nós pretendido e execute o `az aks scale` comando.  Quando o dimensionamento para baixo, nós será cuidadosamente [cordoned e drained](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) para minimizar perturbações para as aplicações em execução.  Quando o dimensionamento, o `az` comando aguarda até que nós estão marcados `Ready` pelo Kubernetes cluster.

## <a name="scale-the-cluster-nodes"></a>Dimensionar os nós de cluster

Utilize o `az aks scale` comando dimensionar os nós do cluster. O exemplo seguinte dimensiona um cluster com o nome *myK8SCluster* para um único nó.

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --node-count 1
```

Saída:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
  "name": "myK8sCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myK8sCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.7.7",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como implementar e gerir AKS com os tutoriais AKS.

> [!div class="nextstepaction"]
> [Tutorial AKS](./tutorial-kubernetes-prepare-app.md)
