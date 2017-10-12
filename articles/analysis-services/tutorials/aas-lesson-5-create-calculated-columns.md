---
title: "Lição 5 do tutorial do Azure Analysis Services: Criar colunas calculadas | Microsoft Docs"
description: Descreve como criar colunas calculadas no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: eab74fbadc6a143ca5a2bc57a1762539a6d489c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-5-create-calculated-columns"></a>Lição 5: Criar colunas calculadas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá criar dados no seu modelo ao adicionar colunas calculadas. Pode criar colunas calculadas (como colunas personalizadas) ao utilizar Obter dados através do Editor de consultas ou, mais tarde, o tipo de designer de modelo que cria aqui. Para obter mais informações, consulte [Colunas calculadas](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns).
  
Pode criar cinco novas colunas calculadas em três tabelas diferentes. Os passos são ligeiramente diferentes para cada tarefa, demonstrando que existem várias formas para criar colunas, mudar-lhes o nomes e colocá-las em vários locais numa tabela.  

Nesta lição também usa pela primeira vez o Data Analysis Expressions (DAX). DAX é uma linguagem especial para criar expressões de fórmulas altamente personalizáveis para modelos de tabela. Neste tutorial, pode usar DAX para criar colunas calculadas, medidas e filtros de função. Para obter mais informações, consulte [DAX em modelos de tabela](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular). 
  
Tempo estimado para concluir esta lição: **15 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 4: Criar relações](../tutorials/aas-lesson-4-create-relationships.md). 
  
## <a name="create-calculated-columns"></a>Criar colunas calculadas  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>Criar uma coluna calculada MonthCalendar na tabela DimDate  
  
1.  Clique no menu **Modelo** > **Vista de modelo** > **Vista de diagrama**.  
  
    As colunas calculadas só podem ser criadas com o designer de modelo na vista de dados.  
  
2.  No designer de modelo, clique na tabela **DimDate** (separador).  
  
3.  Clique com botão direito do rato no cabeçalho de coluna **CalendarQuarter** e clique em **Inserir coluna**.  
  
    Uma nova coluna com o nome **Coluna calculada 1** é inserida à esquerda da coluna **Trimestre do calendário**.  
  
4.  Na barra de fórmulas na parte superior da tabela, digite a seguinte fórmula DAX: O preenchimento automático ajuda-o a digitar os nomes totalmente qualificados de colunas e tabelas e lista as funções que estão disponíveis.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    Os valores são preenchidos em todas as linhas na coluna calculada. Se percorrer a tabela no sentido descendente, verá linhas podem ter valores diferentes para essa coluna, com base nos dados de cada linha.    
  
5.  Mudar o nome desta coluna para **MonthCalendar**. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
A coluna calculada MonthCalendar fornece um nome ordenável para o mês.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>Criar uma coluna calculada DayOfWeek na tabela DimDate  
  
1.  Com a tabela **DimDate** ainda ativa, clique no menu **Coluna** e clique em **Adicionar coluna**.  
  
2.  Na barra de fórmulas, digite a seguinte fórmula:  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    Quando tiver concluído a criação da fórmula, pressione ENTER. A nova coluna é adicionada à direita da tabela.  
  
3.  Mude o nome da coluna para **DayOfWeek**.  
  
4.  Clique no cabeçalho da coluna e, em seguida, arraste a coluna entre a coluna **EnglishDayNameOfWeek** e a coluna **DayNumberOfMonth**.  
  
    > [!TIP]  
    > A movimentação das colunas na tabela facilita a navegação.  
  
A coluna calculada DayOfWeek fornece um nome ordenável para o dia da semana.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>Criar uma coluna calculada ProductSubcategoryName na tabela DimProduct  
  
  
1.  Na tabela **DimProduct**, desloque-se até ao lado direito da tabela. Observe que a coluna mais à direita é denominada **Adicionar coluna** (em itálico), clique no cabeçalho da coluna.  
  
2.  Na barra de fórmulas, digite a seguinte fórmula:  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  Mude o nome da coluna para **ProductSubcategoryName**.  
  
A coluna calculada ProductSubcategoryName é usada para criar uma hierarquia na tabela DimProduct, que inclui dados da coluna EnglishProductSubcategoryName na tabela DimProductSubcategory. Hierarquias não podem abranger mais de uma tabela. Na lição 9, irá criar hierarquias.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>Criar uma coluna calculada ProductCategoryName na tabela DimProduct  
  
1.  Com a tabela **DimProduct** ainda ativa, clique no menu **Coluna** e clique em **Adicionar coluna**.  
  
2.  Na barra de fórmulas, digite a seguinte fórmula:  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  Mude o nome da coluna para **ProductCategoryName**.  
  
A coluna calculada ProductCategoryName é usada para criar uma hierarquia na tabela DimProduct, que inclui dados da coluna EnglishProductCategoryName na tabela DimProductCategory. Hierarquias não podem abranger mais de uma tabela.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>Criar uma coluna calculada Margin na tabela FactInternetSales  
  
1.  No designer de modelo, selecione a tabela **FactInternetSales**.  
  
2.  Crie uma nova coluna calculada entre a coluna **SalesAmount** e a coluna **TaxAmt**.  
  
3.  Na barra de fórmulas, digite a seguinte fórmula:  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  Mude o nome da coluna para **Margin**.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    A coluna calculada Margin é usada para analisar margens de lucro para cada venda.  
  
## <a name="whats-next"></a>Passos seguintes?
[Lição 6: Criar medidas](../tutorials/aas-lesson-6-create-measures.md).
  
  
  
