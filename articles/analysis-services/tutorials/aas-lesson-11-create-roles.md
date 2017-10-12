---
title: "Lição 11 do tutorial do Azure Analysis Services: Criar funções | Microsoft Docs"
description: "Descreve como criar funções no projeto de tutorial do Azure Analysis Services."
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
ms.openlocfilehash: 0dfcb9d9fc8cd32f95c5097ec653864364b27bcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-11-create-roles"></a>Lição 11: Criar funções

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá criar funções. As funções fornecem segurança de dados e objetos da base de dados de modelo, limitando o acesso somente aos utilizadores que são membros da função. Cada função é definida com uma única permissão: nenhum, leitura, leitura e processo, processo ou administrador. As funções podem ser definidas durante a criação do modelo com o Gestor de função. Depois de um modelo ter sido implementado, pode gerir funções com o SQL Server Management Studio (SSMS). Para saber mais, consulte [Funções](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular):
  
> [!NOTE]  
> A criação de funções não é necessário para concluir este tutorial. Por predefinição, a conta em que tem sessão iniciada neste momento tem privilégios de administrador no modelo. No entanto, para que outros utilizadores na sua organização possam navegar através de um cliente de relatório, deverá criar pelo menos uma função com permissões de leitura e adicionar esses utilizadores como membros.  
  
Irá criar três funções:  
  
-   **Gestor de vendas** – esta função pode incluir utilizadores da sua organização aos quais atribui permissão de leitura para todos os objetos de modelo e dados.  
  
-   **Analista de vendas dos EUA** – essa função pode incluir utilizadores na sua organização que pretende que apenas sejam capazes de procurar dados relacionados com vendas nos Estados Unidos. Para essa função, deve usar uma fórmula DAX para definir um *filtro de linha*, que restringe os membros para procurar dados apenas para os Estados Unidos.  
  
-   **Administrador** – essa função pode incluir os utilizadores aos quais atribui permissões de administrador, fornecendo acesso ilimitado e permissões para executar tarefas administrativas na base de dados modelo.  
  
Uma vez que as contas de utilizador e grupo do Windows são exclusivas na sua organização, pode adicionar contas da sua organização específicas de membros. Contudo, neste tutorial também pode deixar o campo dos membros em branco. Irá testar posteriormente a eficácia de cada função na Lição 12: Analyze em Excel.  
  
Tempo estimado para concluir esta lição: **15 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 10: Criar partições](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Criar funções  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Para criar uma função de utilizador gestor de vendas  
  
1.  No Gestor de modelos tabulares, clique com botão direito do rato em **Funções** > **Funções**.  
  
2.  No Gestor de funções, clique em **Nova**.  
  
3.  Clique na função nova e na coluna **Nome** mude o nome para **Gestor de vendas**.  
  
4.  Na coluna **Permissões**, clique na lista pendente e selecione a permissão de **leitura**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Opcional: Clique no separador **Membros** e clique em **Adicionar**. Na caixa de diálogo **Selecionar utilizadores ou grupos**, introduza os utilizadores ou grupos do Windows pertencentes à sua organização e que pretende incluir na função.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Para criar uma função de utilizador analista de vendas dos EUA  
  
1.  No Gestor de funções, clique em **Nova**.    
  
2.  Mude o nome da função para **Analista de vendas dos EUA**.  
  
3.  Forneça a essa função a permissão de **leitura**.  
  
4.  Clique no separador Filtros da linha e, em seguida, somente na tabela **DimGeography**, na coluna Filtro DAX, digite a seguinte fórmula:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    A fórmula do filtro de linha deve resultar para um valor booleano (VERDADEIRO/FALSO). Com essa fórmula, está a especificar que somente as linhas com o valor de código de região de país "US" estão visíveis para o utilizador.  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Opcional: Clique no separador **Membros** e clique em **Adicionar**. Na caixa de diálogo **Selecionar utilizadores ou grupos**, introduza os utilizadores ou grupos do Windows pertencentes à sua organização e que pretende incluir na função.  
  
#### <a name="to-create-an-administrator-user-role"></a>Para criar uma função de utilizador administrador  
  
1.  Clique em **Novo**.  
  
2.  Mude o nome da função para **Administrador**.  
  
3.  Forneça a essa função a permissão de **administrador**.  
  
4.  Opcional: Clique no separador **Membros** e clique em **Adicionar**. Na caixa de diálogo **Selecionar utilizadores ou grupos**, introduza os utilizadores ou grupos do Windows pertencentes à sua organização e que pretende incluir na função. 
  
  
## <a name="whats-next"></a>Passos seguintes?
[Lição 12: Analyze no Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)

  
  
