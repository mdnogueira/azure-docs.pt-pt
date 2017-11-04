---
title: Substitua bateria em dispositivos do Microsoft Azure StorSimple | Microsoft Docs
description: "Descreve como remover, substitua e manter o módulo de bateria cópia de segurança no dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3c8a6654-4826-4883-aad8-75f332347c53
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: d1646d800692d93d7dfc2e9a9c48c3671c280e02
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Substitua o módulo de bateria cópia de segurança no dispositivo StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [substituir o módulo de bateria cópia de segurança no dispositivo StorSimple](storsimple-8000-battery-replacement.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
A inclusão principal energia e arrefecimento módulo (PCM) no dispositivo StorSimple do Microsoft Azure tem um pacote de bateria adicionais. Este pacote fornece power para que o dispositivo StorSimple pode guardar os dados se houver perda de energia de AC para o bastidor primário. Este pacote de bateria é referido como o *módulo de cópia de segurança de bateria*. O módulo de cópia de segurança de bateria existe apenas para o bastidor primário no dispositivo StorSimple (o bastidor EBOD não contém um módulo de bateria cópia de segurança). 

Este tutorial explica como:

* Remover o módulo de cópia de segurança de bateria 
* Instalar um novo módulo de cópia de segurança de bateria
* Manter o módulo de cópia de segurança de bateria

> [!IMPORTANT]
> Antes de remover e de substituir um módulo de cópia de segurança da bateria, reveja as informações de segurança no [introdução à substituição de componente de hardware do StorSimple](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="remove-the-backup-battery-module"></a>Remover o módulo de cópia de segurança de bateria
O módulo de bateria cópia de segurança para o dispositivo StorSimple é uma unidade substituível em campo. Antes de este estar instalado o PCM, o módulo de bateria deve ser armazenado no respetivo empacotamento original. Execute os seguintes passos para remover a cópia de segurança da bateria.

#### <a name="to-remove-the-backup-battery-module"></a>Para remover o módulo de cópia de segurança de bateria
1. No portal clássico do Azure, aceda a **dispositivos** > **manutenção** > **estado do Hardware**. Em **partilhado componentes**, ver o estado da bateria.
2. Identifica PCM na qual a bateria falhou. Figura 1 mostra a cópia de segurança do dispositivo StorSimple.
   
    ![Backplane dos módulos de inclusão principal do dispositivo](./media/storsimple-battery-replacement/IC740994.png)
   
    **Figura 1** Back of Mostrar módulos PCM e o controlador de dispositivo primário
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
   
    Como apresentado pelo número 3 na figura 2, o indicador de monitorização GUIADO no PCM 0 corresponde a **falhas de bateria** deve ser lit.
   
    ![Backplane do dispositivo PCM monitorização indicador LEDs](./media/storsimple-battery-replacement/IC740992.png)
   
    **Figura 2** PCM do Back que mostra o indicador de monitorização LEDs
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia de AC |
   | 2 |Falha de ventoinha |
   | 3 |Falhas de bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia de DC |
   | 6 |Bom estado de funcionamento de bateria |
3. Para remover o PCM com uma falha da bateria, siga os passos no [remover um PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).
4. Com o PCM removido, de comparação de precisão rodar o identificador de módulo de bateria para cima, conforme indicado na figura seguinte e solicitar a até remover a bateria.
   
    ![A remover a bateria do PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Figura 3** a remover a bateria do PCM
5. Colocar o módulo a unidade substituível em campo empacotamento.
6. Devolva a unidade danificada para a Microsoft para manutenção e tratamento adequado.

## <a name="install-a-new-backup-battery-module"></a>Instalar um novo módulo de cópia de segurança de bateria
Execute os seguintes passos para instalar o módulo de bateria de substituição no PCM na inclusão principal do dispositivo StorSimple.

#### <a name="to-install-the-battery-module"></a>Para instalar o módulo de bateria
1. Colocar o módulo de cópia de segurança de bateria a orientação adequada no PCM.
2. Prima o identificador de módulo de bateria para seat o conector.
3. Substitua o PCM na inclusão principal ao seguir as diretrizes no [substituir um energia e arrefecimento módulo no dispositivo StorSimple](storsimple-power-cooling-module-replacement.md).
4. Depois de concluída a substituição, aceda a **dispositivos** > **manutenção** > **estado do Hardware** no portal clássico do Azure. Verifique o estado da bateria para se certificar de que a instalação foi concluída com êxito. Estado verde indica que a bateria está em bom estada.

## <a name="maintain-the-backup-battery-module"></a>Manter o módulo de cópia de segurança de bateria
No dispositivo StorSimple, o módulo de cópia de segurança de bateria fornece energia para o controlador durante um evento de perda de energia. Permite que o dispositivo StorSimple guardar dados críticos antes de ser encerrado de forma controlada. Com dois baterias totalmente debitadas nos PCMs, o sistema pode processar dois eventos de perda consecutivos.

No portal clássico do Azure, o **estado do Hardware** no **manutenção** página indica se a bateria é funcionar incorretamente ocorrer sistematicamente ou o fim-de-vida está a aproximar-se. O estado de bateria é indicado pelo **bateria no PCM 0** ou **bateria na PCM 1** em **partilhado componentes**. Esta página irá mostrar um **DEGRADED** estado para atingir o fim-de-vida, e **falha** para fim-de-vida atingido. 

> [!NOTE]
> Pode reportar a bateria **falha** quando simplesmente tem de ser cobrado.
> 
> 

Se o **DEGRADED** estado é apresentado, recomendamos o seguinte método de ação:

* O sistema poderá ter teve uma falha de energia recente ou poderão estar sob as baterias manutenção periódica. Observe o sistema para 12 horas antes de continuar.
  
  * Se ainda não o estado **DEGRADED** após 12 horas de ligação contínua a AC de energia com os controladores e PCMs em execução, em seguida, a bateria tem de ser substituído. . [Contacte a Microsoft Support](storsimple-contact-microsoft-support.md) para um módulo de bateria cópia de segurança de substituição.
  * Se o estado fica OK decorridas 12 horas, a bateria está operacional e -necessárias apenas cobrada uma taxa de manutenção.
* Se não tiver ocorrido uma falha associada de energia de AC e o PCM está ativado e ligado à potência de AC, a bateria tem de ser substituído. [Contacte o Support Microsoft](storsimple-contact-microsoft-support.md) ordenar um módulo de bateria cópia de segurança de substituição.

> [!IMPORTANT]
> Elimine a bateria falhada em conformidade com regulamentos national e regionais. 
> 
> 

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-hardware-component-replacement.md).

