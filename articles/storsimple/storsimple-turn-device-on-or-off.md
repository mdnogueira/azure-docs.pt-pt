---
title: "Ativar ou desativar a dispositivo da série 8000 do StorSimple | Microsoft Docs"
description: "Explica como ativar um novo dispositivo StorSimple, ative um dispositivo que foi encerrado ou perdido de energia e desativar um dispositivo em execução."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e6a6b0ab5c493aa99438f27222d1112053951c97
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Ativar ou desativar o dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral
Não é necessário como parte da operação do sistema normal encerrar um dispositivo de Microsoft Azure StorSimple. No entanto, terá de ativar um novo dispositivo ou um dispositivo que tiveram de ser encerrada. Geralmente, é necessário um encerramento nos casos em que deve substituir o hardware falhada, fisicamente mover uma unidade ou demorar um dispositivo de serviço fora de. Este tutorial descreve o procedimento necessário para ativar e encerrar o dispositivo StorSimple em cenários diferentes.

## <a name="turn-on-a-new-device"></a>Ativar um novo dispositivo
Os passos para ativar um dispositivo StorSimple pela primeira vez diferem consoante se o dispositivo é um 8100 ou um modelo de 8600. O 8100 tem um bastidor primário único, enquanto o 8600 é um dispositivo de inclusão de dupla com um bastidor primário e um bastidor EBOD. Os passos detalhados para ambos os modelos são abordados nas secções seguintes.

