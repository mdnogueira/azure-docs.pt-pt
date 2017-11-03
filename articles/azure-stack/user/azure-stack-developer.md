---
title: "Desenvolver aplicações para a pilha do Azure | Microsoft Docs"
description: "Saiba considerações de desenvolvimento em aplicações de fazer o protótipo na pilha do Azure"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 062c17173f87eec8e0eaa3f74323cbf8a8f48571
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-stack"></a>Desenvolver para o Azure Stack
Pode começar hoje, a desenvolver aplicações, mesmo se não tiver acesso a um ambiente de pilha do Azure. Porque a pilha do Azure fornece serviços do Microsoft Azure que executam no seu centro de dados, pode utilizar as ferramentas e processos semelhantes para desenvolver em relação a pilha do Azure, tal como faria com o Azure.  Com um bit de preparação e de orientação dos tópicos seguintes, pode utilizar o Azure para emular um ambiente de pilha do Azure:

* No Azure, pode criar os modelos Azure Resource Manager que também são implementáveis com pilha do Azure.  Consulte [considerações sobre os modelos](azure-stack-develop-templates.md) para obter orientações sobre os modelos para garantir a portabilidade a desenvolver.
* Não há um delta na disponibilidade do serviço e o controlo de versões do serviço entre o Azure e pilha do Azure. Pode utilizar o [módulo de política do Azure pilha](azure-stack-policy-module.md) para restringir os tipos de recursos e a disponibilidade do serviço do Azure para o que está disponível na pilha do Azure. Restrições serviços disponíveis irão ajudar a sua aplicação dependem de serviços disponíveis para a pilha do Azure.
* O [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates) são exemplos de cenário comum de como desenvolver os modelos, para que podem ser implementados do Azure e pilha do Azure.


