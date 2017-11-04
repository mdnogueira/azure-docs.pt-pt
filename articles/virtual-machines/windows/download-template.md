---
title: Transferir o modelo para uma VM do Azure | Microsoft Docs
description: "Transferir o templatefor uma VM para o ajudar a automatizar implementações no modelo de implementação Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 9e4c0c3cf0e233447369a24b1d5fe27495abd1cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="download-the-template-for-a-vm"></a>Transferir o modelo para uma VM
Quando cria uma VM no Azure utilizando o portal ou PowerShell, um modelo do Resource Manager é criado automaticamente para si. Pode utilizar este modelo rapidamente duplicar uma implementação. O modelo contém informações sobre todos os recursos num grupo de recursos. Para uma máquina virtual, isto significa que o modelo contém tudo o que é criado para suportar a VM nesse grupo de recursos, incluindo os recursos de rede.

## <a name="download-the-template-using-the-portal"></a>Transferir o modelo utilizando o portal
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Um hub menu, selecione **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
4. Selecione **scripts de automatização**.
5. Selecione **transferir** e guarde o ficheiro. zip no computador local.
6. Abra o ficheiro. zip e extrair os ficheiros para uma pasta. O ficheiro. zip irá conter:
   
   * Deploy.ps1
   * Deploy.SH 
   * deployer.RB
   * DeploymentHelper.cs
   * Parameters. JSON
   * Template

O ficheiro Template é o modelo.

## <a name="download-the-template-using-powershell"></a>Transferir o modelo com o PowerShell
Também pode transferir o modelo. JSON ficheiro utilizando o [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) cmdlet. Pode utilizar o `-path` parâmetro para fornecer o nome e caminho do ficheiro. JSON. Este exemplo mostra como transferir o modelo para o grupo de recursos com o nome **myResourceGroup** para o **C:\users\public\downloads** pasta no computador local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a implementação de recursos através de modelos, consulte [instruções do modelo do Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

