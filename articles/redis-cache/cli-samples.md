---
title: Exemplos de cache CLI de Redis do Azure | Microsoft Docs
description: Exemplos da CLI do Azure para a Cache de Redis do Azure.
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: sdanie
ms.openlocfilehash: a0f3b294f2a655a5ff891d4fd1be9137080349a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Exemplos da CLI do Azure para o Azure Redis Cache

A tabela seguinte inclui ligações para bash scripts compiladas com a CLI do Azure.

| | |
|---|---|
|**Criar cache**||
| [Criar uma cache](./scripts/create-cache.md) | Cria um grupo de recursos e uma camada básica a Cache de Redis do Azure. |
| [Criar uma cache de premium com clustering](./scripts/create-premium-cache-cluster.md) | Cria um grupo de recursos e uma cache de escalão premium com clustering ativada.|
| [Obter os detalhes da cache](./scripts/show-cache.md) | Obtém os detalhes de uma instância da Cache de Redis do Azure, incluindo o estado de aprovisionamento. |
| [Obter o nome de anfitrião, portas e as chaves](./scripts/cache-keys-ports.md) | Obtém o nome de anfitrião, portas e as chaves de uma instância da Cache de Redis do Azure. |
|**Aplicação Web, cache**||
| [Ligar uma aplicação web a uma cache de redis](./../app-service/scripts/app-service-cli-app-service-redis.md) | Cria uma aplicação web do Azure e uma cache de redis, em seguida, adiciona os detalhes de ligação redis para as definições da aplicação. |
|**Eliminar cache**||
| [Eliminar uma cache](./scripts/delete-cache.md) | Elimina uma instância da Cache de Redis do Azure  |
| | |

Para obter mais informações sobre o Azure CLI 2.0, consulte [instalar o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) e [introdução ao Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).