---
title: "O contentor do Azure Service início rápido - implementar clusters DC/SO | Microsoft Docs"
description: "O contentor do Azure Service início rápido - implementar o Cluster DC/OS"
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
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8070d224fe6281e61f67483d4f1dd905a2ab99eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-dcos-cluster"></a>Criar um cluster do DC/OS

DC/OS fornece uma plataforma distribuída para as aplicações modernas e de execução. Com o serviço de contentor do Azure, o aprovisionamento de um cluster DC/SO pronto produção é simples e rápida. Este detalhes de início rápido os passos básicos necessários para implementar um DC/SO do cluster e executam a carga de trabalho básica.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Criar o cluster DC/OS

Criar um cluster DC/OS, com o [az acs criar](/cli/azure/acs#create) comando.

O exemplo seguinte cria um cluster DC/OS, com o nome *myDCOSCluster* e cria as chaves SSH, caso ainda não existam. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

Em alguns casos, como numa versão de avaliação limitada, uma subscrição do Azure tem acesso limitado aos recursos do Azure. Caso a implementação falhe devido a um número limitado de núcleos disponíveis, reduza a contagem de agentes pré-definida ao adicionar `--agent-count 1` ao comando [az acs create](/cli/azure/acs#create). 

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

O túnel SSH, pode ser testado, navegando até `http://localhost`. Se uma porta outro que foi utilizado 80, ajuste a localização para corresponder. 

Se o túnel SSH foi criado com êxito, é devolvido o portal de DC/OS.

![IU DCOS](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Instalar o DC/SO CLI

A interface de linha de comandos do DC/OS é utilizada para gerir um cluster DC/SO a partir da linha de comandos. Instalar a cli de DC/SO utilizando o [az acs dcos install-cli](/azure/acs/dcos#install-cli) comando. Se estiver a utilizar o Azure CloudShell, a CLI de DC/SO já está instalada. 

Se estiver a executar a CLI do Azure no macOS ou Linux, poderá ter de executar o comando com sudo.

```azurecli
az acs dcos install-cli
```

Antes do CLI pode ser utilizada com o cluster, tem de ser configurado para utilizar o túnel SSH. Para tal, execute o seguinte comando, ajustar a porta, se necessário.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Executar uma aplicação

A predefinição mecanismo para um cluster do DC/SO de ACS de agendamento é Marathon. Marathon é utilizada para iniciar uma aplicação e gerir o estado da aplicação no cluster DC/OS. Para agendar uma aplicação através do Marathon, crie um ficheiro denominado *marathon adequada*e copie o seguinte conteúdo para a mesma. 

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
dcos marathon app add marathon-app.json
```

Para ver o estado de implementação da aplicação, execute o seguinte comando.

```azurecli
dcos marathon app list
```

Quando o **a AGUARDAR** muda de valor de coluna de *verdadeiro* para *falso*, implementação de aplicação foi concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Obter o endereço IP público dos agentes de cluster DC/OS.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Navegar para este endereço devolve o site NGINX predefinido.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Eliminar o cluster DC/OS

Quando já não é necessário, pode utilizar o [eliminação do grupo de az](/cli/azure/group#delete) comando para remover o grupo de recursos, o cluster DC/OS e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Este início rápido, que implementar um cluster DC/OS e tem de executar um contentor de Docker simple no cluster. Para saber mais sobre o serviço de contentor do Azure, avance para os tutoriais de ACS.

> [!div class="nextstepaction"]
> [Gerir um Cluster de ACS DC/OS](container-service-dcos-manage-tutorial.md)