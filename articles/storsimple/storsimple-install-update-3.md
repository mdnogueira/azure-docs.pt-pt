---
title: "Instalar a atualização 3 no dispositivo StorSimple | Microsoft Docs"
description: "Explica como instalar a atualização 3 do StorSimple 8000 série no seu dispositivo de série 8000 do StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c6c4634d-4f3a-4bc4-b307-a22bf18664e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72b004a6c2604e0fc20b71b4b69217622f8f9ea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-3-on-your-storsimple-8000-series-device"></a>Instalar a atualização 3 no seu dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como instalar a atualização 3 num dispositivo StorSimple em execução uma versão anterior do software através do portal clássico do Azure e utilizar o método de correção. O método de correção é utilizado quando um gateway é configurado numa interface de rede que não sejam dados 0 do dispositivo StorSimple e está a tentar atualizar a partir de uma versão do software de 1 anterior à atualização.

A atualização 3 incluem software de dispositivos, o controlador de LSI e firmware, Storport e atualizações de Spaceport. Se a atualização a partir do Update 2 ou uma versão anterior, também será necessário para aplicar a iSCSI, WMI e em certos casos, as atualizações de firmware do disco. O software de dispositivos, WMI, iSCSI, LSI controlador, Spaceport e Storport correções atualizações não acontece e podem ser aplicadas através do portal clássico do Azure. As atualizações de firmware do disco são atualizações acontece e só podem ser aplicadas através da interface do Windows PowerShell do dispositivo. 

> [!IMPORTANT]
> * Um conjunto de pré-verificações de manuais e automáticas terminado antes da instalação para determinar o estado de funcionamento do dispositivo em termos de conectividade de rede e estado de hardware. Estas verificações prévias são efetuadas apenas se aplicar as atualizações do portal clássico do Azure.
> * Recomendamos que instale as atualizações de software e do controlador através do portal clássico do Azure. Só deve passar para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se falhar a verificação de pré-atualização de gateway no portal. Consoante a versão que está a atualizar, as atualizações poderão demorar horas 1.5 2.5 instalar. As atualizações de modo de manutenção tem de ser instaladas através da interface do Windows PowerShell do dispositivo. As atualizações de modo de manutenção acontece atualizações, estas resultará na indisponíveis para o seu dispositivo.
> * Se executar opcional Snapshot Manager do StorSimple, certifique-se de que a atualização a versão do Snapshot Manager para a atualização 2 antes de atualizar o dispositivo.
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Instalar a atualização 3 através do portal clássico do Azure
Execute os seguintes passos para atualizar o seu dispositivo para [atualização 3](storsimple-update3-release-notes.md).

> [!NOTE]
> Se aplicar a atualização 2 ou posterior (incluindo 2.1 de atualização), Microsoft será capaz de informações de diagnóstico adicionais do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão a ter problemas, estamos melhor equipped recolher informações do dispositivo e diagnosticar problemas. Ao aceitar atualização 2 ou posterior, permitem-na fornecer este suporte proativa.
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Certifique-se de que o dispositivo está em execução **StorSimple 8000 série Update 3 (6.3.9600.17759)**. O **atualizado pela última vez data** também deverá ser modificado. 
   - Se estão a atualizar a partir de uma versão antes da atualização 2, também verá que as atualizações de modo de manutenção estão disponíveis (esta mensagem poderá continuar a apresentar para até 24 horas depois de instalar as atualizações).
     As atualizações de modo de manutenção são atualizações acontece resultam num período de indisponibilidade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo. Em alguns casos quando estiver a executar 1.2 de atualização, o firmware do disco já pode ser atualizado, caso em que não precisa de instalar quaisquer atualizações de modo de manutenção.
   - Se estiver a atualizar a partir da atualização 2 ou posterior, o dispositivo deve ser atualizado. Pode ignorar o passo seguinte.

