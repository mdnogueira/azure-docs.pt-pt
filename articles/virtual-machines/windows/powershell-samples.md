---
title: "Exemplos do PowerShell de Máquina Virtual do Azure | Microsoft Docs"
description: "Exemplos do PowerShell de Máquina Virtual do Azure"
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
ms.date: 09/20/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9d0db50e5dcf6af97e15019c11fdeafb209de639
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="azure-virtual-machine-powershell-samples"></a>Exemplos do PowerShell de Máquina Virtual do Azure

A tabela seguinte inclui ligações para exemplos de scripts do PowerShell que criar e gerir máquinas virtuais do Windows.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um grupo de recursos, a máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria várias máquinas virtuais da elevada disponibilidade e a configuração de balanceamento de carga.|
| [Criar uma VM e executar o script de configuração](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Script de personalizada do Azure para instalar o IIS. |
| [Criar uma VM e execute a configuração DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão do Azure pretendido Estado Configuration (DSC) para instalar o IIS. |
| [Carregar um VHD e criar VMs](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Carrega um ficheiro VHD local para o Azure, cria e imagem do VHD e, em seguida, cria uma VM a partir dessa imagem. |
| [Criar uma VM a partir de um disco de SO gerido](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual, anexar um disco existente geridos como disco do SO. |
| [Criar uma VM a partir de um instantâneo](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo ao primeiro criar um disco gerido de instantâneo e, em seguida, anexar o disco novo gerido como disco do SO. |
|**Criar máquinas virtuais utilizando o novo AzVM**||
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-powershell-sample-create-vm-auto.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um grupo de recursos, a máquina virtual e todos os recursos relacionados.|
| [Criar uma VM e executar o script de configuração](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis-auto.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Script de personalizada do Azure para instalar o IIS. |
| [Criar uma VM e execute a configuração DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc-auto.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão do Azure pretendido Estado Configuration (DSC) para instalar o IIS. |
|**Gerir o armazenamento**||
| [Criar disco gerido a partir de um VHD na subscrição idêntica ou diferente](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado, como um disco do SO ou a partir de um VHD como disco de dados na subscrição idêntica ou diferente de dados.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um disco gerido a partir de um instantâneo. |
| [Disco gerido de cópia para subscrição idêntica ou diferente](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Cópias geridos disco à subscrição idêntica ou diferente, mas na mesma região que o ascendente gerido disco. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento numa região diferente. |
| [Criar um instantâneo de um VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria instantâneos de um VHD para criar vários discos geridos idênticos do instantâneo num curto período de tempo.  |
| [Instantâneo de cópia para subscrição idêntica ou diferente](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Instantâneo de cópias a subscrição idêntica ou diferente, mas na mesma região que o instantâneo de principal. |
|**Proteger máquinas virtuais**||
| [Encriptar uma VM e discos de dados](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Cria um cofre de chaves do Azure, uma chave de encriptação e um principal de serviço, em seguida, encripta uma VM. |
|**Monitorizar máquinas virtuais**||
| [Monitorizar uma VM com o Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual, instala o agente do Operations Management Suite e inscreve a VM com uma área de trabalho do OMS.  |
| | |

