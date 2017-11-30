---
title: Script do PowerShell do Azure de exemplo - atualizar o nome de utilizador do RDP e a palavra-passe | Microsoft Docs
description: "Script do PowerShell do Azure de exemplo - atualizar o nome de utilizador do RDP e a palavra-passe para todos os nós de cluster do Service Fabric de um tipo de nó específico."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/17/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b97cee11c9a85cbd60a05bdbdcd010a0f0a106f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Atualizar o nome de utilizador de administrador e a palavra-passe das VMs num cluster

Cada tipo de nó num cluster de Service Fabric é um conjunto de dimensionamento de máquina virtual. Este script de exemplo atualiza o nome de utilizador de administrador e a palavra-passe para as máquinas virtuais de cluster de um tipo de nó específico.  Adicione a extensão de VMAccessAgent ao conjunto de dimensionamento, porque a palavra-passe de administrador não é que uma escala modificável define a propriedade.  As alterações de nome de utilizador e palavra-passe se aplicam a todos os nós no conjunto de dimensionamento. Personalize os parâmetros conforme necessário.

Se necessário, instale o Azure PowerShell com a instrução foi encontrada no [Guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos: cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Obtém as propriedades de um tipo de nó de cluster (um conjunto de dimensionamento da máquina virtual).   |
| [AzureRmVmssExtension adicionar](/powershell/module/azurerm.compute/add-azurermvmssextension)| Adiciona uma extensão para o conjunto de dimensionamento de máquina virtual.|
| [Atualização AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|Atualiza o estado de um dimensionamento da máquina virtual definido para o estado de um objeto VMSS local.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de Azure Powershell adicionais para o Azure Service Fabric podem ser encontradas no [exemplos do PowerShell do Azure](../service-fabric-powershell-samples.md).
