---
title: "Criar uma cópia da sua VM do Linux com a CLI do Azure 1.0 | Microsoft Docs"
description: "Saiba como criar uma cópia da máquina virtual Linux do Azure com a CLI do Azure 1.0 no modelo de implementação Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 62ae54f3596c9383cbf3b401fcfdb42ecfdee63c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Criar uma cópia de uma máquina virtual de Linux em execução no Azure com a CLI do Azure 1.0
Este artigo mostra como criar uma cópia da sua máquina virtual do Azure (VM) com Linux utilizando o modelo de implementação Resource Manager. Primeiro copiar sobre o sistema operativo e os discos de dados para um novo contentor, em seguida, configurar os recursos de rede e criar a nova máquina virtual.

Também pode [carregar e criar uma VM a partir da imagem de disco personalizado](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- Azure CLI 1.0 – CLI para os modelos de implementação de gestão clássica e de recursos (este artigo)
- [CLI 2.0 do Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="before-you-begin"></a>Antes de começar
Certifique-se de que cumpre os seguintes pré-requisitos antes de iniciar os passos:

* Tem a [CLI do Azure](../../cli-install-nodejs.md) transferido e instalado no seu computador. 
* Também precisa de algumas informações sobre a sua VM do Linux do Azure existente:

| Informações de VM de origem | Onde obtê-lo |
| --- | --- |
| o nome da VM |`azure vm list` |
| Nome do grupo de recursos |`azure vm list` |
| Localização |`azure vm list` |
| Nome da conta de armazenamento |`azure storage account list -g <resourceGroup>` |
| Nome do contentor |`azure storage container list -a <sourcestorageaccountname>` |
| Nome de ficheiro de VHD de VM de origem |`azure storage blob list --container <containerName>` |

* Terá de efetuar algumas opções sobre a nova VM:   <br> -Nome do contentor   <br> Nome da - VM   <br> Tamanho da - VM   <br> nome da - vNet   <br> -Nome da sub-rede   <br> Nome do - IP   <br> Nome - NIC

## <a name="login-and-set-your-subscription"></a>Início de sessão e definir a sua subscrição
1. Início de sessão para a CLI.

    ```azurecli
    azure login
    ```
2. Certifique-se de que está no modo Resource Manager.

    ```azurecli
    azure config mode arm
    ```
3. Defina a subscrição correta. Pode utilizar o 'lista de contas do azure' para ver todas as subscrições.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Parar a VM
Pare e desalocar a VM de origem. Pode utilizar 'lista de vm do azure' para obter uma lista de todas as VMs na sua subscrição e os respetivos recursos nomes de grupo.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Copie o VHD
Pode copiar o VHD do armazenamento de origem para o destino utilizando o `azure storage blob copy start`. Neste exemplo, vamos copie o VHD para a mesma conta de armazenamento, mas um contentor diferente.

Para copiar o VHD para outro contentor na mesma conta de armazenamento, escreva:

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurar a rede virtual para a nova VM
Configure uma rede virtual e a NIC para a nova VM. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Criar nova VM
Agora, pode criar uma VM a partir do disco virtual carregado [utilizando um modelo do resource manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou através da CLI, especificando o URI para o disco copiado escrevendo:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Passos seguintes
Para saber como utilizar a CLI do Azure para gerir a sua nova máquina virtual, consulte [comandos da CLI do Azure para o Azure Resource Manager](../azure-cli-arm-commands.md).

