---
title: Atualizar o dispositivo StorSimple | Microsoft Docs
description: "Explica como utilizar a funcionalidade de atualização do StorSimple para instalar atualizações de modo normal e a manutenção e correções."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/18/2016
ms.author: v-sharos
ms.openlocfilehash: 8490110942741b049b6d44ac93697303cef40e8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="update-your-storsimple-8000-series-device"></a>Atualizar o seu dispositivo de série de 8000 do StorSimple
## <a name="overview"></a>Descrição geral
As funcionalidades de atualizações do StorSimple permitem-lhe manter o dispositivo StorSimple facilmente atualizados. Dependendo do tipo de atualização, pode aplicar atualizações ao dispositivo através do portal clássico do Azure ou através da interface do Windows PowerShell. Este tutorial descreve os tipos de atualização e sobre como instalar cada um deles.

Pode aplicar dois tipos de atualizações de dispositivo: 

* Regular (ou modo Normal) atualizações
* Atualizações de modo de manutenção

Pode instalar atualizações regulares através do portal clássico do Azure ou o Windows PowerShell; No entanto, deve utilizar o Windows PowerShell para instalar atualizações de modo de manutenção. 

Cada tipo de atualização é descrito em separado, abaixo.

### <a name="regular-updates"></a>Atualizações regulares
Atualizações regulares são não acontece atualizações que podem ser instaladas quando o dispositivo estiver no modo Normal. Estas atualizações são aplicadas através do Web site Microsoft Update para cada controlador de dispositivo. 

> [!IMPORTANT]
> Uma ativação pós-falha controlador poderão ocorrer durante o processo de atualização. No entanto, isto não irá afetar a disponibilidade de sistema ou a operação.
> 
> 

* Para obter mais informações sobre como instalar atualizações regulares através do portal clássico do Azure, consulte [instalar atualizações regulares através do portal clássico do Azure](#install-regular-updates-via-the-azure-classic-portal).
* Também pode instalar atualizações regulares através do Windows PowerShell para StorSimple. Para obter mais informações, consulte [instalar atualizações regulares através do Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Atualizações de modo de manutenção
As atualizações de modo de manutenção são atualizações acontece, tais como atualizações de firmware do disco. Estas atualizações requerem o dispositivo para ser colocada em modo de manutenção. Para obter mais informações, consulte [passo 2: modo de manutenção introduza](#step2). Não é possível utilizar o portal clássico do Azure para instalar atualizações de modo de manutenção. Em vez disso, deve utilizar o Windows PowerShell para StorSimple. 

Para obter detalhes sobre como instalar atualizações de modo de manutenção, consulte [atualizações de modo de manutenção instalar através do Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> As atualizações de modo de manutenção devem ser aplicadas em separado para cada controlador. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalar atualizações regulares através do portal clássico do Azure
Pode utilizar o portal clássico do Azure para aplicar atualizações ao dispositivo StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações regulares através do Windows PowerShell para StorSimple
Em alternativa, pode utilizar o Windows PowerShell para StorSimple para aplicar atualizações regular (modo Normal).

> [!IMPORTANT]
> Apesar de poder instalar atualizações regulares através do Windows PowerShell para StorSimple, recomendamos vivamente que instala atualizações regulares através do portal clássico do Azure. A partir do Update 1, verificações prévias serão executadas antes de instalar atualizações a partir do portal. Estas verificações prévias serão alta impedem mais falhas e certifique-se de uma experiência smoother. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações de modo de manutenção através do Windows PowerShell para StorSimple
Utilizar o Windows PowerShell para StorSimple para aplicar atualizações de modo de manutenção para o dispositivo StorSimple. Todos os pedidos de e/s são colocadas em pausa neste modo. Os serviços, tais como a memória de acesso aleatório não volátil (NVRAM) ou o serviço clustering também estão parados. Tanto os controladores são reiniciados, quando introduzir ou sair neste modo. Quando sair deste modo, todos os serviços retomará e devem estar em bom estado. (Isto pode demorar alguns minutos.)

Se precisar de aplicar as atualizações de modo de manutenção, irá receber um alerta através do portal clássico do Azure que tem atualizações que devem ser instaladas. Este alerta inclui instruções para utilizar o Windows PowerShell para StorSimple para instalar as atualizações. Depois de atualizar o seu dispositivo, utilize o mesmo procedimento para alterar o dispositivo para o modo normal. Para obter instruções passo a passo, consulte [passo 4: modo de manutenção de saída](#step4).

> [!IMPORTANT]
> * Antes de introduzir o modo de manutenção, certifique-se de que ambos os controladores de dispositivo estão em bom Estados, verificando o **estado do Hardware** no **manutenção** página no portal clássico do Azure. Se o controlador não está em bom estado, contacte Support da Microsoft para os passos seguintes. Para obter mais informações, aceda ao contactar o suporte da Microsoft. 
> * Quando estiver no modo de manutenção, terá de aplicar primeiro a atualização de um controlador e, em seguida, no outro controlador de.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Passo 1: Ligar à consola de série<a name="step1">
Em primeiro lugar, utilize uma aplicação como o PuTTY para aceder à consola de série. O procedimento seguinte explica como utilizar o PuTTY para ligar à consola de série.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Passo 2: Introduza o modo de manutenção<a name="step2">
Depois de ligar para a consola, determine se existem atualizações para instalar e, em modo de manutenção para instalá-los.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Passo 3: Instalar as atualizações<a name="step3">
Em seguida, instale as atualizações.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Passo 4: Modo de manutenção de saída<a name="step4">
Por fim, saia do modo de manutenção.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalar correções através do Windows PowerShell para StorSimple
Ao contrário das atualizações do Microsoft Azure StorSimple, correções estão instaladas a partir de uma pasta partilhada. Tal como acontece com as atualizações, existem dois tipos de correções: 

* Correções regulares 
* Correções de modo de manutenção  

Os procedimentos seguintes explicam como utilizar o Windows PowerShell para StorSimple para instalar regular e correções do modo de manutenção.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>O que acontece às atualizações se efetuar uma reposição de fábrica do dispositivo?
Se um dispositivo é reposto para as definições de fábrica, todas as atualizações serão perdidas. Depois do dispositivo de reposição de fábrica registado e configurado, terá de instalar manualmente as atualizações através do portal clássico do Azure e/ou o Windows PowerShell para StorSimple. Para obter mais informações sobre a reposição de fábrica, consulte [repor o dispositivo para as predefinições de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [através do Windows PowerShell para StorSimple para administrar o dispositivo StorSimple](storsimple-windows-powershell-administration.md).
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

