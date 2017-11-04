---
title: "Instalar o portal clássico do dispositivo de série 8000 do StorSimple atualização 5 | Microsoft Docs"
description: "Explica como instalar a atualização 5 do StorSimple 8000 série no seu dispositivo da série 8000 do StorSimple no portal clássico."
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
ms.date: 10/10/2017
ms.author: alkohli
ms.openlocfilehash: 9d8dc7aebbeea7ad428be4af66e4e991f60c8301
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalar a atualização 5 no dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como instalar a atualização 5 num dispositivo StorSimple em execução uma versão anterior do software através do portal clássico do Azure e utilizar o método de correção. O método de correção é utilizado quando estiver a tentar instalar a atualização 5 num dispositivo a executar 3 versões anterior à atualização. O método de correção também é utilizado se estiver configurado um gateway numa interface de rede que não sejam dados 0 do dispositivo StorSimple e está a tentar atualizar a partir de uma versão do software de 1 anterior à atualização.

A atualização 5 inclui o software de dispositivos, Storport e Spaceport, atualizações de segurança do SO e atualizações do SO e atualizações de firmware do disco.  O software de dispositivos, Spaceport, Storport, segurança e outras atualizações do SO são atualizações não acontece. As atualizações não acontece ou regulares podem ser aplicadas através do portal clássico do Azure ou através do método de correção. As atualizações de firmware do disco são atualizações acontece e são aplicadas quando o dispositivo estiver no modo de manutenção através do método do correção através da interface do Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * Um conjunto de pré-verificações de manuais e automáticas terminado antes da instalação para determinar o estado de funcionamento do dispositivo em termos de conectividade de rede e estado de hardware. Estas verificações prévias são efetuadas apenas se aplicar as atualizações do portal do Azure.
> * Se estiver a executar uma versão antes da atualização 3, recomendamos vivamente que instale a atualização 5 através do método do correção. Para ajudar o suporte ajudá-lo durante a atualização, [iniciar um pedido de suporte](storsimple-8000-contact-microsoft-support.md).
> * Se estiver a executar o Update 3 e posterior, recomendamos que instale o software e outras atualizações regulares através do portal clássico do Azure. Consoante a versão que está a atualizar, as atualizações poderão demorar 4 horas (ou superior) para instalar. As atualizações de modo de manutenção tem de ser instaladas através da interface do Windows PowerShell do dispositivo. As atualizações de modo de manutenção acontece atualizações, estas resultam em indisponíveis para o seu dispositivo.
> * Se executar opcional Snapshot Manager do StorSimple, certifique-se de que a atualização a versão do Snapshot Manager para atualização 5 antes de atualizar o dispositivo.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-classic-portal"></a>Instalar a atualização 5 através do portal clássico do Azure
Execute os seguintes passos para atualizar o seu dispositivo para [atualizar 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft obtém informações de diagnóstico adicionais do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão a ter problemas, estamos melhor equipped recolher informações do dispositivo e diagnosticar problemas.

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Certifique-se de que o dispositivo está em execução **StorSimple 8000 série Update 5 (6.3.9600.17845)**. O **atualizado pela última vez data** deverá ser modificado.

* Agora verá que as atualizações de modo de manutenção estão disponíveis (esta mensagem poderá continuar a apresentar para até 24 horas depois de instalar as atualizações). As atualizações de modo de manutenção são atualizações acontece resultam num período de indisponibilidade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo.

* Transferir as atualizações de modo de manutenção utilizando os passos apresentados na [para transferir as correções](#to-download-hotfixes) para procurar e transferir KB4037263, as atualizações de firmware de disco instala (as atualizações de outras já devem estar instaladas por agora). Siga os passos apresentados na [instalar e certifique-se correções do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar o modo de manutenção de atualizações.

## <a name="install-update-5-as-a-hotfix"></a>Instalar a atualização 5 como uma correção


As versões de software que podem ser atualizadas utilizando o método de correções são:

* Atualização 0.1, 0,2, 0,3
* Atualizar 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2
* Atualização 3, 3.1
* Atualização 4

> [!NOTE]
> O método recomendado para instalar a atualização 5 é através do portal clássico do Azure. No entanto, se estiver a executar uma versão antes da atualização 3, recomendamos vivamente que utilize este método para instalar a atualização 5. Também deve utilizar este procedimento se falhar a verificação de gateway ao tentar instalar as atualizações através do portal clássico do Azure. A verificação de falha quando tiver um gateway atribuído a uma interface de rede 0 não sejam de dados e o dispositivo está em execução uma versão de software anterior ao Update 1.

O método de correção envolve os seguintes três passos:

1. Transferir as correções do catálogo Microsoft Update.
2. Instalar e verifique se as correções de modo normal.
3. Instalar e certifique-se a correção de modo de manutenção.

#### <a name="download-updates-for-your-device"></a>Transferir atualizações para o seu dispositivo

Tem de transferir e instalar as seguintes correções na ordem prescrita e as pastas sugeridas:

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Atualização de software<br> Transfira ambos _HcsSfotwareUpdate.exe_ e _CisMSDAgent.exe_ |Normal <br></br>Não acontece |~ minutos de 25 |FirstOrderUpdate|

Se atualizar a partir de um dispositivo com a Update 4, apenas terá de instalar as atualizações acumulativas de SO como segundo atualizações de ordem.

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Pacote de atualizações cumulativas do SO <br> Transferir a versão do Windows Server 2012 R2 |Normal <br></br>Não acontece |- |SecondOrderUpdate|

Se instalar a partir de um dispositivo com o Update 3 ou anterior, instale o seguinte para além das atualizações cumulativas.

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Controlador de LSI e atualizações de firmware <br> Atualização de firmware USM (versão 3.38) |Normal <br></br>Não acontece |~ 3 h <br> (inclui 2A. + 2B. + 2 C.)|SecondOrderUpdate|
| 2 C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Pacote de atualizações de segurança do SO <br> Transferir a versão do Windows Server 2012 R2 |Normal <br></br>Não acontece |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Pacote de atualizações do SO <br> Transferir a versão do Windows Server 2012 R2 |Normal <br></br>Não acontece |- |SecondOrderUpdate|


Se também pretender instalar as atualizações de firmware do disco em todas as atualizações apresentadas nas tabelas anteriores. Pode verificar se tem as atualizações de firmware do disco, executando o `Get-HcsFirmwareVersion` cmdlet. Se estiver a executar estas versões de firmware: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, em seguida, não terá de instalar estas atualizações.

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação | Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmware de disco |Manutenção <br></br>Acontece |~ 30 minutos | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Se a atualização a partir do Update 4, o tempo de instalação total está próximo de 4 horas.
> * Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que tanto os controladores de dispositivo estão online e de todos os componentes de hardware estão em bom Estados.

Execute os seguintes passos para transferir e instalar as correções.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [versão de atualização 5](storsimple-update5-release-notes.md).

