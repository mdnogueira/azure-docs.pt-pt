---
title: Utilizar o Power BI com o SQL Data Warehouse | Microsoft Docs
description: "Sugestões para utilizar o Power BI com o Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: b12bee87-2268-40c2-81bf-ab27588b32e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: 4b7609fc5d6ce7bf0e3bd3ebf6d8f52e93a40a75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-power-bi-with-sql-data-warehouse"></a>Utilizar o Power BI com o SQL Data Warehouse
Como com a base de dados SQL do Azure, SQL Server dados armazém direta estabelecer a ligação permite ao utilizador tirar partido do poderoso pushdown lógica juntamente com as capacidades analíticas do Power BI.  Com ligação direta, as consultas são enviadas para o Azure SQL Data Warehouse em tempo real como explorar os dados.  Isto, combinada com a escala do SQL Data Warehouse, permite aos utilizadores criar relatórios dinâmicos em minutos contra terabytes de dados.  Além disso, a introdução do abrir no botão do Power BI permite aos utilizadores ligar diretamente o Power BI ao seu SQL Data Warehouse sem recolher informações a partir de outras partes do Azure.

Ao utilizar a ligação direta Nota:

* Especifique o nome de servidor completamente qualificado ao ligar (consulte abaixo para obter mais detalhes)
* Certifique-se de que as regras de firewall para a base de dados estão configuradas para "Permitir o acesso aos serviços do Azure".
* Cada ação, tal como selecionar uma coluna ou adicionar um filtro diretamente irá consultar o armazém de dados
* Os mosaicos são atualizados aproximadamente a cada 15 minutos (atualização não precisa de ser agendado)
* As perguntas e respostas não estão disponível para estabelecer a ligação direta conjuntos de dados
* Alterações do esquema não são captadas automaticamente
* Todas as consultas de ligação direta serão o tempo limite depois de dois minutos

Estas restrições e notas podem alterar como continuar a melhorar as experiências. Os passos para ligar-se detalhadas abaixo.  

## <a name="using-the-open-in-power-bi-button"></a>Com o botão 'Abrir no Power BI'
A forma mais fácil para alternar entre o armazém de dados do SQL Server e o Power BI está aberta no botão do Power BI. Este botão permite-lhe perfeitamente começar a criar novos dashboards no Power BI.  

1. Para começar a utilizar navegue até à sua instância do SQL Data Warehouse no Portal clássico do Azure.
2. Clique no botão “Abrir no Power BI”.
3. Se não estamos a conseguir iniciar sessão diretamente ou se não tiver uma conta do Power BI, terá de início de sessão.  
4. Será direcionado para a página de ligação do SQL Data Warehouse, com as informações do seu SQL Data Warehouse pré-preenchido.
5. Depois de introduzir as suas credenciais lhe ficará completamente ligado ao seu SQL Data Warehouse.

## <a name="connecting-through-the-power-bi-portal"></a>Ligar através do portal do Power BI
Além de utilizar a Open no botão do Power BI, os utilizadores também podem ligar ao seu SQL Data Warehouse através do Portal do Power BI.

1. Clique em 'Obter dados de' na parte inferior do painel de navegação.
2. Selecione 'Bases de dados'.
3. Uma vez na página de bases de dados, selecione 'Azure SQL Data Warehouse' e, em seguida, clique em "Ligar".
4. Introduza as informações de ligação necessárias.  O nome do servidor e o nome de base de dados podem ser encontrados no Portal do Azure.
5. O utilizador será direcionado para a página principal do Power BI e depois da ligação é efetuada uma nova entrada em 'Conjuntos de dados' será apresentada com o nome da sua instância.  
6. Pode clicar em novo conjunto de dados para explorar todas as tabelas e vistas na base de dados. Selecionar uma coluna enviará uma consulta para a origem, criar dinamicamente o visual. Estes elementos visuais podem ser guardados num novo relatório e volta afixados ao dashboard.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
