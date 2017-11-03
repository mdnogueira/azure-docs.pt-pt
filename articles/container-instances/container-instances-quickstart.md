---
title: "Guia de introdução - criar o primeiro contentor de instâncias de contentor do Azure"
description: "Implementar e começar a utilizar o Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 15e6127d419bb41f1b146aff147c43dce2233d8d
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o seu primeiro contentor no Azure Container Instances

Instâncias de contentor do Azure torna mais fácil criar e gerir contentores de Docker no Azure, sem ter de aprovisionar as máquinas virtuais ou que adotar um serviço de nível mais elevado. Este guia de introdução, criar um contentor no Azure e expô-la à internet com um endereço IP público. Esta operação é concluída com um único comando. Dentro de alguns segundos, verá isto no seu browser:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-app-browser]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.12 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

As instâncias do Azure Container Instances são recursos do Azure e têm de ser colocadas num grupo de recursos do Azure, que é uma coleção lógica na qual estes recursos são implementados e geridos.

Criar um grupo de recursos com o [criar grupo az] [ az-group-create] comando.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contentor

Pode criar um contentor, fornecendo um nome, uma imagem de Docker e um grupo de recursos do Azure para o [criar contentor de az] [ az-container-create] comando. Opcionalmente, pode expor o contentor na Internet com um endereço IP público. Neste caso, vamos utilizar um contentor que aloja uma aplicação Web muito simples escrita em [Node.js](http://nodejs.org).

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public
```

Dentro de alguns segundos, deve obter uma resposta ao seu pedido. Inicialmente, o contentor estará no estado **A criar**, mas deverá ser iniciado ao fim de poucos segundos. Pode verificar o estado com o [mostrar de contentor az] [ az-container-show] comando:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

Na parte inferior da saída, verá o estado de aprovisionamento do contentor e o respetivo endereço IP:

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Depois do contentor é movido para o **com êxito** Estado, pode chegá-lo no seu browser utilizando o endereço IP fornecido.

![Aplicação implementada com o Azure Container Instances vista no browser][aci-app-browser]

## <a name="pull-the-container-logs"></a>Extrair os registos de contentor

Pode obter os registos para o contentor que criou com a [az contentor registos] [ az-container-logs] comando:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Saída:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>Eliminar o contentor

Quando tiver terminado com o contentor, pode removê-la utilizando o [delete de contentor az] [ az-container-delete] comando:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Todo o código para o contentor utilizado neste guia de introdução de está disponível [no GitHub][app-github-repo], juntamente com o respetivo Dockerfile. Se quiser tentar criá-lo sozinho e implementá-lo no Azure Container Instances com o Azure Container Registry, avance para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances tutorials](./container-instances-tutorial-prepare-app.md) (Tutoriais do Azure Container Instances)

Para experimentar opções para executar contentores num sistema orquestração no Azure, consulte o [Service Fabric] [ service-fabric] ou [serviço de contentor do Azure (AKS)] [ container-service] inícios rápidos.  

<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
[container-service]: ../aks/kubernetes-walkthrough.md


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png