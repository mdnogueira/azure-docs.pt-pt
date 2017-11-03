---
title: "Script CLI do Azure de exemplo - criar uma definição de aplicações geridas | Microsoft Docs"
description: "Script CLI do Azure de exemplo - criar uma definição de aplicações geridas"
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
ms.openlocfilehash: 6cfc51e5787ad6ab100638d0965b69232cda070a
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Criar uma definição de aplicações geridas com a CLI do Azure

Este script publica uma definição de aplicação gerida para um catálogo de serviço. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza o seguinte comando para criar a definição de aplicações geridas. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [criar a definição de managedapp AZ](https://docs.microsoft.com/cli/azure/managedapp/definition#az_managedapp_definition_create) | Crie uma definição de aplicações geridas. Forneça o pacote que contém os ficheiros necessários. |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](../overview.md).
* Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).
