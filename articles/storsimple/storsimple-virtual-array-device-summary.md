---
title: Painel de resumo de dispositivo de matriz Virtual StorSimple | Microsoft Docs
description: "Descreve o painel de resumo do dispositivo para o Gestor de dispositivos do StorSimple e explica como utilizá-lo para monitorizar o estado de funcionamento a matriz de Virtual StorSimple."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 35413d597c3b6b1c7600241a78572b63f982d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Utilize o painel de resumo do dispositivo para o Gestor de dispositivos do StorSimple ligado à matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

O painel de dispositivo do Gestor de dispositivos do StorSimple fornece um resumo de uma matriz de virtuais StorSimple que está registado com uma determinada StorSimple Gestor de dispositivos, realçando os problemas com dispositivos que necessitam de atenção um administrador de sistema. Este tutorial apresenta o painel de resumo do dispositivo, explica o conteúdo e a função e descreve as tarefas que pode realizar a partir deste painel.

O painel de resumo do dispositivo apresenta as seguintes informações:

![Dashboard de dispositivo](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Gestão

No painel do dispositivo StorSimple, consulte as opções para gerir o dispositivo StorSimple. Consulte os comandos de gestão na parte superior do painel e no lado esquerdo. Utilize estas opções para adicionar partilhas ou volumes, ou atualizar ou efetuar a ativação pós-falha a matriz de virtual.

A área de essentials captura algumas das propriedades importantes, como o estado, modelo, versão do software, bem como uma hiperligação para o **IU da Web** da matriz. Se estiver numa rede interna, pode iniciar diretamente o [IU da web do local](storsimple-ova-web-ui-admin.md) para administrar a matriz de virtual.

![Essentials do dispositivo](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Resumo de dispositivo StorSimple

* O **alertas** mosaico fornece um instantâneo de todos os alertas ativos para a matriz de virtual, agrupada por gravidade do alerta. Clique no mosaico para abrir o **alertas** painel e clique num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.

* O **capacidade** mosaico mostra o armazenamento primário que está aprovisionado e restantes entre o dispositivo virtual relativo para o armazenamento total disponível para o mesmo. **Aprovisionado** refere-se para a quantidade de armazenamento que está preparado e atribuído para utilização, **restante** refere-se à capacidade restantes que pode ser aprovisionada entre este dispositivo. O **camadas restantes** capacidade é a capacidade disponível que pode ser aprovisionada, incluindo a nuvem, enquanto o **restantes Local** é a capacidade restante nos discos ligados a esta matriz virtual.

* No **utilização** gráfico, pode ver o armazenamento primário utilizado na sua matriz virtual, bem como o armazenamento de nuvem consumido nos últimos 7 dias, a predefinição de período de tempo. Utilize o **editar** opção no canto superior direito do gráfico para escolher uma escala de hora diferente.

* O **partilhas** ou **Volumes** mosaico fornece um resumo do número de partilhas ou volumes no seu dispositivo agrupados por Estado. Clique no mosaico para abrir o **partilhas** ou **Volumes** painel de lista e, em seguida, clique numa partilha individual ou um volume para ver ou modificar as respetivas propriedades. Para obter mais informações, consulte como [gerir partilhas](storsimple-virtual-array-manage-shares.md) ou [gerir volumes](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Passos seguintes
Aprenda a:
- [Gerir partilhas numa matriz Virtual StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Gerir volumes numa matriz Virtual StorSimple](storsimple-virtual-array-manage-volumes.md)

