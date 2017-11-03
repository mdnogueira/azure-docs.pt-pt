---
title: "Introdução ao Azure Advisor | Microsoft Docs"
description: "Introdução ao Azure Advisor."
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como aceder Advisor através do portal do Azure, obter recomendações, implemente recomendações, procure recomendações e recomendações de atualização.

## <a name="get-advisor-recommendations"></a>Receba recomendações do Assistente

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **mais serviços**.

3. No painel de menu de serviço, em **monitorização e gestão**, clique em **Azure Advisor**.  
 É apresentado o dashboard do Advisor.

   ![Acesso do Azure Advisor no portal do Azure](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. No dashboard do Advisor, selecione a subscrição para o qual pretende receber recomendações.  
O dashboard do Advisor apresenta personalizadas recomendações para a subscrição selecionada. 

5. Para obter recomendações para uma determinada categoria, clique dos separadores: **elevada disponibilidade**, **segurança**, **desempenho**, ou **custo**.
 
> [!NOTE]
> Para obter recomendações do Assistente de acesso, primeiro tem *registar a sua subscrição* com o Advisor. Uma subscrição é registada quando um *subscrição proprietário* inicia o dashboard do Advisor e clica no **obter recomendações** botão. Este é um *operação única*. Depois da subscrição está registada, pode aceder recomendações do assistente como *proprietário*, *contribuinte*, ou *leitor* para uma subscrição, um grupo de recursos ou um recurso específico.

  ![Dashboard do Azure do Advisor](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Obter os detalhes de recomendação do Advisor e implementar uma solução

O **recomendação** painel no Advisor oferece informações adicionais sobre a recomendação. 

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, iniciar [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. No **recomendações do assistente** dashboard, clique em **obter recomendações**.

3. Na lista de recomendações, clique uma recomendação que pretende rever em detalhe.  
O **recomendação** é apresentado o painel.

4. No **recomendações** painel, rever informações sobre as ações que pode efetuar para resolver um potencial problema ou tirar partido de uma oportunidade de custo de guardar. 
  
  ![O painel recomendação do Advisor](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Pesquisa para obter recomendações do Assistente

Pode procurar recomendações para uma determinada subscrição ou grupo de recursos. Também pode procurar recomendações por Estado.

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, iniciar [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Pesquisa de recomendações por filtragem para subscrições, grupos de recursos e o estado de recomendação (**Active Directory** ou **Snoozed**).

3. Para apresentar uma lista de recomendações do assistente que se baseiam nos seus critérios de filtro de pesquisa, clique em **obter recomendações**.

  ![Critérios de filtro de pesquisa do Advisor](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Snooze ou dispensar recomendações do Assistente

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, iniciar [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Clique em **obter recomendações**e, em seguida, na lista de recomendações, clique numa recomendação.

3. No **recomendação** painel, clique em **Snooze**.  

   ![Exemplo de ação de recomendação do Advisor](./media/advisor-get-started/advisor-snooze.png)

4. Especificar um período de tempo de suspensão ou selecionar **nunca** para dispensar a recomendação.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Advisor, consulte:
* [Introdução ao Advisor do Azure](advisor-overview.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
-  [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de custo do Assistente](advisor-performance-recommendations.md)
