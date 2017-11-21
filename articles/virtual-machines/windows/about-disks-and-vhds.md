---
title: "Sobre não geridos (os blobs de páginas) e geridas armazenamento de discos para VMs do Windows do Microsoft Azure | Microsoft Docs"
description: "Saiba mais sobre as noções básicas de não geridos (os blobs de páginas) e geridos de armazenamento de discos para máquinas virtuais do Windows no Azure."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0142c64d-5e8c-4d62-aa6f-06d6261f485a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: 1a8dc028e2e872820a209bcdde5cca57853dd419
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>Sobre o armazenamento de discos para VMs do Windows Azure
Tal como qualquer outro computador, máquinas virtuais no Azure utilizar discos como um local para armazenar os dados, aplicações e um sistema operativo. Todas as máquinas virtuais do Azure de ter, pelo menos, dois discos – um disco de sistema operativo Windows e um disco temporário. O disco do sistema operativo é criado a partir de uma imagem e o disco do sistema operativo e a imagem são discos rígidos virtuais (VHDs) armazenados numa conta de armazenamento do Azure. Máquinas virtuais podem ter também um ou mais discos de dados, que também são armazenados como VHDs. 

Neste artigo, iremos falar sobre as utilizações diferentes para os discos e, em seguida, aborda os diferentes tipos de discos, pode criar e utilizar. Este artigo também está disponível para [máquinas virtuais do Linux](../linux/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Discos utilizados por VMs

Vamos ver como os discos são utilizados por VMs.

### <a name="operating-system-disk"></a>Disco do sistema operativo
Cada máquina virtual possui um disco de sistema de operativo anexado. Tem registado como uma unidade SATA e identificados como a unidade c: por predefinição. Este disco tem uma capacidade máxima de 2048 gigabytes (GB). 

### <a name="temporary-disk"></a>Disco temporário
Cada VM contém um disco temporário. O disco temporário fornece armazenamento de curta duração para aplicações e processos e destina-se para armazenar apenas os dados, tais como ficheiros de página ou a troca. Dados no disco temporário podem ser perdidos durante uma [evento de manutenção](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou quando a [voltar a implementar uma VM](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Durante um reinício padrão da VM, devem manter os dados na unidade temporária.

O disco temporário assinalada como como unidade d: por predefinição e utilizado para armazenar pagefile.sys. Para remapear este disco para uma letra de unidade diferente, consulte o artigo [alterar a letra de unidade de disco temporário Windows](change-drive-letter.md). O tamanho do disco temporário varia, com base no tamanho da máquina virtual. Para obter mais informações, consulte [máquinas virtuais de tamanhos para Windows](sizes.md).

Para obter mais informações sobre como o Azure utiliza o disco temporário, consulte [compreender unidade temporária no Microsoft Virtual Machines do Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>Disco de dados
Um disco de dados é uma imagem VHD que está ligada a uma máquina virtual para armazenar dados de aplicação ou outros dados que tem de manter. Os discos de dados estão registados como unidades de SCSI e são etiquetados com uma letra que escolher. Cada disco de dados tem uma capacidade máxima de 4095 GB. O tamanho da máquina virtual determina quantos discos de dados, pode anexar à mesma e o tipo de armazenamento pode utilizar para alojar os discos.

> [!NOTE]
> Para obter mais informações sobre as capacidades de máquinas virtuais, consulte [máquinas virtuais de tamanhos para Windows](sizes.md).
> 

Quando criar uma máquina virtual a partir de uma imagem, o Azure cria um disco de sistema operativo. Se utilizar uma imagem que inclui os discos de dados, o Azure também cria os discos de dados quando cria a máquina virtual. Caso contrário, adicione discos de dados depois de criar a máquina virtual.

Pode adicionar os discos de dados para uma máquina virtual em qualquer altura, pelo **anexar** o disco à máquina virtual. Pode utilizar uma imagem VHD que tiver carregado ou copiados para a sua conta de armazenamento, ou que o Azure cria por si. Anexar um disco de dados associa o ficheiro VHD a VM colocando uma concessão no VHD, pelo que não pode ser eliminada do armazenamento enquanto ainda está ligado.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Uma recomendação última: Utilize cortar com discos padrão não geridos 

Se utilizar discos padrão não geridos (HDD), deverá ativar a limitação. Operações de COMPACTAÇÃO elimina os blocos no disco, é-lhe faturado apenas para o armazenamento que está a utilizar, na verdade, de modo. Isto permite poupar nos custos se criar ficheiros grandes e, em seguida, elimine-os. 

Pode executar este comando para verificar a definição de COMPACTAÇÃO. Abra uma linha de comandos na sua VM do Windows e o tipo:


```
fsutil behavior query DisableDeleteNotify
```

Se o comando devolve 0, cortar está ativada corretamente. Se devolve 1, execute o seguinte comando para ativar a limitação:

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> Nota: O suporte começa com o Windows Server 2012 / Windows 8 e superior, consulte ver [nova API permite que as aplicações enviar sugestões "Compactar e Unmap" para o suporte de armazenamento](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints).
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Passos seguintes
* [Anexar um disco](attach-disk-portal.md) para adicionar armazenamento adicional para a VM.
* [Criar um instantâneo](snapshot-copy-managed-disk.md).
* [Converter discos geridos](convert-unmanaged-to-managed-disks.md).


