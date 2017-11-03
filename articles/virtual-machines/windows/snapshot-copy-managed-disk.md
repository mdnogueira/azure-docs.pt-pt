---
title: "Criar um instantâneo de um VHD no Azure | Microsoft Docs"
description: "Saiba como criar uma cópia de uma VM do Azure para utilizar como um back cópias de segurança ou de resolução de problemas."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: dba70db512d88dfc57107bade0df50d1834eb883
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>Criar um instantâneo

Tire um instantâneo de um disco de SO ou dados problemas de VHD para cópia de segurança ou para resolver problemas de VM. Um instantâneo é uma cópia completa só de leitura de um VHD. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Utilize o portal do Azure para criar um instantâneo 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. A partir do canto superior esquerdo, clique em **novo** e procure **instantâneo**.
3. No painel do instantâneo, clique em **criar**.
4. Introduza um **nome** para o instantâneo.
5. Selecione um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) ou escreva o nome para um novo. 
6. Selecione um localização do datacenter do Azure.  
7. Para **disco de origem**, selecione o disco de gerido para instantâneo.
8. Selecione o **tipo de conta** utilizar para armazenar o instantâneo. Recomendamos **Standard_LRS** , a menos que o ficheiro necessário armazenados num disco elevado desempenho.
9. Clique em **Criar**.

## <a name="use-powershell-to-take-a-snapshot"></a>Utilize o PowerShell para criar um instantâneo
Os passos seguintes mostram como obter o disco VHD para ser copiado, criar as configurações de instantâneo e tirar um instantâneo do disco utilizando o [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet. 

Certifique-se de que tem a versão mais recente do módulo do AzureRM.Compute PowerShell instalado. Execute o seguinte comando para instalá-lo.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para obter mais informações, consulte [controlo de versões do Azure PowerShell](/powershell/azure/overview).


1. Defina alguns parâmetros. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Obter o disco VHD seja copiado.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Crie as configurações de instantâneo. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Tire o instantâneo.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Se planear utilizar o instantâneo para criar um disco gerido e ligá-lo uma VM que tem de ser a execução elevada, utilize o parâmetro `-AccountType Premium_LRS` com o comando AzureRmSnapshot de novo. O parâmetro cria o instantâneo, de modo a que seja armazenada como um disco de gerido para Premium. Os discos Premium geridos são mais dispendiosos do que padrão. Por isso, não se esqueça de que precisar realmente Premium antes de poder utilizar esse parâmetro.

## <a name="next-steps"></a>Passos seguintes

Crie uma máquina virtual a partir de um instantâneo ao criar um disco gerido a partir de um instantâneo e, em seguida, anexar o disco novo gerido como disco do SO. Para obter mais informações, consulte o [criar uma VM a partir de um instantâneo](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) exemplo.
