---
title: Desligar um disco de dados de uma VM do Windows - Azure | Microsoft Docs
description: "Saiba como desligar um disco de dados de uma máquina virtual no Azure utilizando o modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: bbbd31313db44d32a829e9e4c6c9b5fd9c0e533e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desligar um disco de dados de máquina virtual do Windows
Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Remove o disco da máquina virtual, mas não o remover do armazenamento.

> [!WARNING]
> Se desanexar um disco que não é eliminado automaticamente. Se tiver subscrito para o Premium storage, continuará a implicar custos de armazenamento para o disco. Para obter mais informações, consulte [preços e faturação quando utilizar o Premium Storage](premium-storage.md#pricing-and-billing).
>
>

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.

## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal
1. No portal hub, selecione **máquinas virtuais**.
2. Selecione a máquina virtual que tenha o disco de dados que pretende desanexar e clique em **parar** ao anular atribuição de VM.
3. No painel da máquina virtual, selecione **discos**.
4. Na parte superior do **discos** painel, selecione **editar**.
5. No **discos** painel, na extremidade direita do disco de dados que pretende desanexar, clique em de ![imagem do botão de anulação de exposições](./media/detach-disk/detach.png) exposição do botão.
5. Depois do disco foi removido, clique em Guardar na parte superior do painel.
6. No painel da máquina virtual, clique em **descrição geral** e, em seguida, clique em de **iniciar** botão na parte superior do painel para reiniciar a VM.



O disco permanece no armazenamento, mas já não está ligado a uma máquina virtual.

## <a name="detach-a-data-disk-using-powershell"></a>Desligar um disco de dados com o PowerShell
Neste exemplo, o primeiro comando obtém a máquina virtual com o nome **MyVM07** no **RG11** utilizando o cmdlet Get-AzureRmVM de grupo de recursos. O comando armazena a máquina virtual no **$VirtualMachine** variável.

O segundo comando remove o disco de dados com o nome DataDisk3 da máquina virtual.

O comando final atualiza o estado da máquina virtual para concluir o processo de como remover o disco de dados.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```

Para obter mais informações, consulte [remover AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Passos seguintes
Se pretender reutilizar o disco de dados, pode apenas [anexe-o para outra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

