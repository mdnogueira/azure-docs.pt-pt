---
title: "Gerir a capacidade de computação no Azure SQL Data Warehouse (portal do Azure) | Microsoft Docs"
description: "Tarefas de portais do Azure para gerir poder de computação. Dimensionar os recursos de computação ao ajustar as DWUs. Ou, colocar em pausa e retomar a recursos de computação para reduzir os custos."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 63888d5dd103b585cf18e4787d3e779810163e3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gerir a capacidade de computação no Azure SQL Data Warehouse (portal do Azure)
> [!div class="op_single_selector"]
> * [Descrição geral](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>


## <a name="scale-compute-power"></a>Capacidade de computação de escala
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar os recursos de computação:

1. Abra o [portal do Azure][Azure portal], abra a sua base de dados e clique em **escala**.

    ![Clique em escala][1]
2. No painel da escala, mova o controlo de deslize esquerda ou direita alterar a definição de DWU.

    ![Mover o controlo de deslize][2]
3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Clicar em Guardar][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Computação pausa
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para colocar em pausa uma base de dados:

1. Abra o [portal do Azure] [ Azure portal] e abrir a base de dados. Tenha em atenção que o estado é **Online**.

    ![Estado online][6]
2. Para suspender a recursos de computação e memória, clique em **pausa**, e, em seguida, é apresentada uma mensagem de confirmação. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Confirme a colocar em pausa][7]
3. Apesar do armazém de dados do SQL Server está a iniciar a base de dados, o estado é **Pausing**.
4. Quando o estado é **em pausa**, é realizar a operação de colocação em pausa e já não estão a ser-lhe cobrados dwus.

    ![Estado de pausa][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Retomar de computação
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para retomar a uma base de dados:

1. Abra o [portal do Azure] [ Azure portal] e abrir a base de dados. Tenha em atenção que o estado é **em pausa**.

    ![Base de dados de pausa][4]
2. Para retomar a base de dados, clique em **iniciar**, e, em seguida, é apresentada uma mensagem de confirmação. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Confirmar retomar][5]
3. Enquanto o armazém de dados do SQL Server está a iniciar a base de dados, o estado é "a retomar".
4. Quando o estado é **online**, a base de dados está pronto.

    ![Estado online][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte [descrição geral da gestão][Management overview].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
