---
title: Gerir registos de controlo de acesso para a matriz Virtual StorSimple | Microsoft Docs
description: "Descreve como gerir registos de controlo de acesso (ACRs) para determinar os anfitriões que podem ligar a um volume na matriz Virtual StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Utilize o Gestor de dispositivo StorSimple para gerir registos de controlo de acesso para a matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Registos de controlo de acesso (ACRs) permitem-lhe especificar os anfitriões que podem ligar a um volume na matriz Virtual StorSimple (também conhecido como o local no dispositivo virtual StorSimple). ACRs estão definidas para um volume específico e incluem os nomes de qualificado iSCSI (IQNs) de anfitriões. Quando um anfitrião tenta ligar a um volume, o dispositivo verificará se existe o ACR associado a esse volume para o nome IQN e, se existir uma correspondência, em seguida, a ligação for estabelecida. O **registos de controlo de acesso** painel dentro de **configuração** secção do seu serviço do Gestor de dispositivos mostra todos os registos de controlo de acesso com os IQNs correspondentes dos anfitriões.

![Gerir registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Este tutorial explica as seguintes tarefas de relacionados com o ACR comuns:

* Obter o IQN
* Adicionar um registo de controlo de acesso
* Editar um registo de controlo de acesso
* Elimine um registo de controlo de acesso

> [!IMPORTANT]
> 
> * Ao atribuir um ACR a um volume, asseguramos que o volume não estiver simultaneamente acedido por mais do que um anfitrião agrupado porque esta foi danificado o volume.
> * Quando eliminar um ACR a partir de um volume, certifique-se de que o anfitrião correspondente está não a aceder ao volume porque a eliminação pode resultar numa interrupção de leitura e escrita.


## <a name="get-the-iqn"></a>Obter o IQN

Execute os passos seguintes para obter o IQN de um anfitrião do Windows que está a executar o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adicionar um ACR

Utilizar **registos de controlo de acesso** painel dentro de **configuração** secção do serviço StorSimple Manager de dispositivo para adicionar ACRs. Normalmente, um ACR associar um volume.

Para informações sobre como associar um ACR um volume, aceda a [adicionar um volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Ao atribuir um ACR a um volume, asseguramos que o volume não estiver simultaneamente acedido por mais do que um anfitrião agrupado porque esta foi danificado o volume.


Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Na página serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, dentro de **configuração** secção, clique em **registos de controlo de acesso**.
2. No **registos de controlo de acesso** painel, clique em **adicionar**.
3. No **adicionar ACR** painel, efetue o seguinte procedimento:
   
    1. Forneça um **Nome** para o ACR.
    
    2. Em **nome do iniciador iSCSI**, forneça o nome IQN do anfitrião do Windows. Para obter o IQN de anfitrião do Windows Server, efetue o seguinte:
   
    3. Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows. Na janela de propriedades do iniciador iSCSI, no **configuração** separador, selecione e copie a cadeia do **nome do iniciador** campo.
    Cole este cadeia no **IQN** campo no **adicionar ACR** painel.
   
    6. Clique em **adicionar** para adicionar o ACR.  
   
        ![Adicionar registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. A listagem de tabela é atualizada para refletir esta adição.

## <a name="edit-an-acr"></a>Editar um ACR

Utilizar o **registos de controlo de acesso** painel dentro de **configuração** secção do seu serviço do Gestor de dispositivos no portal do Azure para editar ACRs.

> [!NOTE]
> Não deve modificar um ACR que está atualmente em utilização. Para editar um ACR associado um volume que está a ser utilizada, deve primeiro ter o volume offline.


Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-acr"></a>Para editar um ACR

1. Na página serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, dentro de **configuração** secção, **registos de controlo de acesso**.
2. No **registos de controlo de acesso** painel, da tabela a listagem dos registos de controlo de acesso, faça duplo clique ACR que pretende modificar.
3. No **registos de controlo de acesso de edição** painel, efetue o seguinte procedimento:
   
    1. Forneça o IQN para o ACR.
   
    2. Clique em **guardar** na parte superior do painel para guardar o ACR modificada. Consulte a seguinte mensagem de confirmação:
   
        ![Editar registos de controlo de acesso](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Elimine um registo de controlo de acesso

Utilizar o **configuração** página no portal do Azure para eliminar ACRs.

> [!NOTE]
> 
> * Não deverá eliminar um ACR que está atualmente em utilização. Para eliminar um ACR associado um volume que está a ser utilizada, deve primeiro ter o volume offline.
> * Quando eliminar um ACR a partir de um volume, certifique-se de que o anfitrião correspondente está não a aceder ao volume porque a eliminação pode resultar numa interrupção de leitura e escrita.


Execute os seguintes passos para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar um registo de controlo de acesso

1. Na página serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, dentro de **configuração** secção, **registos de controlo de acesso**.

2. No **registos de controlo de acesso** painel, da tabela a listagem dos registos de controlo de acesso, faça duplo clique ACR que pretende eliminar.

3. No painel de registos de controlo do acesso de edição, clique em **eliminar**.
   
    ![Eliminar ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Quando lhe for pedido para confirmação, clique em **eliminar** para continuar com a eliminação. A listagem de tabela é atualizada para refletir a eliminação.
   
   ![Mensagem de aviso](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [adicionar volumes e configurar ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

