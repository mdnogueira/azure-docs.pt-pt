---
title: "Atualizar um cluster do serviço de contentor do Azure (AKS) | Microsoft Docs"
description: "Atualizar um cluster do serviço de contentor do Azure (AKS)"
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
ms.openlocfilehash: 15e3e96587962ef9cc531e1825f37b92d26928fd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Atualizar um cluster do serviço de contentor do Azure (AKS)

Serviço de contentor do Azure (AKS) torna mais fácil efetuar tarefas de gestão comuns, incluindo Kubernetes clusters a atualizar.

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster do AKS

Antes de atualizar um cluster, utilize o `az aks get-versions` comando para verificar qual Kubernetes versões estão disponíveis para atualização.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Saída:

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

Temos três versões disponíveis para atualização: 1.7.9, 1.8.1 e 1.8.2. Podemos utilizar o `az aks upgrade` comando para atualizar para a versão mais recente disponível.  Durante o processo de atualização, nós são cuidadosamente [cordoned e drained](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) para minimizar perturbações para as aplicações em execução.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
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
    "kubernetesVersion": "1.8.2",
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

Agora pode confirmar a atualização foi concluída com êxito com o `az aks show` comando.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Saída:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como implementar e gerir AKS com os tutoriais AKS.

> [!div class="nextstepaction"]
> [Tutorial AKS](./tutorial-kubernetes-prepare-app.md)