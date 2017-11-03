---
title: "Como ver dados relacionados ativos no catálogo de dados do Azure | Microsoft Docs"
description: "Este artigo explica como visualizar recursos de dados relacionados de um recurso de dados selecionadas no catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/17/2017
ms.author: maroche
ms.openlocfilehash: d45f2cabe712a7982f99a9d280fed4494fc4d377
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Como ver dados relacionados ativos no catálogo de dados do Azure?
Catálogo de dados do Azure permite-lhe ver os recursos de dados relacionados com um relações de recurso e a vista de dados selecionados entre eles. 

## <a name="supported-data-sources"></a>Origens de dados suportadas 
Quando registar recursos de dados das seguintes origens de dados, o catálogo de dados do Azure regista automaticamente metadados sobre relações de associação entre os recursos de dados selecionada. 

- SQL Server
- Base de Dados SQL do Azure
- MySQL
- Oracle

## <a name="view-related-data-assets"></a>Ver dados relacionados ativos
Para ver os recursos de dados que estão relacionados com um conjunto de dados selecionado, utilize o **relações** separador conforme mostrado na imagem seguinte: 

![Catálogo de dados do Azure - ver relacionados com os recursos de dados](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

Neste exemplo, existem duas relações para selecionado **ProductSubcategory** recurso de dados: 

- ProductSubcategoryID a coluna da tabela Produto tem uma relação de chave externa com ProductSubcategoryID coluna da tabela ProductSubcategory selecionada. 
- ProductCategoryID a coluna da tabela ProductSubCategory tem uma relação de chave externa com ProductCategoryID coluna da tabela ProductCategory selecionada.

> [!NOTE]
> Tenha em atenção a direção da seta na vista de árvore de relações.  

Para ver mais detalhes, tais como o nome completamente qualificado da coluna, mova o rato sobre e vir um pop-up semelhante para a imagem seguinte: 

![Catálogo de dados do Azure - pop-up de relação](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Para incluir as relações entre os recursos que já foram registados, volte a registar esses recursos.

## <a name="next-steps"></a>Passos seguintes
- [How to manage data assets (Como gerir recursos de dados)](data-catalog-how-to-manage.md)