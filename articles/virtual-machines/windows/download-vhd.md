---
title: Transferir um VHD do Windows a partir do Azure | Microsoft Docs
description: Transferir um VHD do Windows no portal do Azure.
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
ms.openlocfilehash: d8bf89a4b7c2a158302f9ba09a182a3d8d062adc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="download-a-windows-vhd-from-azure"></a>Transferir um VHD do Windows a partir do Azure

Neste artigo, irá aprender a transferir um [Windows de disco rígido virtual (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ficheiro a partir do Azure no portal do Azure. 

Máquinas virtuais (VMs) em utilização do Azure [discos](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) como um local para armazenar os dados, aplicações e um sistema operativo. Todas as VMs do Azure de ter, pelo menos, dois discos – um disco de sistema operativo Windows e um disco temporário. O disco do sistema operativo for criado inicialmente a partir de uma imagem e o disco do sistema operativo e a imagem são VHDs armazenados na conta de armazenamento do Azure. Máquinas virtuais podem ter também um ou mais discos de dados, que também são armazenados como VHDs.

## <a name="stop-the-vm"></a>Parar a VM

Não é possível transferir um VHD a partir do Azure se está ligado a uma VM em execução. Terá de parar a VM para transferir um VHD. Se pretender utilizar um VHD como um [imagem](tutorial-custom-images.md) para criar outras VMs com novos discos, utilize [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar o sistema operativo contido no ficheiro e, em seguida, pare a VM. Para utilizar o VHD como um disco para uma nova instância de uma VM existente ou o disco de dados, apenas terá de parar e Desalocação da VM.

Para utilizar o VHD como uma imagem para criar outras VMs, conclua estes passos:

1.  Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  [Ligar à VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Na VM, abra a janela de linha de comandos como administrador.
4.  Altere o diretório para *%windir%\system32\sysprep* e execute o sysprep.exe.
5.  Na caixa de diálogo Ferramenta de preparação do sistema, selecione **introduza sistema Out-of-Box experiência (OOBE)**e certifique-se de que **Generalize** está selecionada.
6.  Nas opções de encerramento, selecione **encerramento**e, em seguida, clique em **OK**. 

Para utilizar o VHD como um disco para uma nova instância de uma VM existente ou o disco de dados, conclua estes passos:

1.  No menu do Hub no portal do Azure, clique em **máquinas virtuais**.
2.  Selecione a VM na lista.
3.  No painel para a VM, clique em **parar**.

    ![Parar VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar SAS URL

Para transferir o ficheiro VHD, terá de gerar um [assinatura de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL. Quando o URL é gerado, uma hora de expiração é atribuída para o URL.

1.  No menu do painel para a VM, clique em **discos**.
2.  Selecione o disco do sistema operativo para a VM e, em seguida, clique em **exportar**.
3.  Definir a hora de expiração do URL para *36000*.
4.  Clique em **gerar URL**.

    ![Gerar URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> A hora de expiração é aumentada da predefinição para fornecer tempo suficiente para transferir o ficheiro VHD grande para um sistema operativo Windows Server. Pode esperar um ficheiro VHD que contém o sistema operativo Windows Server demorar várias horas a transferir dependendo da sua ligação. Se estiver a transferir um VHD para um disco de dados, o tempo predefinido é suficiente. 
> 
> 

## <a name="download-vhd"></a>Transferir o VHD

1.  Sob o URL que foi gerado, clique em transferir o ficheiro VHD.

    ![Transferir o VHD](./media/download-vhd/export-download.png)

2.  Poderá ter de clicar em **guardar** no browser para iniciar a transferência. O nome predefinido para o ficheiro VHD é *abcd*.

    ![Clique em Guardar no browser](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [carregar um ficheiro VHD para o Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Criar discos geridos a partir de discos não geridos numa conta do storage](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gerir discos do Azure com o PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

