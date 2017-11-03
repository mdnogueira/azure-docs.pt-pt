---
title: Desativar e eliminar uma matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs
description: "Descreve como remover o dispositivo StorSimple do serviço ao desativar-o primeiro e, em seguida, eliminá-lo."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 8dea36f92b034f8c6cdb6875634848d37f4c6606
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Desativar e eliminar uma matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral

Ao desativar uma matriz de Virtual StorSimple, quebrar a ligação entre o dispositivo e o serviço Gestor de dispositivos do StorSimple correspondente. Este tutorial explica como:

* Desativar um dispositivo 
* Eliminar um dispositivo desativado

As informações neste artigo aplica-se apenas às matrizes de Virtual StorSimple. Para obter informações sobre 8000 série, aceda a como [desativar ou eliminar um dispositivo](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Quando desativar?

A desativação de uma operação permanente e não pode ser anulada. Não é possível registar um dispositivo desativado com o serviço do Gestor de dispositivos do StorSimple novamente. Poderá ter de desativar e eliminar uma matriz de Virtual StorSimple nos seguintes cenários:

* **A ativação pós-falha planeada** : O dispositivo está online e de que pretende efetuar a ativação pós-falha do seu dispositivo. Se estiver a planear atualizar para um dispositivo maior, poderá ter de efetuar a ativação pós-falha do seu dispositivo. Depois da propriedade de dados é transferida e a ativação pós-falha estiver concluída, o dispositivo de origem é eliminado automaticamente.
* **Ativação pós-falha não planeada** : O dispositivo estiver offline e terá de efetuar a ativação pós-falha do dispositivo. Este cenário poderá ocorrer durante um desastre quando houver uma falha no Centro de dados e o dispositivo primário está inativo. Planear a ativação pós-falha do dispositivo para um dispositivo secundário. Depois da propriedade de dados é transferida e a ativação pós-falha estiver concluída, o dispositivo de origem é eliminado automaticamente.
* **Desativar** : que pretende desativar o dispositivo. Isto requer que para primeiro desativar o dispositivo e, em seguida, eliminá-lo. Quando desativar um dispositivo, já não pode aceder a todos os dados que esteja armazenados localmente. Só pode aceder e recuperar os dados armazenados na nuvem. Se pretender manter os dados do dispositivo após a desativação, deve ter um instantâneo de nuvem de todos os seus dados antes de desativar um dispositivo. Este instantâneo na nuvem permite-lhe recuperar todos os dados de uma fase posterior.

## <a name="deactivate-a-device"></a>Desativar um dispositivo

Para desativar o seu dispositivo, execute os seguintes passos.

#### <a name="to-deactivate-the-device"></a>Para desativar o dispositivo

1. No seu serviço, aceda a **gestão > dispositivos**. No **dispositivos** painel, clique e selecione o dispositivo que pretende desativar.
   
    ![Selecione o dispositivo para desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. No seu **dashboard dispositivo** painel, clique em **... Mais** e na lista, selecione **desativar**.
   
    ![Clique em Desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. No **desativar** painel, escreva o nome do dispositivo e, em seguida, clique em **desativar**. 
   
    ![Confirmar a desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    O processo de desativar é iniciado e demora alguns minutos a concluir.
   
    ![Desativar em curso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Após a desativação, atualiza a lista de dispositivos.
   
    ![Desativar concluída](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Pode agora eliminar este dispositivo.

## <a name="delete-the-device"></a>Elimine o dispositivo

Um dispositivo tem de ser desativada primeiro eliminá-la. Eliminar um dispositivo remove-o da lista de dispositivos ligados ao serviço. O serviço, em seguida, pode já não está a gerir o dispositivo foi eliminado. Os dados associados ao dispositivo permanecem, no entanto, na nuvem. Estes dados accrues, em seguida, os encargos.

Para eliminar o dispositivo, execute os seguintes passos.

#### <a name="to-delete-the-device"></a>Para eliminar o dispositivo

1. No Gestor de dispositivos do StorSimple, aceda a **gestão > dispositivos**. No **dispositivos** painel, selecione um dispositivo desativado que pretende eliminar.
2. No **dashboard dispositivo** painel, clique em **... Mais** e, em seguida, clique em **eliminar**.
   
   ![Selecione o dispositivo para eliminar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. No **eliminar** painel, escreva o nome do seu dispositivo para confirmar a eliminação e, em seguida, clique em **eliminar**. Eliminar o dispositivo não elimina os dados na nuvem associados ao dispositivo. 
   
   ![Confirmar eliminação](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A eliminação é iniciado e demora alguns minutos a concluir.
   
   ![Eliminar em curso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Depois do dispositivo é eliminado, pode ver a lista atualizada de dispositivos.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre como efetuar a ativação pós-falha, aceda a [ativação pós-falha e recuperação após desastre da sua matriz de Virtual StorSimple](storsimple-virtual-array-failover-dr.md).

* Para obter mais informações sobre como utilizar o serviço do Gestor de dispositivos do StorSimple, aceda a [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar a matriz de Virtual StorSimple](storsimple-virtual-array-manager-service-administration.md). 

