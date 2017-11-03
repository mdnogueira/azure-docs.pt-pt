---
title: Restaurar o Azure SQL Data Warehouse (portal do Azure) | Microsoft Docs
description: Tarefas do portais do Azure para restaurar o Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 09/21/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: f6bc8671410dc7015a8d2a4bea1ba11f9ae526c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Restaurar o Azure SQL Data Warehouse (portal)
> [!div class="op_single_selector"]
> * [Descrição geral][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
Neste artigo, irá aprender a restaurar o Azure SQL Data Warehouse, utilizando o portal do Azure.

## <a name="before-you-begin"></a>Antes de começar
**Certifique-se a capacidade DTU.** Cada instância do SQL Data Warehouse está hospedada num servidor de SQL Server (por exemplo, myserver.database.windows.net) que tem uma quota de (DTU) de unidade de débito de dados predefinida. Antes, pode restaurar o SQL Data Warehouse, certifique-se de que o servidor SQL tem suficiente restante quota de DTU para a base de dados que está a restaurar. Para saber como calcular a quota de DTU ou pedir mais DTUs, consulte [pedir uma alteração de quota DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Restaurar uma base de dados do Active Directory ou em pausa
Para restaurar uma base de dados:

1. Inicie sessão no [Portal do Azure][Azure portal].
2. No painel esquerdo, selecione **procurar**e, em seguida, selecione **servidores SQL**.

    ![Selecione Procurar > servidores SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Localizar o servidor e, em seguida, selecioná-lo.

    ![Selecione o seu servidor](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Localizar a instância do SQL Data Warehouse que pretende restaurar a partir de e, em seguida, selecioná-lo.

    ![Selecione a instância do armazém de dados do SQL Server para restaurar](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Na parte superior do painel do armazém de dados, selecione **restaurar**.

    ![Selecione o restauro](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Especifique um novo **nome de base de dados**.
7. Selecione a versão mais recente **ponto de restauro**.

   Certifique-se de que escolhe o último ponto de restauro. Porque os pontos de restauro são apresentados na hora Universal Coordenada (UTC), a opção predefinida poderá não ser o último ponto de restauro.

      ![Selecione um ponto de restauro](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Selecione **OK**.
9. O processo de restauro da base de dados começará e pode utilizar **notificações** para monitorizar o processo.

> [!NOTE]
> Depois de terminar o restauro, pode configurar a base de dados recuperada, seguindo [configurar a base de dados após a recuperação][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada
Para restaurar uma base de dados eliminada:

1. Inicie sessão no [Portal do Azure][Azure portal].
2. No painel esquerdo, selecione **procurar**e, em seguida, selecione **servidores SQL**.

    ![Selecione Procurar > servidores SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Localizar o servidor e, em seguida, selecioná-lo.

    ![Selecione o seu servidor](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Desloque para baixo até o **operações** secção no painel do seu servidor.
5. Selecione o **eliminou bases de dados** mosaico.

    ![Selecione o mosaico de bases de dados eliminadas](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Selecione a base de dados eliminada que pretende restaurar.

    ![Selecione uma base de dados a restaurar](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Especifique um novo **nome de base de dados**.

    ![Adicionar um nome para a base de dados](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Selecione **OK**.
9. O processo de restauro da base de dados começará e pode utilizar **notificações** para monitorizar o processo.

> [!NOTE]
> Para configurar a base de dados após o restauro foi concluída, consulte [configurar a base de dados após a recuperação][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as funcionalidades de continuidade do negócio das edições do SQL Database do Azure, leia o [descrição geral da continuidade de negócio da linha de base de dados do Azure SQL][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
