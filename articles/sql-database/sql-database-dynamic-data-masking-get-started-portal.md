---
title: "Portal do Azure: máscara de dados dinâmicos da base de dados SQL | Microsoft Docs"
description: "Como começar com a máscara de dados dinâmicos da base de dados SQL no Portal do Azure"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: "2"
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 11/22/2016
ms.author: ronitr
ms.openlocfilehash: aae91ef276625b13119c449636c9accc61e0a302
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Começar com o portal do Azure de máscara de dados dinâmicos da base de dados do SQL Server

Este artigo mostra como implementar [máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md) com o portal do Azure. Também pode implementar através de máscara de dados dinâmicos [cmdlets da SQL Database do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) ou [REST API](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurar dados dinâmicos máscara da base de dados no portal do Azure
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue para a página de definições da base de dados que inclui os dados confidenciais que pretende mascarar.
3. Clique em de **máscara de dados dinâmicos** mosaico inicia a **máscara de dados dinâmicos** página de configuração.
   
   * Em alternativa, pode desloque para baixo até o **operações** secção e clique em **máscara de dados dinâmicos**.
     
     ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. No **máscara de dados dinâmicos** página de configuração, poderá ver algumas colunas de base de dados que o motor de recomendações tem sinalizados para máscara. Para aceitar as recomendações, basta clicar **adicionar máscara** para uma ou mais colunas e numa máscara é criado com base no tipo predefinido para esta coluna. Pode alterar a função de máscara clicando na regra de máscara e editar a máscara de formato de campo num formato diferente da sua preferência. Certifique-se clicar em **guardar** para guardar as definições.
   
    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Para adicionar uma máscara para nenhuma coluna na base de dados, na parte superior a **máscara de dados dinâmicos** página de configuração, clique em **adicionar máscara** para abrir o **Adicionar regra de máscara** página de configuração .
   
    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Selecione o **esquema**, **tabela** e **coluna** para definir o campo designado de máscara.
7. Escolha um **formato de máscara de campo** da lista de categorias de máscara de dados confidenciais.
   
    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Clique em **guardar** nos página de regra para atualizar o conjunto de regras de política de máscara de dados dinâmicos de máscara de máscara de dados.
9. Escreva o utilizadores do SQL Server ou as identidades do AAD devem ser excluídas da máscara, e que tenham acesso aos dados confidenciais sem máscara. Deve ser uma lista delimitada por vírgulas de utilizadores. Os utilizadores com privilégios de administrador tenham sempre acesso aos dados sem máscara originais.
   
    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Para torná-lo para a camada da aplicação pode apresentar dados confidenciais para utilizadores da aplicação privilegiado, adicione o utilizador SQL ou utiliza a identidade do AAD da aplicação para consultar a base de dados. É altamente recomendável que esta lista contém um número mínimo de utilizadores com privilégios para minimizar a exposição de dados confidenciais.
   > 
   > 
10. Clique em **guardar** nos página de configuração para guardar a nova ou atualizada de máscara de dados de política de máscara.


## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição geral de máscara de dados dinâmicos, consulte [máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md).
* Também pode implementar através de máscara de dados dinâmicos [cmdlets da SQL Database do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) ou [REST API](https://msdn.microsoft.com/library/dn505719.aspx).
