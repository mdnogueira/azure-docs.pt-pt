---
title: "Copiar uma VM com Linux através do Azure CLI 2.0 | Microsoft Docs"
description: "Saiba como criar uma cópia da sua VM do Linux do Azure utilizando o Azure CLI 2.0 e discos geridos."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 98b27f5f86cdb17893a5c98950a2299f8aa30105
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>Criar uma cópia de uma VM com Linux através da utilização do Azure CLI 2.0 e discos geridos


Este artigo mostra como criar uma cópia da sua máquina virtual do Azure (VM) com Linux utilizando o 2.0 CLI do Azure e o modelo de implementação Azure Resource Manager. Também pode efetuar estes passos com a [CLI 1.0 do Azure](copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Também pode [carregar e criar uma VM a partir de um VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos


-   Instalar [CLI do Azure 2.0](/cli/azure/install-az-cli2)

-   Iniciar sessão para uma conta do Azure com [início de sessão az](/cli/azure/#login).

-   Tem uma VM do Azure para utilizar como origem para a sua cópia.

## <a name="step-1-stop-the-source-vm"></a>Passo 1: Parar a VM de origem


Desalocar a VM de origem utilizando [az vm desalocar](/cli/azure/vm#deallocate).
O exemplo seguinte deallocates VM com o nome **myVM** no grupo de recursos **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Passo 2: Copiar a VM de origem


Para copiar uma VM, crie uma cópia do disco rígido virtual subjacente. Este processo cria um VHD especializado, como um disco gerido que contém a mesma configuração e definições como a VM de origem.

Para mais informações sobre Managed Disks do Azure, veja [Azure Managed Disks Overview (Descrição geral dos Managed Disks do Azure)](../windows/managed-disks-overview.md). 

1.  Lista cada VM e o nome do respetivo SO o disco com [lista de vm az](/cli/azure/vm#list). O exemplo seguinte apresenta uma lista de todas as VMs no grupo de recursos com o nome **myResourceGroup**:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Copiar o disco através da criação de um novo geridos através de um disco [criar disco de az](/cli/azure/disk#create). O exemplo seguinte cria um disco chamado **myCopiedDisk** do disco gerido com o nome **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Certifique-se os discos geridos agora no seu grupo de recursos utilizando [lista de discos az](/cli/azure/disk#list). O exemplo seguinte lista os discos geridos no grupo de recursos com o nome **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Passo 3: Configurar uma rede virtual


Os seguintes passos opcionais criar uma nova rede virtual, a sub-rede, o endereço IP público e o cartão de interface de rede virtual (NIC).

Se estiver a copiar uma VM para fins ou implementações adicionais de resolução de problemas, pode não querer utilizar uma VM numa rede virtual existente.

Se pretender criar uma infraestrutura de rede virtual para as suas VMs copiados, siga os próximos passos. Se não pretender criar uma rede virtual, avance para o [passo 4: criar uma VM](#step-4-create-a-vm).

1.  Criar a rede virtual, utilizando [az rede vnet criar](/cli/azure/network/vnet#create). O exemplo seguinte cria uma rede virtual denominada **myVnet** e uma sub-rede designada **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Criar um IP público utilizando [az público-ip da rede criar](/cli/azure/network/public-ip#create). O exemplo seguinte cria um IP público com o nome **myPublicIP** com o nome DNS **mypublicdns**. (O nome DNS tem de ser exclusivos, por isso, forneça um nome exclusivo.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Criar o NIC utilizando [nic da rede az criar](/cli/azure/network/nic#create).
    O exemplo seguinte cria um NIC com o nome **myNic** que está anexada a **mySubnet** sub-rede:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Passo 4: Criar uma VM

Agora, pode criar uma VM utilizando [az vm criar](/cli/azure/vm#create).

Especifique o disco copiado gerido a utilizar como o disco do SO (– anexar--disco do SO), da seguinte forma:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Passos seguintes

Para saber como utilizar a CLI do Azure para gerir a nova VM, consulte [comandos da CLI do Azure para o Azure Resource Manager](../azure-cli-arm-commands.md).
