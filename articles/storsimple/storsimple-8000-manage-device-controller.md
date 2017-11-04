---
title: "Gerir controladores de dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: Saiba como parar, reiniciar, encerre ou repor os controladores de dispositivo StorSimple.
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
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>Gerir os controladores de dispositivo do StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve as operações diferentes que podem ser efetuadas nos controladores de dispositivo StorSimple. Os controladores de dispositivo StorSimple são controladores redundante (ponto a ponto) uma configuração de ativo-passivo. Num determinado momento, apenas um controlador está ativo e está a processar todas as operações de disco e rede. O controlador de outro está num modo passivo. Se o controlador de Active Directory falhar, o controlador passivo automaticamente fica ativo.

Este tutorial inclui instruções passo a passo para gerir os controladores de dispositivo utilizando o:

* **Controladores** painel para o seu dispositivo no serviço do Gestor de dispositivos do StorSimple.
* Windows PowerShell para StorSimple.

Recomendamos que gere os controladores de dispositivo através do serviço do Gestor de dispositivos do StorSimple. Se uma ação só pode ser efetuada através do Windows PowerShell para StorSimple, o tutorial faz uma nota do mesmo.

Depois de ler este tutorial, será capaz de:

* Reiniciar ou encerrar um controlador de dispositivo do StorSimple
* Encerrar um dispositivo StorSimple
* Repor o dispositivo StorSimple para as predefinições de fábrica

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou encerrar um único controlador
Um controlador reinício ou encerramento não é necessário como parte da operação do sistema normal. Operações de encerramento para um controlador de dispositivo único são comuns apenas nos casos em que um componente de hardware do dispositivo falhou requer substituição. Também poderá ser necessário um reinício do controlador uma situação em que o desempenho é afetado pela utilização de memória excessiva ou um controlador malfunctioning. Também ser necessário reiniciar um controlador depois de uma substituição de controlador com êxito, se pretender ativar e testar o controlador substituído.

Reiniciar um dispositivo não é acontece aos iniciadores ligados, assumindo que o controlador passivo está disponível. Se um controlador passivo não estiver disponível ou ativado desativado, em seguida, reiniciar o controlador de Active Directory poderão resultar na interrupção do serviço e o período de indisponibilidade.

