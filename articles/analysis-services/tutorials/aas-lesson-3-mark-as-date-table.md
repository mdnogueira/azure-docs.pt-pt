---
title: "Lição 3 do tutorial do Azure Analysis Services: marcar como tabela de datas | Microsoft Docs"
description: Descreve como marcar uma tabela de datas no projeto de tutorial do Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: c62f2726fef5219155a08b70c61162c914600d1d
ms.contentlocale: pt-pt
ms.lasthandoff: 06/03/2017

---
<a id="lesson-3-mark-as-date-table" class="xliff"></a>

# Lição 3: marcar como tabela de datas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Na Lição 2: obter dados, importou uma tabela de dimensões com o nome DimDate. Enquanto no seu modelo esta tabela é denominada DimDate, também pode ser conhecida como uma *tabela de datas* que contém dados de data e hora.  
  
Sempre que utilizar funções de inteligência de tempo DAX, como quando cria medidas mais tarde, deve especificar as propriedades que incluem uma *tabela de datas* e um identificador exclusivo *Coluna de datas* nessa tabela.
  
Nesta lição, marque a tabela DimDate como a *Tabela de datas* e a coluna de datas (na tabela de datas) como a *Coluna de datas* (identificador exclusivo).  

Antes de marcar a tabela de datas e a coluna de datas, este é um bom momento para fazer a manutenção do sistema para que seja mais fácil compreender o modelo. Observe, na tabela DimDate, uma coluna denominada **FullDateAlternateKey**. Esta coluna contém uma linha para cada dia em cada ano civil incluído na tabela. Utiliza muito esta coluna em fórmulas de medições e relatórios. Porém, FullDateAlternateKey não é um identificador válido para esta coluna. Deve mudar o respetivo nome para **Data**, de modo a facilitar a identificação e a inclusão em fórmulas. Sempre que possível, é recomendável mudar o nome aos objetos, como tabelas e colunas, para facilitar a sua identificação no SSDT e nas aplicações de relatório do cliente, como Power BI e Excel. 
  
Tempo estimado para concluir esta lição: **3 minutos**  
  
<a id="prerequisites" class="xliff"></a>

## Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 2: obter dados](../tutorials/aas-lesson-2-get-data.md). 

<a id="to-rename-the-fulldatealternatekey-column" class="xliff"></a>

### Para mudar o nome da coluna FullDateAlternateKey

1.  No designer de modelo, clique na tabela **DimDate**.

2.  Clique duas vezes no cabeçalho da coluna **FullDateAlternateKey** e, em seguida, mude o nome para **Data**.

  
<a id="to-set-mark-as-date-table" class="xliff"></a>

### Para definir Marcar como tabela de datas  
  
1.  Selecione a coluna **Data** e, em seguida, na janela **Propriedades**, em **Tipo de dados**, confirme que **Data** está selecionado.  
  
2.  Clique no menu **Tabela**, clique em **Data**e, em seguida, clique em **Marcar como Tabela de Datas**.  
  
3.  Na caixa de diálogo **Marcar como Tabela de Datas**, na caixa de listagem **Datas**, selecione a coluna **Data** como o identificador exclusivo. Está geralmente selecionado predefinição. Clique em **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

<a id="whats-next" class="xliff"></a>

## Passos seguintes?
[Lição 4: criar relações](../tutorials/aas-lesson-4-create-relationships.md)
  

