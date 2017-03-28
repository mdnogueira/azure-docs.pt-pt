---
title: "Guia de Introdução do Azure - Criar CLI da VM do Windows | Microsoft Docs"
description: "Aprender rapidamente a criar máquinas virtuais do Windows com a CLI do Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/20/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 0f9ff487e289eadb857508134b7e08b00360fdd3
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>Criar uma máquina virtual do Windows com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia detalha a utilização da CLI do Azure para implementar uma virtual a executar o Windows Server 2016.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, veja [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#create). 

O exemplo seguinte cria uma VM com o nome `myVM`. Este exemplo utiliza `azureuser` para um nome de utilizador administrativo e ` myPassword12` como palavra-passe. Atualize estes valores para algo adequado ao ambiente. Estes valores são necessários quando criar uma ligação com a máquina virtual.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo. Anote o endereço IP público. Este endereço é utilizado para aceder à VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP com o endereço IP público da máquina virtual. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a máquina virtual.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, pode ser utilizado o seguinte comando para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre instalação de uma função e a configuração de firewall](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar amostras de CLI de implementação de VM](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
