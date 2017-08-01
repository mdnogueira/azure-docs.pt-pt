---
title: Criar o seu primeiro contentor do Azure Container Instances
description: "Implementar e começar a utilizar o Azure Container Instances"
services: container-service
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 3b15d6645b988f69f1f05b27aff6f726f34786fc
ms.openlocfilehash: 933299ce5a5d6f5b2262d40ae768019ccaf8796a
ms.contentlocale: pt-pt
ms.lasthandoff: 07/26/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o seu primeiro contentor no Azure Container Instances

O Azure Container Instances permite criar e gerir facilmente contentores no Azure. Neste início rápido, vai criar um contentor no Azure e expô-lo na Internet com um endereço IP público. Esta operação é concluída com um único comando. Dentro de alguns segundos, verá isto no seu browser:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-app-browser]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos da CLI do Azure Container Instances só estão atualmente disponíveis no Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

As instâncias do Azure Container Instances são recursos do Azure e têm de ser colocadas num grupo de recursos do Azure, que é uma coleção lógica na qual estes recursos são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contentor

Para criar um contentor, pode fornecer um nome, uma imagem do Docker e um grupo de recursos do Azure. Opcionalmente, pode expor o contentor na Internet com um endereço IP público. Neste caso, vamos utilizar um contentor que aloja uma aplicação Web muito simples escrita em [Node.js](http://nodejs.org).

Os comandos da CLI do Azure Container Instances só estão atualmente disponíveis no Azure Cloud Shell.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public 
```

Dentro de alguns segundos, deve obter uma resposta ao seu pedido. Inicialmente, o contentor estará no estado **A criar**, mas deverá ser iniciado ao fim de poucos segundos. Pode verificar o estado com o comando`show`:

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

Quando o contentor passar para o estado **Bem-sucedido**, pode alcançá-lo no browser com o endereço IP fornecido. 

![Aplicação implementada com o Azure Container Instances vista no browser][aci-app-browser]

## <a name="pull-the-container-logs"></a>Extrair os registos de contentor

Pode utilizar o comando `logs` para extrair os registos do contentor que criou:

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

Quando já não precisar do contentor, pode removê-lo com o comando `delete`:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Todos os códigos do contentor utilizados neste início rápido estão disponíveis no [GitHub][app-github-repo], juntamente com os respetivos Dockerfiles. Se quiser tentar criá-lo sozinho e implementá-lo no Azure Container Instances com o Azure Container Registry, avance para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances tutorials](./container-instances-tutorial-prepare-app.md) (Tutoriais do Azure Container Instances)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
