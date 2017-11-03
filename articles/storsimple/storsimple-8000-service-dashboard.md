---
title: "Dispositivos de série de 8000 do StorSimple utilize resumo | Microsoft Docs"
description: "Descreve o painel de resumo do serviço StorSimple e explica como utilizá-lo para monitorizar o estado de funcionamento da solução StorSimple."
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: d987a4ae170f21532a886552cbe1eb5a0d25fc3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Utilize o painel de resumo de serviço para o dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral

O painel de resumo do Gestor de dispositivos do StorSimple serviço fornece um resumo de todos os dispositivos que estão ligados ao serviço do Gestor de dispositivos do StorSimple, realçando os dispositivos que necessitam de atenção um administrador de sistema. Este tutorial apresenta o painel de resumo de serviço, explica o conteúdo de dashboard e a função e descreve as tarefas que pode realizar a partir desta página.

![Resumo de serviço](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Comandos de gestão

No painel Resumo de serviço do StorSimple, consulte as opções para gerir o serviço StorSimple Manager de dispositivo e os dispositivos de série 8000 do StorSimple registados para este serviço. Consulte os comandos de gestão na parte superior do painel e no lado esquerdo.

![Barra de comandos](./media/storsimple-8000-service-dashboard/service-summary2.png)

Utilize estas opções para desempenhar várias operações como adicionar partilhas ou volumes ou monitor várias tarefas em execução nos dispositivos StorSimple.


## <a name="essentials"></a>Essentials

A área de essentials captura algumas das propriedades importantes, como o grupo de recursos, a localização e a subscrição no qual foi criado o seu dispositivo do StorSimple Manager.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Resumo de serviço do Gestor de dispositivos do StorSimple

* O **alertas** mosaico fornece um instantâneo de todos os alertas ativos em todos os dispositivos, agrupados por gravidade do alerta.

    ![Mosaico alertas](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Clicar no mosaico abre o **alertas** painel, onde pode clicar num alerta individual para ver detalhes adicionais sobre este alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.

    ![Clique no mosaico alertas](./media/storsimple-8000-service-dashboard/service-summary8.png)

* O **capacidade** apresenta mosaico mostra o armazenamento primário que está aprovisionado e restantes em todos os dispositivos em relação ao armazenamento total disponível em todos os dispositivos. **Aprovisionado** refere-se para a quantidade de armazenamento que está preparado e atribuído para utilização, **restante** refere-se à capacidade restantes que pode ser aprovisionada em todos os dispositivos.

    ![Mosaico de capacidade](./media/storsimple-8000-service-dashboard/service-summary6.png)

    O **camadas restantes** capacidade é a capacidade disponível que pode ser aprovisionada, incluindo a nuvem, enquanto o **restantes Local** é a capacidade restante nos discos ligados aos dispositivos de série 8000 do StorSimple.


* No **utilização** gráfico, pode ver as métricas relevantes para os seus dispositivos. Pode ver o armazenamento primário utilizado em todos os dispositivos e o armazenamento de nuvem consumidos pelos dispositivos nos últimos 7 dias, a predefinição de período de tempo. 

    ![Mosaico utilização](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Para escolher uma escala de hora diferente, utilize o **editar** opção no canto superior direito do gráfico.

     ![Clique no mosaico utilização](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportar dados do gráfico](./media/storsimple-8000-service-dashboard/service-summary11.png)

* O **dispositivos** mosaico fornece um resumo do número de dispositivos de série 8000 do StorSimple no seu StorSimple Gestor de dispositivos agrupados por Estado do dispositivo. 

    ![Mosaico de dispositivos](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Clique neste mosaico para abrir o **dispositivos** painel de lista e, em seguida, clique num dispositivo individual para explorar o resumo de dispositivo específico para o dispositivo. Também pode efetuar ações específicas do dispositivo a partir do painel de resumo determinado dispositivo. Para obter mais informações sobre o painel de resumo do dispositivo, vá para [painel Resumo do dispositivo](storsimple-8000-device-dashboard.md).

    ![Clique no mosaico dispositivos](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Consulte os registos de atividade

Para ver as várias operações executadas dentro do Gestor de dispositivos do StorSimple, clique o **registos de atividade** ligação no lado esquerdo do painel de resumo de serviço do StorSimple. Isto leva-o para o **registos de atividade** painel, onde pode ver um resumo das operações recentes realizadas.

![Registos de Atividade](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

