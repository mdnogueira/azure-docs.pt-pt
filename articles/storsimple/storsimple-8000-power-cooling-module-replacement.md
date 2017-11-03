---
title: "Substituir um PCM no seu dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: "Explica como remover e substituir a energia e arrefecimento módulo (PCM) no dispositivo StorSimple"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 7d181e6e434c998573dbea4b541cfacf7a28ee66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Substituir um energia e arrefecimento módulo no dispositivo StorSimple
## <a name="overview"></a>Descrição geral
A energia e arrefecimento módulo (PCM) no seu dispositivo do Microsoft Azure StorSimple é composta por uma fonte de alimentação e arrefecimento ventoinhas que são controladas através do site primário e inclusões EBOD. Não há só um modelo de PCM está certificado para cada bastidor. A inclusão principal está certificada para um PCM W 764 e a inclusão EBOD está certificada para um PCM W 580. Embora os PCMs para a inclusão principal e a inclusão EBOD forem diferentes, o procedimento de substituição é idêntico.

Este tutorial explica como:

* Remover um PCM
* Instalar um substituto PCM

> [!IMPORTANT]
> Antes de remover e de substituir um PCM, reveja as informações de segurança no [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Antes de substituir um PCM
Tenha em atenção os seguintes problemas importantes antes de substituir o seu PCM:

* Se a fonte de alimentação o PCM falhar, deixe o defeituoso módulo instalado, mas a remover o cabo de energia. A ventoinha irá continuar a receber a energia da inclusão e continuar a fornecer arrefecimento adequado. Se a ventoinha falhar, o PCM tem de ser substituído imediatamente.
* Antes de remover o PCM, desligue a potência a PCM desativar o comutador principal (se presente) ou removendo fisicamente o cabo de energia. Esta opção fornece um aviso para o sistema que está imminent um encerramento de energia.
* Certifique-se de que as outra PCM está funcional para a operação de sistema contínuo antes de substituir o PCM defeituoso. Um PCM defeituoso têm de ser substituída por um PCM totalmente operacional logo que possível.
* Substituição de módulo PCM demora apenas alguns minutos a concluir, mas têm de ser preenchido com 10 minutos de remover o PCM falhada para impedir o sobreaquecimento.
* Tenha em atenção que os módulos de W PCM 764 substituição fornecidos da fábrica de não contêm o módulo de bateria cópia de segurança. Terá de remover a bateria do seu PCM defeituoso e, em seguida, insira-o para o módulo de substituição antes de efetuar a substituição. Para obter mais informações, consulte como [remova o e insira um módulo de cópia de segurança de bateria](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Remover um PCM
Siga estas instruções quando estiver pronto para remover um energia e arrefecimento módulo (PCM) a partir do dispositivo Microsoft Azure StorSimple.

> [!NOTE]
> Antes de remover o PCM, certifique-se de que tem uma substituição correta (764 W para o bastidor primário) ou 580 W para o bastidor EBOD.

#### <a name="to-remove-a-pcm"></a>Para remover um PCM
1. No portal clássico do Azure, clique em **definições > monitorização > Estado de funcionamento do Hardware**. Verificar o estado dos componentes PCM em **partilhado componentes** para identificar que PCM falhou:
   
   * Se uma fonte de alimentação PCM 0 falhou, o estado de **fonte de alimentação no PCM 0** será vermelho.
   * Se uma fonte de alimentação PCM 1 falhou, o estado de **fonte de alimentação na PCM 1** será vermelho.
   * Se a ventoinha na PCM 1 falhou, o estado do **arrefecimento 0 para PCM 0** ou **arrefecimento 1 para PCM 0** será vermelho.
2. Localize o PCM falha na cópia de segurança da inclusão principal. Se estiver a executar um modelo de 8600, identifique o bastidor primário ao observar o número de identificação de unidade de sistema apresentadas no painel frontal LED de apresentação. A predefinição é de ID de unidade apresentado na caixa primária **00**, enquanto que a predefinição é de ID de unidade apresentado na caixa EBOD **01**. O diagrama e tabela seguintes explicam o painel frontal da apresentação de LED.
   
    ![ID de sistema no painel frontal do OPS Manager](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Figura 1** painel de início do dispositivo  
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Botão Mute |
   | 2 |Energia do sistema |
   | 3 |Falha do módulo |
   | 4 |Índice de falhas lógica |
   | 5 |Apresentação de ID de unidade |
3. O indicador de monitorização LEDs in the back of a inclusão principal também pode ser utilizado para identificar o PCM defeituoso. Consulte o seguinte diagrama e tabela para compreender como utilizar os LEDs para localizar o PCM defeituoso. Por exemplo, se o LED correspondente a **ventoinha falhar** é lit, a ventoinha falhou. Da mesma forma, se o LED correspondente **AC falhar** é lit, a fonte de alimentação falhou. 
   
    ![Backplane do indicador de monitorização do dispositivo PCM LEDs](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Figura 2** do Back PCM com indicador LEDs
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia de AC |
   | 2 |Falha de ventoinha |
   | 3 |Falhas de bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia de DC |
   | 6 |Bom estado de funcionamento de bateria |
4. Consulte o diagrama seguinte de cópia de segurança do dispositivo StorSimple para localizar o módulo PCM falhado. PCM 0 é à esquerda e PCM 1 é à direita. A tabela seguinte explica os módulos.
   
     ![Backplane dos módulos de inclusão principal do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Figura 3** Back of dispositivo com módulos de plug-in 
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
5. Desative o PCM defeituoso e desligue o cabo de alimentação de energia. Agora pode remover o PCM.
6. GRASP o bloqueio temporário e do lado do identificador PCM entre o botão do controlo e forefinger e squeeze-los em conjunto para abrir o identificador.
   
    ![Abrir PCM identificador](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Figura 4** abrir o identificador PCM
7. Grip o identificador e remova o PCM.
   
    ![Remover dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Figura 5** remover o PCM

## <a name="install-a-replacement-pcm"></a>Instalar um substituto PCM
Siga estas instruções para instalar um PCM no dispositivo StorSimple. Certifique-se de que introduziu o módulo de bateria cópia de segurança antes de instalar a substituição PCM (aplica-se a 764 PCMs W apenas). Para obter mais informações, consulte como [remova o e insira um módulo de cópia de segurança de bateria](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Para instalar um PCM
1. Certifique-se de que tem o PCM de substituição correta para este inclusão. A inclusão principal tem um PCM W 764 e a inclusão EBOD tem um PCM W 580. Não deve tentar utilizar o PCM W 580 na inclusão principal ou o PCM W 764 no bastidor EBOD. A imagem seguinte mostra onde identificar estas informações sobre a etiqueta que é affixed para o PCM.
   
    ![Etiqueta PCM do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Figura 6** etiqueta PCM
2. Verifique a existência de danos ao bastidor, prestando especial atenção para os conectores. 
   
   > [!NOTE]
   > **Não instale o módulo se são bent qualquer pins de conector.**
   > 
   > 
3. Com o identificador PCM na posição aberta, deslize o módulo para a inclusão.
   
    ![Instalar o dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **A figura 7** instalar o PCM
4. Feche manualmente o identificador PCM. Deve ouvir um clique, envolva o bloqueio temporário identificador.
   
   > [!NOTE]
   > Para garantir que os pins de conector tem parte, pode tug gently o identificador de sem a libertar o bloqueio temporário. Se o PCM slides out, isso implica que o bloqueio temporário foi fechado antes que os conectores parte.
   
5. Ligue os cabos de energia para a origem de energia e o PCM.
6. Proteja o strain relief bales.
7. Ative o PCM.
8. Certifique-se de que a substituição foi concluída com êxito: no portal do Azure do seu serviço do Gestor de dispositivos do StorSimple, navegue para o seu dispositivo e, em seguida, **definições > monitorização > Estado de funcionamento do Hardware**. Sob o **partilhado componentes**, o estado do PCM deve estar verde.
   
   > [!NOTE]
   > Pode demorar alguns minutos para que a substituição PCM ao inicializar totalmente.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

