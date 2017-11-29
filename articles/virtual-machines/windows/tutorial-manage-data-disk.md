---
title: Gerir discos do Azure com o Azure PowerShell | Microsoft Docs
description: Tutorial - Gerir discos do Azure com o Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 956f44068db8fe9c8c7a839a0ce80c19e2b2f11c
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="manage-azure-disks-with-powershell"></a>Gerir discos do Azure com o PowerShell

Máquinas virtuais do Azure utilizar discos para armazenar as VMs do sistema operativo, aplicações e dados. Quando cria uma VM é importante escolher um tamanho de disco e a configuração adequada para a carga de trabalho esperada. Este tutorial abrange a implementação e gestão de discos VM. Pode obter informações sobre:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Standard e discos Premium
> * Desempenho de disco
> * Anexar e preparar os discos de dados

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o módulo do Azure PowerShell versão 3.6 ou posterior. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure. 

## <a name="default-azure-disks"></a>Predefinição discos do Azure

Quando é criada uma máquina virtual do Azure, dois discos são automaticamente anexados à máquina virtual. 

**Disco do sistema operativo** -discos de sistema de operativo podem ser dimensionados de forma até 4 terabyte e aloja o sistema de operativo VMs.  O disco do SO está atribuído uma letra de unidade de *c:* por predefinição. O disco de configuração do disco de SO a colocação em cache está otimizado para desempenho do SO. O disco de SO **não devem** alojar aplicações ou dados. Para aplicações e dados, utilize um disco de dados, que está detalhado neste artigo.

**Disco temporário** -temporários discos utilizam uma unidade de estado sólido que está localizada no mesmo anfitrião da VM do Azure. Discos temporários são altamente performant e podem ser utilizados para operações, tais como o processamento de dados temporária. No entanto, se a VM for movida para um novo anfitrião, todos os dados armazenados num disco temporário são removidos. O tamanho do disco temporário é determinado pelo tamanho da VM. Discos temporários estão atribuídos uma letra de unidade de *d:* por predefinição.

### <a name="temporary-disk-sizes"></a>Tamanhos de disco temporário

| Tipo | Tamanho da VM | Tamanho máximo de disco temporário (GB) |
|----|----|----|
| [Fins gerais](sizes-general.md) | A e a série de D | 800 |
| [Com otimização de computação](sizes-compute.md) | Série F | 800 |
| [Com otimização de memória](../virtual-machines-windows-sizes-memory.md) | Série de D e G | 6144 |
| [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md) | Série de L | 5630 |
| [GPU](sizes-gpu.md) | Série N | 1440 |
| [Elevado desempenho](sizes-hpc.md) | A e a série de H | 2000 |

## <a name="azure-data-disks"></a>Discos de dados do Azure

Discos de dados adicionais podem ser adicionados para instalar aplicações e armazenar dados. Os discos de dados devem ser utilizados em qualquer situação em que o armazenamento de dados durável e reativa for pretendido. Cada disco de dados tem uma capacidade máxima de 1 terabyte. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM. Para cada VM vCPU, podem ser anexados a discos de dados de dois. 

### <a name="max-data-disks-per-vm"></a>Discos de dados de máx. por VM

| Tipo | Tamanho da VM | Discos de dados de máx. por VM |
|----|----|----|
| [Fins gerais](sizes-general.md) | A e a série de D | 32 |
| [Com otimização de computação](sizes-compute.md) | Série F | 32 |
| [Com otimização de memória](../virtual-machines-windows-sizes-memory.md) | Série de D e G | 64 |
| [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md) | Série de L | 64 |
| [GPU](sizes-gpu.md) | Série N | 48 |
| [Elevado desempenho](sizes-hpc.md) | A e a série de H | 32 |

## <a name="vm-disk-types"></a>Tipos de disco da VM

O Azure oferece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard

O Armazenamento Standard está protegido por HDDs e fornece armazenamento económico, mantendo o desempenho. Discos padrão são ideais para um dev económica e teste de carga de trabalho.

### <a name="premium-disk"></a>Disco Premium

Os discos Premium são apoiados por disco de elevado desempenho, baixa latência baseadas em SSD. Perfeita para as VMs com a carga de trabalho de produção. Armazenamento Premium suporta-série DS, série DSv2-série, série GS e série FS VMs. Os discos Premium são fornecidos em três tipos (P10 P20, P30, P40, P50), o tamanho do disco determina o tipo de disco. Quando selecionar, um tamanho de disco, o valor é arredondado para o tipo de próximo. Por exemplo, se o tamanho é inferior a 128 GB o tipo de disco será P10, entre 129 e 512 P20, 512 P30, P40 para 2TB e P50 4TB. 

### <a name="premium-disk-performance"></a>Desempenho do disco Premium

|Tipo de disco de armazenamento Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Tamanho do disco (arredondar) | 128 GB | 512 GB | 1.024 GB (1 TB) |
| IOPs por disco | 500 | 2,300 | 5,000 |
Débito por disco | 100 MB/s | 150 MB/s | 200 MB/s |

Enquanto a tabela acima identifica IOPS máximo por disco, um nível mais elevado de desempenho pode ser alcançado ao striping vários discos de dados. Por exemplo, discos de 64 dados podem ser anexados a Standard_GS5 VM. Se cada um destes discos é dimensionada como um P30, pode ser conseguido 80.000 IOPS máximo. Para obter informações detalhadas sobre o IOPS máximo por VM, consulte [VM tipos e tamanhos](./sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se for necessário, isto [script de exemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma por si. Quando trabalhar com o tutorial, substitua o grupo de recursos e VM nomes sempre que necessário.

Criar a configuração inicial com [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). O exemplo a seguir configura um disco que é 128 gigabytes de tamanho.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig -Location EastUS -CreateOption Empty -DiskSizeGB 128
```

Criar o disco de dados com o [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) comando.

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myDataDisk -Disk $diskConfig
```

Coloque a máquina virtual que pretende adicionar o disco de dados com o [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) comando.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Adicionar o disco de dados para a configuração de máquina virtual com o [adicionar AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk) comando.

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk -VM $vm -Name myDataDisk -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1
```

Atualizar a máquina virtual com o [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk) comando.

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="prepare-data-disks"></a>Preparar os discos de dados

Depois de um disco tem foi anexado à máquina virtual, o sistema operativo tem de ser configurado para utilizar o disco. O exemplo seguinte mostra como configurar manualmente o primeiro disco adicionado à VM. Este processo também pode ser automatizado, utilizando o [extensão de script personalizado](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuração manual

Crie uma ligação de RDP com a máquina virtual. Abra o PowerShell e execute este script.

```azurepowershell-interactive
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre os tópicos de discos VM, tais como:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Standard e discos Premium
> * Desempenho de disco
> * Anexar e preparar os discos de dados

Avançar para o próximo tutorial para saber mais sobre automatizar a configuração de VM.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
