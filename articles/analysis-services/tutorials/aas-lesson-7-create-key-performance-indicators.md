---
title: "Lição 7 do tutorial do Azure Analysis Services: criar indicadores-chave de desempenho | Microsoft Docs"
description: Descreve como criar indicadores-chave de desempenho no projeto de tutorial do Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/20/2017
ms.author: owend
ms.openlocfilehash: 9671136eea4e390a7763404e402cf94f1d351cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-7-create-key-performance-indicators"></a>Lição 7: Criar indicadores-chave de desempenho

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá criar indicadores-chave de desempenho (KPI). KPI são usados para medir o desempenho de um valor definido por uma medida *base*, em relação a um valor de *destino* também definido por uma medida ou valor absoluto. Em aplicações cliente de relatório, os KPI podem proporcionar aos profissionais comerciais uma forma rápida e fácil de compreender um resumo do sucesso da empresa ou para identificar tendências. Para saber mais, consulte [KPI](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular)
  
Tempo estimado para concluir esta lição: **15 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 6: Criar medidas](../tutorials/aas-lesson-6-create-measures.md).   
  
## <a name="create-key-performance-indicators"></a>Criar indicadores-chave de desempenho  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>Para criar um KPI InternetCurrentQuarterSalesPerformance  
  
1.  No designer de modelo, selecione a tabela **FactInternetSales**.  
  
2.  Na grelha de medidas, clique na célula vazia.  
  
3.  Na barra de fórmulas, acima da tabela, digite a seguinte fórmula: 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    Esta medida serve como a medida base do KPI.  
  
4.  Na grelha de medidas, clique com o botão direito do rato em **InternetCurrentQuarterSalesPerformance** > **Criar KPI**.   
  
5.  Na caixa de diálogo Indicadores-chave de desempenho (KPI), em **Destino** selecione **Valor absoluto** e, em seguida, digite **1.1**.  
  
7.  No campo esquerdo do controlo de deslize (em baixo), digite **1**; no campo direito (em cima) do controlo deslizante, digite **1.07**.  
  
8.  Em **Selecionar estilo do ícone**, selecione o losango (vermelho), triângulo (amarelo), círculo (verde).
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > Observe o rótulo expansível **Descrições** por baixo dos estilos de ícone disponíveis. Use as descrições para os vários elementos KPI para os tornar mais identificáveis nas aplicações cliente.  
  
9. Clique em **OK** para concluir os KPI.  
  
    Na grelha de medidas, observe o ícone junto à medida **InternetCurrentQuarterSalesPerformance**. Este ícone indica que essa medida serve como um valor base para um KPI.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>Para criar um KPI InternetCurrentQuarterMarginPerformance  
  
1.  Na grelha de medidas para a tabela **FactInternetSales**, clique numa célula vazia.  
  
2.  Na barra de fórmulas, acima da tabela, digite a seguinte fórmula:  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Clique com o botão direito do rato em **InternetCurrentQuarterMarginPerformance** > **Criar KPI**.  
  
4.  Na caixa de diálogo Indicadores-chave de desempenho (KPI), em **Destino** selecione **Valor absoluto** e, em seguida, digite **1.25**.   
  
5.  No campo (em baixo) à esquerda, deslize até que o campo exiba **0,8**; no campo (em cima) à direita, deslize até que o campo exiba **1.03**.  
  
6.  Em **Selecionar estilo do ícone**, selecione o losango (vermelho), triângulo (amarelo), círculo (verde) e clique em **OK**.  
  
## <a name="whats-next"></a>Passos seguintes?
[Lição 8: Criar perspetivas](../tutorials/aas-lesson-8-create-perspectives.md).
  
  
