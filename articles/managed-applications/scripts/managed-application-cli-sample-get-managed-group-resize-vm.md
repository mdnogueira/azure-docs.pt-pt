---
title: Exemplo de script de CLI do Azure - obter um grupo de recursos geridos e redimensionar VMs | Microsoft Docs
description: "Exemplo de script do CLI do Azure - introdução de um grupo de recursos geridos e redimensionar VMs"
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
ms.openlocfilehash: c78d2646471e40d60972cf91cb5bbd351f71a66c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Obter recursos num grupo de recursos geridos e redimensionar VMs com a CLI do Azure

Este script obtém os recursos de um grupo de recursos geridos e redimensiona as VMs nesse grupo de recursos.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para implementar a aplicação gerida. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [lista de managedapp AZ](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Lista de aplicações geridas. Indique os valores de consulta focar-se os resultados. |
| [lista de recursos de AZ](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Lista de recursos. Forneça valores de grupo e a consulta focar-se o resultado de um recurso. |
| [redimensionamento de vm AZ](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Atualize o tamanho de uma máquina virtual. |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](../overview.md).
* Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).