Transferir as atualizações de modo de manutenção utilizando os passos apresentados na [para transferir as correções](#to-download-hotfixes) para procurar e transferir KB3121899, as atualizações de firmware de disco instala (as atualizações de outras já devem estar instaladas por agora). Siga os passos apresentados na [instalar e certifique-se correções do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar o modo de manutenção de atualizações. 

## <a name="install-update-3-as-a-hotfix"></a>Instalar a atualização 3 como uma correção
Utilize este procedimento se falhar a verificação de gateway ao tentar instalar as atualizações através do portal clássico do Azure. A verificação falha à medida que tem um gateway atribuído a uma interface de rede 0 não sejam de dados e o dispositivo está a executar uma versão de software antes da atualização 1.

As versões de software que podem ser atualizadas utilizando o método de correções são:

* Atualização 0.1, 0,2, 0,3
* Atualizar 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2 

> [!IMPORTANT]
> * Se o dispositivo estiver a executar a versão de lançamento (GA), entre em contacto com [Microsoft Support](storsimple-contact-microsoft-support.md) para ajudá-lo com a atualização.
> 
> 

O método de correção envolve os seguintes três passos:

1. Transferir as correções do catálogo Microsoft Update.
2. Instalar e verifique se as correções de modo normal.
3. Instalar e verifique se a correção de modo de manutenção (apenas quando a atualização anterior à atualização de software 2).

#### <a name="download-updates-for-your-device"></a>Transferir atualizações para o seu dispositivo
**Se o dispositivo estiver em execução atualização 2.1 ou 2.2**, tem de transferir e instalar as seguintes correções previstas ordem:

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação |
| --- | --- | --- | --- | --- |
| 1. |KB3186843 |Atualização de software &#42; |Normal <br></br>Não acontece |~ 45 minutos |
| 2. |KB3186859 |Controlador de LSI e firmware |Normal <br></br>Não acontece |~ 20 minutos |
| 3. |KB3121261 |Corrija Storport e Spaceport </br> Windows Server 2012 R2 |Normal <br></br>Não acontece |~ 20 minutos |

&#42;  *Tenha em atenção que a atualização de software consiste em dois ficheiros binários: precedido de atualização de software do dispositivo com `all-hcsmdssoftwareupdate` e o agente Cis e Mds precedido pela `all-cismdsagentupdatebundle`. A atualização de software do dispositivo tem de estar instalada antes do agente Cis e Mds. Também tem de reiniciar o controlador de Active Directory através do `Restart-HcsController` cmdlet depois de aplicar o agente Cis e Mds atualizar (e antes de aplicar o restante atualizações).* 

**Se o dispositivo está em execução atualização 0.1, 0,2, 0,3, 1.0, 1.1, 1.2 ou 2.0**, tem de transferir e instalar as seguintes correções para o software, além de LSI controladores e firmware atualizações (mostradas na tabela anterior), pela ordem prevista:

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação |
| --- | --- | --- | --- | --- |
| 4. |KB3146621 |pacote de iSCSI |Normal <br></br>Não acontece |~ 20 minutos |
| 5. |KB3103616 |Pacote WMI |Normal <br></br>Não acontece |~ minutos de 12 |

<br></br>

**Se o dispositivo estiver a executar versões 0,2, 0,3, 1.0, 1.1 e 1.2**, também poderá ter de instalar as atualizações de firmware do disco em todas as atualizações apresentadas nas tabelas anteriores. Pode verificar se tem as atualizações de firmware do disco, executando o `Get-HcsFirmwareVersion` cmdlet. Se estiver a executar estas versões de firmware: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, em seguida, não terá de instalar estas atualizações.

| Ordem | KB | Descrição | Tipo de atualização | Hora de instalação |
| --- | --- | --- | --- | --- |
| 6. |KB3121899 |Firmware de disco |Manutenção <br></br>Acontece |~ 30 minutos |

<br></br>

> [!IMPORTANT]
> * Este procedimento tem de ser efetuada apenas uma vez para aplicar a atualização 3. Pode utilizar o portal clássico do Azure para aplicar as atualizações subsequentes.
> * Se a atualização a partir do Update 2.2, a hora de instalação total está próximo horas 1.1.
> * Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que tanto os controladores de dispositivo estão online e de todos os componentes de hardware estão em bom Estados.
> 
> 

Execute os seguintes passos para transferir e instalar as correções.

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [Update 3 versão](storsimple-update3-release-notes.md).

