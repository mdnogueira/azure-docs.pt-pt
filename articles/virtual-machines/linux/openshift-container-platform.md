---
title: Implementar OpenShift contentor plataforma no Azure | Microsoft Docs
description: Implemente OpenShift contentor plataforma no Azure.
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
ms.openlocfilehash: c91b7232b2f87e0b4b5e659126b96a6ef8b4202c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implementar OpenShift contentor plataforma no Azure

Existem várias formas de implementar OpenShift contentor plataforma no Azure. Pode implementar manualmente todos os componentes de infraestrutura do Azure necessários e, em seguida, siga a plataforma de contentor OpenShift [documentação](https://docs.openshift.com/container-platform/3.6/welcome/index.html).
Também pode utilizar um modelo do Resource Manager existente que simplifica a implementação do cluster OpenShift contentor plataforma. Assim que esse modelo for localizado [aqui](https://github.com/Microsoft/openshift-container-platform/).

Outra opção consiste em utilizar o [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Para ambas as opções, é necessária uma subscrição do Red Hat. Durante a implementação, a instância RHEL é registada para a subscrição do Red Hat e ligada para o ID do conjunto que contém a elegibilidade para a plataforma de contentor OpenShift.
Certifique-se de que tem um nome de utilizador do Red Hat subscrição Manager, palavra-passe e ID do conjunto (RHSM nomedeutilizador, palavra-passe de RHSM e ID do conjunto) válido. Pode verificar as informações, iniciando sessão no https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Implementar utilizando o modelo do Resource Manager OpenShift contentor plataforma

Para implementar com o modelo do Resource Manager, um ficheiro de parâmetros é utilizado para fornecer todos os parâmetros de entrada. Se pretender personalizar qualquer um dos itens de implementação que não são abrangidos utilizando os parâmetros de entrada, bifurcação o repositório do github e alterar os itens adequados.

Algumas opções de personalização comuns incluem (mas não se limitando):

- CIDR de VNet [variável no azuredeploy. JSON]
- Tamanho da VM de bastion [variável no azuredeploy. JSON]
- Convenções de nomenclatura [variáveis em azuredeploy. JSON]
- Informações específicas de cluster OpenShift - modificadas através do ficheiro de anfitriões [deployOpenShift.sh]

### <a name="configure-parameters-file"></a>Configurar o ficheiro de parâmetros

Utilize o `appId` valor o principal de serviço que criou anteriormente para o `aadClientId` parâmetro. 

O exemplo seguinte cria um ficheiro de parâmetros com o nome **azuredeploy.parameters.json** com todas as entradas necessárias.

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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
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
        "enableAzure": {
            "value": "true"
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

Substitua itens entre {} com as suas informações pertinentes.

### <a name="deploy-using-azure-cli"></a>Implementar utilizando a CLI do Azure

> [!NOTE] 
> O seguinte comando requer a CLI do Azure 2.0.8 ou posterior. Pode verificar o az CLI versão com o `az --version` comando. Para atualizar a versão do CLI, consulte [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

O exemplo seguinte implementa o cluster de OpenShift e todos os recursos relacionados para um grupo de recursos com o nome myResourceGroup com um nome de implementação de myOpenShiftCluster. O modelo é referenciado diretamente a partir do repositório github e o nome de um ficheiro de parâmetros local **azuredeploy.parameters.json** ficheiro é utilizado.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implementação demora menos de 30 minutos a concluir consoante o número total de nós implementados. O URL da consola OpenShift e nome de DNS do mestre de OpenShift está impresso ao terminal quando concluir a implementação.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-using-openshift-container-platform-marketplace-offer"></a>Implementar utilizando oferta do marketplace de OpenShift contentor plataforma

A forma mais simples de implementar OpenShift contentor plataforma no Azure está a utilizar o [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Esta opção é a mais simples, mas também tem capacidades limitadas de personalização. A oferta inclui três opções de configuração:

- Breve: Implementa um não-HA cluster com um nó principal, um nó de infraestrutura, dois nós de aplicação e um nó de Bastion. Todos os nós são os tamanhos de Standard DS2v2 VM. Este cluster necessita de 10 núcleos totais e é ideal para testes de pequena escala.
- Média: Implementa um cluster do HA com três nós mestre, dois nós de infraestrutura, quatro nós de aplicação e um nó de Bastion. Todos os nós exceto o Bastion são os tamanhos de Standard DS3v2 VM. O nó de Bastion é uma DS2v2 padrão. Este cluster necessita de 38 núcleos.
- Grande: Implementa um HA Cluster com três nós mestre, dois nós de infraestrutura, seis nós de aplicação e um nó de Bastion. O principal e os nós de infraestrutura são os tamanhos de Standard DS3v2 VM, os nós de aplicação são os tamanhos de VM de DS4v2 padrão e o nó de Bastion for um DS2v2 padrão. Este cluster necessita de 70 núcleos.

Configuração do fornecedor de nuvem do Azure é opcional para médio e grande tamanhos de cluster. O tamanho do cluster pequeno não atribua uma opção para configurar o fornecedor de nuvem do Azure.

## <a name="connect-to-the-openshift-cluster"></a>Ligar ao OpenShift cluster

Quando a implementação estiver concluída, ligar a consola OpenShift através do browser, utilizando o `OpenShift Console Uri`. Em alternativa, pode ligar ao mestre de OpenShift utilizando o seguinte comando:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode utilizar o [eliminação do grupo de az](/cli/azure/group#delete) comando para remover o grupo de recursos, de cluster de OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- [Publicar tarefas de implementação](./openshift-post-deployment.md)
- [Resolução de problemas de implementação de OpenShift](./openshift-troubleshooting.md)
- [Introdução à plataforma de contentor OpenShift](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
