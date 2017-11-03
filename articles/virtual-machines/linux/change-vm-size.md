---
title: Como redimensionar uma VM com Linux com o 2.0 CLI do Azure | Microsoft Docs
description: "Como aumentar verticalmente ou reduzir verticalmente uma máquina virtual do Linux, ao alterar o tamanho da VM."
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 23fc9f7f34732079682857d4ee685fe811751698
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>Redimensionar uma máquina virtual de Linux utilizando a CLI 2.0

Depois de aprovisionar uma máquina virtual (VM), pode aumentar ou reduzir verticalmente a VM alterando o [tamanho da VM][vm-sizes]. Em alguns casos, tem de Desalocação pela primeira vez a VM. Terá de Desalocação da VM, se o tamanho pretendido não está disponível no cluster de hardware que está a alojar a VM. Este artigo fornece detalhes sobre como redimensionar uma VM com Linux com o 2.0 CLI do Azure. Também pode efetuar estes passos com a [CLI 1.0 do Azure](change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="resize-a-vm"></a>Redimensionar uma VM
Redimensionar uma VM, terá da versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

1. Ver a lista de tamanhos VM disponíveis no cluster de hardware em que a VM está alojada com [az vm-vm-redimensionamento-opções de lista](/cli/azure/vm#list-vm-resize-options). O exemplo seguinte lista os tamanhos de VM para a VM com o nome `myVM` no grupo de recursos `myResourceGroup` região:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se o tamanho da VM pretendido estiver listado, redimensione a VM com [az vm redimensionar](/cli/azure/vm#resize). O exemplo seguinte redimensiona VM com o nome `myVM` para o `Standard_DS3_v2` tamanho:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Reinicia a VM durante este processo. Após o reinício, o SO existente e os discos de dados são novamente mapeados. Nada no disco temporário é perdido.

3. Se o tamanho da VM pretendido não estiver listado, terá de Desalocação pela primeira vez a VM com [az vm desalocar](/cli/azure/vm#deallocate). Este processo permite que a VM, em seguida, ser redimensionada para qualquer dimensão disponível que a região suporta e, em seguida, iniciado. Os seguintes passos desalocar, redimensionar e, em seguida, inicie a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Também Desalocação da VM versões quaisquer endereços IP dinâmicos atribuídos à VM. Os SO e discos de dados não são afetados.

## <a name="next-steps"></a>Passos seguintes
Para escalabilidade adicional, execute várias instâncias VM e aumentar horizontalmente. Para obter mais informações, consulte [Dimensionar automaticamente máquinas Linux de um conjunto de dimensionamento de Máquina Virtual][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
