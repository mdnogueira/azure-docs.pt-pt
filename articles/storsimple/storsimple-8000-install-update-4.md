---
title: "Instalar a atualização 4 no dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: "Explica como instalar a atualização 4 do StorSimple 8000 série no seu dispositivo de série 8000 do StorSimple."
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
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 57d6d63c55f8ad4da5d1905a1e209da454b0491c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalar a atualização 4 no dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como instalar a atualização 4 num dispositivo StorSimple em execução uma versão anterior do software através do portal do Azure e utilizar o método de correção. O método de correção é utilizado quando um gateway é configurado numa interface de rede que não sejam dados 0 do dispositivo StorSimple e está a tentar atualizar a partir de uma versão do software de 1 anterior à atualização.

Atualização 4 inclui o software de dispositivos, USM firmware, LSI controladores e firmware, Storport e Spaceport, SO atualizações de segurança e um anfitrião de outras atualizações do SO.  O software de dispositivos, USM de firmware, Spaceport, Storport e outras atualizações do SO são atualizações não acontece. As atualizações não acontece ou regulares podem ser aplicadas através do portal do Azure ou através do método de correção. As atualizações de firmware do disco são atualizações acontece e só podem ser aplicadas através do método do correção através da interface do Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * Um conjunto de pré-verificações de manuais e automáticas terminado antes da instalação para determinar o estado de funcionamento do dispositivo em termos de conectividade de rede e estado de hardware. Estas verificações prévias são efetuadas apenas se aplicar as atualizações do portal do Azure.
> * Recomendamos que instale o software e outras atualizações regulares através do portal do Azure. Só deve passar para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se falhar a verificação de pré-atualização de gateway no portal. Consoante a versão que está a atualizar, as atualizações poderão demorar 4 horas (ou superior) para instalar. As atualizações de modo de manutenção também tem de ser instaladas através da interface do Windows PowerShell do dispositivo. As atualizações de modo de manutenção acontece atualizações, estas resultará na indisponíveis para o seu dispositivo.
> * Se executar opcional Snapshot Manager do StorSimple, certifique-se de que a atualização a versão do Snapshot Manager para a atualização 4 antes de atualizar o dispositivo.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalar a atualização 4 através do portal do Azure
Execute os seguintes passos para atualizar o seu dispositivo para [atualização 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft obtém informações de diagnóstico adicionais do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão a ter problemas, estamos melhor equipped recolher informações do dispositivo e diagnosticar problemas. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Certifique-se de que o dispositivo está em execução **StorSimple 8000 série atualização 4 (6.3.9600.17820)**. O **atualizado pela última vez data** também deverá ser modificado.

* Agora verá que as atualizações de modo de manutenção estão disponíveis (esta mensagem poderá continuar a apresentar para até 24 horas depois de instalar as atualizações). As atualizações de modo de manutenção são atualizações acontece resultam num período de indisponibilidade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo.

* Transferir as atualizações de modo de manutenção utilizando os passos apresentados na [para transferir as correções](#to-download-hotfixes) para procurar e transferir KB4011837, as atualizações de firmware de disco instala (as atualizações de outras já devem estar instaladas por agora). Siga os passos apresentados na [instalar e certifique-se correções do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar o modo de manutenção de atualizações.

## <a name="install-update-4-as-a-hotfix"></a>Instalar a atualização 4 como uma correção
O método recomendado para instalar a atualização 4 é através do portal do Azure.

Utilize este procedimento se falhar a verificação de gateway ao tentar instalar as atualizações através do portal do Azure. A verificação falha à medida que tem um gateway atribuído a uma interface de rede 0 não sejam de dados e o dispositivo está a executar uma versão de software antes da atualização 1.

As versões de software que podem ser atualizadas utilizando o método de correções são:

* Atualização 0.1, 0,2, 0,3
* Atualizar 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2
* Atualização 3, 3.1


O método de correção envolve os seguintes três passos:

1. Transferir as correções do catálogo Microsoft Update.
2. Instalar e verifique se as correções de modo normal.
3. Instalar e certifique-se a correção de modo de manutenção.

#### <a name="download-updates-for-your-device"></a>Transferir atualizações para o seu dispositivo

Tem de transferir e instalar as seguintes correções na ordem prescrita e as pastas sugeridas:

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Atualização de software |Normal <br></br>Não acontece |~ minutos de 25 |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Controlador de LSI e atualizações de firmware <br> Atualização de firmware USM (versão 3.38) |Normal <br></br>Não acontece |~ 3 h <br> (inclui 2A. + 2B. + 2 C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pacote de atualizações de segurança do SO <br> Transferir o Windows Server 2012 R2 |Normal <br></br>Não acontece |- |SecondOrderUpdate|
| 2 C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Pacote de atualizações do SO <br> Transferir o Windows Server 2012 R2 |Normal <br></br>Não acontece |- |SecondOrderUpdate|

Se também pretender instalar as atualizações de firmware do disco em todas as atualizações apresentadas nas tabelas anteriores. Pode verificar se tem as atualizações de firmware do disco, executando o `Get-HcsFirmwareVersion` cmdlet. Se estiver a executar estas versões de firmware: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, em seguida, não terá de instalar estas atualizações.

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação | Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Firmware de disco |Manutenção <br></br>Acontece |~ 30 minutos | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Este procedimento precisa de ser executada apenas uma vez para aplicar a atualização 4. Pode utilizar o portal do Azure para aplicar as atualizações subsequentes.
> * Se atualizar a partir da atualização 3 ou 3.1, a hora de instalação total está próximo de 4 horas.
> * Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que tanto os controladores de dispositivo estão online e de todos os componentes de hardware estão em bom Estados.

Execute os seguintes passos para transferir e instalar as correções.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [versão de atualização 4](storsimple-update4-release-notes.md).

