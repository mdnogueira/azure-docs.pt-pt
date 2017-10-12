---
title: "Lição suplementar do tutorial do Azure Analysis Services: Hierarquias desbalanceadas | Microsoft Docs"
description: Descreve como corrigir hierarquias desbalanceadas no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: d34b2123153406640cf03bc9f57efa557af4cfaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Lição suplementar - Hierarquias desbalanceadas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição suplementar, irá resolver um problema comum quando dinamizar as hierarquias que contêm valores em branco (membros) em diferentes níveis. Por exemplo, uma organização onde um gestor de alto nível tem os gerentes de departamento e não gerentes como subordinados diretos. Ou hierarquias geográficas compostas por país-região-cidade, onde algumas cidades não têm um estado ou província principal, como Washington D.C., a cidade do Vaticano. Quando uma hierarquia tem membros em branco, geralmente desce a níveis diferentes ou desbalanceados.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Modelos de tabela no nível de compatibilidade 1400 têm uma propriedade adicional para **ocultar membros** para hierarquias. A **predefinição** pressupõe que não existem membros em branco em qualquer nível. A definição **Ocultar membros em branco** exclui membros em branco da hierarquia quando adicionados a uma tabela dinâmica ou relatório.  
  
Tempo estimado para concluir esta lição: **20 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico de lição suplementar faz parte de um tutorial de modelação em tabela. Antes de executar as tarefas nesta lição suplementar, deve concluir todas as lições anteriores ou ter concluído um projeto de modelo de exemplo de vendas na Internet Adventure Works. 

Se criou o projeto de vendas na Internet AW como parte do tutorial, o modelo ainda não contém dados ou hierarquias desbalanceadas. Para concluir esta lição suplementar, primeiro tem de criar o problema ao adicionar algumas tabelas adicionais, criar relações, colunas calculadas, uma medida e uma nova hierarquia da organização. Só demora cerca de 15 minutos. Em seguida, fica resolvido em apenas alguns minutos.  

## <a name="add-tables-and-objects"></a>Adicionar tabelas e objetos
  
### <a name="to-add-new-tables-to-your-model"></a>Para adicionar novas tabelas ao seu modelo
  
1.  No Explorador de Modelos em Tabela, expanda **Origens de Dados**, clique com o botão direito do rato na sua ligação > **Importar Tabelas Novas**.
  
2.  No Navigator, selecione **DimEmployee** e **FactResellerSales**e, em seguida, clique em **OK**.

3.  No Editor de consultas, clique em **Importar**

4.  Crie as seguintes [relações](../tutorials/aas-lesson-4-create-relationships.md):

    | Tabela 1           | Coluna       | Direção do filtro   | Tabela 2     | Coluna      | Ativa |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Predefinição            | DimDate     | Data        | Sim    |
    | FactResellerSales | DueDate      | Predefinição            | DimDate     | Data        | Não     |
    | FactResellerSales | ShipDateKey  | Predefinição            | DimDate     | Data        | Não     |
    | FactResellerSales | ProductKey   | Predefinição            | DimProduct  | ProductKey  | Sim    |
    | FactResellerSales | EmployeeKey  | Para ambas as tabelas | DimEmployee | EmployeeKey | Sim    |

5. Na tabela **DimEmployee**, crie as seguintes [colunas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md): 

    **Caminho** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,2)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,3)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,4)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,5)) 
    ```

6.  Na tabela **DimEmployee**, crie uma [hierarquia](../tutorials/aas-lesson-9-create-hierarchies.md) com o nome **Organização**. Adicione a seguintes colunas pela ordem: **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  Na tabela **FactResellerSales**, crie as seguintes [medidas](../tutorials/aas-lesson-6-create-measures.md):

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Use [Analyze em Excel](../tutorials/aas-lesson-12-analyze-in-excel.md) para abrir o Excel e criar automaticamente uma tabela dinâmica.

9.  Em **Campos da tabela dinâmica**, adicione a hierarquia da **Organização** da tabela **DimEmployee** para as **Linhas** e as medidas **ResellerTotalSales** da tabela **FactResellerSales** para **Valores**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Como pode ver na tabela dinâmica, a hierarquia exibe linhas que são irregulares. Há muitas linhas em que os membros em branco são apresentados.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>Para corrigir a hierarquia desbalanceada, definindo Ocultar propriedade dos membros

1.  No **Explorador de modelos tabulares**, expanda **Tabelas** > **DimEmployee** > **Hierarquias** > **Organização**.

2.  Em **Propriedades** > **Ocultar membros**, selecione **Ocultar membros em branco**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  No Excel, atualize a tabela dinâmica. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Agora parece muito melhor!

## <a name="see-also"></a>Veja Também   
[Lição 9: Criar hierarquias](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Lição suplementar - Segurança dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Lição suplementar - Linhas detalhadas](../tutorials/aas-supplemental-lesson-detail-rows.md)  