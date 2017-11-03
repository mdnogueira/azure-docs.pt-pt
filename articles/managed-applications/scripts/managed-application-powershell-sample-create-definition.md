---
title: "Script do PowerShell do Azure de exemplo - criar uma definição de aplicações geridas | Microsoft Docs"
description: "Script do PowerShell do Azure de exemplo - criar uma definição de aplicações geridas"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: ee721b7c68924f5e05fbdc4bc4b919837d4cc05b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Criar uma definição de aplicações geridas com o PowerShell

Este script publica uma definição de aplicação gerida para um catálogo de serviço.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza o seguinte comando para criar a definição de aplicações geridas. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo AzureRmManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplicationdefinition) | Crie uma definição de aplicações geridas. Forneça o pacote que contém os ficheiros necessários. |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](../overview.md).
* Para obter mais informações sobre o PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
