---
title: Utilizar ACR com um cluster do Azure DC/SO | Microsoft Docs
description: "Utilizar um registo de contentor do Azure com um cluster DC/SO no serviço de contentor do Azure"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, contentores, microserviços, Mesos, Azure, partilha de ficheiros, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 36e57bb6ebf9f55d42c526a361fed33b4238b313
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Utilize o ACR com um cluster DC/OS para implementar a sua aplicação

Neste artigo, vamos explorar como utilizar o registo de contentor do Azure com um cluster DC/OS. Utilizar ACR permite-lhe em privado armazenar e gerir imagens de contentor. Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Implementar o registo de contentor do Azure (se necessário)
> * Configurar a autenticação de ACR num cluster DC/OS
> * Carregar uma imagem para o registo de contentor do Azure
> * Executar uma imagem de contentor do registo de contentor do Azure

Precisa de um cluster de ACS DC/OS para concluir os passos neste tutorial. Se for necessário, [este script de exemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar uma por si.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Implementar o registo de contentor do Azure

Se necessário, crie um registo de contentor do Azure com o [az acr criar](/cli/azure/acr#create) comando. 

O exemplo seguinte cria um registo com um nome de gerar aleatoriamente. O registo também é configurado com uma conta de administrador utilizando a `--admin-enabled` argumento.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

Quando o registo tiver sido criado, a CLI do Azure produz dados semelhantes ao seguinte. Tome nota do `name` e `loginServer`, estes são utilizados em passos posteriores.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Obter o contentor de credenciais de registo utilizando o [mostrar de credencial de acr az](/cli/azure/acr/credential) comando. Substitui o `--name` pelo indicado no último passo. Tome nota de uma palavra-passe, é necessário num passo posterior.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Para obter mais informações sobre o registo de contentor do Azure, consulte [introdução aos registos de contentor do Docker privadas](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Gerir a autenticação de ACR

A forma convencional push e pull imagem de um registo privada é primeiro autenticar com o registo. Para tal, utilizaria o `docker login` comando em qualquer cliente que precisa de aceder ao registo privado. Uma vez que um cluster DC/OS pode conter vários nós, todos os de que tem de ser autenticada com o ACR, é útil automatizar este processo em cada nó. 

### <a name="create-shared-storage"></a>Criar armazenamento partilhado

Este processo utiliza uma partilha de ficheiros do Azure que foi montada em cada nó no cluster. Se já não configurar o armazenamento partilhado, consulte o artigo [configurar uma partilha de ficheiros no interior de um cluster DC/SO](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Configurar a autenticação de ACR

Em primeiro lugar, obter o FQDN do mestre de DC/OS e armazene-o numa variável.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Crie uma ligação SSH com o mestre (ou o mestre de primeiro) do cluster baseado no DC/SO. Se um valor predefinido não foi utilizado ao criar o cluster, atualize o nome de utilizador.

```azurecli-interactive
ssh azureuser@$FQDN
```

Execute o seguinte comando para iniciar sessão no registo de contentor do Azure. Substitua o `--username` com o nome do registo do contentor e o `--password` com um das palavras-passe fornecidas. Substitua o último argumento *mycontainerregistry.azurecr.io* no exemplo com o nome de loginServer do registo de contentor. 

Este comando armazena os valores de autenticação em localmente o `~/.docker` caminho.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Crie um ficheiro comprimido que contém os valores de autenticação de registo do contentor.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Copie este ficheiro para o armazenamento partilhado de cluster. Este passo faz com que o ficheiro disponível em todos os nós do cluster DC/OS.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Carregar imagem para o ACR

Agora a partir de uma máquina de desenvolvimento ou qualquer outro sistema com o Docker instalado, criar uma imagem e carregá-la para o registo de contentor do Azure.

Crie um contentor a partir da imagem Ubuntu.

```azurecli-interactive
docker run ubuntu --name base-image
```

Capture agora o contentor para uma nova imagem. O nome da imagem tem de incluir o `loginServer` nome de registrywith o contentor um formato de `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Inicie sessão no registo de contentor do Azure. Substitua o nome com o nome de loginServer, nome de utilizador – com o nome do registo do contentor e o – palavra-passe com um das palavras-passe fornecidas.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Por fim, carregue a imagem para o registo ACR. Neste exemplo carrega uma imagem com o nome dcos demonstração.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Executar uma imagem de ACR

Para utilizar uma imagem do registo ACR, crie um ficheiro nomes *acrDemo.json* e copie o seguinte texto para a mesma. Substitua o nome da imagem com o nome do contentor registo loginServer e o nome de imagem, por exemplo `loginServer/imageName`. Tome nota do `uris` propriedade. Esta propriedade contém a localização do ficheiro de autenticação de registo do contentor, que neste caso, é a partilha de ficheiros do Azure que está montada em cada nó no cluster DC/OS.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Implemente a aplicação com a CLI de DC/OC.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial tem de configurar o DC/OS para utilizar o registo de contentor do Azure, incluindo as seguintes tarefas:

> [!div class="checklist"]
> * Implementar o registo de contentor do Azure (se necessário)
> * Configurar a autenticação de ACR num cluster DC/OS
> * Carregar uma imagem para o registo de contentor do Azure
> * Executar uma imagem de contentor do registo de contentor do Azure
