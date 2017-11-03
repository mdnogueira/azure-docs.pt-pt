---
title: Alterar o modo de dispositivo StorSimple | Microsoft Docs
description: Descreve os modos de dispositivo StorSimple e explica como utilizar o Windows PowerShell para StorSimple para alterar o modo de dispositivo.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: e9d7d277-8a2f-45eb-9fef-355486e14cbc
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2016
ms.author: alkohli
ms.openlocfilehash: 33c65bf2eecff3914f3227c76f7d638a4507e1f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Alterar o modo de dispositivo no dispositivo StorSimple
Este artigo fornece uma breve descrição de vários modos na qual pode operar o dispositivo StorSimple. O dispositivo StorSimple pode funcionar em três modos: normal, a manutenção e a recuperação. 

Depois de ler este artigo, ficará a saber:

* São que os modos de dispositivo do StorSimple
* Como descobrir que modo o dispositivo StorSimple está no
* Como alterar desde a carga normal ao modo de manutenção e *vice versa*

As tarefas de gestão acima só podem ser efetuadas através da interface do Windows PowerShell do dispositivo StorSimple.

## <a name="about-storsimple-device-modes"></a>Sobre os modos de dispositivo do StorSimple
O dispositivo StorSimple pode funcionar no modo normal, de manutenção ou de recuperação. Cada um dos modos destas brevemente é descrita abaixo.

### <a name="normal-mode"></a>Modo normal
Isto é definido como modo normal operacional para um dispositivo StorSimple totalmente configurado. Por predefinição, o seu dispositivo deve estar no modo normal.

### <a name="maintenance-mode"></a>Modo de manutenção
Por vezes, o dispositivo StorSimple poderá ter de ser colocado no modo de manutenção. Este modo permite-lhe efetuar manutenção no dispositivo e instalar atualizações acontece, por exemplo, os relacionados com o firmware do disco.

Pode colocar o sistema no modo de manutenção apenas através do Windows PowerShell para StorSimple. Todos os pedidos de e/s são colocadas em pausa neste modo. Os serviços, tais como a memória de acesso aleatório não volátil (NVRAM) ou o serviço clustering também estão parados. Ambos os controladores são reiniciados quando introduzir ou sair neste modo. Quando sair do modo de manutenção, todos os serviços retomará e devem estar em bom estado. Esta operação poderá demorar alguns minutos.

> [!NOTE]
> **Modo de manutenção só é suportado num dispositivo funcionar corretamente. Não é suportada num dispositivo em que um ou ambos os controladores não estão a funcionar.**
> </br>
> 
> 

### <a name="recovery-mode"></a>Modo de recuperação
Modo de recuperação pode ser descrito como "Windows modo de segurança com suporte de rede". Modo de recuperação envolva a equipa de Support da Microsoft e permite-lhes diagnósticos no sistema. O principal objetivo do modo de recuperação é obter os registos do sistema.

Se o sistema entra no modo de recuperação, deverá contactar Support da Microsoft para os passos seguintes. Para obter mais informações, aceda a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md).

> [!NOTE]
> **Não é possível colocar o dispositivo em modo de recuperação. Se o dispositivo está num Estado incorreto, o modo de recuperação tenta colocar o dispositivo num Estado em que Microsoft Support técnico pode examiná-lo.**
> 
> 

## <a name="determine-storsimple-device-mode"></a>Determinar o modo de dispositivo do StorSimple
#### <a name="to-determine-the-current-device-mode"></a>Para determinar o modo atual do dispositivo
1. Inicie sessão da consola de série do dispositivo ao seguir os passos no [utilizar o PuTTY para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Observe a mensagem de faixa no menu da consola de série do dispositivo. Esta mensagem explicitamente indica se o dispositivo está no modo de manutenção ou de recuperação. Se a mensagem não contém quaisquer informações específicas relativas para o modo de sistema, o dispositivo está no modo normal.

## <a name="change-the-storsimple-device-mode"></a>Alterar o modo de dispositivo do StorSimple
É possível colocar o dispositivo StorSimple no modo de manutenção (de modo normal) para executar a manutenção ou instalar atualizações de modo de manutenção. Execute os seguintes procedimentos para introduzir ou sair do modo de manutenção.

> [!IMPORTANT]
> Antes de introduzir o modo de manutenção, certifique-se de que ambos os controladores de dispositivo estão em bom Estados acedendo a **estado do Hardware** no **manutenção** página no portal clássico do Azure. Se um ou ambos os controladores de não estão em bom Estados, contacte Support da Microsoft para os passos seguintes. Para obter mais informações, aceda a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md).
> 
> 

#### <a name="to-enter-maintenance-mode"></a>Introduza o modo de manutenção
1. Inicie sessão da consola de série do dispositivo ao seguir os passos no [utilizar o PuTTY para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando solicitado, forneça o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é: `Password1`.
3. Na linha de comandos, escreva 
   
    `Enter-HcsMaintenanceMode`
4. Verá uma mensagem de aviso informá-lo de que modo de manutenção irá interromper todos os pedidos de e/s e sever a ligação ao portal clássico do Azure e lhe-á de confirmação. Tipo **Y** para introduzir o modo de manutenção.
5. Tanto os controladores irão reiniciar automaticamente. Quando o reinício estiver concluído, será apresentada outra mensagem que indica que o dispositivo estiver no modo de manutenção.

#### <a name="to-exit-maintenance-mode"></a>Para sair do modo de manutenção
1. Inicie sessão na consola de série do dispositivo. Certifique-se da mensagem de faixa que o dispositivo está no modo de manutenção.
2. Na linha de comandos, escreva:
   
    `Exit-HcsMaintenanceMode`
3. Serão apresentada uma mensagem de aviso e uma mensagem de confirmação. Tipo **Y** para sair do modo de manutenção.
4. Tanto os controladores irão reiniciar automaticamente. Quando o reinício estiver concluído, será apresentada outra mensagem que indica que o dispositivo está no modo normal.

## <a name="next-steps"></a>Passos seguintes
Saiba como [aplicar atualizações de modo normal e manutenção](storsimple-update-device.md) no dispositivo StorSimple.

