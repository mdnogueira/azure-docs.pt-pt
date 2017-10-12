---
title: "Lição 12 do tutorial do Azure Analysis Services: Analyze em Excel | Microsoft Docs"
description: Descreve como utilizar o Analyze em Excel num projeto do tutorial do Azure Analysis Services.
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
ms.openlocfilehash: e257862a88d39b96360703117f544c43e82b0e3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-12-analyze-in-excel"></a>Lição 12: Analyze em Excel

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá utilizar a funcionalidade Analyze no Excel para abrir o Microsoft Excel, criar automaticamente uma ligação para o espaço de trabalho do modelo e adicionar automaticamente uma tabela dinâmica à folha de cálculo. A funcionalidade Analyze em Excel fornece uma forma rápida e fácil de testar a eficácia do design do seu modelo antes de implementar o modelo. Não irá executar quaisquer análises de dados nesta lição. A finalidade desta lição familiarizá-lo a si, o autor do modelo, com todas as ferramentas possíveis para testar o seu design de modelo.   
  
Para concluir esta lição, o Excel deve ser instalado no mesmo computador que o SSDT.
  
Tempo estimado para concluir esta lição: **5 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 11: Criar funções](../tutorials/aas-lesson-11-create-roles.md).  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>Procurar através das perspetivas predefinidas e das vendas na Internet  
Nestas primeiras tarefas, procure o seu modelo ao utilizar a perspetiva predefinida, que inclui todos os objetos do modelo, e também ao utilizar a perspetiva de vendas na Internet que utilizou anteriormente. A perspetiva de vendas na Internet exclui o objeto de tabela do cliente.  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>Para procurar através da perspetiva predefinida  
  
1.  Clique no menu **Modelo** > **Analyze em Excel**.  
  
2.  Na caixa de diálogo **Analyze em Excel**, clique em **OK**.  
  
    O Excel abre com um livro novo. Uma ligação à origem de dados é criada com a conta de utilizador atual e a perspetiva predefinida é usada para definir os campos visíveis. Uma tabela dinâmica é adicionada automaticamente à folha de cálculo.  
  
3.  No Excel, na **lista de campos da tabela dinâmica**, observe os grupos de medições **DimDate** e **FactInternetSales** que são exibidos. As tabelas **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** e **FactInternetSales** também são apresentadas com as respetivas colunas.  
  
4.  Feche o Excel sem guardar o livro.  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>Para procurar através da perspetiva vendas na Internet  
  
1.  Clique no menu **Modelo** e clique em **Analyze em Excel**.  
  
2.  Na caixa de diálogo **Analyze em Excel**, deixe selecionado o **Utilizador Windows atual** e, em seguida, na caixa de listagem pendente **Perspetiva**, selecione **Vendas na Internet** e clique em **OK**. 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  No Excel, nos **campos da tabela dinâmica**, observe se a tabela DimCustomer foi excluída da lista de campos.  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  Feche o Excel sem guardar o livro.  
  
## <a name="browse-by-using-roles"></a>Procurar através das funções  
Funções são uma parte importante de qualquer modelo de tabela. Sem, pelo menos, uma função à qual os utilizadores são adicionados como membros, os utilizadores não podem aceder e analisar os dados com o seu modelo. A funcionalidade Analyze em Excel dispõe de uma forma de testar as funções que definiu.  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Para procurar através da função de utilizador Gestor de vendas  
  
1.  No SSDT, clique no menu **Modelo** e clique em **Analyze no Excel**.  
  
2.  Em **Especificar nome de utilizador ou função a ser usada para se ligar ao modelo**, selecione **Função** e, em seguida, na caixa de listagem pendente, selecione **Gestor de vendas** e clique em **OK**.  
  
    O Excel abre com um livro novo. É criada automaticamente uma tabela dinâmica. A lista de campos da tabela dinâmica inclui todos os campos de dados disponíveis no seu modelo novo.  
      
3.  Feche o Excel sem guardar o livro.  
  
## <a name="whats-next"></a>Passos seguintes?
Avance para a próxima lição: [Lição 13: Implementar](../tutorials/aas-lesson-13-deploy.md).

  
  
  
