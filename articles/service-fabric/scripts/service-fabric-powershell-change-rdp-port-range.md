---
title: Exemplo de Script do PowerShell do Azure - alterar o intervalo de portas RDP | Microsoft Docs
description: Exemplo de Script do PowerShell do Azure - altera o intervalo de portas RDP de um cluster implementado.
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 9089d071c69830323a94140a5ebb4e9e865499c6
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-rdp-port-range-values"></a>Atualize os valores de intervalo de portas RDP

Este script de exemplo altera os valores de intervalo de porta RDP no nó de cluster VMs após o cluster ter sido implementado.  O Azure PowerShell é utilizado para que as VMs subjacentes não ciclo.  O script obtém o `Microsoft.Network/loadBalancers` recursos no grupo de recursos e atualizações do cluster a `inboundNatPools.frontendPortRangeStart` e `inboundNatPools.frontendPortRangeEnd` valores. Personalize os parâmetros conforme necessário.

Se necessário, instale o Azure PowerShell com a instrução foi encontrada no [Guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtém o `Microsoft.Network/loadBalancers` recursos. |
|[Conjunto AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|Atualizações a `Microsoft.Network/loadBalancers` recursos.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de Azure Powershell adicionais para o Azure Service Fabric podem ser encontradas no [exemplos do PowerShell do Azure](../service-fabric-powershell-samples.md).
