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
ms.openlocfilehash: 159f30fc59a050b9a4ff983e8ac84e424104b484
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implementar OpenShift contentor plataforma no Azure

Pode utilizar um dos vários métodos para implementar o contentor de OpenShift plataforma no Azure:

- Pode implementar manualmente os componentes de infraestrutura do Azure necessários e, em seguida, siga a plataforma de contentor OpenShift [documentação](https://docs.openshift.com/container-platform/3.6/welcome/index.html).
- Também pode utilizar um existente [modelo do Resource Manager](https://github.com/Microsoft/openshift-container-platform/) que simplifica a implementação do cluster OpenShift contentor plataforma.
- Outra opção consiste em utilizar o [oferta Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Para todas as opções, é necessária uma subscrição do Red Hat. Durante a implementação, a instância do Red Hat Enterprise Linux é registada para a subscrição do Red Hat e ligada para o ID do conjunto que contém a elegibilidade para a plataforma de contentor OpenShift.
Certifique-se de que tem um nome de utilizador válido do Red Hat subscrição Manager (RHSM), a palavra-passe e o ID do conjunto. Pode verificar esta informação ao iniciar sessão https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Implementar utilizando o modelo do Resource Manager OpenShift contentor plataforma

Para implementar utilizando o modelo do Resource Manager, pode utilizar um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar qualquer um dos itens de implementação que não são abrangidos utilizando os parâmetros de entrada, bifurcar o repositório do GitHub e altere os itens adequados.

Algumas opções de personalização comuns incluem, mas não estão limitadas a:

- Rede virtual CIDR (variável no azuredeploy. JSON)
- Tamanho da VM de bastion (variável no azuredeploy. JSON)
- Convenções de nomenclatura (variáveis no azuredeploy. JSON)
- Especificações do cluster OpenShift, modificada através do ficheiro de anfitriões (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurar o ficheiro de parâmetros

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

Substitua os itens entre parênteses com as suas informações específicas.

### <a name="deploy-by-using-azure-cli"></a>Implementar utilizando a CLI do Azure

> [!NOTE] 
> O seguinte comando requer a CLI do Azure 2.0.8 ou posterior. Pode verificar a versão do CLI com o `az --version` comando. Para atualizar a versão do CLI, consulte [instalar o Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latesti).

O exemplo seguinte implementa o cluster de OpenShift e todos os recursos relacionados para um grupo de recursos com o nome myResourceGroup, com um nome de implementação de myOpenShiftCluster. O modelo é referenciado diretamente a partir de um local parâmetros azuredeploy.parameters.json ficheiro com o nome de ficheiro é utilizado e o repositório do GitHub.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implementação demora menos de 30 minutos para concluir, dependendo do número total de nós implementados. O URL da consola do OpenShift e o nome DNS das impressões mestres OpenShift para o terminal quando a conclusão da implementação.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Implementar utilizando a oferta OpenShift contentor plataforma Azure Marketplace

A forma mais simples de implementar OpenShift contentor plataforma no Azure está a utilizar o [oferta Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Esta é a opção mais simples, mas também tem capacidades limitadas de personalização. A oferta inclui três opções de configuração:

- **Pequeno**: implementa um cluster de não ser de elevada disponibilidade (HA) com um nó principal, o nó de uma infraestrutura, dois nós de aplicação e nó de um bastion. Todos os nós são os tamanhos de VM de DS2v2 padrão. Este cluster necessita de 10 núcleos totais e é ideal para testes em pequena escala.
- **Média**: implementa um cluster do HA com três nós principais, dois nós de infraestrutura, quatro nós de aplicação e um bastion nó. Todos os nós exceto o nó de bastion são os tamanhos de VM de DS3v2 padrão. O nó de bastion é uma DS2v2 padrão. Este cluster necessita de 38 núcleos.
- **Grande**: implementa um cluster do HA com três nós principais, dois nós de infraestrutura, seis nós de aplicação e um bastion nó. Os nós de infraestrutura e o mestre de são os tamanhos de VM de DS3v2 padrão. Os nós de aplicação são os tamanhos de VM de DS4v2 padrão e o nó de bastion for um DS2v2 padrão. Este cluster necessita de 70 núcleos.

Configuração do fornecedor de solução de nuvem do Azure é opcional para tamanhos de cluster de média e grande. O tamanho do cluster pequeno não atribua uma opção para configurar o fornecedor de solução em nuvem do Azure.

## <a name="connect-to-the-openshift-cluster"></a>Ligar ao OpenShift cluster

Quando a conclusão da implementação, ligar à consola do OpenShift com o seu browser, utilizando o `OpenShift Console Uri`. Em alternativa, pode ligar ao mestre de OpenShift usando o seguinte comando:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Limpar recursos

Utilize o [eliminação do grupo de az](/cli/azure/group#delete) comando para remover o grupo de recursos, de cluster de OpenShift e todos os recursos relacionados quando já não forem necessárias.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação de OpenShift no Azure](./openshift-troubleshooting.md)
- [Introdução à plataforma de contentor OpenShift](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
