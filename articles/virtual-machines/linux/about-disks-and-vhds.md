---
title: "Sobre não geridos (os blobs de páginas) e geridas armazenamento de discos para as VMs de Linux do Microsoft Azure | Microsoft Docs"
description: "Saiba mais sobre as noções básicas de não geridos (os blobs de páginas) e geridos de armazenamento de discos para computadores virtuais Linux no Azure."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7be8dd52-98f7-4187-9b78-55197915bc9b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: fee78c87c1d73f2a0816d6e52ad48a93eef8dfc3
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>Sobre o armazenamento de discos para as VMs Linux do Azure
Tal como qualquer outro computador, máquinas virtuais no Azure utilizar discos como um local para armazenar os dados, aplicações e um sistema operativo. Todas as máquinas virtuais do Azure de ter, pelo menos, dois discos – um disco de sistema operativo Linux e um disco temporário. O disco do sistema operativo é criado a partir de uma imagem e o disco do sistema operativo e a imagem são, na verdade, discos rígidos virtuais (VHDs) armazenados numa conta de armazenamento do Azure. Máquinas virtuais podem ter também um ou mais discos de dados, que também são armazenados como VHDs. 

Neste artigo, iremos falar sobre as utilizações diferentes para os discos e, em seguida, aborda os diferentes tipos de discos, pode criar e utilizar. Este artigo também está disponível para [máquinas virtuais Windows](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Discos utilizados por VMs

Vamos ver como os discos são utilizados por VMs.

## <a name="operating-system-disk"></a>Disco do sistema operativo
Cada máquina virtual possui um disco de sistema de operativo anexado. Este é registado como uma unidade SATA e assinalada como /dev/sda por predefinição. Este disco tem uma capacidade máxima de 2048 gigabytes (GB). 

## <a name="temporary-disk"></a>Disco temporário
Cada VM contém um disco temporário. O disco temporário fornece armazenamento de curta duração para aplicações e processos e destina-se para armazenar apenas os dados, tais como ficheiros de página ou a troca. Dados no disco temporário podem ser perdidos durante uma [evento de manutenção](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou quando a [voltar a implementar uma VM](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Durante um reinício padrão da VM, devem manter os dados na unidade temporária.

Em máquinas virtuais do Linux, o disco é normalmente **/dev/sdb** é formatado e montado para **/mnt** pelo agente Linux do Azure. O tamanho do disco temporário varia, com base no tamanho da máquina virtual. Para obter mais informações, consulte [tamanhos de máquinas virtuais do Linux](../windows/sizes.md).

Para obter mais informações sobre como o Azure utiliza o disco temporário, consulte [compreender unidade temporária no Microsoft Virtual Machines do Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco de dados
Um disco de dados é uma imagem VHD que está ligada a uma máquina virtual para armazenar dados de aplicação ou outros dados que tem de manter. Os discos de dados estão registados como unidades de SCSI e são etiquetados com uma letra que escolher. Cada disco de dados tem uma capacidade máxima de 4095 GB. O tamanho da máquina virtual determina quantos discos de dados, pode anexar à mesma e o tipo de armazenamento pode utilizar para alojar os discos.

> [!NOTE]
> Para obter mais detalhes sobre as capacidades de máquinas virtuais, consulte [tamanhos de máquinas virtuais do Linux](../windows/sizes.md).
> 

Quando criar uma máquina virtual a partir de uma imagem, o Azure cria um disco de sistema operativo. Se utilizar uma imagem que inclui os discos de dados, o Azure também cria os discos de dados quando cria a máquina virtual. Caso contrário, adicione discos de dados depois de criar a máquina virtual.

Pode adicionar os discos de dados para uma máquina virtual em qualquer altura, pelo **anexar** o disco à máquina virtual. Pode utilizar uma imagem VHD que tiver carregado ou copiados para a sua conta de armazenamento, ou que o Azure cria por si. Anexar um disco de dados associa o ficheiro VHD a VM, colocando uma concessão no VHD, pelo que não pode ser eliminada do armazenamento enquanto ainda está ligado.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passos seguintes
* [Anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para adicionar armazenamento adicional para a VM.
* [Criar um instantâneo](snapshot-copy-managed-disk.md).
* [Converter discos geridos](convert-unmanaged-to-managed-disks.md).

