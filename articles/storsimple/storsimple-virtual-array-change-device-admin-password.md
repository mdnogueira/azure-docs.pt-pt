---
title: "Palavra-passe de administrador de dispositivo de matriz Virtual StorSimple alteração | Microsoft Docs"
description: Descreve como utilizar o portal do Azure ou a IU da web do matriz Virtual StorSimple para alterar a palavra-passe de administrador do dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 260a23003d705e6598da8c51bb5a96f2539a0014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Alterar o dispositivo de matriz Virtual StorSimple administrador palavra-passe através do Gestor de dispositivos do StorSimple

## <a name="overview"></a>Descrição geral

Quando utilizar a interface do Windows PowerShell para aceder à matriz de Virtual do StorSimple, é necessário para introduzir uma palavra-passe de administrador do dispositivo. Quando o dispositivo StorSimple primeiro está aprovisionado e foi iniciado, a palavra-passe predefinida é *Password1*. Para garantir a segurança dos seus dados, a palavra-passe predefinida expira na primeira vez que iniciar sessão e é necessários para alterar esta palavra-passe.

Também pode utilizar a IU da web local ou o portal do Azure para alterar a palavra-passe de administrador do dispositivo em qualquer altura depois do dispositivo for implementado num ambiente de produção. Cada um destes procedimentos é descrita neste artigo.

 ![Painel de dispositivos](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Utilize o portal do Azure para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo através do portal do Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Para alterar a palavra-passe de administrador do dispositivo através do portal do Azure

1. Na página serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, dentro de **gestão** secção, clique em **dispositivos**. Esta ação abre o **dispositivos** painel que lista todos os seus dispositivos de matriz Virtual StorSimple.

2. No **dispositivos** painel, faça duplo clique no dispositivo que necessita de uma alteração de palavra-passe.

3. No **definições** painel para o seu dispositivo, clique em **segurança**.

4. No **definições de segurança** painel, efetue o seguinte procedimento:
   
   1. Desloque para baixo até o **palavra-passe de administrador do dispositivo** secção. Forneça uma palavra-passe de administrador que contém 8 e 15 carateres.
   2. Confirme a palavra-passe.
   3. Clique em **guardar** na parte superior do painel.

Agora é atualizada a palavra-passe de administrador do dispositivo. Pode utilizar esta palavra-passe modificado para aceder ao dispositivo localmente.

![Painel de definições de segurança](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Utilize a IU da web local para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo através da IU da web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para alterar a palavra-passe de administrador do dispositivo através da IU da web local

1. No local IU da web, clique em **manutenção** > **alteração de palavra-passe** para o seu dispositivo.
   
    ![alterar password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Introduza o **palavra-passe atual**.
3. Forneça um **nova palavra-passe**. A palavra-passe tem de ser, pelo menos, 8 carateres de comprimento. Tem de conter 3 dos 4 dos seguintes procedimentos: carateres em maiúsculas, minúsculas, numérico e especiais.
   
    Tenha em atenção que a palavra-passe não pode ser o mesmo que as últimas 24 palavras-passe.
4. Reintroduza a palavra-passe para confirmá-la.
   
    ![alterar password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Na parte inferior da página, clique em **aplicar**. Agora é aplicada a nova palavra-passe. Se a alteração de palavra-passe não for bem sucedida, verá o erro seguinte:
   
    ![Erro de palavra-passe](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Depois da palavra-passe foi atualizada com êxito, será notificado. Em seguida, pode utilizar esta palavra-passe modificado para aceder ao dispositivo localmente.


## <a name="next-steps"></a>Passos seguintes
Saiba como [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

