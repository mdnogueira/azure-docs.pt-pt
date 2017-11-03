---
title: "Substituir um controlador de dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: "Explica como remover e substitua um ou ambos os módulos de controlador no seu dispositivo de série 8000 do StorSimple."
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 849eccff114c2fd6d952e44d095d0cc89a238675
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Substituir um módulo de controlador no dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como remover e substitua um ou ambos os módulos de controlador no dispositivo StorSimple. Também descreve a lógica subjacente para os cenários de substituição de controlador único e dupla.

> [!NOTE]
> Antes de efetuar a substituição de um controlador, recomendamos que atualize sempre o firmware de controlador para a versão mais recente.
> 
> Para evitar danos para o dispositivo StorSimple, não ejete o controlador até os LEDs são que mostra como um dos seguintes:
> 
> * Todos os lights são OFF.
> * LED 3, ![ícone de verificação verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), e ![ícone vermelho entre](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) são flashing, e LED 0 e LED 7 são **ON**.


A tabela seguinte mostra os cenários de substituição do controlador suportada.

| Caso | Cenário de substituição | Procedimento aplicável |
|:--- |:--- |:--- |
| 1 |Um controlador é em estado de falha, o controlador de outro é bom estado de funcionamento e Active Directory. |[Único substituição de controlador](#replace-a-single-controller), que descreve o [lógica por trás de substituição de um único controlador](#single-controller-replacement-logic), bem como o [passos de substituição](#single-controller-replacement-steps). |
| 2 |Ambos os controladores falharam e necessitam de substituição. O chassis, discos e bastidor de disco estão em bom Estados. |[Substituição de controlador dupla](#replace-both-controllers), que descreve o [lógica por trás de uma substituição de controlador dupla](#dual-controller-replacement-logic), bem como o [passos de substituição](#dual-controller-replacement-steps). |
| 3 |Os controladores do mesmo dispositivo ou a partir de diferentes dispositivos estão a ser alternados. O chassis, discos e inclusões de disco estão em bom Estados. |Será apresentada uma mensagem de alerta de erro de correspondência de ranhura. |
| 4 |Um controlador está em falta e o outro controlador de falha. |[Substituição de controlador dupla](#replace-both-controllers), que descreve o [lógica por trás de uma substituição de controlador dupla](#dual-controller-replacement-logic), bem como o [passos de substituição](#dual-controller-replacement-steps). |
| 5 |Falharam de um ou ambos os controladores. Não é possível aceder ao dispositivo através da consola de série ou a comunicação remota do Windows PowerShell. |[Contacte o Support Microsoft](storsimple-8000-contact-microsoft-support.md) para um procedimento de substituição de controlador manual. |
| 6 |Os controladores têm uma versão de compilação diferente, o que pode ser devido a:<ul><li>Os controladores têm uma versão de software diferentes.</li><li>Os controladores têm uma versão de firmware diferentes.</li></ul> |Se as versões do software controlador forem diferentes, a lógica de substituição que Deteta e atualiza a versão do software no controlador de substituição.<br><br>Se as versões de firmware do controlador são diferentes e a versão de firmware antigo é **não** automaticamente actualizável, uma mensagem de alerta será apresentado no portal do Azure. Deve procurar as atualizações e instalar as atualizações de firmware.</br></br>Se as versões de firmware do controlador são diferentes e a versão de firmware antigo é automaticamente actualizável, a lógica de substituição de controlador detetará esta e, depois do controlador é iniciado, o firmware será atualizado automaticamente. |

Terá de remover um módulo de controlador, se tiver falhou. Um ou ambos os módulos controlador podem falhar, o que pode resultar numa substituição de controlador único ou substituição de controlador dupla. Para obter procedimentos de substituição e a lógica por trás-las, consulte o seguinte:

* [Replace a single controller](#replace-a-single-controller) (Substituir um controlador individual)
* [Substituir tanto os controladores](#replace-both-controllers)
* [Remover um controlador](#remove-a-controller)
* [Inserir um controlador](#insert-a-controller)
* [Identificar o controlador de Active Directory no seu dispositivo](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Antes de remover e de substituir um controlador, reveja as informações de segurança no [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Substituir um único controlador
Quando um dos dois controladores de dispositivo do Microsoft Azure StorSimple falhou, é funcionar incorretamente ocorrer sistematicamente ou está em falta, é necessário substituir um único controlador.

### <a name="single-controller-replacement-logic"></a>Lógica de substituição de controlador único
Na substituição de um único controlador, primeiro deve remover o controlador de falha. (O controlador restante no dispositivo é o controlador de Active Directory.) Quando inserir o controlador de substituição, as ações seguintes ocorrem:

1. O controlador de substituição começa imediatamente a comunicar com o dispositivo StorSimple.
2. Um instantâneo do disco rígido virtual (VHD) para o controlador de Active Directory é copiado no controlador de substituição.
3. O instantâneo é modificado para que quando o controlador de substituição é iniciado a partir deste VHD, será reconhecido como um controlador de espera.
4. Quando as modificações estiverem concluídas, o controlador de substituição será iniciado como o controlador de espera.
5. Quando estiver a executar ambos os controladores, o cluster estiver online.

### <a name="single-controller-replacement-steps"></a>Passos de substituição de controlador único
Conclua os passos seguintes se falhar um dos controladores no seu dispositivo do Microsoft Azure StorSimple. (O controlador de outro tem de ser Active Directory e em execução. Se os dois controladores falharam ou avaria, aceda a [passos de substituição de controlador dupla](#dual-controller-replacement-steps).)

> [!NOTE]
> Pode demorar 30 – 45 minutos para que o controlador ao reiniciar e recuperar completamente o procedimento de substituição de controlador único. O tempo total para o procedimento completo, incluindo anexar os cabos é aproximadamente 2 horas.


#### <a name="to-remove-a-single-failed-controller-module"></a>Para remover uma única falha no módulo de controlador
1. No portal do Azure, vá para o serviço do Gestor de dispositivos do StorSimple, clique em **dispositivos**e, em seguida, clique no nome do dispositivo que pretende monitorizar.
2. Aceda a **monitorização > Estado de funcionamento do Hardware**. O estado do controlador 0 ou 1 controlador deve ser vermelho, que indica uma falha.
   
   > [!NOTE]
   > O controlador de falha na substituição de um único controlador é sempre um controlador de espera.
   
3. Utilize figura 1 e a tabela seguinte para localizar o módulo de controlador falhada.
   
    ![Backplane dos módulos de inclusão principal do dispositivo](./media/storsimple-controller-replacement/IC740994.png)
   
    **Figura 1** dispositivo Back do StorSimple
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
4. No controlador de falha, remova as portas de dados a todos os cabos de rede ligada. Se estiver a utilizar um modelo 8600, também o removerá os cabos SAS que ligam o controlador para o controlador de EBOD.
5. Siga os passos no [remover um controlador](#remove-a-controller) para remover o controlador de falha.
6. Instale a substituição de fábrica a mesma ranhura a partir do qual o controlador de falha foi removido. Isto aciona a lógica de substituição de controlador único. Para obter mais informações, consulte [único lógica de substituição de controlador](#single-controller-replacement-logic).
7. Enquanto a lógica de substituição de controlador único avança em segundo plano, voltar a ligar os cabos. Asseguramos para ligar todos os cabos exatamente da mesma forma que foram ligados antes da substituição.
8. Depois de reinicia o controlador, verifique o **Estado controlador** e **estado do Cluster** no portal do Azure para verificar se o controlador é para um bom estado de funcionamento e está no modo de espera.

> [!NOTE]
> Se estiver a monitorizar o dispositivo através da consola de série, poderá ver vários reinícios enquanto é recuperar o controlador do procedimento de substituição. Quando é apresentado o menu da consola de série, em seguida, sabe que a substituição está concluída. Se o menu não aparecer dentro de duas horas de iniciar a substituição de controlador, [contacte a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> A iniciar a atualização 4, também pode utilizar o cmdlet `Get-HCSControllerReplacementStatus` na interface do Windows PowerShell do dispositivo para monitorizar o estado do processo de substituição de controlador.
> 

## <a name="replace-both-controllers"></a>Substituir tanto os controladores
Quando tanto os controladores do dispositivo Microsoft Azure StorSimple tiverem falhado, são funcionar incorretamente ocorrer sistematicamente ou estão em falta, terá de substituir tanto os controladores. 

### <a name="dual-controller-replacement-logic"></a>Lógica de substituição de controlador dupla
Substituição de controlador dupla, que remova primeiro os dois controladores de falha e, em seguida, inserir substituições. Quando os controladores de duas substituição são inseridos, as ações seguintes ocorrem:

1. O controlador de substituição na ranhura 0 verifica o seguinte:
   
   1. É utilizar versões atuais do firmware e software?
   2. É uma parte do cluster?
   3. É o controlador de elemento de rede em execução e é agrupado?
      
      Se nenhuma destas condições forem verdadeiras, o controlador de procura para a mais recente cópia de segurança diária (localizado no **nonDOMstorage** na unidade S). O controlador copia o instantâneo do VHD mais recente da cópia de segurança.
2. O controlador na ranhura 0 utiliza o instantâneo para a imagem de si próprio.
3. Entretanto, o controlador na ranhura 1 aguarda controlador 0 para concluir a criação de imagens e iniciar.
4. Depois de iniciado o controlador 0, o controlador 1 Deteta o cluster criada pelo controlador 0, que aciona a lógica de substituição de controlador único. Para obter mais informações, consulte [único lógica de substituição de controlador](#single-controller-replacement-logic).
5. Em seguida, tanto os controladores estarão em execução e o cluster irá ficar online.

> [!IMPORTANT]
> Seguir uma substituição de controlador dupla, depois do dispositivo StorSimple é configurado, é essencial que demorar uma manual cópia de segurança do dispositivo. Cópias de segurança de configuração do dispositivo diárias não são acionadas até depois de ter decorrido 24 horas. Trabalhar com [Microsoft Support](storsimple-8000-contact-microsoft-support.md) efectuar uma manual cópia de segurança do seu dispositivo.


### <a name="dual-controller-replacement-steps"></a>Passos de substituição de controlador dupla
Este fluxo de trabalho é necessário quando ambos os controladores de dispositivo do Microsoft Azure StorSimple falharam. Isto pode acontecer num centro de dados em que o sistema arrefecimento deixa de funcionar e, como resultado, ambos os controladores falharem num período de tempo curto. Dependendo se o dispositivo StorSimple está ativado ou desativar e, se estiver a utilizar um 8600 ou um modelo de 8100, um conjunto diferente de passos, é necessário.

> [!IMPORTANT]
> Pode demorar 1 hora para o controlador ao reiniciar e completamente recuperar a partir de um procedimento de substituição de controlador dupla 45 minutos. O tempo total para o procedimento completo, incluindo anexar os cabos é de aproximadamente 2,5 horas.

#### <a name="to-replace-both-controller-modules"></a>Para substituir os módulos de controlador
1. Se o dispositivo está desativado, ignore este passo e avance para o passo seguinte. Se o dispositivo estiver ativado, desative o dispositivo.
   
   1. Se estiver a utilizar um modelo de 8600, desative a inclusão principal primeiro e, em seguida, desative a inclusão EBOD.
   2. Aguarde até que o dispositivo foi encerrado por completo. Todos os LEDs in the back of o dispositivo será desativada.
2. Remova todos os cabos de rede que estão ligados às portas de dados. Se estiver a utilizar um modelo 8600, também o removerá os cabos SAS que se ligam a inclusão principal ao bastidor EBOD.
3. Remova tanto os controladores do dispositivo StorSimple. Para obter mais informações, consulte [remover um controlador](#remove-a-controller).
4. Inserir o substituto de fábrica do controlador 0 primeiro e, em seguida, inserir o controlador 1. Para obter mais informações, consulte [inserir um controlador](#insert-a-controller). Isto aciona a lógica de substituição de controlador dupla. Para obter mais informações, consulte [lógica de substituição de controlador dupla](#dual-controller-replacement-logic).
5. Enquanto a lógica de substituição de controlador avança em segundo plano, voltar a ligar os cabos. Asseguramos para ligar todos os cabos exatamente da mesma forma que foram ligados antes da substituição. Consulte as instruções detalhadas para o seu modelo no cabo da secção de dispositivo do [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Ative o dispositivo StorSimple. Se estiver a utilizar um modelo de 8600:
   
   1. Certifique-se de que o bastidor EBOD está ativado no primeiro.
   2. Aguarde até que o bastidor EBOD está em execução.
   3. Ative a inclusão principal.
   4. Depois do primeiro controlador de ser reiniciado e está em bom estado de funcionamento, o sistema será executado.
      
      > [!NOTE]
      > Se estiver a monitorizar o dispositivo através da consola de série, poderá ver vários reinícios enquanto é recuperar o controlador do procedimento de substituição. Quando for apresentado o menu da consola de série, em seguida, sabe que a substituição está concluída. Se o menu não aparecer dentro de horas 2,5 de iniciar a substituição de controlador, [contacte a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Remover um controlador
Utilize o procedimento seguinte para remover um módulo defeituoso controlador de dispositivo StorSimple.

> [!NOTE]
> As ilustrações seguintes são para o controlador 0. Para o controlador 1, estes seriam ser invertidos.


#### <a name="to-remove-a-controller-module"></a>Para remover um módulo de controlador
1. GRASP o bloqueio temporário módulo entre o botão do controlo e forefinger.
2. Gently squeeze o botão do controlo e forefinger em conjunto para libertar o bloqueio temporário de controlador.
   
    ![Ao libertar o bloqueio temporário de controlador](./media/storsimple-controller-replacement/IC741047.png)
   
    **Figura 2** bloqueio temporário de controlador de libertação
3. Utilize o bloqueio temporário como um identificador, faça deslizar o controlador fora de chassis.
   
    ![Controlador deslizante fora de chassis](./media/storsimple-controller-replacement/IC741048.png)
   
    **Figura 3** a deslizante controlador fora de chassis

## <a name="insert-a-controller"></a>Inserir um controlador
Utilize o procedimento seguinte para instalar um módulo de fábrica fornecido pelo controlador depois de remover um módulo defeituoso do dispositivo StorSimple.

#### <a name="to-install-a-controller-module"></a>Para instalar um módulo de controlador
1. Verifique se há quaisquer danos os conectores de interface. Não instale o módulo se qualquer um dos pins conector está danificado ou bent.
2. Faça deslize o módulo de controlador para o chassis enquanto o bloqueio temporário totalmente é libertado.
   
    ![Controlador deslizante num chassis](./media/storsimple-controller-replacement/IC741053.png)
   
    **Figura 4** deslizantes controlador para o chassis
3. Com o módulo de controlador inserido, comece a fechar o bloqueio temporário enquanto continua a enviar o módulo de controlador para o chassis. O bloqueio temporário irá interagir com orientar o controlador para o local.
   
    ![Fechar bloqueio temporário de controlador](./media/storsimple-controller-replacement/IC741054.png)
   
    **Figura 5** fechar o bloqueio temporário de controlador
4. Terminar quando o bloqueio temporário ajusta no local. O **OK** LED agora deve estar no.
   
   > [!NOTE]
   > Pode demorar até 5 minutos para que o controlador e o LED para ativar.
  
5. Para verificar que a substituição é efetuada com êxito, no portal do Azure, aceda ao seu dispositivo e, em seguida, navegue para **Monitor** > **estado de funcionamento do Hardware**e certifique-se de que o controlador 0 e o controlador 1 estão em bom Estados (estado está verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identificar o controlador de Active Directory no seu dispositivo
Existem muitas situações, tais como substituição de registo ou um controlador de dispositivo da primeira vez, que necessitam para localizar o controlador de Active Directory num dispositivo StorSimple. Controlador ativo processa todos os os firmware e de rede operações de disco. Pode utilizar qualquer um dos seguintes métodos para identificar o controlador de Active Directory:

* [Utilizar o portal do Azure para identificar o controlador de Active Directory](#use-the-azure-portal-to-identify-the-active-controller)
* [Utilizar o Windows PowerShell para StorSimple para identificar o controlador de Active Directory](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Verifique o dispositivo físico para identificar o controlador de Active Directory](#check-the-physical-device-to-identify-the-active-controller)

Cada um destes procedimentos é descrita a seguir.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Utilizar o portal do Azure para identificar o controlador de Active Directory
No portal do Azure, navegue para o seu dispositivo e, em seguida, **Monitor** > **estado de funcionamento do Hardware**e desloque-se para o **controladores** secção. Aqui pode verificar que controlador está ativo.

![Identificar o controlador de Active Directory no portal do Azure](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** portal do Azure que mostra o controlador de Active Directory

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Utilizar o Windows PowerShell para StorSimple para identificar o controlador de Active Directory
Quando acedem ao seu dispositivo através da consola de série, é apresentada uma mensagem de faixa. A mensagem de faixa contém informações básicas de dispositivos, como o modelo, o nome, a versão do software instalado e o estado do controlador de que está a aceder. A imagem seguinte mostra um exemplo de uma mensagem de faixa:

![Mensagem de faixa de série](./media/storsimple-controller-replacement/IC741098.png)

**A figura 7** faixa mensagem que mostra controlador 0 como ativo

Pode utilizar a mensagem de faixa para determinar se o controlador de que está ligado à é ativas ou passivas.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Verifique o dispositivo físico para identificar o controlador de Active Directory
Para identificar o controlador de Active Directory no seu dispositivo, localize o LED azul acima a porta de 5 de dados na cópia de segurança da inclusão principal.

Se este LED é blinking, o controlador está ativo e o controlador outro está no modo de reserva dinâmica. Utilize o seguinte diagrama e tabela como um auxiliar.

![Dispositivo primário bastidor backplane com dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Back of inclusão principal com portas de dados e LEDs monitorização

| Etiqueta | Descrição |
|:--- |:--- |
| 1-6 |DATA 0 – 5 portas de rede |
| 7 |LED azul |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

