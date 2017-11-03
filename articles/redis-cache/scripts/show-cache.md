---
title: Exemplo de Script CLI do Azure - Obtenha detalhes de uma Cache de Redis do Azure | Microsoft Docs
description: Exemplo de Script CLI do Azure - Obtenha detalhes de uma Cache de Redis do Azure
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.openlocfilehash: 0927508117bda67b62a188f89a2d2e151fe02a5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-details-of-an-azure-redis-cache"></a>Obter os detalhes de uma Cache de Redis do Azure

Neste cenário, irá aprender a obter os detalhes de uma instância da Cache de Redis do Azure, incluindo o respetivo estado de aprovisionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para obter os detalhes de uma instância da Cache de Redis do Azure. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Mostrar de redis AZ](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Obter os detalhes de uma instância da Cache de Redis do Azure. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI de Cache de Redis do Azure adicionais podem ser encontrados no [documentação de Cache de Redis do Azure](../cli-samples.md).