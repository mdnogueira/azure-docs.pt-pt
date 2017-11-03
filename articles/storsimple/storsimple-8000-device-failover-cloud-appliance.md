---
title: "Ativação pós-falha de StorSimple, recuperação após desastre para uma aplicação de nuvem do StorSimple | Microsoft Docs"
description: "Saiba como efetuar a ativação pós-falha 8000 do StorSimple série dispositivo físico para uma aplicação de nuvem."
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Ativação pós-falha para o seu dispositivo de nuvem do StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para efetuar a ativação pós-falha de um dispositivo físico a série 8000 do StorSimple para uma aplicação de nuvem do StorSimple se ocorrer um desastre. StorSimple utiliza a funcionalidade de ativação pós-falha do dispositivo para migrar dados a partir de um dispositivo físico de origem no Centro de dados para uma aplicação de nuvem em execução no Azure. As orientações neste tutorial aplica-se a dispositivos físicos de série 8000 do StorSimple e aplicações em nuvem com versões do software Update 3 e posterior.

Para saber mais sobre a ativação pós-falha do dispositivo e como são utilizadas para recuperar de desastres, aceda a [ativação pós-falha e recuperação após desastre para os dispositivos de série 8000 do StorSimple](storsimple-8000-device-failover-disaster-recovery.md).

Para efetuar a ativação pós-falha de um dispositivo físico StorSimple para outro dispositivo físico, aceda a [ativação pós-falha para um dispositivo físico StorSimple](storsimple-8000-device-failover-physical-device.md). Para efetuar a ativação pós-falha de um dispositivo para si próprio, aceda a [falhar ao mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações de ativação pós-falha do dispositivo. Para obter mais informações, aceda a [considerações comuns para ativação pós-falha do dispositivo](storsimple-8000-device-failover-disaster-recovery.md).

- Tem de ter uma aplicação de nuvem do StorSimple criada e configurada antes de executar este procedimento. Se a execução atualizar a versão do software 3 ou posterior, considere a utilização de um dispositivo de nuvem 8020 para a DR. O modelo 8020 tem 64 TB e utiliza o armazenamento Premium. Para obter mais informações, aceda a [implementar e gerir um dispositivo de nuvem do StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Passos para efetuar a ativação pós-falha para uma aplicação de nuvem

Execute os seguintes passos para restaurar o dispositivo para um dispositivo do StorSimple nuvem de destino.

1.  Certifique-se de que o contentor de volume que pretende efetuar a ativação pós-falha associou instantâneos de nuvem. Para obter mais informações, aceda a [serviço Gestor de dispositivos do StorSimple utilizado para criar cópias de segurança](storsimple-8000-manage-backup-policies-u2.md).
2. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. No **dispositivos** painel, aceda à lista de dispositivos ligados com o serviço.
    ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Selecione e clique o dispositivo de origem. O dispositivo de origem tem os contentores de volume que pretende efetuar a ativação pós-falha. Aceda a **definições > contentores de Volume**.

    ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Selecione um contentor de volume que pretende efetuar a ativação pós-falha para outro dispositivo. Clique no contentor de volume para apresentar a lista de volumes dentro deste contentor. Selecione um volume, rato e clique em **Colocar Offline** para colocar offline o volume.

    ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Repita este processo para todos os volumes no contentor de volume.

     ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Repita o passo anterior para todos os contentores de volume que pretende efetuar a ativação pós-falha para outro dispositivo.

7. Volte à **dispositivos** painel. Na barra de comandos, clique em **falhar**.

    ![Clique em ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. No **falhar** painel, execute os seguintes passos:
   
    1. Clique em **origem**. Selecione os contentores de volume para a ativação pós-falha. **Apenas os contentores de volume com instantâneos de nuvem associados e offline volumes são apresentados.**
        ![Selecione a origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Clique em **destino**. Selecione uma aplicação de nuvem de destino na lista pendente de dispositivos disponíveis. **Apenas os dispositivos que tem capacidade suficiente para acomodar os contentores de volume de origem são apresentados na lista.**

        ![Selecione o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Reveja as definições de ativação pós-falha em **resumo** e selecione a caixa de verificação que indica que os volumes nos contentores de volume selecionado estão offline. 

        ![Reveja as definições de ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. É criada uma tarefa de ativação pós-falha. Para monitorizar a tarefa de ativação pós-falha, clique na notificação da tarefa.

    ![Tarefa de ativação pós-falha de monitorização](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Uma vez concluída a ativação pós-falha, volte atrás para o **dispositivos** painel.

    1. Selecione o dispositivo que foi utilizado como o destino para a ativação pós-falha.

       ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Clique em **contentores de Volume**. Todos os contentores volume, juntamente com os volumes do dispositivo antigo, deverá estar listados.

       Se o contentor de volume, ativação pós-falha tenha afixado localmente volumes, os volumes são a ativação pós-falha como volumes em camadas. Volumes localmente afixados não são suportadas num dispositivo StorSimple em nuvem.

       ![Contentores de volume de destino de vista](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Passos seguintes

* Depois de ter de efetuar uma ativação pós-falha, poderá ser necessário [desative ou elimine o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Para informações sobre como utilizar o serviço do Gestor de dispositivos do StorSimple, aceda a [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

