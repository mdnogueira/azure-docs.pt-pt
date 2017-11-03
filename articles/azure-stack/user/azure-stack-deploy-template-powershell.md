---
title: Implementar modelos com o PowerShell na pilha do Azure | Microsoft Docs
description: "Saiba como implementar uma máquina virtual utilizando um modelo do Resource Manager e o PowerShell."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e4837be016b569dbd0b4bf8e071e6381b8daa85f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Implementar modelos na pilha do Azure com o PowerShell

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Utilize o PowerShell para implementar modelos Azure Resource Manager para o Kit de desenvolvimento de pilha do Azure.  Modelos do Resource Manager implementar e aprovisionar todos os recursos para a sua aplicação numa operação única e coordenada.

## <a name="run-azurerm-powershell-cmdlets"></a>Executar os cmdlets do AzureRM PowerShell
Neste exemplo, executar um script para implementar uma máquina virtual para o Kit de desenvolvimento de pilha do Azure com um modelo do Resource Manager.  Antes de continuar, certifique-se de que tem [configurado PowerShell](azure-stack-powershell-configure-user.md)  

O VHD utilizado neste modelo de exemplo é WindowsServer-2012 R2 Datacenter.

1. Aceda a <http://aka.ms/AzureStackGitHub>, procure o **101-simples-windows-vm** modelo e guarde-o para a seguinte localização: c:\\modelos\\ azuredeploy-101-simples-windows-vm.json.
2. No PowerShell, execute o seguinte script de implementação. Substitua *username* e *palavra-passe* com o nome de utilizador e palavra-passe. Em utilizações subsequentes, aumentar o valor para o *$myNum* parâmetro para impedir a sua implementação de substituição.
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
       New-AzureRmResourceGroupDeployment `
           -Name myDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
           -NewStorageAccountName mystorage$myNum `
           -DnsNameForPublicIP mydns$myNum `
           -AdminUsername <username> `
           -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -VmName myVM$myNum `
           -WindowsOSVersion 2012-R2-Datacenter
   ```
3. Abra a pilha de Azure portal, clique em **procurar**, clique em **máquinas virtuais**e procure a sua nova máquina virtual (*myDeployment001*).


## <a name="next-steps"></a>Passos seguintes
[Implementar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)

