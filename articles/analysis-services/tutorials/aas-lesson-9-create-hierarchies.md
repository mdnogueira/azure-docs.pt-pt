---
title: "Lição 9 do tutorial do Azure Analysis Services: Criar hierarquias | Microsoft Docs"
description: 
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 28e4e24f5706e88ede25060d5459617befd4aea9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="lesson-9-create-hierarchies"></a>Lição 9: Criar hierarquias

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá criar hierarquias. Hierarquias são grupos de colunas organizados em níveis. Por exemplo, uma hierarquia de Geografia pode ter subníveis para país, estado, município e cidade. As hierarquias podem aparecer separadas de outras colunas numa lista de campos de aplicações cliente relatório, tornando mais fácil para os utilizadores clientes navegarem e incluírem num relatório. Para obter mais informações, consulte [Hierarquias](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)
  
Para criar hierarquias, use o designer de modelo da *vista de diagrama*. A criação e a gestão de hierarquias não é suportada na vista de dados.  
  
Tempo estimado para concluir esta lição: **20 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 8: Criar perspetivas](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Criar hierarquias  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Para criar uma hierarquia de categoria na tabela DimProduct  
  
1.  No designer de modelo (vista de diagrama), clique com botão direito do rato na tabela **DimProduct** > **Criar hierarquia**. Uma nova hierarquia aparece na parte inferior da janela da tabela. Mude o nome da hierarquia **Categoria**.  
  
2.  Clique e arraste a coluna **ProductCategoryName** para a nova hierarquia **Categoria**.  
  
3.  Na hierarquia **Categoria**, clique com o botão direito do rato em **ProductCategoryName** > **Mudar nome** e, em seguida, digite **Categoria**.  
  
    > [!NOTE]  
    > Mudar o nome de uma coluna numa hierarquia não muda o nome dessa coluna na tabela. Uma coluna numa hierarquia é apenas uma representação da coluna na tabela.  
  
4.  Clique e arraste a coluna **ProductCategoryName** para a nova hierarquia **Categoria**. Mude o nome para **Subcategoria**. 
  
5.  Clique com botão direito do rato na coluna **ModelName** > **Adicionar à hierarquia** e, em seguida, selecione **Categoria**. Mude o nome para **Modelo**.

6.  Finalmente, adicione **EnglishProductName** à hierarquia Categoria. Mude o nome para **Produto**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Para criar hierarquias na tabela DimDate  
  
1.  Na tabela **DimDate**, crie uma hierarquia com o nome **Calendário**.  
  
3.  Adicione as seguintes colunas pela ordem:

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  Na tabela **DimDate**, crie uma hierarquia com o nome **Fiscal**. Inclua as seguintes colunas pela ordem:  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  Por fim, na tabela **DimDate**, crie uma hierarquia **ProductionCalendar**. Inclua as seguintes colunas pela ordem:  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>Passos seguintes?
[Lição 10: Criar partições](../tutorials/aas-lesson-10-create-partitions.md). 
  
  
