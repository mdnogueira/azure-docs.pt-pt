---
title: Gerir controladores de dispositivo StorSimple | Microsoft Docs
description: Saiba como parar, reiniciar, encerre ou repor os controladores de dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4ee989d0-956f-4c14-951e-fd4e490ea09d
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 8182d0ccf9fb57a9dba383d376d1c557845bcc8a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>Gerir os controladores de dispositivo do StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [gerir os controladores de dispositivo do StorSimple](storsimple-8000-manage-device-controller.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
Este tutorial descreve as operações diferentes que podem ser efetuadas nos controladores de dispositivo StorSimple. Os controladores de dispositivo StorSimple são controladores redundante (ponto a ponto) uma configuração de ativo-passivo. Num determinado momento, apenas um controlador está ativo e está a processar todas as operações de disco e rede. O controlador de outro está num modo passivo. Se o controlador de Active Directory falhar, o controlador passivo fica ativo automaticamente.

Este tutorial inclui instruções passo a passo para gerir os controladores de dispositivo utilizando o:

* **Controladores** secção o **manutenção** página no serviço StorSimple Manager
* Windows PowerShell para StorSimple.

Recomendamos que gere os controladores de dispositivo através do serviço StorSimple Manager. Se uma ação só pode ser efetuada através do Windows PowerShell para StorSimple, o tutorial faz uma nota do mesmo.

Depois de ler este tutorial, será capaz de:

* Reiniciar ou encerrar um controlador de dispositivo do StorSimple
* Encerrar um dispositivo StorSimple
* Repor o dispositivo StorSimple para as predefinições de fábrica

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou encerrar um único controlador
Um controlador reinício ou encerramento não é necessário como parte da operação do sistema normal. Operações de encerramento para um controlador de dispositivo único são comuns apenas nos casos em que um componente de hardware do dispositivo falhou requer substituição. Também poderá ser necessário um reinício do controlador uma situação em que o desempenho é afetado pela utilização de memória excessiva ou um controlador malfunctioning. Também ser necessário reiniciar um controlador depois de uma substituição de controlador com êxito, se pretender ativar e testar o controlador substituído.

Reiniciar um dispositivo não é acontece aos iniciadores ligados, assumindo que o controlador passivo está disponível. Se um controlador passivo não estiver disponível ou ativado desativado, em seguida, reiniciar o controlador de Active Directory poderão resultar na interrupção do serviço e o período de indisponibilidade.

> [!IMPORTANT]
> * **Nunca deve ser removido fisicamente um controlador em execução como isto resultaria em perda de redundância e um maior risco de indisponibilidade.**
> * O procedimento seguinte aplica-se apenas para o dispositivo físico StorSimple. Para obter informações sobre como iniciar, parar e reiniciar o dispositivo virtual, consulte [trabalhar com o dispositivo virtual](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).
> 
> 

Pode reiniciar ou encerrar um controlador de dispositivo único utilizando o portal clássico do Azure do serviço StorSimple Manager ou do Windows PowerShell para StorSimple

Para gerir os controladores de dispositivo do portal clássico do Azure, execute os seguintes passos.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Para reiniciar ou encerrar um controlador no portal clássico
1. Navegue para **dispositivos > manutenção**.
2. Aceda a **estado do Hardware** e certifique-se de que o estado de ambos os controladores no seu dispositivo é **bom estado de funcionamento**.
   
    ![Certifique-se de que os controladores de dispositivo StorSimple estão em bom Estados](./media/storsimple-manage-device-controller/IC766017.png)
3. Na parte inferior do **manutenção** página, clique em **gerir controladores**.
   
    ![Gerir controladores de dispositivo do StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>
   
   > [!NOTE]
   > Se não forem apresentadas **gerir controladores**, em seguida, tem de instalar as atualizações. Para obter mais informações, consulte [atualizar o dispositivo StorSimple](storsimple-update-device.md).
   > 
   > 
4. No **alterar definições do controlador** diálogo caixa, efetue o seguinte:
   
   1. Do **selecione controlador** pendente lista, selecione o controlador de que pretende gerir. As opções são controlador 0 e o controlador 1. Estes controladores também são identificados como ativas ou passivas.
      
      > [!NOTE]
      > Um controlador não pode ser gerido caso esteja disponível ou ativado desativado e não irá aparecer na lista pendente.
      > 
      > 
   2. Do **selecionar ação** pendente lista, escolha **reinício controlador** ou **encerrar controlador**.
      
       ![Reinicie o controlador passivo do dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766020.png)
   3. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-device-controller/IC740895.png).

Isto irá reiniciar ou encerre o controlador. A tabela abaixo resume os detalhes do que acontece, consoante a seleção que tiver efetuado no **alterar definições do controlador** caixa de diálogo.  

| Seleção # | Se optar por... | Isto irá acontecer. |
| --- | --- | --- |
| 1. |Reinicie o controlador passivo. |Será criada uma tarefa para reiniciar o controlador e será notificado depois da tarefa é criada com êxito. Isto irá iniciar o reinício de controlador. Pode monitorizar o processo de reinício acedendo **serviço > Dashboard > ver os registos de operações** e, em seguida, a filtragem por parâmetros específicos ao seu serviço. |
| 2. |Reinicie o controlador de Active Directory. |Verá o seguinte aviso: "se reiniciar o controlador de Active Directory, o dispositivo serão ativadas pós-falha para o controlador passivo. Pretende continuar?" </br>Se optar por prosseguir com esta operação, os passos seguintes será idênticos às utilizadas para reiniciar o controlador passivo (consulte seleção 1). |
| 3. |Encerre o controlador passivo. |Verá a seguinte mensagem: "após a conclusão do encerramento, terá de enviar o botão de energia no seu controlador de ativá-la. Tem a certeza de que pretende encerrar este controlador? " </br>Se optar por prosseguir com esta operação, os passos seguintes será idênticos às utilizadas para reiniciar o controlador passivo (consulte seleção 1). |
| 4. |Encerre o controlador de Active Directory. |Verá a seguinte mensagem: "após a conclusão do encerramento, terá de enviar o botão de energia no seu controlador de ativá-la. Tem a certeza de que pretende encerrar este controlador? " </br>Se optar por prosseguir com esta operação, os passos seguintes será idênticos às utilizadas para reiniciar o controlador passivo (consulte seleção 1). |

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou encerrar um controlador no Windows PowerShell para StorSimple
Execute os seguintes passos para encerrar ou reiniciar um único controlador no dispositivo StorSimple no portal clássico do Azure.

1. Aceder ao dispositivo utilizando a consola de série ou de uma sessão telnet a partir de um computador remoto. Ligar ao controlador 0 ou 1 de controlador, seguindo os passos no [utilizar o PuTTY para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
3. Na mensagem de faixa, tome nota do controlador de que está ligado (controlador 0 ou 1 controlador) e se é o Active Directory ou o controlador passivo (modo de espera).
   
   * Para encerrar um único controlador, na linha de comandos, escreva:
     
       `Stop-HcsController`
     
       Isto irá encerrar o controlador de que estão ligados a. Se parar o controlador de Active Directory, em seguida,-serão ativadas pós-falha para o controlador passivo antes de ser encerrado para baixo.
   * Para reiniciar um controlador, na linha de comandos, escreva:
     
       `Restart-HcsController`
     
       Esta ação irá reiniciar o controlador de que estão ligados a. Se reiniciar o controlador de Active Directory, serão ativadas pós-falha para o controlador passivo antes de reiniciar.

## <a name="shut-down-a-storsimple-device"></a>Encerrar um dispositivo StorSimple
Esta secção explica como encerrar em execução ou de um dispositivo StorSimple falhado de um computador remoto. Um dispositivo está desativado após ambos os controladores de dispositivo a ser encerrado. Um encerramento de dispositivo é feito quando o dispositivo está a ser movido fisicamente ou é retirado do serviço.

> [!IMPORTANT]
> Antes de encerrar o dispositivo, verifique o estado de funcionamento dos componentes do dispositivo. Navegue para **dispositivos > manutenção > Estado do Hardware** e certifique-se de que o estado de LED de todos os componentes é verde. Apenas um dispositivo em bom estado de funcionamento terão um estado verde. Se o dispositivo está a ser desligado para substituir um componente malfunctioning, verá um falha (vermelho) ou um Estado degradado (amarelo) para os respetivos componentes.
> 
> 

#### <a name="to-shut-down-a-storsimple-device"></a>Para encerrar um dispositivo StorSimple
1. Utilize o [reiniciar ou encerrar um controlador](#restart-or-shut-down-a-single-controller) procedimento para identificar e encerre o controlador passivo no seu dispositivo. Pode efetuar esta operação no portal clássico do Azure ou no Windows PowerShell para StorSimple.
2. Repita o passo anterior para encerrar controlador ativo.
3. Agora tem de observar o back-plane do dispositivo. Depois de dois controladores são totalmente encerrados, o estado LEDs em ambos os controladores deve blinking vermelho. Se tiver de desativar o dispositivo completamente neste momento, Inverte os comutadores de energia no energia e arrefecimento módulos (PCMs) para a posição OFF. Isto deve desativar o dispositivo.

<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Repor o dispositivo para as predefinições de fábrica
> [!IMPORTANT]
> Se precisar de repor o seu dispositivo para as predefinições de fábrica, contacte Support da Microsoft. O procedimento descrito abaixo deve ser utilizado apenas em conjunto com Support da Microsoft.
> 
> 

Este procedimento descreve como repor o dispositivo do Microsoft Azure StorSimple para as predefinições de fábrica com o Windows PowerShell para StorSimple.
Repor um dispositivo remove todos os dados e definições de todo o cluster por predefinição.

Execute os seguintes passos para repor o dispositivo do Microsoft Azure StorSimple para as predefinições de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para repor o dispositivo para as predefinições no Windows PowerShell para StorSimple
1. Aceder ao dispositivo através da respetiva consola de série. Consulte a mensagem de faixa para se certificar de que está ligado ao controlador ativo.
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
3. Na linha de comandos, escreva o seguinte comando para repor a todo o cluster, a remover todas as definições de controlador, metadados e dados:
   
    `Reset-HcsFactoryDefault`
   
    Para repor a um único controlador em vez disso, utilize o [reposição HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) cmdlet com o `-scope` parâmetro.)
   
    O sistema irá reiniciar várias vezes. Será notificado quando a reposição foi concluída com êxito. Consoante o modelo de sistema, pode demorar 45-60 minutos para um dispositivo 8100 e 60 90 minutos para que um 8600 concluir este processo.
   
   > [!TIP]
   > * Se estiver a utilizar a atualização 1.2 ou anteriormente utilizar o `–SkipFirmwareVersionCheck` parâmetro para ignorar a verificação da versão de firmware (caso contrário, verá um erro de incompatibilidade de firmware: reposição de fábrica não pode continuar devido a um erro de correspondência de versões de firmware. ).
   > * O procedimento de reposição de fábrica pode falhar com dispositivos StorSimple que estiver a executar o Update 1 ou 1.1 no portal de administração pública e tem efetuado uma substituição de controlador simples ou dupla com êxito (com controladores de substituição que foram fornecidos com o software de pré-atualização 1). Isto acontece quando o reposição de fábrica de imagem é validada a presença de um ficheiro de SHA1 num controlador que não existe para 1 de pré-atualização de software. Se vir que esta fábrica repor falha, contacte a Microsoft Support para ajudá-lo com os passos seguintes. Este problema não é utilizado com os controladores de substituição que foram enviados de fábrica com Update 1 ou posterior software.
   > 
   > 

## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre a gestão de controladores de dispositivo
Nesta secção, iremos ter resumidos algumas das perguntas frequentes sobre gerir controladores de dispositivo StorSimple.

**P.** O que acontece se ambos os controladores de dispositivo são bom estado de funcionamento e desativada no e consigo reiniciar ou encerrar controlador ativo?

**R.** Se ambos os controladores no seu dispositivo são bom estado de funcionamento e ativado, será solicitado para confirmação. Pode optar por:

* **Reinicie o controlador de Active Directory** – será notificado de que a reiniciar um controlador de Active Directory irá fazer com que o dispositivo para a ativação pós-falha para o controlador passivo. O controlador será reiniciado.
* **Encerrar um controlador de Active Directory** – será notificado de que a encerrar um controlador de Active Directory resultará num período de indisponibilidade. Também terá de enviar o botão de energia no dispositivo para ativar o controlador.

**P.** O que acontece se o controlador passivo no meu dispositivo está indisponível ou desativada desativado e consigo reiniciar ou encerrar controlador ativo?

**R.** Se o controlador passivo no seu dispositivo está indisponível ou desativada desativado e optar por:

* **Reinicie o controlador de Active Directory** – será notificado de que continuar a operação irá resultar numa interrupção temporária do serviço e lhe-á de confirmação.
* **Encerrar um controlador de Active Directory** – será notificado se continuar a operação resultará num período de indisponibilidade e que terá de push o botão de energia num ou ambos os controladores para ativar o dispositivo. Será solicitado para confirmação.

**P.** Quando executa o controlador reinício ou encerramento não consegue progresso?

**R.** Reiniciar ou encerrar um controlador poderão falhar se:

* Uma atualização de dispositivo está em curso.
* Já está em curso um reinício de controlador.
* Um encerramento de controlador já está em curso.

**P.** Como pode descobrir o se um controlador foi reiniciado ou encerrado?

**R.** Pode verificar o estado de controlador na página de manutenção. O estado do controlador irá indicar se um controlador foi reiniciado ou encerrado. Além disso, a página de alertas irá conter um alerta informativo, se o controlador foi reiniciado ou encerrado. As operações de reinício e encerramento do controlador também são registadas nos registos de operação. Para mais informações sobre os registos de operações, aceda a [ver os registos de operação](storsimple-service-dashboard.md#view-the-operations-logs).

**P.** Existe qualquer impacto para a e/s como resultado de ativação pós-falha do controlador?

**R.** As ligações de TCP entre iniciadores e o controlador de Active Directory serão repostas como resultado de ativação pós-falha do controlador, mas serão possível restabelecer quando o controlador passivo pressupõe que a operação. Poderão ocorrer uma interrupção temporária (menos de 30 segundos) na atividade de e/s entre iniciadores e o dispositivo no decorrer desta operação.

**P.** Como posso devolver os meus controladores depois de serem encerrar e removido de serviço?

**R.** Para devolver os controladores de serviço, tem de inseri-lo para o chassis conforme descrito em [substituir um módulo de controlador no dispositivo StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Passos seguintes
* Se tiver quaisquer problemas com os controladores de dispositivo do StorSimple que não é possível resolver utilizando os procedimentos indicados neste tutorial, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md).
* Para saber mais sobre como utilizar o serviço StorSimple Manager, aceda à [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

