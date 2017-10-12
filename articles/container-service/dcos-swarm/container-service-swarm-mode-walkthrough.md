---
title: "Guia de início rápido – Cluster Azure Docker CE para Linux | Microsoft Docs"
description: Aprenda rapidamente a criar um cluster Docker CE para contentores do Linux no Azure Container Service com a CLI do Azure.
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
ms.date: 08/25/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 01357ceca1d78c80c901c9fbec08ce85f02fb958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-docker-ce-cluster"></a>Implementar um cluster do Docker CE

Neste guia rápido, verá como implementar um cluster Docker CE com a CLI do Azure. Depois, é implementada e executada no cluster uma aplicação de vários contentores que consiste num front-end da Web e numa instância de Redis. Depois de concluída, a aplicação está acessível através da Internet.

O Docker CE no Azure Container Service encontra-se em pré-visualização e **não deve ser utilizado em cargas de trabalho de produção**.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *ukwest*.

```azurecli-interactive
az group create --name myResourceGroup --location ukwest
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

Crie um cluster Docker CE no Azure Container Service com o comando [az acs create](/cli/azure/acs#create). 

O exemplo seguinte cria um cluster com o nome *mySwarmCluster* com um nó principal do Linux e três nós de agente do Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

Em alguns casos, como numa versão de avaliação limitada, uma subscrição do Azure tem acesso limitado aos recursos do Azure. Caso a implementação falhe devido a um número limitado de núcleos disponíveis, reduza a contagem de agentes pré-definida ao adicionar `--agent-count 1` ao comando [az acs create](/cli/azure/acs#create). 

Ao fim de vários minutos, o comando é concluído e devolve informações sobre o cluster no formato json.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Ao longo deste guia de início rápido, precisará do FQDN do conjunto principal do Docker Swarm e do conjunto de agentes do Docker. Execute o seguinte comando para devolver os FQDNs principal e de agente.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Saída:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Crie um túnel SSH para o principal do Swarm. Substitua `MasterFQDN` pelo endereço FQDN do principal do Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Defina a variável de ambiente `DOCKER_HOST`. Isto permite-lhe executar comandos do Docker relativamente ao Docker Swarm sem ter de especificar o nome do anfitrião.

```bash
export DOCKER_HOST=localhost:2374
```

Está pronto para executar os serviços do Docker no Docker Swarm.


## <a name="run-the-application"></a>Executar a aplicação

Crie um ficheiro com o nome `azure-vote.yaml` e copie o seguinte conteúdo para o mesmo.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Execute o comando [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) para criar o serviço Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Saída:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Utilize o comando [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) para devolver o estado de implementação da aplicação.

```bash
docker stack ps azure-vote
```

Quando o `CURRENT STATE` de cada serviço for `Running`, a aplicação está pronta.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:redis-v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testar a aplicação

Navegue para o FQDN do conjunto de agentes do Swarm para testar a aplicação do Azure Vote.

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