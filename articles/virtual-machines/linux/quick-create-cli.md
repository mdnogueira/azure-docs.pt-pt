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
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/13/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c10f4c575c7b9a68e7a1ff8fedf0f17f4fb8599d
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Criar uma máquina virtual Linux com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este detalhes de início rápido, utilizando a CLI do Azure para implementar uma máquina virtual com Ubuntu server. Quando o servidor estiver implementado, uma ligação SSH é criada e um servidor NGINX Web é instalado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm#az_vm_create). 

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

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web 

Por predefinição, só são permitidas ligações SSH para máquinas virtuais do Linux implementadas no Azure. Se esta VM vier a ser um servidor Web, tem de abrir a porta 80 a partir da Internet. Utilize o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta pretendida.  
 
 ```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o *<publicIpAddress>* pelo endereço IP público correto da sua máquina virtual.  No exemplo acima, o nosso endereço IP era *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Instalar o NGINX

Utilize os seguintes comandos para atualizar as origens de pacote e instalar o pacote NGINX mais recente. 

```bash 
# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do NGINX predefinida. Certifique-se de que utiliza o *publicIpAddress* que documentou acima para visitar a página predefinida. 

![Site predefinido do NGINX](./media/quick-create-cli/nginx.png) 


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#delete) para remover o Grupo de Recursos, a VM e todos os recursos relacionados. Sair sessão SSH para a VM, em seguida, elimine os recursos da seguinte forma:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, implementou uma máquina virtual simples, uma regra de grupo de segurança de rede e instalou um servidor Web. Para saber mais sobre as máquinas virtuais do Azure, continue para o tutorial das VMs do Linux.


> [!div class="nextstepaction"]
> [Tutoriais das máquinas virtuais do Linux do Azure](./tutorial-manage-vm.md)
