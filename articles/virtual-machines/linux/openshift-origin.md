---
title: Implementar OpenShift origem no Azure | Microsoft Docs
description: Implemente OpenShift origem no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 1860ede19202566947b68b715e6bd354f64c1085
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Implementar OpenShift origem no Azure

Pode utilizar uma das duas formas de implementar OpenShift origem no Azure:

- Pode implementar manualmente todos os componentes de infraestrutura do Azure necessários e, em seguida, siga a origem de OpenShift [documentação](https://docs.openshift.org/3.6/welcome/index.html).
- Também pode utilizar um existente [modelo do Resource Manager](https://github.com/Microsoft/openshift-origin) que simplifica a implementação do cluster OpenShift origem.

## <a name="deploy-by-using-the-openshift-origin-template"></a>Implementar utilizando o modelo de origem OpenShift

Utilize o `appId` valor o principal de serviço que criou anteriormente para o `aadClientId` parâmetro.

O exemplo seguinte cria um ficheiro de parâmetros com o nome azuredeploy.parameters.json com todas as entradas necessárias.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-by-using-azure-cli"></a>Implementar utilizando a CLI do Azure


> [!NOTE] 
> O seguinte comando requer a CLI do Azure 2.0.8 ou posterior. Pode verificar a versão do CLI com o `az --version` comando. Para atualizar a versão do CLI, consulte [instalar o Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

O exemplo seguinte implementa o cluster de OpenShift e todos os recursos relacionados para um grupo de recursos com o nome myResourceGroup, com um nome de implementação de myOpenShiftCluster. O modelo é referenciado diretamente a partir do repositório GitHub utilizando um ficheiro de parâmetros local com o nome azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implementação demora menos de 25 minutos a concluir, dependendo do número total de nós implementados. O URL da consola do OpenShift e o nome DNS das impressões mestres OpenShift para o terminal quando a conclusão da implementação.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Ligar ao OpenShift cluster

Quando a conclusão da implementação, ligar à consola do OpenShift com o seu browser, utilizando o `OpenShift Console Uri`. Em alternativa, pode ligar ao mestre de OpenShift usando o seguinte comando:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Utilize o [eliminação do grupo de az](/cli/azure/group#delete) comando para remover o grupo de recursos, de cluster de OpenShift e todos os recursos relacionados quando já não forem necessárias.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação de OpenShift](./openshift-troubleshooting.md)
- [Introdução ao OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
