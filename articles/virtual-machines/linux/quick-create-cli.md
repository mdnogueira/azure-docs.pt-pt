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
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: d051382954db989ce4152602d249383e7b9dfa46
ms.lasthandoff: 04/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Criar uma máquina virtual Linux com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia detalha a utilização da CLI do Azure para implementar uma virtual a executar o Ubuntu 16.04 LTS. Quando o servidor estiver implementado, acedemos à VM através de SSH, de modo a instalar o NGINX. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) antes de começar.

Além disso, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, veja [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm#create). 

O exemplo seguinte cria uma VM com o nome `myVM` e cria chaves SSH caso estas ainda não existam numa localização chave predefinida. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web 

Por predefinição, só são permitidas ligações SSH para máquinas virtuais do Linux implementadas no Azure. Se esta VM vier a ser um servidor Web, tem de abrir a porta 80 a partir da Internet.  É necessário um único comando para abrir a porta pretendida.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o `<publicIpAddress>` pelo endereço IP público correto da sua máquina virtual.  No exemplo acima, o nosso endereço IP era `40.68.254.142`.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Instalar o NGINX

Utilize o script de bash seguinte para atualizar as origens de pacotes e instalar o pacote NGINX mais recente. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do NGINX predefinida. Certifique-se de que utiliza o `publicIpAddress` que documentou acima para visitar a página predefinida. 

![Site predefinido do NGINX](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, pode ser utilizado o seguinte comando para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre a criação de máquinas virtuais altamente disponíveis](create-cli-complete.md)

[Explorar amostras de CLI de implementação de VM](cli-samples.md)