> [!IMPORTANT]
> * **Nunca deve ser removido fisicamente um controlador em execução como isto resultaria em perda de redundância e um maior risco de indisponibilidade.**
> * O procedimento seguinte aplica-se apenas para o dispositivo físico StorSimple. Para obter informações sobre como iniciar, parar e reiniciar a aplicação de nuvem do StorSimple, consulte [trabalhar com a aplicação de nuvem](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Pode reiniciar ou encerrar um controlador de dispositivo único através do portal do Azure do serviço do Gestor de dispositivos do StorSimple ou do Windows PowerShell para StorSimple.

Para gerir os controladores de dispositivo do portal do Azure, execute os seguintes passos.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Para reiniciar ou encerrar um controlador no portal do Azure
1. No seu serviço do Gestor de dispositivos do StorSimple, aceda a **dispositivos**. Selecione o seu dispositivo da lista de dispositivos. 

    ![Escolha um dispositivo](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Aceda a **definições > controladores**.
   
    ![Certifique-se de que os controladores de dispositivo StorSimple estão em bom Estados](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. No **controladores** painel, certifique-se de que o estado de ambos os controladores no seu dispositivo é **bom estado de funcionamento**. Selecionar um controlador, clique com botão direito e, em seguida, selecione **reiniciar** ou **Encerrar**.

    ![Selecionar reiniciar ou encerrar os controladores de dispositivo StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. É criada uma tarefa para reiniciar ou encerrar o controlador e é apresentadas com avisos aplicáveis, se aplicável. Para monitorizar o reinício ou encerramento, aceda a **serviço > registos de atividade** e, em seguida, filtre os parâmetros específicos ao seu serviço. Se um controlador foi encerrado, terá de push o botão de energia para ativar o controlador de ativá-la.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou encerrar um controlador no Windows PowerShell para StorSimple
Execute os seguintes passos para encerrar ou reiniciar um único controlador no dispositivo StorSimple do Windows PowerShell para StorSimple.

1. Acede ao dispositivo através da consola de série ou de uma sessão telnet a partir de um computador remoto. Para ligar ao controlador 0 ou 1 de controlador, siga os passos no [utilizar o PuTTY para ligar à consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
3. Na mensagem de faixa, tome nota do controlador de que está ligado (controlador 0 ou 1 controlador) e se é o Active Directory ou o controlador passivo (modo de espera).
   
   * Para encerrar um único controlador, na linha de comandos, escreva:
     
       `Stop-HcsController`
     
       O controlador de que estão ligados a este encerrado. Se parar o controlador de Active Directory, em seguida, o dispositivo a ativação pós-falha para o controlador passivo.

   * Para reiniciar um controlador, na linha de comandos, escreva:
     
       `Restart-HcsController`
     
       Esta ação reinicia o controlador de que estão ligados a. Se reiniciar o controlador de Active Directory,-lo a ativação pós-falha para o controlador passivo antes de reiniciar.

## <a name="shut-down-a-storsimple-device"></a>Encerrar um dispositivo StorSimple

Esta secção explica como encerrar em execução ou de um dispositivo StorSimple falhado de um computador remoto. Um dispositivo está a ser desativado depois dos controladores de dispositivo estão a ser encerrados. Um encerramento de dispositivo é feito quando o dispositivo for movido fisicamente ou é retirado do serviço.

> [!IMPORTANT]
> Antes de encerrar o dispositivo, verifique o estado de funcionamento dos componentes do dispositivo. Navegue para o seu dispositivo e, em seguida, clique em **definições > Estado de funcionamento do Hardware**. No **hardware e estado de funcionamento** painel, certifique-se de que o estado de LED de todos os componentes verde. Apenas um dispositivo em bom estado de funcionamento tem o estado verde. Se o dispositivo está a ser desligado para substituir um componente malfunctioning, verá um falha (vermelho) ou um Estado degradado (amarelo) para os respetivos componentes.


#### <a name="to-shut-down-a-storsimple-device"></a>Para encerrar um dispositivo StorSimple

1. Utilize o [reiniciar ou encerrar um controlador](#restart-or-shut-down-a-single-controller) procedimento para identificar e encerre o controlador passivo no seu dispositivo. Pode efetuar esta operação no portal do Azure ou no Windows PowerShell para StorSimple.
2. Repita o passo anterior para encerrar controlador ativo.
3. Tem agora observe o back-plane do dispositivo. Depois de dois controladores são totalmente encerrados, o estado LEDs em ambos os controladores deve blinking vermelho. Se tiver de desativar o dispositivo completamente neste momento, Inverte os comutadores de energia no energia e arrefecimento módulos (PCMs) para a posição OFF. Isto deve desativar o dispositivo.

## <a name="reset-the-device-to-factory-default-settings"></a>Repor o dispositivo para as predefinições de fábrica

> [!IMPORTANT]
> Se precisar de repor o seu dispositivo para as predefinições de fábrica, contacte Support da Microsoft. O procedimento descrito abaixo deve ser utilizado apenas em conjunto com Support da Microsoft.

Este procedimento descreve como repor o dispositivo do Microsoft Azure StorSimple para as predefinições de fábrica com o Windows PowerShell para StorSimple.
Repor um dispositivo remove todos os dados e definições de todo o cluster por predefinição.

Execute os seguintes passos para repor o dispositivo do Microsoft Azure StorSimple para as predefinições de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para repor o dispositivo para as predefinições no Windows PowerShell para StorSimple
1. Aceder ao dispositivo através da respetiva consola de série. Consulte a mensagem de faixa para se certificar de que estão ligadas ao **Active Directory** controlador.
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
3. Na linha de comandos, escreva o seguinte comando para repor a todo o cluster, a remover todas as definições de controlador, metadados e dados:
   
    `Reset-HcsFactoryDefault`
   
    Para repor a um único controlador em vez disso, utilize o [reposição HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) cmdlet com o `-scope` parâmetro.)
   
    O sistema irá reiniciar várias vezes. Será notificado quando a reposição foi concluída com êxito. Consoante o modelo de sistema, pode demorar 45-60 minutos para um dispositivo 8100 e 60 90 minutos para que um 8600 concluir este processo.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre a gestão de controladores de dispositivo
Nesta secção, iremos ter resumidos algumas das perguntas frequentes sobre gerir controladores de dispositivo StorSimple.

**P.** O que acontece se ambos os controladores de dispositivo são bom estado de funcionamento e desativada no e consigo reiniciar ou encerrar controlador ativo?

**R.** Se ambos os controladores no seu dispositivo são bom estado de funcionamento e desativada, lhe for pedida a confirmação. Pode optar por:

* **Reinicie o controlador de Active Directory** – for notificado de que a reiniciar um controlador de Active Directory causou o dispositivo para a ativação pós-falha para o controlador passivo. Reinicia o controlador.
* **Encerrar um controlador de Active Directory** – for notificado de que a encerrar um controlador de Active Directory resulta num período de indisponibilidade. Terá também de push o botão de energia no dispositivo para ativar o controlador.

**P.** O que acontece se o controlador passivo no meu dispositivo está indisponível ou desativada desativado e consigo reiniciar ou encerrar controlador ativo?

**R.** Se o controlador passivo no seu dispositivo está indisponível ou desativada desativado e optar por:

* **Reinicie o controlador de Active Directory** – for notificado de que continuar a operação irá resultar numa interrupção temporária do serviço e que lhe for pedida a confirmação.
* **Encerrar um controlador de Active Directory** – for notificado de que continuar a operação resulta no período de indisponibilidade. Terá também de push o botão de energia num ou ambos os controladores para ativar o dispositivo. Lhe for pedida a confirmação.

**P.** Quando executa o controlador reinício ou encerramento não consegue progresso?

**R.** Reiniciar ou encerrar um controlador poderão falhar se:

* Uma atualização de dispositivo está em curso.
* Já está em curso um reinício de controlador.
* Um encerramento de controlador já está em curso.

**P.** Como pode descobrir o se um controlador foi reiniciado ou encerrado?

**R.** Pode verificar o estado do controlador no painel de controlador. O estado do controlador irá indicar se um controlador no processo de reiniciar ou encerrar. Além disso, o **alertas** painel contêm um alerta informativo, se o controlador é reiniciado ou encerrado. As operações de reinício e encerramento do controlador também são registadas nos registos acitivity. Para obter mais informações sobre os registos de acitivity, aceda a [ver os registos de atividade](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**P.** Existe qualquer impacto para a e/s como resultado de ativação pós-falha do controlador?

**R.** As ligações de TCP entre iniciadores e o controlador de Active Directory serão repostas como resultado de ativação pós-falha do controlador, mas serão possível restabelecer quando o controlador passivo pressupõe que a operação. Poderão ocorrer uma interrupção temporária (menos de 30 segundos) na atividade de e/s entre iniciadores e o dispositivo no decorrer desta operação.

**P.** Como posso devolver os meus controladores depois de serem encerrar e removido de serviço?

**R.** Para devolver os controladores de serviço, tem de inseri-lo para o chassis conforme descrito em [substituir um módulo de controlador no dispositivo StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Passos seguintes
* Se tiver quaisquer problemas com os controladores de dispositivo do StorSimple que não é possível resolver utilizando os procedimentos indicados neste tutorial, [contacte a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Para saber mais sobre como utilizar o serviço do Gestor de dispositivos do StorSimple, aceda à [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

