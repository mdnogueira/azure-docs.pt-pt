---
title: "Alterar as palavras-passe através do Gestor de dispositivos do StorSimple | Microsoft Docs"
description: "Descreve como utilizar o serviço StorSimple Manager para alterar o seu dispositivo e Snapshot Manager do StorSimple administrador palavras-passe."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f178509c-f4e1-48a8-90b2-d4ad050eeb30
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 843d0a4bcc1807a6b1995d067e0f9e572307775d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Utilizar o serviço StorSimple Manager para alterar as palavras-passe do StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [utilizar o serviço StorSimple Manager para alterar as palavras-passe do StorSimple](storsimple-8000-change-passwords.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
Portal clássico do Azure **configurar** página contém todos os parâmetros de dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço StorSimple Manager. Este tutorial explica como pode utilizar o **configurar** página alterar o administrador do dispositivo ou a palavra-passe do Snapshot Manager do StorSimple.

## <a name="change-the-device-administrator-password"></a>Alterar palavra-passe do administrador do dispositivo
Quando utilizar a interface do Windows PowerShell para aceder ao dispositivo StorSimple, é necessário para introduzir uma palavra-passe de administrador do dispositivo. Quando o primeiro dispositivo StorSimple é registado com um serviço, a palavra-passe predefinida para esta interface é *Password1*. Para garantir a segurança dos seus dados, são necessárias para alterar esta palavra-passe no final do processo de registo. Não pode sair do processo de registo sem alterar esta palavra-passe. Para obter mais informações, consulte [passo 3: configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Em seguida, é possível alterar a palavra-passe que foi definida pela primeira vez através da interface do Windows PowerShell durante o registo através do portal clássico do Azure. Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a palavra-passe de administrador do dispositivo
1. No portal clássico, clique em **dispositivos** > **configurar** para o seu dispositivo.
2. Desloque para baixo até o **palavra-passe de administrador do dispositivo** secção. Forneça uma palavra-passe de administrador que contém 8 e 15 carateres. A palavra-passe tem de ser uma combinação de 3 ou mais carateres em maiúsculas, minúsculas, numérico e especiais.
3. Confirme a palavra-passe.
4. Clique em **Guardar** na parte inferior da página.

Agora deve ser atualizada a palavra-passe de administrador do dispositivo. Pode utilizar esta palavra-passe modificado para aceder à interface do Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Alterar a palavra-passe do Snapshot Manager do StorSimple
O software Snapshot Manager do StorSimple reside no anfitrião do Windows e permite aos administradores gerir cópias de segurança do dispositivo StorSimple sob a forma de instantâneos locais e na nuvem.

Quando configurar um dispositivo no Snapshot Manager do StorSimple, será solicitado para fornecer o endereço IP do dispositivo e a palavra-passe para autenticar o dispositivo de armazenamento. Esta palavra-passe pela primeira vez é configurada através da interface do Windows PowerShell. Para obter mais informações, consulte [passo 3: configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Em seguida, é possível alterar a palavra-passe que foi definida pela primeira vez através da interface do Windows PowerShell durante o registo através do portal clássico. Execute os seguintes passos para alterar a palavra-passe do Snapshot Manager do StorSimple.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Para alterar a palavra-passe do Snapshot Manager do StorSimple
1. No portal clássico, clique em **dispositivos** > **configurar** para o seu dispositivo.
2. Desloque para baixo até o **Snapshot Manager do StorSimple** secção. Introduza uma palavra-passe 14 ou 15 carateres. Certifique-se de que a palavra-passe contém uma combinação de 3 ou mais carateres em maiúsculas, minúsculas, numérico e especiais.
3. Confirme a palavra-passe.
4. Clique em **Guardar** na parte inferior da página.

A palavra-passe do Snapshot Manager do StorSimple agora deve ser atualizada.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [segurança do StorSimple](storsimple-security.md).
* Saiba mais sobre [modificar a configuração do dispositivo](storsimple-modify-device-config.md).
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

