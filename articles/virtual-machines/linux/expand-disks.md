---
title: "Expanda os discos rígidos virtuais numa VM com Linux no Azure | Microsoft Docs"
description: "Saiba como expandir os discos rígidos virtuais numa VM com Linux com o 2.0 CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2017
ms.author: iainfou
ms.openlocfilehash: b82cc0473c003da767ee230ab485c69b233977d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Como expandir os discos rígidos virtuais numa VM com Linux com a CLI do Azure
O tamanho de disco rígido virtual predefinido para o sistema operativo (SO) é, geralmente, 30 GB numa máquina virtual (VM) do Linux no Azure. Pode [adicionar discos de dados](add-disk.md) fornecer espaço de armazenamento adicional, mas também pode pretender expandir um disco de dados existente. Este artigo fornece detalhes sobre como expandir discos geridos para uma VM com Linux com o 2.0 CLI do Azure. Também pode expandir o disco de SO não gerido com o [CLI do Azure 1.0](expand-disks-nodejs.md).

> [!WARNING]
> Certificar-se sempre de que a cópia de segurança os dados antes de executar disco redimensiona operações. Para obter mais informações, consulte [cópia de segurança VMs com Linux no Azure](tutorial-backup-vms.md).

## <a name="expand-disk"></a>Expanda o disco
Certifique-se de que a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Este artigo requer uma VM existente no Azure pelo menos um disco de dados ligada e está preparado. Se ainda não tiver uma VM que pode utilizar, consulte [criar e preparar uma VM com discos de dados](tutorial-manage-disks.md#create-and-attach-disks).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem *myResourceGroup* e *myVM*.

1. Não não possível efetuar operações em discos rígidos virtuais com a VM em execução. Desalocar a VM com [az vm desalocar](/cli/azure/vm#deallocate). O exemplo seguinte deallocates VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop`não libertar os recursos de computação. Para libertar recursos de computação, utilize `az vm deallocate`. A VM tem de ser desalocada para expandir o disco rígido virtual.

2. Ver uma lista de discos geridos num grupo de recursos com [lista de discos az](/cli/azure/disk#list). O exemplo seguinte mostra uma lista de discos geridos no grupo de recursos com o nome *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expanda o disco necessário com [atualização de disco az](/cli/azure/disk#update). O exemplo seguinte expande o disco gerido com o nome *myDataDisk* ser *200*tamanho Gb:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Se expandir um disco gerido, o tamanho atualizado está mapeado para o tamanho de disco gerido mais próximo. Para uma tabela dos escalões e tamanhos de disco gerido disponíveis, consulte [geridos discos descrição geral do Azure - preços e faturação](../windows/managed-disks-overview.md#pricing-and-billing).

3. Iniciar a VM com [início de vm az](/cli/azure/vm#start). O exemplo seguinte inicia a VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH para a VM com as credenciais adequadas. Pode obter o endereço IP público da sua VM com [mostrar de vm az](/cli/azure/vm#show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

5. Para utilizar o disco expandido, precisa de expandir a partição e o sistema de ficheiros subjacente.

    a. Se já montado, desmonte o disco:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Utilize `parted` para ver informações de disco e redimensionar a partição:

    ```bash
    sudo parted /dev/sdc
    ```

    Ver informações sobre o esquema de partição existente com `print`. O resultado é semelhante ao exemplo seguinte, que mostra que o disco subjacente é 215Gb de tamanho:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Expanda a partição com `resizepart`. Introduza o número de partição, *1*e um tamanho para a nova partição:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Para sair, introduza:`quit`

5. Partição redimensionado, verifique a consistência da partição com `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

6. Agora redimensionar o sistema de ficheiros com `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

7. Montar a partição para a localização pretendida, tais como `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

8. Para verificar o disco do SO foi redimensionado, utilize `df -h`. O resultado de exemplo seguinte mostra a unidade de dados, */dev/sdc1*, está agora 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Passos seguintes
Se precisar de armazenamento adicional, também [adicionar discos de dados para uma VM com Linux](add-disk.md). Para obter mais informações sobre a encriptação de disco, consulte [encriptar discos numa VM com Linux utilizando a CLI do Azure](encrypt-disks.md).
