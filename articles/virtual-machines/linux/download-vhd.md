---
title: Transferir um VHD de Linux a partir do Azure | Microsoft Docs
description: Transferir um VHD de Linux utilizando a CLI do Azure e o portal do Azure.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: davidmu
ms.openlocfilehash: 3eb88478b43f8e3a36ae04bf3703f238e8cb1f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="download-a-linux-vhd-from-azure"></a>Transferir um VHD de Linux a partir do Azure

Neste artigo, irá aprender a transferir um [Linux de disco rígido virtual (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ficheiro a partir do Azure utilizando a CLI do Azure e o portal do Azure. 

Máquinas virtuais (VMs) em utilização do Azure [discos](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) como um local para armazenar os dados, aplicações e um sistema operativo. Todas as VMs do Azure de ter, pelo menos, dois discos – um disco de sistema operativo Windows e um disco temporário. O disco do sistema operativo for criado inicialmente a partir de uma imagem e o disco do sistema operativo e a imagem são VHDs armazenados na conta de armazenamento do Azure. Máquinas virtuais podem ter também um ou mais discos de dados, que também são armazenados como VHDs.

Se ainda não o fez, instale [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Parar a VM

Não é possível transferir um VHD a partir do Azure se está ligado a uma VM em execução. Terá de parar a VM para transferir um VHD. Se pretender utilizar um VHD como um [imagem](tutorial-custom-images.md) para criar outras VMs com novos discos, tem de retirar o aprovisionamento e generalizar o sistema operativo contido no ficheiro e pare a VM. Para utilizar o VHD como um disco para uma nova instância de uma VM existente ou o disco de dados, apenas terá de parar e Desalocação da VM.

Para utilizar o VHD como uma imagem para criar outras VMs, conclua estes passos:

1. Utilize o SSH, o nome da conta e o endereço IP público da VM para ligar à mesma e desaprovisioná-lo. O + parâmetro user também remove a última conta de utilizador aprovisionado. Se estiver baking credenciais de conta para a VM, deixe terminar este + parâmetro de utilizador. O exemplo a seguir remove a última conta de utilizador aprovisionado:

    ```bash
    ssh azureuser@40.118.249.235
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Inicie sessão na sua conta do Azure com [início de sessão az](https://docs.microsoft.com/cli/azure/#login).
3. Pare e Desalocação da VM.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalize a VM. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Para utilizar o VHD como um disco para uma nova instância de uma VM existente ou o disco de dados, conclua estes passos:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  No menu Hub, clique em **Virtual Machines**.
3.  Selecione a VM na lista.
4.  No painel para a VM, clique em **parar**.

    ![Parar VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar SAS URL

Para transferir o ficheiro VHD, terá de gerar um [assinatura de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL. Quando o URL é gerado, uma hora de expiração é atribuída para o URL.

1.  No menu do painel para a VM, clique em **discos**.
2.  Selecione o disco do sistema operativo para a VM e, em seguida, clique em **exportar**.
3.  Clique em **gerar URL**.

    ![Gerar URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Transferir o VHD

1.  Sob o URL que foi gerado, clique em transferir o ficheiro VHD.

    ![Transferir o VHD](./media/download-vhd/export-download.png)

2.  Poderá ter de clicar em **guardar** no browser para iniciar a transferência. O nome predefinido para o ficheiro VHD é *abcd*.

    ![Clique em Guardar no browser](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [carregar e criar uma VM com Linux a partir do disco personalizado com o 2.0 CLI do Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gerir discos do Azure a CLI do Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

