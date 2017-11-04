---
title: "Converter uma máquina virtual do Linux no Azure de discos não geridos para discos geridos - discos gerida do Azure | Microsoft Docs"
description: "Como converter uma VM com Linux de discos não geridos em discos geridos utilizando o Azure CLI 2.0 no modelo de implementação Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/23/2017
ms.author: iainfou
ms.openlocfilehash: b9156998e36ddb8c4893cc0df98a7ddf3ff5d4f2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual Linux de discos não geridos para gerido discos

Se tiver existentes máquinas de virtuais de Linux (VMs) que utilizam discos não geridos, pode converter as VMs para utilizar discos geridos através de [Azure geridos discos](../windows/managed-disks-overview.md) serviço. Este processo converte o disco do SO e discos de dados anexados.

Este artigo mostra como converter VMs utilizando a CLI do Azure. Se precisar de instalar ou atualizá-lo, consulte [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar
* Reveja [as FAQ sobre a migração para discos geridos](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Converter a VMs de instância única
Esta secção abrange como converter as VMs do Azure de instância única de discos não geridos para gerido discos. (Se as suas VMs num conjunto de disponibilidade, consulte a secção seguinte). Pode utilizar este processo para converter as VMs de discos de discos para discos premium gerido ou de standard (HDD) não geridos premium (SSD) não gerido para gerido de discos padrão.

1. Desalocar a VM utilizando [az vm desalocar](/cli/azure/vm#deallocate). O exemplo seguinte deallocates VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converta a VM em discos geridos utilizando [converter az vm](/cli/azure/vm#convert). O seguinte processo converte VM com o nome `myVM`, incluindo o disco do SO e discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Iniciar a VM após a conversão para discos geridos utilizando [início de vm az](/cli/azure/vm#start). O exemplo seguinte inicia a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs num conjunto de disponibilidade

Se as VMs que pretende converter gerido discos estão num conjunto de disponibilidade, terá primeiro de converter a conjunto de disponibilidade para um conjunto de disponibilidade gerido.

Todas as VMs no conjunto de disponibilidade tem de ser desalocadas antes de converter o conjunto de disponibilidade. Recomendamos que converta todas as VMs em discos geridos depois do próprio conjunto de disponibilidade foi convertida para um conjunto de disponibilidade gerido. Em seguida, iniciar todas as VMs e continuar a funcionar como normal.

1. Lista todas as VMs num conjunto através de disponibilidade [lista de conjunto de disponibilidade de vm az](/cli/azure/vm/availability-set#list). O exemplo seguinte apresenta uma lista de todas as VMs no conjunto nomeada de disponibilidade `myAvailabilitySet` no grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desalocar todas as VMs utilizando [az vm desalocar](/cli/azure/vm#deallocate). O exemplo seguinte deallocates VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converter o conjunto, utilizando disponibilidade [converter o conjunto de disponibilidade az vm](/cli/azure/vm/availability-set#convert). O exemplo seguinte converte o conjunto nomeada de disponibilidade `myAvailabilitySet` no grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converter todas as VMs em discos geridos utilizando [converter az vm](/cli/azure/vm#convert). O seguinte processo converte VM com o nome `myVM`, incluindo o disco do SO e discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Iniciar todas as VMs após a conversão para discos geridos utilizando [início de vm az](/cli/azure/vm#start). O exemplo seguinte inicia a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre as opções de armazenamento, consulte [descrição geral do Azure gerida discos](../windows/managed-disks-overview.md).
