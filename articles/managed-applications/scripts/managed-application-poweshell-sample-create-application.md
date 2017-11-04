---
title: "Script do PowerShell do Azure de exemplo - implementar uma aplicação gerida | Microsoft Docs"
description: "Script do PowerShell do Azure de exemplo - implementar uma definição de aplicações geridas"
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
ms.openlocfilehash: 2429d561beffed5bc171b9dbc2c2c9c88eba3313
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Implementar uma aplicação gerida para um catálogo de serviço com o PowerShell

Este script implementa uma definição de aplicação gerida a partir do catálogo de serviço.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza o seguinte comando para implementar a aplicação gerida. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplication) | Crie uma aplicação gerida. Forneça o ID de definição e parâmetros para o modelo. |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](../overview.md).
* Para obter mais informações sobre o PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
