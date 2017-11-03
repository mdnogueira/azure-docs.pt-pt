---
title: Gerir registos de controlo de acesso no StorSimple | Microsoft Docs
description: "Descreve como utilizar os registos de controlo de acesso (ACRs) para determinar os anfitriões que podem ligar a um volume no dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2f1475d8-36a5-4cc4-84b9-adf8a310b60c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: a87624b5706c1d9b8c2b9926e5580996a89ce984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Utilizar o serviço StorSimple Manager para gerir registos de controlo de acesso
## <a name="overview"></a>Descrição geral
Registos de controlo de acesso (ACRs) permitem-lhe especificar os anfitriões que podem ligar a um volume no dispositivo StorSimple. ACRs estão definidas para um volume específico e incluem os nomes de qualificado iSCSI (IQNs) de anfitriões. Quando um anfitrião tenta ligar a um volume, o dispositivo verificará se existe o ACR associados a esse volume para o nome IQN e se existir uma correspondência, em seguida, a ligação for estabelecida. O controlo de acesso regista secção no **configurar** página apresenta todos os registos de controlo de acesso com os IQNs correspondentes dos anfitriões.

Este tutorial explica as seguintes tarefas de relacionados com o ACR comuns:

* Adicionar um registo de controlo de acesso 
* Editar um registo de controlo de acesso 
* Elimine um registo de controlo de acesso 

> [!IMPORTANT]
> * Ao atribuir um ACR a um volume, asseguramos que o volume não estiver simultaneamente acedido por mais do que um anfitrião agrupado porque esta foi danificado o volume. 
> * Quando eliminar um ACR a partir de um volume, certifique-se de que o anfitrião correspondente está não a aceder ao volume porque a eliminação pode resultar numa interrupção de leitura e escrita.
> 
> 

## <a name="add-an-access-control-record"></a>Adicionar um registo de controlo de acesso
Utilizar o serviço StorSimple Manager **configurar** página para adicionar ACRs. Normalmente, irá associar um ACR um volume.

Execute os seguintes passos para adicionar um ACR.

#### <a name="to-add-an-access-control-record"></a>Para adicionar um registo de controlo de acesso
1. Na página serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, clique em de **configurar** separador.
2. Na tabela listagem sob **registos de controlo de acesso**, forneça um **nome** para o ACR.
3. Forneça o nome IQN do anfitrião do Windows em **nome do iniciador iSCSI**. Para obter o IQN de anfitrião do Windows Server, efetue o seguinte:
   
   * Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows.
   * Na janela **Propriedades do Iniciador iSCSI**, no separador **Configuração**, selecione e copie a cadeia do campo **Nome do Iniciador**.
   * Cole este cadeia no **nome do iniciador iSCSI** campo da tabela de ACRs no portal clássico do Azure.
4. Clique em **guardar** para guardar o ACR recentemente criado. A listagem de tabela será atualizada para refletir esta adição.

## <a name="edit-an-access-control-record"></a>Editar um registo de controlo de acesso
Utilizar o **configurar** página no portal clássico do Azure para editar ACRs. 

> [!NOTE]
> Pode modificar apenas esses ACRs que estão atualmente não em utilização. Para editar um ACR associado um volume que está a ser utilizada, primeiro tem de seguir o volume offline.
> 
> 

Execute os seguintes passos para editar um ACR.

#### <a name="to-edit-an-access-control-record"></a>Para editar um registo de controlo de acesso
1. Na página serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, clique em de **configurar** separador.
2. Na tabela listagem dos registos de controlo de acesso, coloque o cursor sobre o ACR que pretende modificar.
3. Forneça um novo nome e/ou IQN para o ACR.
4. Clique em **guardar** para guardar o ACR modificada. A listagem de tabela será atualizada para refletir esta alteração.

## <a name="delete-an-access-control-record"></a>Elimine um registo de controlo de acesso
Utilizar o **configurar** página no portal clássico do Azure para eliminar ACRs. 

> [!NOTE]
> Pode eliminar apenas esses ACRs que estão atualmente não em utilização. Para eliminar um ACR associado um volume que está a ser utilizada, primeiro tem de seguir o volume offline.
> 
> 

Execute os seguintes passos para eliminar um registo de controlo de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar um registo de controlo de acesso
1. Na página serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, clique em de **configurar** separador.
2. Numa listagem de tabela de registos de controlo de acesso (ACRs), coloque o cursor sobre o ACR que pretende eliminar.
3. Um ícone Eliminar (**x**) será apresentado na coluna da direita Alpine para o ACR que selecionar. Clique em de **x** ícone Eliminar o ACR.
4. Quando lhe for pedido para confirmação, clique em **Sim** para continuar com a eliminação. A listagem de tabela será atualizada para refletir a eliminação.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [gerir volumes do StorSimple](storsimple-manage-volumes.md).
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

