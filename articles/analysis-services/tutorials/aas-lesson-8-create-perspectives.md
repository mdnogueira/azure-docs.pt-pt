---
title: "Lição 8 do tutorial do Azure Analysis Services: Criar perspetivas | Microsoft Docs"
description: Descreve como criar perspetivas no projeto de tutorial do Azure Analysis Services.
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
ms.openlocfilehash: 68f4e9744007bcf71c5b9239ecfb6f18cf0cd86e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-8-create-perspectives"></a>Lição 8: Criar perspetivas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá criar uma perspetiva de vendas na Internet. Uma perspetiva define um subconjunto exibível de um modelo que fornece pontos de vista concentrados, específicos da empresa ou específicos de aplicações. Quando um utilizador se liga a um modelo através de uma perspetiva, vê apenas os objetos de modelo (tabelas, colunas, medidas, hierarquias e KPI) como campos definidos nessa perspetiva. Para obter mais informações, consulte [Perspetivas](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular).
  
A perspetiva de vendas na Internet que criou nesta lição exclui o objeto de tabela DimCustomer. Quando cria uma perspetiva que exclui determinados objetos de exibição, esse objeto ainda existe no modelo. No entanto, não é visível na lista de campos de relatórios do cliente. Colunas calculadas e medidas incluídas numa perspetiva ou não ainda podem ser calculadas a partir de dados de objeto que foram excluídos.  
  
A finalidade desta lição é descrever como criar perspetivas e se familiarizar-se com as ferramentas de criação de modelos tabulares. Se expandir este modelo para incluir posteriormente tabelas adicionais, poderá criar perspetivas adicionais para definir diferentes pontos de vista do modelo, por exemplo, inventário e vendas.  
  
Tempo estimado para concluir esta lição: **5 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de realizar as tarefas desta lição, deve ter concluído a lição anterior: [Lição 7: Criar os principais indicadores-chave](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  
  
## <a name="create-perspectives"></a>Criar perspetivas  
  
#### <a name="to-create-an-internet-sales-perspective"></a>Para criar uma perspetiva de vendas na Internet  
  
1.  Clique no menu **Modelo** > **Perspetivas** > **Criar e gerir**.  
  
2.  Na caixa de diálogo **Perspetivas** , clique em **Nova perspetiva**.  
  
3.  Faça duplo clique no cabeçalho da coluna **Nova perspetiva** e mude o nome para **Vendas na Internet**.  
  
4.  Selecione todas as tabelas, *exceto* **DimCustomer**.  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    Numa lição posterior, irá utilizar a funcionalidade Analyze em Excel para testar esta perspetiva. A lista de campos de tabela dinâmica do Excel inclui cada tabela, exceto a tabela DimCustomer.  

## <a name="whats-next"></a>Passos seguintes?
[Lição 9: Criar hierarquias](../tutorials/aas-lesson-9-create-hierarchies.md).
  
  
  
  
