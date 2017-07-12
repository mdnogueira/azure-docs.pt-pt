---
title: "Guia de introdução ao Azure - Criar a CLI da VM | Microsoft Docs"
description: "Aprender rapidamente a criar máquinas virtuais com a CLI do Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: a7cba5b2c43704d92e36d6f808efaa9fc73fdf36
ms.contentlocale: pt-pt
ms.lasthandoff: 06/20/2017

---

<a id="create-a-linux-virtual-machine-with-the-azure-cli" class="xliff"></a>

# Criar uma máquina virtual Linux com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia fornece detalhes utilizando a CLI do Azure para implementar uma virtual a executar o Ubuntu 16.04 LTS. Quando o servidor estiver implementado, uma ligação SSH é criada e um servidor NGINX Web é instalado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

<a id="create-a-resource-group" class="xliff"></a>

## Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

<a id="create-virtual-machine" class="xliff"></a>

## Criar a máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm#create). 

O exemplo seguinte cria uma VM com o nome *myVM* e cria chaves SSH caso estas ainda não existam numa localização chave predefinida. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

<a id="open-port-80-for-web-traffic" class="xliff"></a>

## Abrir a porta 80 para o tráfego da Web 

Por predefinição, só são permitidas ligações SSH para máquinas virtuais do Linux implementadas no Azure. Se esta VM vier a ser um servidor Web, tem de abrir a porta 80 a partir da Internet. Utilize o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta pretendida.  
 
 ```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

<a id="ssh-into-your-vm" class="xliff"></a>

## Aceder através de SSH à VM

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o *<publicIpAddress>* pelo endereço IP público correto da sua máquina virtual.  No exemplo acima, o nosso endereço IP era *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

<a id="install-nginx" class="xliff"></a>

## Instalar o NGINX

Utilize o script de bash seguinte para atualizar as origens de pacotes e instalar o pacote NGINX mais recente. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

<a id="view-the-nginx-welcome-page" class="xliff"></a>

## Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do NGINX predefinida. Certifique-se de que utiliza o *publicIpAddress* que documentou acima para visitar a página predefinida. 

![Site predefinido do NGINX](./media/quick-create-cli/nginx.png) 


<a id="clean-up-resources" class="xliff"></a>

## Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#delete) para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

<a id="next-steps" class="xliff"></a>

## Passos seguintes

Neste guia de introdução, implementou uma máquina virtual simples, uma regra de grupo de segurança de rede e instalou um servidor Web. Para saber mais sobre as máquinas virtuais do Azure, continue para o tutorial das VMs do Linux.


> [!div class="nextstepaction"]
> [Tutoriais das máquinas virtuais do Linux do Azure](./tutorial-manage-vm.md)

