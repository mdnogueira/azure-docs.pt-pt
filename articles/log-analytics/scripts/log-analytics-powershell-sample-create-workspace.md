---
title: "Script do PowerShell do Azure de exemplo - criar uma área de trabalho do Log Analytics | Microsoft Docs"
description: "Script do PowerShell do Azure de exemplo - criar uma área de trabalho de análise de registos a"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
tags: 
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 30d036ae56acc3a798d2776f292243f65cbea43d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Criar uma área de trabalho de análise de registos com o PowerShell

Este script permite-lhe em funcionamento rapidamente com uma área de trabalho do Log Analytics do Azure, que é necessária se pretender começar a recolher, analisar e ação de colocar em dados.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar uma nova área de trabalho de análise de registos na sua subscrição. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Obtém informações sobre uma área de trabalho existente. |
| [Novo AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | Cria uma área de trabalho na localização e grupo de recursos especificado. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

