---
title: Exemplo de script de PowerShell do Azure - obter um grupo de recursos geridos e redimensionar VMs | Microsoft Docs
description: "Exemplo de script do PowerShell do Azure - introdução de um grupo de recursos geridos e redimensionar VMs"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: poweshell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f549f26cb3f9fdb2d805d2efb2c0e1706abe3edb
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Obter recursos num grupo de recursos geridos e redimensionar VMs com o PowerShell

Este script obtém os recursos de um grupo de recursos geridos e redimensiona as VMs nesse grupo de recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para implementar a aplicação gerida. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermmanagedapplication) | Lista de aplicações geridas. Forneça o nome do grupo de recursos focar-se os resultados. |
| [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresource) | Lista de recursos. Forneça um grupo de recursos e o tipo de recurso focar-se o resultado. |
| [Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) | Atualize o tamanho de uma máquina virtual. |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](../overview.md).
* Para obter mais informações sobre o PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