* [Novo dispositivo com apenas a inclusão principal](#new-device-with-primary-enclosure-only)
* [Novo dispositivo com a inclusão EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Novo dispositivo com apenas a inclusão principal
O modelo do StorSimple 8100 é um dispositivo de inclusão único. O dispositivo inclui redundante energia e arrefecimento módulos (PCMs). Tanto PCMs tem de ser instaladas e ligadas a fontes de alimentação diferentes para garantir a elevada disponibilidade.

Execute os seguintes passos para instalar os cabos do dispositivo de energia.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Para a configuração do dispositivo e de cabos de instruções, aceda a [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Certifique-se de que segue as instruções exatamente.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Novo dispositivo com a inclusão EBOD
O modelo do StorSimple 8600 tem um bastidor primário e um bastidor EBOD. Isto requer que as unidades ser instalou os cabos em conjunto para conectividade Serial Attached SCSI (SAS) e de energia.

Quando configurar este dispositivo pela primeira vez, execute os passos para SAS cablagem primeiro e, em seguida, conclua os passos para power cablagem.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Para a configuração do dispositivo e de cabos de instruções, aceda a [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Certifique-se de que segue as instruções exatamente.

## <a name="turn-on-a-device-after-shutdown"></a>Ativar um dispositivo após encerramento
Os passos para ativar um dispositivo StorSimple depois de ter sido encerrado são diferentes dependendo se o dispositivo é um 8100 ou um modelo de 8600. O 8100 tem um bastidor primário único, enquanto o 8600 é um dispositivo de inclusão de dupla com um bastidor primário e um bastidor EBOD.

* [Dispositivos com apenas a inclusão principal](#device-with-primary-enclosure-only)
* [Dispositivo com a inclusão EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivos com apenas a inclusão principal
Após um encerramento, utilize o procedimento seguinte para ativar um dispositivo StorSimple com um bastidor primário e não bastidor EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Para ativar um dispositivo com apenas um bastidor primário
1. Certifique-se de que a potência muda em ambos os energia e arrefecimento módulos (PCMs) estão na posição OFF. Se os comutadores não estiverem na posição OFF, inverte-los para a posição OFF e aguarde que o lights ir.
2. Ative o dispositivo por indicar os comutadores de energia em ambos os PCMs para a posição de no. Deve ativar o dispositivo.
3. Verifique o seguinte para verificar se o dispositivo é completamente no:
   
   1. O LEDs OK em ambos os módulos PCM são verdes.
   2. O estado LEDs em ambos os controladores são verde sólida.
   3. O LED azul dos controladores de é blinking, que indica que o controlador de Active Directory.
      
      Se qualquer uma das seguintes condições não forem cumpridas, o dispositivo não está em bom estado. . [Contacte a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Dispositivo com a inclusão EBOD
Após um encerramento, utilize o procedimento seguinte para ativar um dispositivo StorSimple com um bastidor primário e um bastidor EBOD. Execute cada passo na sequência exatamente tal como descrito. Falha ao fazê-lo pode resultar em perda de dados.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Para ativar um dispositivo com um site primário e um bastidor EBOD
1. Certifique-se de que o bastidor EBOD está ligado ao bastidor primário. Para obter mais informações, consulte [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Certifique-se de que a energia e arrefecimento módulos (PCMs) no EBOD e inclusões primários estão na posição OFF. Se os comutadores não estiverem na posição OFF, inverte-los para a posição OFF e aguarde que o lights ir.
3. Ative o bastidor EBOD primeiro por indicar os comutadores de energia em ambos os PCMs para a posição de no. O LEDs PCM deve estar verde. Um controlador EBOD LED verde esta unidade indica que o bastidor EBOD está ativada.
4. Ative o bastidor primário por indicar os comutadores de energia em ambos os PCMs para a posição de no. Todo o sistema agora deve estar no.
5. Certifique-se de que o LEDs SAS são verdes, que garante que a ligação entre o bastidor EBOD e a inclusão principal é bom.

## <a name="turn-on-a-device-after-a-power-loss"></a>Ativar um dispositivo após uma falha de energia
Uma falha de energia ou uma interrupção, pode encerrar um dispositivo StorSimple. A interrupção de energia pode acontecer num de fontes de alimentação ou ambas as fontes de alimentação. Os passos de recuperação são diferentes dependendo se o dispositivo é um modelo 8100 ou de uma 8600. O 8100 tem um bastidor primário único, enquanto o 8600 é um dispositivo de inclusão de dupla com um bastidor primário e um bastidor EBOD. Esta secção descreve o procedimento de recuperação para cada cenário.

* [Dispositivos com apenas a inclusão principal](#8100)
* [Dispositivo com a inclusão EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivos com apenas a inclusão principal<a name="8100">
O sistema pode continuar a conclusão da operação normal, se houver perda de energia a uma das respetivas fontes de alimentação. No entanto, para garantir a elevada disponibilidade do dispositivo, restaure power para a fonte de alimentação logo que possível.

Se existir uma falha de energia ou uma interrupção de energia em ambas as fontes de alimentação, o sistema será encerrado de forma ordenada e controlada. Quando o energia é restaurado, o sistema irá ligar automaticamente.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo com a inclusão EBOD<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Falha de energia no um power fornecer
O sistema pode continuar o funcionamento normal, se houver perda de energia a uma das respetivas fontes de alimentação o bastidor primário ou a inclusão EBOD. No entanto, para garantir a elevada disponibilidade do dispositivo, restaure power para a fonte de alimentação logo que possível.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Falha de energia em ambas as fontes de alimentação no site primário e inclusões EBOD
Se existir uma falha de energia ou uma interrupção de energia em ambas as fontes de alimentação, o bastidor EBOD irá encerrar imediatamente e a inclusão principal será encerrado de forma ordenada e controlada. Quando é restaurada energia, o dispositivo é iniciado automaticamente.

Se a capacidade é desligada manualmente, em seguida, siga os passos seguintes para restaurar a energia para o sistema.

1. Ative o bastidor EBOD.
2. Depois do bastidor EBOD no, ative o bastidor primário.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Falha de energia em ambas as fontes de alimentação no bastidor EBOD
Quando configurar o seu cabos, certifique-se de que nunca está ligado a EBOD individualmente para uma PDU separado. Se o EBOD e inclusão principal falhar ao mesmo tempo, o sistema será recuperada.

Se apenas a inclusão EBOD falha em ambas as fontes de alimentação, o sistema não será recuperada automaticamente. Siga os passos seguintes para ativar o sistema e restaurá-lo para um bom estado de funcionamento:

1. Se o bastidor primário está ativado, desactivar energia e arrefecimento módulos (PCMs).
2. Aguarde alguns minutos para que o sistema encerrar.
3. Ative o bastidor EBOD.
4. Depois do bastidor EBOD no, ative o bastidor primário.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Ativar um dispositivo após o site primário e EBOD bastidor ligação
Se a ligação entre o controlador de espera e o controlador EBOD correspondente, o dispositivo continua a trabalhar. Se se perder a ligação entre o controlador de Active Directory do sistema e o controlador EBOD correspondente, deve ocorrer a ativação pós-falha e o dispositivo deve continuar a funcionar como normal.

Quando os dois cabos Serial Attached SCSI (SAS) são removidos ou a ligação entre o bastidor EBOD e a inclusão principal é severed, o dispositivo deixa de funcionar. Neste momento, execute os seguintes passos.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Para ativar o dispositivo após a ligação está perdido
1. Acesso do dispositivo de cópia de segurança.
2. Se a ligação de cabo SAS entre o bastidor EBOD e a inclusão principal for interrompida, todos os fila SAS LEDs na caixa EBOD será desativada.
3. Encerre energia e arrefecimento módulos (PCMs) sobre a inclusão EBOD e a inclusão principal.
4. Aguarde até que todos os lights na cópia de segurança de ambas as caixas de desativar o tempo limite.
5. Reinsert cabos SAS e certifique-se de que existe uma boa ligação entre o bastidor EBOD e a inclusão principal.
6. Ative o bastidor EBOD primeiro por indicar ambas PCM muda para a posição de no.
7. Certifique-se de que o bastidor EBOD encontra-se por a verificar se o LED verde está ON.
8. Ative a inclusão principal.
9. Certifique-se de que o bastidor primário encontra-se por a verificar se o LED de controlador verde está ON.
10. Certifique-se de que a ligação de inclusão EBOD com a inclusão principal está boa, verificando se a fila SAS LEDs (quatro por controlador EBOD) estão em todos os.

> [!IMPORTANT]
> Se os cabos SAS estiver danificados ou a ligação entre o bastidor EBOD e a inclusão principal é não boa, quando ativar o sistema, entra em modo de recuperação. . [Contacte a Microsoft Support](storsimple-8000-contact-microsoft-support.md) se isto acontecer.


## <a name="turn-off-a-running-device"></a>Desativar um dispositivo em execução
Um dispositivo StorSimple em execução poderá ter de ser encerradas se está a ser movido, retirada do serviço, ou um componente malfunctioning que tem de ser substituído. Os passos são diferentes dependendo se o dispositivo StorSimple é uma 8100 ou um modelo de 8600. O 8100 tem um bastidor primário único, enquanto o 8600 é um dispositivo de inclusão de dupla com um bastidor primário e um bastidor EBOD. Esta secção descreve em detalhe os passos para encerrar um dispositivo em execução.

* [Dispositivo com a inclusão principal](#8100a)
* [Dispositivo com a inclusão EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo com a inclusão principal<a name="8100a">
Para encerrar o dispositivo de forma controlada e ordenada, pode fazê-lo através do portal clássico do Azure ou através do Windows PowerShell para StorSimple. 

> [!IMPORTANT]
> Não encerre um dispositivo em execução, utilizando o botão de energia do dispositivo de cópia de segurança.
> 
> Antes de encerrar o dispositivo, certifique-se de que todos os componentes do dispositivo estão em bom Estados. No portal clássico do Azure, navegue para **dispositivos** > **manutenção** > **estado do Hardware**e verifique o estado de todos os componentes é verde. Isto acontece apenas para um sistema de bom estado de funcionamento. Se o sistema está a ser desligado para substituir um componente malfunctioning, verá um falha (vermelho) ou degradado Estado (amarelo) para o componente no respetivo o **estado do Hardware**.
> 
> 

Depois de acesso do Windows PowerShell para StorSimple ou portal clássico do Azure, siga os passos em [encerrar um dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo com a inclusão EBOD<a name="8600a">
> [!IMPORTANT]
> Antes de encerrar a inclusão principal e a inclusão EBOD, certifique-se de que todos os componentes do dispositivo estão em bom Estados. No portal do Azure, navegue para **dispositivos** > **Monitor** > **estado de funcionamento do Hardware**e certifique-se de que todos os componentes estão em bom Estados.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Para encerrar um dispositivo em execução com a inclusão EBOD
1. Siga todos os passos apresentados na [encerrar um dispositivo StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) para o bastidor primário.
2. Depois do bastidor primário é encerrado, encerre o EBOD por indicar desativar comutadores de energia e arrefecimento módulo (PCM).
3. Para verificar que o EBOD foi encerrado, verifique se todos os lights da caixa a inclusão EBOD estão desativadas.

> [!NOTE]
> Não devem ser removidos os cabos SAS que são utilizados para ligar a inclusão EBOD ao bastidor primário até depois do sistema é encerrado.

## <a name="next-steps"></a>Passos seguintes
[Contacte o Support Microsoft](storsimple-8000-contact-microsoft-support.md) se ocorrerem problemas ao ativar ou encerrar um dispositivo StorSimple.

