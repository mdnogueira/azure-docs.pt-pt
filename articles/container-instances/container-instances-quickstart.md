---
title: "Guia de introdução - criar o primeiro contentor de instâncias de contentor do Azure"
description: "Implementar e começar a utilizar o Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 160ba84d2c022ca3af2eb13a9689a282b4a6b198
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o seu primeiro contentor no Azure Container Instances
Instâncias de contentor do Azure torna mais fácil criar e gerir contentores de Docker no Azure, sem ter de aprovisionar as máquinas virtuais ou que adotar um serviço de nível mais elevado. Este guia de introdução, criar um contentor no Azure e expô-la à internet com um endereço IP público. Esta operação é concluída com um único comando. Dentro de alguns segundos, verá isto no seu browser:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-app-browser]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode utilizar a Shell de nuvem do Azure ou uma instalação local da CLI do Azure para concluir este guia de introdução. Se optar por instalar e utilizar a CLI localmente, este guia de introdução requer que está a executar a CLI do Azure versão 2.0.21 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

As instâncias do Azure Container Instances são recursos do Azure e têm de ser colocadas num grupo de recursos do Azure, que é uma coleção lógica na qual estes recursos são implementados e geridos.

Criar um grupo de recursos com o [criar grupo az] [ az-group-create] comando.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contentor

Pode criar um contentor, fornecendo um nome, uma imagem de Docker e um grupo de recursos do Azure para o [criar contentor de az] [ az-container-create] comando. Opcionalmente, pode expor o contentor na Internet com um endereço IP público. Este guia de introdução, é implementar um contentor que aloja uma aplicação web pequenas escrita em [Node.js](http://nodejs.org).

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public --ports 80
```

Dentro de alguns segundos, deve obter uma resposta ao seu pedido. Inicialmente, o contentor está no **criar** Estado, mas deve iniciar dentro de alguns segundos. Pode verificar o estado com o [mostrar de contentor az] [ az-container-show] comando:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

Na parte inferior da saída, verá o estado de aprovisionamento do contentor e o respetivo endereço IP:

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
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
Server running...
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET / HTTP/1.1" 200 1663 "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET / HTTP/1.1" 200 1663
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET /favicon.ico HTTP/1.1" 404 19
```

## <a name="delete-the-container"></a>Eliminar o contentor

Quando tiver terminado com o contentor, pode removê-la utilizando o [delete de contentor az] [ az-container-delete] comando:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

Para verificar que o contentor foi eliminado, execute o [lista de contentor az](/cli/azure/container#az_container_list) comando:

```azurecli-interactive
az container list --resource-group myResourceGroup -o table
```

O **mycontainer** contentor não deve aparecer no resultado do comando. Não se tiver nenhum outros contentores no grupo de recursos, é apresentada nenhuma saída.

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
