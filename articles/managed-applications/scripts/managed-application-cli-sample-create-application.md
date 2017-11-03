---
title: "Script CLI do Azure de exemplo - implementar uma aplicação gerida | Microsoft Docs"
description: "Script CLI do Azure de exemplo - implementar uma definição de aplicações geridas"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 62d0247df3b3d9f242877e4ea27ccc871cf797c0
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Implementar uma aplicação gerida para o catálogo de serviço com a CLI do Azure

Este script implementa uma definição de aplicação gerida a partir do catálogo de serviço. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza o seguinte comando para implementar a aplicação gerida. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar AZ managedapp](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_create) | Crie uma aplicação gerida. Forneça o ID de definição e parâmetros para o modelo. |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](../overview.md).
* Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).
