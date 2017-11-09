---
title: "Kubernertes no tutorial do Azure – atualizar cluster | Microsoft Docs"
description: "Kubernertes no tutorial do Azure – cluster de atualização"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2cb81b5cd8b70df8077d9574e0232bc6b3d37c52
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Atualizar Kubernetes no serviço de contentor do Azure (AKS)

Um cluster do serviço de contentor do Azure (AKS) possa ser atualizado utilizando a CLI do Azure. Durante o processo de atualização, Kubernetes nós são cuidadosamente [cordoned e drained](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) para minimizar perturbações para as aplicações em execução.

Neste tutorial, parte oito de oito, um cluster de Kubernetes é atualizado. As tarefas que concluir incluem:

> [!div class="checklist"]
> * Identificar as versões de Kubernetes atual e disponíveis
> * Atualize os nós de Kubernetes
> * Validar uma atualização com êxito

## <a name="before-you-begin"></a>Antes de começar

Tutoriais anteriores, uma aplicação foi compactada uma imagem de contentor, esta imagem carregada para o registo de contentor do Azure e um cluster de Kubernetes criada. A aplicação, em seguida, foi executada no Kubernetes cluster. 

Se não o fez estes passos e gostaria de acompanhar, voltar para o [Tutorial 1 – criar imagens de contentor](./tutorial-kubernetes-prepare-app.md).


## <a name="get-cluster-versions"></a>Obter versões de cluster

Antes de atualizar um cluster, utilize o `az aks get-versions` comando para verificar qual Kubernetes versões estão disponíveis para atualização.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Aqui pode ver que a versão atual do nó é `1.7.7` e essa versão `1.7.9`, `1.8.1`, e `1.8.2` estão disponíveis.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       AgentPoolVersion    AgentPoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>Atualizar cluster

Utilize o `az aks upgrade` comando para atualizar os nós do cluster. Os exemplos seguintes atualiza o cluster para a versão `1.8.2`.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Saída:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "westus2",
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
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.westus2.azmk8s.io",
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

## <a name="validate-upgrade"></a>Validar a atualização

Agora pode confirmar a atualização foi concluída com êxito com o `az aks show` comando.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Saída:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  westus2     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.westus2.azmk8s.io
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizado Kubernetes num AKS cluster. As tarefas seguintes foram concluídas:

> [!div class="checklist"]
> * Identificar as versões de Kubernetes atual e disponíveis
> * Atualize os nós de Kubernetes
> * Validar uma atualização com êxito

Siga esta ligação para obter mais informações sobre AKS.

> [!div class="nextstepaction"]
> [Descrição geral AKS](./intro-kubernetes.md)