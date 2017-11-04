---
title: Anexar um disco de dados para uma VM com Linux | Microsoft Docs
description: Utilize o portal para expor o disco de dados nova ou existente para uma VM com Linux.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 1a7c98207a5f19d514a0cd05b66898e7fb725944
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utilizar o portal para anexar um disco de dados para uma VM com Linux 
Este artigo mostra como anexar discos novos e existentes para uma máquina virtual do Linux através do portal do Azure. Também pode [anexar um disco de dados para uma VM do Windows no portal do Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de anexar discos à sua VM, reveja estas sugestões:

* O tamanho da máquina virtual controla quantos discos de dados, pode anexar. Para obter mais informações, consulte [tamanhos das virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para utilizar o armazenamento Premium, precisa de uma máquina virtual-série DS ou série GS. Pode utilizar discos Standard e Premium com estas máquinas virtuais. Armazenamento Premium está disponível em determinados regiões. Para obter mais informações, consulte [Premium Storage: armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual de Azure](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Os discos anexados a máquinas virtuais estão, na verdade, os ficheiros. vhd armazenados no Azure. Para obter mais informações, consulte [sobre discos e VHD para as máquinas virtuais](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu da esquerda, clique em **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
4. Para o Virtual máquinas página, no **Essentials**, clique em **discos**.
   
    ![Abra as definições do disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Anexe um novo disco

1. No **discos** painel, clique em **+ adicionar disco de dados**.
2. Clique no menu pendente **nome** e selecione **criar disco**:

    ![Criar Azure disco gerido](./media/attach-disk-portal/create-new-md.png)

3. Introduza um nome para o disco gerido. Reveja as definições predefinidas, atualizar conforme necessário e, em seguida, clique em **criar**.
   
   ![Reveja as definições do disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Clique em **guardar** para criar o disco gerido e atualizar a configuração de VM:

   ![Guardar o novo disco gerida do Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Depois do Azure cria o disco e anexa-lo para a máquina virtual, o novo disco está listado nas definições de disco da máquina virtual em **discos de dados**. Como discos geridos são um recurso de nível superior, o disco é apresentado na raiz do grupo de recursos:

   ![Disco gerido do Azure no grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. No **discos** painel, clique em **+ adicionar disco de dados**.
2. Clique no menu pendente **nome** para ver uma lista de discos geridos existentes e acessíveis à sua subscrição do Azure. Selecione o disco de gerido para anexar:

   ![Anexar o disco de gerida do Azure existente](./media/attach-disk-portal/select-existing-md.png)

3. Clique em **guardar** anexar o disco gerido existente e atualize a configuração de VM:
   
   ![Guardar as atualizações de disco gerida do Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Depois de Azure anexa o disco à máquina virtual, este está listado nas definições de disco da máquina virtual em **discos de dados**.



## <a name="next-steps"></a>Passos seguintes
Também pode [anexar um disco de dados](add-disk.md) utilizando a CLI do Azure.
