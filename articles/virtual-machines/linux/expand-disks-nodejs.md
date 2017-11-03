---
title: Expanda o disco do SO na VM com Linux com a CLI do Azure 1.0 | Microsoft Docs
description: "Saiba como expandir o disco virtual do sistema operativo (SO) numa VM com Linux utilizando a CLI do Azure 1.0 e o modelo de implementação Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 0aedcd70b54c2ed47ec327ccf0529a48351353c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Expanda o disco do SO numa VM com Linux utilizando a CLI do Azure com a CLI do Azure 1.0
O tamanho de disco rígido virtual predefinido para o sistema operativo (SO) é, geralmente, 30 GB numa máquina virtual (VM) do Linux no Azure. Pode [adicionar discos de dados](add-disk.md) fornecer espaço de armazenamento adicional, mas também pode pretender expandir o disco de SO. Este artigo fornece detalhes sobre como expandir o disco de SO para uma VM com Linux utilizando discos não geridos com a CLI do Azure 1.0.

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#prerequisites) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](expand-disks.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="prerequisites"></a>Pré-requisitos
É necessário o [mais recente do Azure CLI 1.0](../../cli-install-nodejs.md) instalado e registado [conta do Azure](https://azure.microsoft.com/pricing/free-trial/) utilizando o modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem *myResourceGroup* e *myVM*.

## <a name="expand-os-disk"></a>Expandir disco do SO

1. Não não possível efetuar operações em discos rígidos virtuais com a VM em execução. O exemplo seguinte interrompe e deallocates VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup*:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop`não libertar os recursos de computação. Para libertar recursos de computação, utilize `azure vm deallocate`. A VM tem de ser desalocada para expandir o disco rígido virtual.

2. Atualizar o tamanho da utilização de disco de SO não geridos a `azure vm set` comando. O exemplo seguinte atualiza a VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup* ser *50* GB:

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Inicie VM da seguinte forma:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH para a VM com as credenciais adequadas. Para verificar o disco do SO foi redimensionado, utilize `df -h`. O resultado de exemplo seguinte mostra a partição primária (*/dev/sda1*) está agora 50 GB:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Passos seguintes
Se precisar de armazenamento adicional, também [adicionar discos de dados para uma VM com Linux](add-disk.md). Para obter mais informações sobre a encriptação de disco, consulte [encriptar discos numa VM com Linux utilizando a CLI do Azure](encrypt-disks.md).
