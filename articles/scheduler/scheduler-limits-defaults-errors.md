---
title: "Limites de programador e as predefinições"
description: "Limites de programador e as predefinições"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-limits-and-defaults"></a>Limites de programador e as predefinições
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Quotas de programador, limites, predefinições e limitações
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>O cabeçalho x-ms-request-id
Todos os pedidos efetuados contra o serviço de agendador devolve um cabeçalho de resposta denominado**x-ms-request-id**. Este cabeçalho contém um valor opaco que identifica exclusivamente o pedido.

Se um pedido de forma consistente está a falhar e ter verificado que o pedido é corretamente formulated, pode utilizar este valor para reportar o erro para a Microsoft. No relatório, incluem o valor x-ms-request-id, o tempo aproximado que o pedido foi efetuado, o identificador de subscrição, coleção de tarefas, e/ou tarefa e o tipo de operação de tentativa do pedido.

## <a name="see-also"></a>Veja Também
 [O que é o Scheduler?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Scheduler](scheduler-plans-billing.md)

 [Referência da API REST do Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)

 [Elevada disponibilidade e fiabilidade do Azure Scheduler](scheduler-high-availability-reliability.md)

 [Autenticação de saída do Azure Scheduler](scheduler-outbound-authentication.md)

