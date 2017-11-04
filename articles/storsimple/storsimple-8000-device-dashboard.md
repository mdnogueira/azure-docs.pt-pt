---
title: "Dispositivos de série de 8000 do StorSimple utilize resumo | Microsoft Docs"
description: "Descreve o dispositivo de serviço do Gestor de dispositivos do StorSimple, resumo e como utilizá-la para ver as métricas do storage e ligados iniciadores e localizar o número de série e IQN."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 784d3ce9d8f926b00ac1c6fbf48a05c0b04f900a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Utilizar o dispositivo resumo no serviço do Gestor de dispositivos do StorSimple

## <a name="overview"></a>Descrição geral
Painel de resumo do dispositivo StorSimple dá-lhe uma descrição geral de informações para um dispositivo específico do StorSimple, contrariamente o painel de resumo de serviço, que fornece-lhe informações sobre todos os dispositivos incluídos na sua solução do Microsoft Azure StorSimple.

O painel de resumo de dispositivos fornece um resumo de um dispositivo de série 8000 do StorSimple que está registado com uma determinada StorSimple Gestor de dispositivos, realçando os problemas com dispositivos que necessitam de atenção um administrador de sistema. Este tutorial apresenta o painel de resumo do dispositivo, explica o conteúdo e a função e descreve as tarefas que pode realizar a partir deste painel.

O painel de resumo do dispositivo apresenta as seguintes informações:

![Painel de resumo do dispositivo](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Barra de comandos de gestão

No painel do dispositivo StorSimple, consulte as opções para gerir o dispositivo StorSimple. Consulte os comandos de gestão na parte superior do painel e no lado esquerdo. Utilize estas opções para adicionar partilhas ou volumes, ou atualizar ou efetuar a ativação pós-falha do seu dispositivo.

![Barra de comandos de gestão](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

A área de essentials captura algumas das propriedades importantes, como, o estado, modelo, destino IQN e a versão do software. 

![Essentials do dispositivo](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorização

* O **alertas** mosaico fornece um instantâneo de todos os alertas ativos para o seu dispositivo, agrupado por gravidade do alerta.

    ![Mosaico alerta](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Clique no mosaico para abrir o **alertas** painel e clique num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.

    ![Clique no mosaico alerta](./media/storsimple-8000-device-dashboard/device-summary10.png)

* O **estado e estado de funcionamento** mosaico fornece informações sobre o estado de funcionamento do componente de hardware para um dispositivo, incluindo o estado do dispositivo. O estado do dispositivo pode estar offline, online, desativado ou preparado para configurar.

    ![Mosaico de estado e estado de funcionamento](./media/storsimple-8000-device-dashboard/device-summary5.png)

* O **Volumes** mosaico fornece um resumo do número de volumes no seu dispositivo agrupados por Estado.

    ![Mosaico de volumes](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Clique no mosaico para abrir o **Volumes** painel de lista e, em seguida, clique num volume individual para ver ou modificar as respetivas propriedades.
    
    ![Clique no mosaico volumes](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Para obter mais informações, consulte como [gerir volumes](storsimple-8000-manage-volumes-u2.md).

* No **utilização** gráfico, pode ver o armazenamento primário utilizado entre o dispositivo e o armazenamento de nuvem consumido nos últimos 7 dias, a predefinição de período de tempo.

     ![Mosaico utilização](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Para escolher uma escala de hora diferente, utilize o **editar** opção no canto superior direito do gráfico.

     ![Editar Gráfico de utilização](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Este gráfico, pode ver as métricas para o total (a quantidade de dados escritos pelos anfitriões para o seu dispositivo) de armazenamento primário e o armazenamento de nuvem total consumido pelo seu dispositivo durante um período de tempo.
  
     Neste contexto, *armazenamento primário* refere-se para a quantidade total de dados escritos pelo anfitrião e podem ser divididos por tipo de volume: *primário camadas armazenamento* inclui os armazenadas localmente dados e os dados em camadas a nuvem. *Primário afixado localmente armazenamento* inclui apenas os dados armazenados localmente. *Armazenamento na nuvem*, por outro lado, é uma medida da quantidade total de dados armazenados na nuvem. Este tipo de armazenamento inclui dados em camadas e de cópias de segurança. Os dados armazenados na nuvem é eliminação de duplicados e comprimidos, enquanto que o armazenamento primário indica a quantidade de armazenamento utilizada antes dos dados é comprimidos e eliminação de duplicados. (Pode comparar estes dois números para obter uma ideia da taxa de compressão.) Para os dois principais e de armazenamento na nuvem, as quantidades mostrados baseiam-se na frequência de controlo que configura. Por exemplo, se escolher uma frequência de uma semana, em seguida, o gráfico mostra os dados para cada dia da semana anterior.

     Para ver a quantidade de armazenamento na nuvem consumido ao longo do tempo, selecione o **NUVEM armazenamento utilizado** opção. Para ver o armazenamento total foi escrito pelo anfitrião, selecione o **primário CAMADAS armazenamento utilizado** e **primário LOCALMENTE AFIXADO armazenamento utilizado** opções. 
     Para obter mais informações, consulte [utilizar o serviço do Gestor de dispositivos do StorSimple para monitorizar o dispositivo StorSimple](storsimple-monitor-device.md).


* O **capacidade** mosaico mostra o armazenamento primário que está aprovisionado e restantes entre o dispositivo em relação ao armazenamento total disponível para o mesmo. **Aprovisionado** refere-se para a quantidade de armazenamento que está preparado e atribuído para utilização, **restante** refere-se à capacidade restantes que pode ser aprovisionada entre este dispositivo. 

    ![Mosaico utilização](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Clique neste mosaico para ver como a capacidade é aprovisionada entre volumes em camadas e afixados localmente. O **camadas restantes** capacidade é a capacidade disponível que pode ser aprovisionada, incluindo a nuvem, enquanto o **restantes Local** é a capacidade restante nos discos ligados a este dispositivo.

    ![Clique em gráfico de utilização](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [painel de resumo de serviço do StorSimple](storsimple-8000-service-dashboard.md).
* Saiba mais sobre [utilizando o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

