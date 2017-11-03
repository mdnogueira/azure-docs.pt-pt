---
title: "Oferta de serviços na pilha do Azure | Microsoft Docs"
description: "Como um operador da nuvem, pode oferecer serviços aos seus utilizadores."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.openlocfilehash: 7a54771d99f2719fcc345496b152a5d3acc02121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Descrição geral da oferta de serviços na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

[Microsoft Azure pilha](azure-stack-poc.md) é uma plataforma de nuvem híbrida que lhe permite fornecer serviços a partir do seu centro de dados. Como fornecedor de serviço, pode oferecer serviços aos seus inquilinos. Dentro de uma empresa ou agência government, pode oferecer serviços no local para os seus empregados. Os serviços que possa fornecer incluem, mas não estão limitados a:

- Plataforma como dos serviços de serviço (PaaS), como serviços aplicacionais, Mobile Apps, API Apps, API funções, SQL Server, MySQL.

Pode, inclusive, combinar services para integrar e criar soluções complexas para diferentes utilizadores.

Para fornecer estes serviços para os seus utilizadores, tem de criar [planos, ofertas e quotas](azure-stack-plan-offer-quota-overview.md). Os utilizadores, em seguida, podem subscrever as ofertas para utilizar os serviços.

## <a name="plan-your-service-offers"></a>Planear as ofertas de serviço

Quando estiver a planear as ofertas, tenha em consideração os seguintes pontos:

**Ofertas de avaliação**: pode utilizar ofertas de avaliação para attract novos utilizadores, em seguida, podem atualizar para serviços adicionais. Para criar uma oferta de avaliação, criar uma pequena [planos base](azure-stack-plan-offer-quota-overview.md#base-plan) com um plano de suplemento maior opcional.

**Planeamento da capacidade**: poderá ser preocupados com os utilizadores obtenção grandes quantidades de recursos e clogging o sistema para todos os utilizadores. Para ajudar o desempenho, pode [configurar os esquemas com quotas](azure-stack-plan-offer-quota-overview.md#plans) para utilização de extremidade.

**Delegado fornecedores**: conceder a outros utilizadores a capacidade de criar ofertas no seu ambiente. Por exemplo, se for um fornecedor de serviços, pode [delegar](azure-stack-delegated-provider.md) esta capacidade dos revendedores. Em alternativa, se a uma organização, pode delegar a outras divisões/subsidiárias.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre ofertas, esquemas, quotas e subscrições](azure-stack-plan-offer-quota-overview.md)

