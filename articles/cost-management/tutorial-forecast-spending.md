---
title: "Prever despesas com a gestão de custo do Azure | Microsoft Docs"
description: "Previsão gastos utilizando o histórico de utilização e dados de gastos."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: d8b0cd2a3e5f9829f0844783aad22d375eb9d7a8
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="forecast-future-spending"></a>Prever despesas futuras

Gestão de custo do Azure por Cloudyn ajuda a prever futuros gastos utilizando o histórico de utilização e dados de gastos. Utilize Cloudyn relatórios para ver todos os dados de projeção de custo. Os exemplos neste tutorial explica como rever projeções de custo utilizando os relatórios. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prever despesas futuras

## <a name="forecast-future-spending"></a>Prever despesas futuras

Cloudyn inclui relatórios de projeção de custos para ajudar a prever despesas com base na sua utilização ao longo do tempo. Os seus principal objetivo é ajudar a garantir que as tendências de custo excede as expectativas da sua organização. Os relatórios a que utilizar são custo de projetado de mês atual e o custo de projetado anuais. Ambos mostram despesas futuras prevista se a sua utilização permanece relativamente consistente com os últimos 30 dias de utilização.

O relatório de custo de projetado de mês atual mostra os custos dos seus serviços. Utiliza os custos do início do mês e o mês anterior para mostrar o custo previsto. No menu de relatórios na parte superior do portal, clique em **custo** > **projeção e orçamento** > **custo de projetado de mês atual**. A imagem seguinte mostra um exemplo.

![custo de projetado do mês atual](./media/tutorial-forecast-spending/project-month01.png)

No exemplo, pode ver quais os serviços despendem mais. Os custos do Azure foram inferiores a custos AWS. Se pretender ver detalhes de projeção de custos para VMs do Azure, no **filtro** lista, selecione **VM do Azure**.

![VM do Azure o mês atual projetado custo](./media/tutorial-forecast-spending/project-month02.png)

Siga os mesmos passos anteriores básicos para observar projeções de custo mensal para outros serviços a que se estiver interessado em.

O relatório de custo projetado anuais mostra o custo extrapolated dos seus serviços através de 12 meses seguintes.

No menu de relatórios na parte superior do portal, clique em **custo** > **projeção e orçamento** > **anuais custo projetado**. A imagem seguinte mostra um exemplo.

![relatório de custo prevista anuais](./media/tutorial-forecast-spending/project-annual01.png)

No exemplo, pode ver quais os serviços despendem mais. Semelhante ao exemplo mensal, os custos do Azure foram inferiores a custos AWS. Se pretender ver detalhes de projeção de custos para VMs do Azure, no **filtro** lista, selecione **VM do Azure**.

![custo previsto anual de VMs](./media/tutorial-forecast-spending/project-annual02.png)

Na imagem acima, o custo previsto anual de VMs do Azure é $28,374.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Prever despesas futuras


Avançar para o próximo tutorial para saber como gerir os custos com relatórios de análise de custos e da alocação e de custo.

> [!div class="nextstepaction"]
> [Gerir os custos com relatórios de análise de custos e da alocação e de custo](tutorial-manage-costs.md)
