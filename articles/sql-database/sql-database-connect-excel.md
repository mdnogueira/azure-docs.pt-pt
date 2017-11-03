---
title: "Ligar o Excel à Base de Dados SQL | Microsoft Docs"
description: "Saiba como ligar o Microsoft Excel à base de dados SQL do Azure na nuvem. Importe dados para o Excel para criação de relatórios e exploração de dados."
services: sql-database
keywords: ligar o excel ao sql, importar dados para o excel
documentationcenter: 
author: joseidz
manager: jhubbard
editor: 
ms.assetid: 906924bc-2707-48d3-bac6-397976a0409d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: jhubbard
ms.openlocfilehash: 34ff5c479cfcf1e861a82205eef93dfee01cb4a2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Ligar o Excel a uma base de dados SQL do Azure e criar um relatório

Ligar o Excel a uma base de dados do SQL Server na nuvem e importar dados e criar tabelas e gráficos com base nos valores na base de dados. Neste tutorial, irá configurar a ligação entre o Excel e uma tabela de base de dados, guardar o ficheiro que armazena os dados e as informações de ligação para o Excel e, em seguida, criar um gráfico dinâmico a partir dos valores da base de dados.

Precisa de uma base de dados SQL no Azure antes de começar. Se não tiver uma, consulte [Criar a sua primeira base de dados SQL](sql-database-get-started-portal.md) para obter uma base de dados com dados de exemplo e funcional, em apenas alguns minutos. Neste artigo, irá importar dados de exemplo para o Excel desse artigo, mas pode seguir passos semelhantes com os seus próprios dados.

Irá também precisar de uma cópia do Excel. Este artigo utiliza o [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Ligar o Excel a uma base de dados SQL e criar um ficheiro odc
1. Para ligar o Excel à base de dados SQL, abra o Excel e, em seguida, crie um novo livro ou abra um livro do Excel existente.
2. Na barra de menus na parte superior da página, clique em **Dados**, clique em **De Outras Origens**, e, em seguida, clique em **A Partir do SQL Server**.
   
   ![Selecione a origem de dados: ligar o Excel à base de dados SQL.](./media/sql-database-connect-excel/excel_data_source.png)
   
   É aberto o Assistente de Ligação de Dados.
3. Na caixa de diálogo **Ligar ao Servidor de Bases de Dados**, escreva o **Nome do servidor** da Base de Dados SQL à qual pretende ligar, no formato <*servername*>**. database.windows.net**. Por exemplo, **adworkserver.database.windows.net**.
4. Em **Credenciais de início de sessão**, clique em **Utilize o seguinte nome de utilizador e palavra-passe**, escreva o **Nome de Utilizador** e a **Palavra-passe** que configurou para o servidor de Base de Dados SQL quando o criou e, em seguida, clique em **Seguinte**.
   
   ![Escrever o nome do servidor e as credenciais de início de sessão](./media/sql-database-connect-excel/connect-to-server.png)
   
   > [!TIP]
   > Dependendo do ambiente de rede, poderá não conseguir ligar ou poderá perder a ligação se o servidor de base de dados SQL não permitir tráfego a partir do seu endereço IP de cliente. Aceda ao [portal do Azure](https://portal.azure.com/), clique em servidores SQL, clique no servidor, clique em firewall em definições e adicione o seu endereço IP de cliente. Consulte [Como configurar as definições da firewall](sql-database-configure-firewall-settings.md) para detalhes.
   > 
   > 
5. Na caixa de diálogo **Selecionar Base de Dados e Tabela**, selecione na lista a base de dados com a qual pretende trabalhar e, em seguida, clique nas tabelas ou vistas com que pretende trabalhar (iremos escolher **vGetAllCategories**) e, em seguida, clique em **Seguinte**.
   
    ![Selecione uma base de dados e a tabela.](./media/sql-database-connect-excel/select-database-and-table.png)
   
    É aberta caixa de diálogo **Guarde o ficheiro de ligação de dados e termine**, onde pode fornecer informações sobre o ficheiro de ligação da base de dados do Office (*.odc) que o Excel utiliza. Pode manter as predefinições ou personalizar as suas seleções.
6. Pode manter as predefinições, mas tenha em atenção o **Nome de Ficheiro** em particular. **Descrição**, um **Nome Amigável**, e **Procurar Palavras-chave** ajudam-no, e a outros utilizadores, a lembrar-se ao que se está a ligar a e localizar a ligação. Clique em **Tentar utilizar sempre este ficheiro para atualizar os dados** se pretender que as informações da ligação sejam armazenadas no ficheiro odc, para que possa atualizar quando ligar ao mesmo e, em seguida, clique em **Concluir**.
   
    ![Guardar um ficheiro odc](./media/sql-database-connect-excel/save-odc-file.png)
   
    A caixa de diálogo **Importar dados** é apresentada.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar os dados para o Excel e criar um gráfico dinâmico
Agora que estabeleceu a ligação e criou o ficheiro com os dados e as informações sobre a ligação, está pronto para importar os dados.

1. Na caixa de diálogo **Importar Dados**, clique na opção que pretende para apresentar os dados na folha de cálculo e, em seguida, clique em **OK**. Vamos escolher **PivotChart**. Também pode optar por criar uma **Nova folha de cálculo** ou **Adicionar estes dados ao Modelo de Dados**. Para mais informações sobre Modelos de Dados, consulte [Criar um modelo de dados no Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Clique em **Propriedades** para explorar as informações sobre o ficheiro odc que criou no passo anterior e para escolher as opções de atualização dos dados.
   
    ![Escolher o formato para dados no Excel](./media/sql-database-connect-excel/import-data.png)
   
    A folha de cálculo tem agora um gráfico e uma tabela dinâmicos em branco.
2. Em **Campos PivotTable**, selecione todas as caixas de verificação para os campos que pretende visualizar.
   
    ![Configure o relatório de base de dados.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Se pretender ligar outros livros e folhas de cálculo do Excel à base de dados, clique em **Dados**, clique em **Ligações**, clique em **Adicionar**, escolha a ligação que criou na lista e, em seguida, clique em **Abrir**.
> ![Abrir uma ligação a partir de outro livro](./media/sql-database-connect-excel/open-from-another-workbook.png)
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Saiba como [Ligar a Base de Dados SQL com o SQL Server Management Studio](sql-database-connect-query-ssms.md) para consulta e análise avançadas.
* Saiba mais sobre as vantagens dos [conjuntos elásticos](sql-database-elastic-pool.md).
* Saiba como [criar uma aplicação Web que estabelece ligação à Base de Dados SQL no back-end](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

