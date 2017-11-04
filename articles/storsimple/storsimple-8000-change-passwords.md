---
title: Alterar as palavras-passe do StorSimple | Microsoft Docs
description: "Descreve como utilizar o serviço do Gestor de dispositivos do StorSimple para alterar o seu dispositivo e Snapshot Manager do StorSimple administrador palavras-passe."
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para alterar as palavras-passe do StorSimple

## <a name="overview"></a>Descrição geral
O portal do Azure **definições do dispositivo** opção contém todos os parâmetros de dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço do Gestor de dispositivos do StorSimple. Este tutorial explica como pode utilizar o **segurança** opção em **definições do dispositivo** alterar o administrador do dispositivo ou a palavra-passe do Snapshot Manager do StorSimple.

## <a name="change-the-device-administrator-password"></a>Alterar palavra-passe do administrador do dispositivo
Quando utilizar a interface do Windows PowerShell para aceder ao dispositivo StorSimple, é necessário para introduzir uma palavra-passe de administrador do dispositivo. Quando o primeiro dispositivo StorSimple é registado com um serviço, a palavra-passe predefinida para esta interface é *Password1*. Para garantir a segurança dos seus dados, são necessárias para alterar esta palavra-passe no final do processo de registo. Não pode sair do processo de registo sem alterar esta palavra-passe. Para obter mais informações, consulte [passo 3: configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A palavra-passe que foi definida pela primeira vez através da interface do Windows PowerShell durante o registo pode ser alterada posteriormente através do portal do Azure. Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a palavra-passe de administrador do dispositivo
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**.

2. A partir da lista tabela de dispositivos, selecione e clique no dispositivo cujo palavra-passe que pretende alterar.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. No **definições** painel, aceda a **definições do dispositivo > segurança**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. No **definições de segurança** painel, clique em **palavra-passe** para alterar a palavra-passe de administrador do dispositivo.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. No **palavra-passe** painel, forneça uma palavra-passe de administrador que contém 8 e 15 carateres. A palavra-passe tem de ser uma combinação de 3 ou mais carateres em maiúsculas, minúsculas, numérico e especiais.

6. Confirme a palavra-passe.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Clique em **guardar** e quando for pedida a confirmação, clique em **Sim**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Agora deve ser atualizada a palavra-passe de administrador do dispositivo. Pode utilizar esta palavra-passe modificado para aceder à interface do Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Definir a palavra-passe do Snapshot Manager do StorSimple
O software Snapshot Manager do StorSimple reside no anfitrião do Windows e permite aos administradores gerir cópias de segurança do dispositivo StorSimple sob a forma de instantâneos locais e na nuvem.

Quando configurar um dispositivo no Snapshot Manager do StorSimple, será solicitado para fornecer o endereço IP do dispositivo e a palavra-passe para autenticar o dispositivo de armazenamento.

Pode definir ou alterar a palavra-passe para o Snapshot Manager do StorSimple através do portal do Azure. Execute os seguintes passos para definir ou alterar a palavra-passe do Snapshot Manager do StorSimple.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Para definir a palavra-passe do Snapshot Manager do StorSimple
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**.

2. A partir da lista tabela de dispositivos, selecione e clique no dispositivo cujo Snapshot Manager do StorSimple palavra-passe, que pretende definir ou alterar.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. No **definições** painel, aceda a **definições do dispositivo > segurança**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. No **definições de segurança** painel, clique em **palavra-passe** definir ou alterar a palavra-passe do Snapshot Manager do StorSimple.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. No **palavra-passe** painel, introduza uma palavra-passe 14 ou 15 carateres. Certifique-se de que a palavra-passe contém uma combinação de 3 ou mais carateres em maiúsculas, minúsculas, numérico e especiais.

6. Confirme a palavra-passe.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Clique em **guardar** e quando for pedida a confirmação, clique em **Sim**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

A palavra-passe do Snapshot Manager do StorSimple agora deve ser atualizada.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [segurança do StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [modificar a configuração do dispositivo](storsimple-8000-modify-device-config.md).
* Saiba mais sobre [utilizando o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

