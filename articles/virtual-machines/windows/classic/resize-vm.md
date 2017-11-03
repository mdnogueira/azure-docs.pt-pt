---
title: "Redimensionar uma VM do Windows no modelo de implementação clássica - Azure | Microsoft Docs"
description: "Redimensione a máquina virtual do Windows criada no modelo de implementação clássica, com o Azure Powershell."
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 4277bc8394c7ba140291e9dc776162e87deab96b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Redimensionar uma VM do Windows criados no modelo de implementação clássica
Este artigo mostra como redimensionar uma VM do Windows, criado no modelo de implementação clássica com o Azure Powershell.

Ao considerar a capacidade de redimensionar uma VM, existem dois conceitos que controlam o intervalo de tamanhos disponíveis para redimensionar a máquina virtual. O primeiro conceito é a região na qual a VM está implementada. A lista de tamanhos de VM disponíveis na região está sob o separador de serviços da página web regiões do Azure. O segundo conceito é o hardware físico aloja atualmente a VM. Os servidores físicos que aloja as VMs estão agrupados em clusters de hardware físico comum. O método de alteração um tamanho VM difere consoante se o novo tamanho VM pretendido é suportado pelo cluster hardware atualmente a alojar a VM.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Também pode [redimensionar uma VM criada no modelo de implementação Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>Adicionar a sua conta
Tem de configurar o Azure PowerShell para trabalhar com recursos do Azure clássicos. Siga os passos abaixo para configurar o Azure PowerShell para gerir os recursos clássicos.

1. Na linha de comandos do PowerShell, escreva `Add-AzureAccount` e clique em **Enter**. 
2. Escreva o endereço de correio eletrónico associado à sua subscrição do Azure e clique em **continuar**. 
3. Escreva a palavra-passe para a sua conta. 
4. Clique em **sessão**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Redimensionar no mesmo cluster de hardware
Redimensionar uma VM para um tamanho disponível no cluster de hardware que aloja a VM, execute os seguintes passos.

1. Execute o seguinte comando do PowerShell para listar os tamanhos VM disponíveis no cluster de hardware que aloja o serviço de nuvem que contém a VM.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. Execute os seguintes comandos para redimensionar a VM.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Redimensionar um novo cluster de hardware
Redimensionar uma VM para um tamanho não está disponível no cluster de hardware que aloja a VM, a nuvem devem ser recriadas serviço e todas as VMs no serviço em nuvem. Cada serviço em nuvem está alojado num cluster único de hardware para que todas as VMs no serviço em nuvem tem de ser um tamanho que é suportado num cluster de hardware. Os passos seguintes descrevem como redimensionar uma VM e, em seguida, recriar o serviço em nuvem.

1. Execute o seguinte comando do PowerShell para listar os tamanhos VM disponíveis na região. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Tome nota de todas as definições de configuração para cada VM de serviço em nuvem que contém a VM ser redimensionada. 
3. Elimine todas as VMs no serviço em nuvem, selecionando a opção para manter os discos para cada VM.
4. Recrie a VM ser redimensionada com o tamanho VM pretendido.
5. Recrie todas as outras VMs que foram no serviço de nuvem utilizando um tamanho VM disponível no cluster de hardware agora a alojar o serviço em nuvem.

Um script de exemplo para eliminar e recriar um serviço em nuvem utilizando um tamanho VM nova que pode ser encontrado [aqui](https://github.com/Azure/azure-vm-scripts). 

## <a name="next-steps"></a>Passos seguintes
* [Redimensionar uma VM criada no modelo de implementação Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

