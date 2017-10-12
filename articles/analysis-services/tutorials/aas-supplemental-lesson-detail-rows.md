---
title: "Lição suplementar do tutorial do Azure Analysis Services: Linhas detalhadas | Microsoft Docs"
description: "Descreve como criar uma expressão das linhas detalhadas no tutorial do Azure Analysis Services."
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
ms.date: 05/26/2017
ms.author: owend
ms.openlocfilehash: 9995ad39d9e3fd1a211c513d4097398e99eefc54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="supplemental-lesson---detail-rows"></a>Lição suplementar - Linhas Detalhadas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição suplementar, irá usar o Editor do DAX para definir uma expressão personalizada de linhas detalhadas. Uma expressão de linhas detalhadas é uma propriedade numa medida, disponibilizando mais informações aos utilizadores finais sobre os resultados agregados de uma medida. 
  
Tempo estimado para concluir esta lição: **10 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico de lição suplementar faz parte de um tutorial de modelação em tabela. Antes de executar as tarefas nesta lição suplementar, deve concluir todas as lições anteriores ou ter concluído um projeto de modelo de exemplo de vendas na Internet Adventure Works.  
  
## <a name="what-do-we-need-to-solve"></a>O que precisamos solucionar?
Vamos examinar os detalhes da nossa medida InternetTotalSales antes de adicionar uma expressão de linhas detalhadas.

1.  No SSDT, clique no menu **Modelo** > **Analyze em Excel** para abrir o Excel e criar uma tabela dinâmica em branco.
  
2.  Em **PivotTable Fields**, adicione as medidas **InternetTotalSales** da tabela FactInternetSales para **Valores**, **CalendarYear** da tabela DimDate para **Colunas** e **EnglishCountryRegionName** para **Linhas**. A nossa tabela dinâmica oferece-nos agora os resultados agregados das medidas InternetTotalSales por regiões e ano. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. Na tabela dinâmica, clique duas vezes num valor agregado para um ano e num nome de região. Aqui devemos clicar duas vezes no valor para a Austrália para o ano de 2014. É aberta uma nova folha de cálculo que contém dados, mas os dados não são úteis.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Gostaríamos de ver aqui uma tabela com contém colunas e linhas de dados que contribuem para o resultado agregado da nossa medida InternetTotalSales. Para fazer isso, podemos adicionar uma expressão de linhas detalhadas como uma propriedade da medida.

## <a name="add-a-detail-rows-expression"></a>Adicionar uma expressão de linhas detalhadas

#### <a name="to-create-a-detail-rows-expression"></a>Para criar uma expressão de linhas detalhadas 
  
1. No SSDT, na grelha de medidas da tabela FactInternetSales, clique na medida **InternetTotalSales**. 

2. Em **Propriedades** > **Expressão das linhas detalhadas**, clique no botão do editor para abrir o Editor DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. No Editor DAX, digite a expressão seguinte:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Esta expressão especifica os nomes de colunas e os resultados das medidas da tabela FactInternetSales, e as tabelas relacionadas são devolvidas quando um utilizador clica duas vezes num resultado agregado numa tabela dinâmica ou relatório.

4. Novamente no Excel, elimine a folha criada no passo 3, depois clique duas vezes num valor agregado. Neste momento, com uma propriedade de expressão de linhas detalhadas definida para a medida, é aberta uma nova folha que contém dados muito mais úteis.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Volte a implementar o modelo.

  
## <a name="see-also"></a>Veja Também  
[Função SELECTCOLUMNS (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Lição suplementar - Segurança dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Lição suplementar - Hierarquias desbalanceadas](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  
