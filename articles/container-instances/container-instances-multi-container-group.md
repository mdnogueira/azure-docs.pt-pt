---
title: "Instâncias de contentor do Azure - grupo contentor multi | Documentos do Azure"
description: "Instâncias de contentor do Azure - grupo contentor multi"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 140f58582645ea32f77e901eb13364ed145bbecf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-container-group"></a>Implementar um grupo contentor

Instâncias de contentor do Azure suporta a implementação de vários contentores para um anfitrião único através de um *grupo contentor*. Isto é útil ao criar um sidecar de aplicação para o registo, monitorização ou qualquer outra configuração em que um processo anexado segundo as necessita de um serviço. 

Este documento explica como através de uma configuração de sidecar de contentor multi simples utilizando um modelo Azure Resource Manager a executar.

## <a name="configure-the-template"></a>Configurar o modelo

Crie um ficheiro denominado `azuredeploy.json` e copie o seguinte json para a mesma. 

Neste exemplo, um grupo de contentor com dois contentores e um endereço IP público é definido. O primeiro contentor do grupo é executada uma aplicação com acesso à internet. O contentor segundo, sidecar, faz um pedido de HTTP para a aplicação web principal através de rede local do grupo. 

Neste exemplo sidecar foi ser expandido para acionar um alerta se recebeu um código de resposta HTTP diferente de 200 OK. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",    
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
        "location": "[resourceGroup().location]",
        "properties": {
          "containers": [
            {
              "name": "[variables('container1name')]",
              "properties": {
                "image": "[variables('container1image')]",
                "resources": {
                  "requests": {
                    "cpu": 1,
                    "memoryInGb": 1.5
                    }
                },
                "ports": [
                  {
                    "port": 80
                  }
                ]
              }
            },
            {
              "name": "[variables('container2name')]",
              "properties": {
                "image": "[variables('container2image')]",
                "resources": {
                  "requests": {
                    "cpu": 1,
                    "memoryInGb": 1.5
                    }
                }
              }
            }
          ],
          "osType": "Linux",
          "ipAddress": {
            "type": "Public",
            "ports": [
              {
                "protocol": "tcp",
                "port": "80"
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

Para utilizar um registo de imagem do contentor privada, adicione um objeto para o documento json com o seguinte formato.

```json
"imageRegistryCredentials": [
    {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
    }
]
```

## <a name="deploy-the-template"></a>Implementar o modelo

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create).

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Implementar o modelo com o [criar a implementação do grupo az](/cli/azure/group/deployment#create) comando.

```azurecli-interactive
az group deployment create --name myContainerGroup --resource-group myResourceGroup --template-file azuredeploy.json
```

Dentro de alguns segundos, irá receber uma resposta inicial a partir do Azure. 

## <a name="view-deployment-state"></a>Ver o estado de implementação

Para ver o estado da implementação, utilize o `az container show` comando. Esta ação devolve o endereço IP público aprovisionado através do qual a aplicação pode ser acedida.

```azurecli-interactive
az container show --name myContainerGroup --resource-group myResourceGroup -o table
```

Saída:

```azurecli
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGrou2  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>Ver registos   

Ver o resultado de registo de um contentor utilizando o `az container logs` comando. O `--container-name` argumento especifica o contentor a partir da qual pretende extrair os registos. Neste exemplo, o primeiro contentor está especificado. 

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-app --resource-group myResourceGroup
```

Saída:

```bash
istening on port 80
::1 - - [27/Jul/2017:17:35:29 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:32 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:35 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:38 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver os registos para o contentor do lado do automóvel, execute o mesmo comando, especificando o segundo nome do contentor.

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-sidecar --resource-group myResourceGroup
```

Saída:

```bash
Every 3.0s: curl -I http://localhost                                                                                                                       Mon Jul 17 11:27:36 2017

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 1663
Content-Type: text/html; charset=utf-8
Last-Modified: Sun, 16 Jul 2017 02:08:22 GMT
Date: Mon, 17 Jul 2017 18:27:36 GMT
```

Como pode ver, a sidecar periodicamente é efetuar um pedido de HTTP para a aplicação web principal através de rede local do grupo para se certificar de que está em execução.

## <a name="next-steps"></a>Passos seguintes

Este documento abrange os passos necessários para implementar uma instância de contentor do Azure multi contentor. Para uma experiência de instâncias de contentor do Azure de ponto a ponto, consulte o tutorial de instâncias de contentor do Azure.

> [!div class="nextstepaction"]
> [Tutorial de instâncias de contentor do azure]:./container-instances-tutorial-prepare-app.md
