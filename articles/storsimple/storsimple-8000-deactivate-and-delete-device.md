---
title: "Desativar e eliminar um dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve como remover o dispositivo StorSimple do serviço ao desativar-o primeiro e, em seguida, eliminá-lo."
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Desativar e eliminar um dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve como desativar e eliminar um dispositivo StorSimple que está ligado a um serviço do Gestor de dispositivos do StorSimple. As orientações neste artigo aplica-se apenas a dispositivos de série 8000 do StorSimple, incluindo os dispositivos de nuvem do StorSimple. Se estiver a utilizar uma matriz de Virtual StorSimple, em seguida, aceda a [desativar e eliminar uma matriz de Virtual StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

A desativação de servidores a ligação entre o dispositivo e o serviço Gestor de dispositivos do StorSimple correspondente. Poderá pretender assumir um dispositivo StorSimple fora de serviço (por exemplo, se estiver a substituir ou atualizar o seu dispositivo ou se já não estiver a utilizar StorSimple). Se Sim, terá de desativar o dispositivo antes de poder eliminá-la.

Quando desativar um dispositivo, já não estão acessíveis a todos os dados que foram armazenados localmente no dispositivo. Apenas os dados associados ao dispositivo que foram armazenado na nuvem podem ser recuperados.

> [!WARNING]
> A desativação de uma operação permanente e não pode ser anulada. Não é possível registar um dispositivo desativado com o serviço do Gestor de dispositivos do StorSimple, a menos que este é reposto para as predefinições de fábrica.
>
> O reposição de fábrica processo elimina todos os dados que foram armazenados localmente no seu dispositivo. Por conseguinte, tem de colocar um instantâneo de nuvem de todos os seus dados antes de desativar um dispositivo. Este instantâneo na nuvem permite-lhe recuperar todos os dados de uma fase posterior.

Depois de ler este tutorial, será capaz de:

* Desativar um dispositivo e eliminar os dados.
* Desativar um dispositivo e manter os dados.

> [!NOTE]
> Antes de desativar um dispositivo físico StorSimple ou um dispositivo de nuvem, pare ou elimine a clientes e anfitriões que dependem desse dispositivo.


## <a name="deactivate-and-delete-data"></a>Desativar e eliminação de dados

Se estiver interessado em eliminar completamente o dispositivo e não pretende manter os dados no dispositivo, em seguida, conclua os seguintes passos.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e eliminar os dados

1. Antes de desativar um dispositivo, tem de eliminar o volume de contentores (e os volumes) associados ao dispositivo. Pode eliminar contentores de volume apenas depois de ter eliminado as cópias de segurança associadas.

    > [!NOTE]
    > Antes de desativar um dispositivo físico StorSimple ou um dispositivo de nuvem, certifique-se de que os dados a partir do contentor de volume eliminado, na verdade, são eliminados do dispositivo. Pode monitorizar os gráficos de consumo de nuvem e quando for apresentada a utilização de nuvem drop devido a ter eliminado as cópias de segurança, em seguida, pode avançar para desativar o dispositivo. Se desativar o dispositivo antes deste largar ocorre, os dados é stranded na conta de armazenamento e accrues encargos.

2. Desative o dispositivo da seguinte forma:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. No **dispositivos** painel, selecione o dispositivo que pretende desativar, rato e, em seguida, clique em **desativar**.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. No **desativar** painel, escreva o nome de dispositivo para confirmar e, em seguida, clique em **desativar**. O processo de desativar é iniciado e demora alguns minutos a concluir.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Após a desativação, pode eliminar completamente o dispositivo. Eliminar um dispositivo remove-o da lista de dispositivos ligados ao serviço. O serviço, em seguida, pode já não está a gerir o dispositivo foi eliminado. Utilize os seguintes passos para eliminar o dispositivo:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. No **dispositivos** painel, selecione o dispositivo desativado que pretende eliminar, rato e, em seguida, clique em **eliminar**.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. No **eliminar** painel, escreva o nome de dispositivo para confirmar e, em seguida, clique em **eliminar**. A eliminação demora alguns minutos a concluir.

        ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Após a eliminação foi concluída com êxito, será notificado. Também atualiza a lista de dispositivos para refletir a eliminação.

## <a name="deactivate-and-retain-data"></a>Desativar e manter os dados

Se tiver está interessadas nas eliminar o dispositivo, mas pretende manter os dados, em seguida, conclua os seguintes passos:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e manter os dados
1. Desative o dispositivo. Manter-se todos os contentores de volume e dos instantâneos do dispositivo.
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. No **dispositivos** painel, selecione o dispositivo que pretende desativar, rato e, em seguida, clique em **desativar**.

         ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. No **desativar** painel, escreva o nome de dispositivo para confirmar e, em seguida, clique em **desativar**. O processo de desativar é iniciado e demora alguns minutos a concluir.

         ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Agora pode efetuar a ativação pós-falha os contentores de volume e instantâneos associados. Para obter os procedimentos, vá para [ativação pós-falha e recuperação após desastre para o dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Após a desativação e a ativação pós-falha, pode eliminar completamente o dispositivo. Eliminar um dispositivo remove-o da lista de dispositivos ligados ao serviço. O serviço, em seguida, pode já não está a gerir o dispositivo foi eliminado. Para eliminar o dispositivo, conclua os seguintes passos:
   
   1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. No **dispositivos** painel, selecione o dispositivo desativado que pretende eliminar, rato e, em seguida, clique em **eliminar**.

       ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. No **eliminar** painel, escreva o nome de dispositivo para confirmar e, em seguida, clique em **eliminar**. A eliminação demora alguns minutos a concluir.

       ![Desativar o dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Após a eliminação foi concluída com êxito, será notificado. Também atualiza a lista de dispositivos para refletir a eliminação.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Desativar e eliminar uma aplicação de nuvem

Para uma aplicação de nuvem do StorSimple, a desativação do portal deallocates e elimina a máquina virtual e os recursos criados no aprovisionamento. Depois de a aplicação da cloud ter sido desativada, não é possível restaurá-la para o estado anterior.

![Desativar o dispositivo StorSimple nuvem](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Resultados de Desativação nas seguintes ações:

* A aplicação de nuvem do StorSimple é removida do serviço.
* A máquina virtual para a aplicação de nuvem do StorSimple é eliminada.
* O disco do SO e discos de dados criados para a aplicação de nuvem do StorSimple são removidos.
* O serviço alojado e a rede Virtual que foram criados durante o aprovisionamento são retidos. Se não estiver a utilizar estas entidades, deverá eliminá-los manualmente.
* Os instantâneos de nuvem criados pela aplicação de nuvem StorSimple são retidos.

Depois da aplicação de nuvem é desativada, pode eliminá-lo da lista de dispositivos. Selecione o dispositivo desativado, rato e, em seguida, clique em **eliminar**. Notifica StorSimple que depois do dispositivo é eliminado e a lista de atualizações de dispositivos.

## <a name="next-steps"></a>Passos seguintes

* Para restaurar o dispositivo desativado para as predefinições de fábrica, aceda a [repor o dispositivo para as predefinições de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Para obter assistência técnica, [contacte a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Para obter mais informações sobre como utilizar o serviço do Gestor de dispositivos do StorSimple, aceda a [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

