---
title: "Lição 6 do tutorial do Azure Analysis Services: Criar medidas | Microsoft Docs"
description: Descreve como criar medidas no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: ba0030ca25adf13a3a831facec2ded9cff0bd882
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-6-create-measures"></a>Lição 6: Criar medidas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá criar medidas a serem incluídas no seu modelo. De forma semelhante às colunas calculadas que criou, uma medida é um cálculo criado através de uma fórmula DAX. No entanto, ao contrário das colunas calculadas, as medidas são avaliadas com base num *filtro* selecionado pelo utilizador. Por exemplo, uma coluna específica ou segmentação de dados adicionada ao campo Rótulos de linha numa tabela dinâmica. Um valor para cada célula no filtro é calculado pela medida aplicada. As medidas são cálculos avançados e flexíveis que deseja incluir em quase todos os modelos de tabela para executar cálculos dinâmicos em dados numéricos. Para saber mais, consulte [Medidas](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular).
  
Para criar medidas, deve usar a *Grelha de medidas*. Por predefinição, cada tabela tem uma grelha de medidas vazia; no entanto, normalmente não cria medidas para cada tabela. A grelha de medidas é apresentada por baixo de uma tabela no designer de modelo na vista de dados. Para ocultar ou mostrar a grelha de medidas para uma tabela, clique no menu **Tabela** e clique em **Mostrar grelha de medidas**.  
  
Pode criar uma medida ao clicar numa célula vazia na grelha de medidas e introduzir uma fórmula DAX na barra de fórmulas. Quando clicar ENTER para completar a fórmula, a medida e aparecerá na célula. Também pode criar medidas através de uma função de agregação padrão ao clicar numa coluna e clicar no botão AutoSum (**∑**) na barra de ferramentas. As medidas criadas com a funcionalidade AutoSum aparecem na célula da grelha de medidas, diretamente atrás da coluna, mas podem ser movidas.  
  
Nesta lição, irá criar medidas ao inserir uma fórmula DAX na barra de fórmulas e utilizar o recurso AutoSum.  
  
Tempo estimado para concluir esta lição: **30 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 5: Criar colunas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md).  
  
## <a name="create-measures"></a>Criar medidas  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>Para criar uma medida de DaysCurrentQuarterToDate na tabela DimDate  
  
1.  No designer de modelo, clique na tabela **DimDate**.  
  
2.  Na grelha de medidas, clique na célula vazia superior esquerda.  
  
3.  Na barra de fórmulas, digite a seguinte fórmula:  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Observe que a célula superior esquerda agora contém um nome de medida, **DaysCurrentQuarterToDate**, seguido pelo resultado, **92**. O resultado não é relevante neste momento porque não foi aplicado um filtro de utilizador.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    Ao contrário das colunas calculadas, com fórmulas de medida, pode digitar o nome da medida, seguido por dois-pontos, seguido pela expressão de fórmula.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>Para criar uma medida de DaysInCurrentQuarter na tabela DimDate  
  
1.  Com a tabela **DimDate** ainda ativa no designer de modelo, na grelha de medidas, clique na célula vazia abaixo da medida que criou.  
  
2.  Na barra de fórmulas, digite a seguinte fórmula:  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    Ao criar uma taxa de comparação entre um período incompleto e o período anterior. A fórmula deve calcular a proporção do período decorrido e compará-la à mesma proporção do período anterior. Nesse caso, [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] fornece a proporção decorrida no período atual.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>Para criar uma medida InternetDistinctCountSalesOrder na tabela FactInternetSales  
  
1.  Clique a tabela **FactInternetSales**.   
  
2.  Clique no cabeçalho da coluna **SalesOrderNumber**.  
  
3.  Na barra de ferramentas, clique na seta para baixo ao lado do botão AutoSum (**∑**) e, em seguida, selecione **DistinctCount**.  
  
    A funcionalidade AutoSum cria automaticamente uma medida para a coluna selecionada através da fórmula de agregação padrão DistinctCount.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  Na grelha de medidas, clique na nova medida e, em seguida, na janela **Propriedades**, no **Nome da medida**, mude o nome da medida para **InternetDistinctCountSalesOrder**. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>Para criar medidas adicionais na tabela FactInternetSales  
  
1.  Através da funcionalidade AutoSum, crie e atribua um nome às seguintes medidas:  

    |Coluna|Nome da medida|AutoSum (∑)|Fórmula|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Contagem|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Soma|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Soma|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Soma|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Soma|=SUM([SalesAmount])|  
    |Margin|InternetTotalMargin|Soma|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Soma|=SUM([TaxAmt])|  
    |Frete|InternetTotalFreight|Soma|=SUM([Freight])|  
  
2.  Clicando numa célula vazia na grelha de medidas e usando a barra de fórmulas, crie as seguintes medidas personalizadas pela ordem:  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
As medidas criadas para a tabela FactInternetSales podem ser usadas para analisar dados financeiros essenciais, como vendas, custos e margem de lucro para itens definidos pelo filtro selecionado pelo utilizador.  
  
## <a name="whats-next"></a>Passos seguintes?
[Lição 7: Criar indicadores-chave de desempenho](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  

  
