---
title: "Lição 2 do tutorial do Azure Analysis Services: Obter dados | Microsoft Docs"
description: Descreve como obter e importar dados no projeto de tutorial do Azure Analysis Services.
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
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: e77de4b9a74b528fa8a7ce86424fc14628b2cacc
ms.contentlocale: pt-pt
ms.lasthandoff: 06/03/2017

---

# <a name="lesson-2-get-data"></a>Lição 2: Obter dados

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, irá utilizar Obter dados no SSDT para se ligar à base de dados de exemplo AdventureWorksDW2014, selecionar os dados, pré-visualizar e filtrar e, em seguida, importar para o espaço de trabalho do seu modelo.  
  
Através de Obter dados, pode importar dados de uma ampla variedade de origens: Base de Dados SQL do Azure, Oracle, Sybase, OData Feed, Teradata, ficheiros e muito mais. Os dados também podem ser consultados através de uma expressão de fórmula Power Query M.
  
Tempo estimado para concluir esta lição: **10 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 1: Criar um novo projeto de modelo de tabela](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
## <a name="create-a-connection"></a>Criar uma ligação  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Para criar uma ligação à base de dados AdventureWorksDW2014  
  
1.  No Explorador de modelos de tabela, clique com botão direito do rato em **Origens de dados** > **Importar a partir de origens de dados**.  
  
    Isto inicia Obter dados que lhe permite ligar-se a uma origem de dados. Se não reparar no Gestor de modelos de tabela, em **Solution Explorer**, clique duas vezes em **Model.bim** para abrir o modelo no designer. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  Em Obter dados, clique em **Base de dados** > **Base de dados do SQL Server** > **Ligar**.  
  
3.  Na caixa de diálogo **Base de dados do SQL Server**, no **Servidor**, digite o nome do servidor onde instalou a base de dados AdventureWorksDW2014 e, em seguida, clique em **Ligar**.  

4.  Quando tiver de inserir as credenciais, tem de especificar as credenciais que o Analysis Services usa para se ligar à origem de dados ao importar e processar dados. No **Modo de representação**, selecione **Representar a conta**, insira as credenciais e, em seguida, clique em **Ligar**. É recomendável usar uma conta em que a palavra-passe não expire.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Usar uma conta de utilizador e palavra-passe do Windows é o método mais seguro de se ligar a uma origem de dados.
  
5.  No Navigator, selecione a base de dados **AdventureWorksDW2014** e, em seguida, clique em **OK**. Isso cria a ligação à base de dados. 
  
6.  No Navigator, selecione a caixa de verificação para as seguintes tabelas: **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** e **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
Depois de clicar em OK, é aberto o Editor de consultas. Na próxima secção, selecione somente os dados que pretende importar.

  
## <a name="filter-the-table-data"></a>Filtrar os dados da tabela  
AS tabelas na base de dados de exemplo AdventureWorksDW2014 possuem dados que é necessário incluir no seu modelo. Quando for possível, deverá filtrar os dados desnecessários para economizar espaço na memória usada pelo modelo. Irá filtrar algumas das colunas de tabelas para que não sejam importadas para a base de dados do espaço de trabalho ou para a base de dados do modelo após de este ter sido implementado. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Para filtrar os dados da tabela antes de importar  
  
1.  No Editor de consultas, selecione a tabela **DimCustomer**. É apresentada a tabela DimCustomer na origem de dados (a sua base de dados de exemplo AdventureWorksDWQ2014). 
  
2.  Multisseleção (Ctrl + clique) **SpanishEducation**, **FrenchEducation**, **SpanishOccupation**, **FrenchOccupation** e, em seguida, clique com botão direito do rato e clique em **Remover colunas**. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Uma vez que os valores para essas colunas não são relevantes para a análise de vendas na Internet, não é necessário importar essas colunas. A eliminação de colunas desnecessárias torna seu modelo menor e mais eficiente.  
  
4.  Filtre as tabelas restantes, removendo as seguintes colunas em cada tabela:  
    
    **DimDate**
    
      |Coluna|  
      |--------|  
      |DateKey|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |Coluna|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |Coluna|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |Coluna|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |Coluna|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      |Coluna|  
      |------------------|  
      |**OrderDateKey**|  
      |**DueDateKey**|  
      |**ShipDateKey**|   
  
## <a name="Import"></a>Importar os dados de coluna e as tabelas selecionadas  
Agora que pré-visualizou e filtrou os dados desnecessários, pode importar os restante dos dados que pretende. O assistente importa os dados da tabela juntamente com as relações entre tabelas. Novas tabelas e colunas são criadas no modelo e não é possível importar os dados que filtrou.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>Para importar os dados de coluna e as tabelas selecionadas  
  
1.  Examine as suas seleções. Se tudo estiver correto, clique em **Importar**. A caixa de diálogo de processamento de dados mostra o estado dos dados que estão a ser importados da origem de dados para a base de dados do eu espaço de trabalho.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Clique em **Fechar**.  

  
## <a name="save-your-model-project"></a>Guardar o seu projeto de modelo  
É importante guardar frequentemente o seu projeto de modelo.  
  
#### <a name="to-save-the-model-project"></a>Para guardar o seu projeto de modelo  
  
-   Clique em **Ficheiro** > **Guardar tudo**.  
  
## <a name="whats-next"></a>Passos seguintes?
[Lição 3: Marcar como tabela de datas](../tutorials/aas-lesson-3-mark-as-date-table.md).

  
  

