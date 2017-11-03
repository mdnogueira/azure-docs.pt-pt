---
title: Gerir registos de controlo de acesso no StorSimple | Microsoft Docs
description: "Descreve como utilizar os registos de controlo de acesso (ACRs) para determinar os anfitriões que podem ligar a um volume no dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Utilizar o serviço StorSimple Manager para gerir registos de controlo de acesso

## <a name="overview"></a>Descrição geral
Registos de controlo de acesso (ACRs) permitem-lhe especificar os anfitriões que podem ligar a um volume no dispositivo StorSimple. ACRs estão definidas para um volume específico e incluem os nomes de qualificado iSCSI (IQNs) de anfitriões. Quando um anfitrião tenta ligar a um volume, o dispositivo verificará se existe o ACR associados a esse volume para o nome IQN e se existir uma correspondência, em seguida, a ligação for estabelecida. Regista o controlo de acesso no **configuração** secção do painel de serviço do Gestor de dispositivos do StorSimple apresentar todos os registos de controlo de acesso com os IQNs correspondentes dos anfitriões.

Este tutorial explica as seguintes tarefas de relacionados com o ACR comuns:

* Adicionar um registo de controlo de acesso
* Editar um registo de controlo de acesso
* Elimine um registo de controlo de acesso

> [!IMPORTANT]
> * Ao atribuir um ACR a um volume, asseguramos que o volume não estiver simultaneamente acedido por mais do que um anfitrião agrupado porque esta foi danificado o volume.
> * Quando eliminar um ACR a partir de um volume, certifique-se de que o anfitrião correspondente está não a aceder ao volume porque a eliminação pode resultar numa interrupção de leitura e escrita.

## <a name="get-the-iqn"></a>Obter o IQN

Execute os passos seguintes para obter o IQN de um anfitrião do Windows que está a executar o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Adicionar um registo de controlo de acesso
Utilizar o **configuração** secção no painel de serviço do Gestor de dispositivos do StorSimple para adicionar ACRs. Normalmente, irá associar um ACR um volume.

Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Aceda ao seu serviço do Gestor de dispositivos do StorSimple, faça duplo clique o nome do serviço e, em seguida, dentro de **configuração** secção, clique em **registos de controlo de acesso**.
2. No **registos de controlo de acesso** painel, clique em **+ adicionar ACR**.

    ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. No **adicionar ACR** painel, efetue os seguintes passos:

    1. Forneça um nome para o ACR.
    
    2. Forneça o nome IQN do anfitrião do Windows Server em **iSCSI iniciador. o nome (IQN)**.

    3. Clique em **adicionar** para criar o ACR.

        ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  O ACR adicionado recentemente será apresentado na tabela a listagem das ACRs.

    ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Editar um registo de controlo de acesso
Utilizar o **configuração** secção no painel de serviço do Gestor de dispositivos do StorSimple para editar ACRs.

> [!NOTE]
> Recomenda-se que modificar apenas esses ACRs que estão atualmente não em utilização. Para editar um ACR associado um volume que está a ser utilizada, primeiro tem de seguir o volume offline.

Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-access-control-record"></a>Para editar um registo de controlo de acesso
1.  Aceda ao seu serviço do Gestor de dispositivos do StorSimple, faça duplo clique o nome do serviço e, em seguida, dentro de **configuração** secção, clique em **registos de controlo de acesso**.

    ![Aceda a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na tabela listagem dos registos de controlo de acesso, clique e selecione o ACR que pretende modificar.

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr1.png)

3. No **editar registo de controlo de acesso** painel, forneça um IQN diferentes correspondente para outro anfitrião.

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Clique em **Guardar**. Quando lhe for pedida a confirmação, clique em **Sim**. 

    ![Editar registos de controlo de acesso](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Será notificado quando o ACR é atualizado. A tabela listagem também atualiza a refletir a alteração.

   
## <a name="delete-an-access-control-record"></a>Elimine um registo de controlo de acesso
Utilizar o **configuração** secção no painel de serviço do Gestor de dispositivos do StorSimple para eliminar ACRs.

> [!NOTE]
> Pode eliminar apenas esses ACRs que estão atualmente não em utilização. Para eliminar um ACR associado um volume que está a ser utilizada, primeiro tem de seguir o volume offline.

Execute os seguintes passos para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar um registo de controlo de acesso
1.  Aceda ao seu serviço do Gestor de dispositivos do StorSimple, faça duplo clique o nome do serviço e, em seguida, dentro de **configuração** secção, clique em **registos de controlo de acesso**.

    ![Aceda a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na tabela listagem dos registos de controlo de acesso, clique e selecione o ACR que pretende eliminar.

    ![Aceda a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Contexto para invocar o menu de contexto e selecione **eliminar**.

    ![Aceda a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Quando lhe for pedido para confirmação, reveja as informações e, em seguida, clique em **eliminar**.

    ![Aceda a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Será notificado quando tiver concluído a eliminação. A listagem de tabela é atualizada para refletir a eliminação.

    ![Aceda a registos de controlo de acesso](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [gerir volumes do StorSimple](storsimple-8000-manage-volumes-u2.md).
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

