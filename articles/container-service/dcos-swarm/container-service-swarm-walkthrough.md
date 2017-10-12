---
title: "Guia de início rápido – Cluster Azure Docker Swarm para Linux | Microsoft Docs"
description: Aprenda rapidamente a criar um cluster Docker Swarm para contentores do Linux no Azure Container Service com a CLI do Azure.
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, Docker, Swarm
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 876135d62d548e155f4ebefd8bbd9d9cca8b87d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-docker-swarm-cluster"></a>Implementar um cluster do Docker Swarm

Neste guia rápido, verá como implementar um cluster Docker Swarm com a CLI do Azure. Depois, é implementada e executada no cluster uma aplicação de vários contentores que consiste num front-end da Web e numa instância de Redis. Depois de concluída, a aplicação está acessível através da Internet.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este início rápido requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Saída:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Criar um cluster do Docker Swarm

Crie um cluster Docker Swarm no Azure Container Service com o comando [az acs create](/cli/azure/acs#create). 

O exemplo seguinte cria um cluster com o nome *mySwarmCluster* com um nó principal do Linux e três nós de agente do Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

Em alguns casos, como numa versão de avaliação limitada, uma subscrição do Azure tem acesso limitado aos recursos do Azure. Caso a implementação falhe devido a um número limitado de núcleos disponíveis, reduza a contagem de agentes pré-definida ao adicionar `--agent-count 1` ao comando [az acs create](/cli/azure/acs#create). 

Ao fim de vários minutos, o comando é concluído e devolve informações sobre o cluster no formato json.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Ao longo deste guia de início rápido, precisará do endereço IP do conjunto principal do Docker Swarm e do conjunto de agentes do Docker. Execute o seguinte comando para devolver os dois endereços IP.


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

Saída:

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Crie um túnel SSH para o principal do Swarm. Substitua `IPAddress` pelo endereço IP do principal do Swarm.

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

Defina a variável de ambiente `DOCKER_HOST`. Isto permite-lhe executar comandos do Docker relativamente ao Docker Swarm sem ter de especificar o nome do anfitrião.

```bash
export DOCKER_HOST=:2375
```

Está pronto para executar os serviços do Docker no Docker Swarm.


## <a name="run-the-application"></a>Executar a aplicação

Crie um ficheiro com o nome `docker-compose.yaml` e copie o seguinte conteúdo para o mesmo.

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Execute o seguinte comando para criar o serviço do Azure Vote.

```bash
docker-compose up -d
```

Saída:

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:redis-v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:redis-v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:redis-v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>Testar a aplicação

Navegue para o endereço IP do conjunto de agentes do Swarm para testar a aplicação do Azure Vote.

![Imagem de navegação para o Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Eliminar o cluster
Quando o cluster já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obter o código

Neste início rápido, foram utilizadas imagens de contentor pré-criadas para criar um serviço do Docker. O código da aplicação relacionado, o Dockerfile, e o ficheiro Compor estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Docker Swarm e implementou uma aplicação de vários contentores no mesmo.

Para saber mais sobre como integrar o Docker Swarm no Visual Studio Team Services, continue para o artigo CI/CD com Docker Swarm e VSTS.

> [!div class="nextstepaction"]
> [CI/CD com Docker Swarm e VSTS](./container-service-docker-swarm-setup-ci-cd.md)