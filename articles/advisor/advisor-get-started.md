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
ms.openlocfilehash: dc89cd29e1e8038f0ff317ff6acee332218ebce7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como aceder Advisor através do portal do Azure, recomendações de obter e implementar as recomendações.

## <a name="get-advisor-recommendations"></a>Receba recomendações do Assistente

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **Advisor**.  Se não vir Advisor no painel esquerdo, clique em **mais serviços**.  No painel de menu de serviço, em **monitorização e gestão**, clique em **Advisor**.
 É apresentado o dashboard do Advisor.

   ![Acesso do Azure Advisor no portal do Azure](./media/advisor-get-started/advisor-portal-menu.png) 

4. O dashboard do Advisor será apresentado um resumo das suas recomendações para todas as subscrições seleccionadas.  Pode escolher as subscrições que pretende que as recomendações apresentadas para utilizar a subscrição filtrar a lista pendente.

5. Para obter recomendações para uma categoria específica, clique dos separadores: **elevada disponibilidade**, **segurança**, **desempenho**, ou **custo**.
 
> [!NOTE]
> Para utilizar o Advisor do Azure com uma subscrição, uma subscrição *proprietário* tem de iniciar o dashboard do Advisor.  Esta ação regista a subscrição com o Advisor.  Partir desse ponto, de qualquer subscrição *proprietário*, *contribuinte*, ou *leitor* pode aceder às recomendações do Advisor para a subscrição.  

  ![Dashboard do Azure do Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Obter os detalhes de recomendação do Advisor e implementar uma solução

Pode selecionar uma recomendação no Advisor para ver detalhes adicionais – por exemplo, as ações de recomendação e os recursos afetados – e para implementar a solução para a recomendação.  

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2. Selecione uma categoria de recomendação para apresentar a lista de recomendações dentro dessa categoria ou selecione o **todos os** separador para ver todas as suas recomendações.

3. Clique uma recomendação que pretende rever em detalhe.

4. Reveja as informações sobre a recomendação e os recursos aos quais se aplica a recomendação.

5. Clique em de **ação recomendada** para implementar a recomendação.

## <a name="filter-advisor-recommendations"></a>Filtrar recomendações do Assistente

Pode filtrar recomendações para descer até que são mais importante para si.  Pode filtrar por subscrição, o tipo de recurso ou o estado de recomendação.  

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2.  Utilize as dropdowns no dashboard do Advisor para filtrar por subscrição, o tipo de recurso ou o estado de recomendação.

    ![Critérios de filtro de pesquisa do Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Snooze ou dispensar recomendações do Assistente

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2. Navegue para a recomendação que pretende snooze ou ignorar.

3. Clique na recomendação.

4. Clique em **Snooze**. 

5. Especificar um período de tempo de suspensão ou selecionar **nunca** para dispensar a recomendação.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Excluir subscrições ou grupos de recursos do Advisor

Pode ter grupos de recursos ou subscrições para que não pretende receber recomendações do assistente – como recursos de "teste".  Pode configurar o Advisor só gerar recomendações para as subscrições específicas e grupos de recursos.

> [!NOTE]
> Para incluir ou excluir uma subscrição ou grupo de recursos do Advisor, tem de ser uma proprietário da subscrição.  Se não tiver as permissões necessárias para uma subscrição ou grupo de recursos, a opção para incluir ou excluir, está desativada na interface de utilizador.

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2. Clique em **configurar** na barra de ação.

3. Desmarque qualquer subscrições ou grupos de recursos que pretende receber para obter recomendações do assistente.

    ![Configurar o Advisor exemplo de recursos](./media/advisor-get-started/advisor-configure-resources.png)

4. Clique em de **aplicar** botão.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>Configurar a regra de utilização da CPU média para a recomendação de máquina virtual de utilização baixa

Advisor monitoriza a utilização da máquina virtual nos últimos 14 dias e, em seguida, identifica as máquinas virtuais de baixa utilização. Máquinas virtuais cuja média de utilização da CPU é 5 porcento ou menos e utilização de rede é 7 MB ou menos, de quatro ou mais dias são considerados máquinas de virtuais baixa utilização.

Se pretender que seja mais agressiva em identificar máquinas virtuais de menor utilização, pode ajustar a regra de utilização da CPU média numa base por subscrição.  A regra de utilização média da CPU pode ser definida como 5%, 10%, 15% ou 20%.

> [!NOTE]
> Para ajustar a regra de utilização da CPU média para identificar máquinas virtuais de menor utilização, tem de ser uma subscrição *proprietário*.  Se não tiver as permissões necessárias para uma subscrição ou grupo de recursos, a opção para incluir ou excluir, será desativada na interface de utilizador. 

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2. Clique em **configurar** na barra de ação.

3. Clique em de **regras** separador.

4. Selecione as subscrições gostaria de ajustar a regra de utilização da CPU média para e, em seguida, clique em **editar**.

5. Selecione o valor de utilização da CPU média pretendido e clique em **aplicar**.

6. Clique em **recomendações de atualização** para atualizar as recomendações existentes para utilizar a nova regra de utilização da CPU média. 

   ![Exemplo de regras de recomendação de configurar do Advisor](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Transferir as recomendações do Advisor

Advisor permite-lhe transferir um resumo das suas recomendações.  Pode transferir as recomendações como um ficheiro PDF ou um ficheiro CSV.  Transferir as recomendações permite-lhe facilmente partilhar com os seus colegas ou executar uma análise do seu próprio sobre os dados de recomendação.

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2. Clique em **transferir como CSV** ou **transferir como PDF** na barra de ação.

A opção de transferência respeita a quaisquer filtros que aplicou ao dashboard do Advisor.  Se selecionar a opção de transferência quando estiver a visualizar uma categoria de recomendação específico ou a recomendação, o resumo transferido apenas inclui informações para que a categoria ou a recomendação. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Advisor, consulte:
* [Introdução ao Advisor do Azure](advisor-overview.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
-  [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de custo do Assistente](advisor-performance-recommendations.md)
