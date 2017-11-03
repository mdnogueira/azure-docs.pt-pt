---
title: "Painel de resumo de serviço de matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve o painel de resumo de serviço para o Gestor de dispositivos do StorSimple e explica como utilizá-lo para monitorizar o estado de funcionamento a matriz de Virtual StorSimple."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Utilize o painel de resumo de serviço para o Gestor de dispositivos do StorSimple ligado à matriz Virtual StorSimple
## <a name="overview"></a>Descrição geral
O painel de resumo de serviço para o Gestor de dispositivos do StorSimple fornece um resumo do StorSimple Virtual matrizes (também conhecido como StorSimple no local dispositivos virtuais ou dispositivos virtuais) que estão ligadas ao seu serviço, realçando aqueles que necessitam de atenção um administrador de sistema. Este tutorial apresenta o painel de resumo de serviço, explica o conteúdo e a função e descreve as tarefas que pode realizar a partir deste painel.

![Dashboard de serviço](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Comandos de gestão e essentials
No painel de resumo do StorSimple, consulte as opções para gerir o seu serviço do Gestor de dispositivos do StorSimple, bem como as matrizes de virtuais registadas para este serviço. Consulte os comandos de gestão na parte superior do painel e no lado esquerdo.

Utilize estas opções para desempenhar várias operações como adicionar partilhas ou volumes ou monitor várias tarefas em execução nas matrizes de virtuais.

A área de essentials captura algumas das propriedades importantes, como o grupo de recursos, a localização e a subscrição no qual foi criado o seu dispositivo do StorSimple Manager.

## <a name="storsimple-device-manager-service-summary"></a>Resumo de serviço do Gestor de dispositivos do StorSimple
* O **alertas** mosaico fornece um instantâneo de todos os alertas ativos em todos os dispositivos virtuais, agrupados por gravidade do alerta. Clicar no mosaico abre o **alertas** painel, onde pode clicar num alerta individual para ver detalhes adicionais sobre este alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.
* O **capacidade** apresenta mosaico mostra o armazenamento primário que está aprovisionado e restantes em todos os dispositivos virtuais relativo para o armazenamento total disponível em todos os dispositivos virtuais. **Aprovisionado** refere-se para a quantidade de armazenamento que está preparado e atribuído para utilização, **restante** refere-se à capacidade restantes que pode ser aprovisionada em todos os dispositivos virtuais. O **camadas restantes** capacidade é a capacidade disponível que pode ser aprovisionada, incluindo a nuvem, enquanto o **restantes Local** é a capacidade restante nos discos ligados para as matrizes de virtuais.
* No **utilização** gráfico, pode ver as métricas relevantes para os seus dispositivos virtuais. Pode ver o armazenamento primário utilizado em todos os dispositivos virtuais, bem como o armazenamento de nuvem consumidos pelos dispositivos virtuais nos últimos 7 dias, a predefinição de período de tempo. Utilize o **editar** opção no canto superior direito do gráfico para escolher uma escala de hora diferente.
* O **dispositivos** mosaico fornece um resumo do número de matrizes virtuais no seu StorSimple Gestor de dispositivos agrupados por Estado do dispositivo. Clique neste mosaico para abrir o **dispositivos** painel de lista e, em seguida, clique num dispositivo individual para explorar o resumo de dispositivo específico para o dispositivo. Também pode efetuar ações específicas do dispositivo a partir do painel de resumo determinado dispositivo. Para obter mais informações sobre o painel de resumo do dispositivo, vá para [painel Resumo do dispositivo](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Consulte os registos de atividade
Para ver as várias operações executadas dentro do Gestor de dispositivos do StorSimple, clique o **registos de atividade** ligação no lado esquerdo do painel de resumo de serviço do StorSimple. Isto leva-o para o **registos de atividade** painel, onde pode ver um resumo das operações recentes realizadas.

![Registos de Atividade](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Passos seguintes
Saiba como [utilizar a IU da web local para administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

