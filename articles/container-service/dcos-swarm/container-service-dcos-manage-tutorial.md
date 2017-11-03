---
title: "Tutorial de serviço de contentor do Azure – DC/OS gerir | Microsoft Docs"
description: "Tutorial de serviço de contentor do Azure – gerir DC/OS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e93f782c26c32f97749e817ec59ee3c2ecb7e119
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Tutorial de serviço de contentor do Azure – gerir DC/OS

DC/OS fornece uma plataforma distribuída para as aplicações modernas e de execução. Com o serviço de contentor do Azure, o aprovisionamento de um cluster DC/SO pronto produção é simples e rápida. Este detalhes de início rápido passos básicos necessários para implementam um cluster DC/OS e execute carga de trabalho básica.

> [!div class="checklist"]
> * Criar um cluster de ACS DC/OS
> * Ligar ao cluster
> * Instalar a CLI de DC/SO
> * Implementar uma aplicação para o cluster
> * Dimensionar uma aplicação no cluster
> * Dimensionar os nós de cluster do DC/OS
> * Gestão de DC/SO básicas
> * Eliminar o cluster DC/OS

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Criar o cluster DC/OS

Em primeiro lugar, crie um grupo de recursos com o [criar grupo az](/cli/azure/group#create) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Em seguida, crie um cluster DC/OS, com o [az acs criar](/cli/azure/acs#create) comando.

O exemplo seguinte cria um cluster DC/OS, com o nome *myDCOSCluster* e cria as chaves SSH, caso ainda não existam. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Após vários minutos, o comando estiver concluída e devolve informações sobre a implementação.

## <a name="connect-to-dcos-cluster"></a>Ligar ao cluster DC/OS

Depois de criar um cluster DC/OS, pode ser acede através de um túnel SSH. Execute o seguinte comando para devolver o endereço IP público do mestre de DC/OS. Este endereço IP é armazenado numa variável e utilizado no próximo passo.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Para criar o túnel SSH, execute o seguinte comando e siga o ecrã instruções. Se a porta 80 já está a ser utilizado, o comando falha. Atualizar a porta em túnel para uma utilização não, tais como `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>Instalar o DC/SO CLI

Instalar a cli de DC/SO utilizando o [az acs dcos install-cli](/azure/acs/dcos#install-cli) comando. Se estiver a utilizar o Azure CloudShell, a CLI de DC/SO já está instalada. Se estiver a executar a CLI do Azure no macOS ou Linux, poderá ter de executar o comando com sudo.

```azurecli
az acs dcos install-cli
```

Antes do CLI pode ser utilizada com o cluster, tem de ser configurado para utilizar o túnel SSH. Para tal, execute o seguinte comando, ajustar a porta, se necessário.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Executar uma aplicação

A predefinição mecanismo para um cluster do DC/SO de ACS de agendamento é Marathon. Marathon é utilizada para iniciar uma aplicação e gerir o estado da aplicação no cluster DC/OS. Para agendar uma aplicação através do Marathon, crie um ficheiro denominado **marathon adequada**e copie o seguinte conteúdo para a mesma. 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Execute o seguinte comando para agendar a aplicação para ser executada no cluster DC/OS.

```azurecli
dcos marathon app add marathon-app.json
```

Para ver o estado de implementação da aplicação, execute o seguinte comando.

```azurecli
dcos marathon app list
```

Quando o **tarefas** muda de valor de coluna de *0/1* para *1/1*, implementação de aplicação foi concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Dimensionar a aplicação do Marathon

No exemplo anterior, foi criada uma aplicação de instância única. Para atualizar esta implementação, de modo a que três instâncias da aplicação estão disponíveis, abrir o **marathon adequada** de ficheiros e atualize a propriedade de instância para 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Atualizar a aplicação a utilizar o `dcos marathon app update` comando.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Para ver o estado de implementação da aplicação, execute o seguinte comando.

```azurecli
dcos marathon app list
```

Quando o **tarefas** muda de valor de coluna de *1/3* para *3/1*, implementação de aplicação foi concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Executar a aplicação acessíveis à internet

O cluster de ACS DC/OS é constituído por dois conjuntos de nó, uma pública que é acessível na internet e um privada que não está acessível na internet. O conjunto predefinido é privados nós, que foi utilizada no último exemplo.

Para tornar uma aplicação acessível na internet, tem de implementá-las para o conjunto de nó público. Para tal, atribua o `acceptedResourceRoles` objeto um valor de `slave_public`.

Crie um ficheiro denominado **nginx public.json** e copie o seguinte conteúdo para a mesma.

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Execute o seguinte comando para agendar a aplicação para ser executada no cluster DC/OS.

```azurecli 
dcos marathon app add nginx-public.json
```

Obter o endereço IP público dos agentes DC/SO cluster pública.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Navegar para este endereço devolve o site NGINX predefinido.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Cluster do DC/SO de escala

Nos exemplos anteriores, uma aplicação foi escalada para múltiplas instâncias. Também pode ser escalada para a infraestrutura de DC/OS para fornecer mais ou menos capacidade de cálculo. Isto é feito com o [az acs Dimensionar]() comando. 

Para ver a contagem atual de agentes DC/OS, utilize o [az acs mostrar](/cli/azure/acs#show) comando.

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Para aumentar a contagem de 5, utilize o [az acs Dimensionar](/cli/azure/acs#scale) comando. 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Eliminar o cluster DC/OS

Quando já não é necessário, pode utilizar o [eliminação do grupo de az](/cli/azure/group#delete) comando para remover o grupo de recursos, o cluster DC/OS e todos os recursos relacionados.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a tarefa de gestão básica do DC/OS, incluindo o seguinte. 

> [!div class="checklist"]
> * Criar um cluster de ACS DC/OS
> * Ligar ao cluster
> * Instalar a CLI de DC/SO
> * Implementar uma aplicação para o cluster
> * Dimensionar uma aplicação no cluster
> * Dimensionar os nós de cluster do DC/OS
> * Eliminar o cluster DC/OS

Avançar para o próximo tutorial para saber mais sobre a aplicação no DC/SO no Azure de balanceamento de carga. 

> [!div class="nextstepaction"]
> [Fazer o balanceamento de carga de aplicações](container-service-load-balancing.md)