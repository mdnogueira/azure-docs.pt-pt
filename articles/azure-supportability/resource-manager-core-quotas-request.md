---
title: "Pedidos de aumentar a quota de núcleos de Gestor de recursos do Azure | Microsoft Docs"
description: "Pedidos de aumentar a quota de núcleos de Gestor de recursos do Azure"
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cb6c5b3e86f126d4110d1cd29d8c9891e356e414
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resource-manager-core-quota-increase-requests"></a>Pedidos de aumentar a quota de núcleos de Gestor de recursos

Quotas do Gestor de recursos principais são impostas o nível de região e o nível de família do SKU.
Obter mais informações sobre como as quotas são aplicadas no [subscrição do Azure e limites de serviço](http://aka.ms/quotalimits) página.
Para obter mais informações sobre famílias de SKU, pode comparar custo e desempenho no [preços das Virtual Machines](http://aka.ms/pricingcompute) página.

Para pedir um aumento, crie um incidente de suporte de Quota de núcleos no portal do Azure, [https://portal.azure.com](https://portal.azure.com).

> [!NOTE]
> Saiba como [criar um pedido de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) no portal do Azure

1. Na página de pedido de novo suporte, selecione o tipo de problema como "Quota" e o tipo de Quota como "Núcleos".

    ![Painel de noções básicas de quota](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. Selecione o modelo de implementação como "Gestor de recursos" e selecione uma localização.

    ![Painel de problema de quota](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Selecione as famílias de SKU que requerem um aumento.

    ![Série SKU selecionado](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Introduza os novos limites que gostaria na subscrição.

    ![Novo pedido de quota SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Para remover uma linha, desmarque o SKU na lista pendente família SKU ou clique no ícone de rejeição "x".
Depois de introduzir a quota pretendida para cada família SKU, clique em "Seguinte", na página de passo de problema para continuar com a criação do pedido de suporte.
