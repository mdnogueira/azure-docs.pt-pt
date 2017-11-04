---
title: "Gerir o catálogo de cópias de segurança do StorSimple | Microsoft Docs"
description: "Explica como utilizar a página de cópia de segurança do catálogo de serviço do Gestor de dispositivos do StorSimple à lista, selecione e eliminar conjuntos de cópia de segurança."
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para gerir o catálogo de cópias de segurança
## <a name="overview"></a>Descrição geral
O serviço do Gestor de dispositivos do StorSimple **catálogo de cópias de segurança** painel apresenta todos os conjuntos de cópias de segurança que são criados quando são efetuadas cópias de segurança de manuais ou agendadas. Pode utilizar esta página para listar todas as cópias de segurança para uma política de cópia de segurança ou um volume, selecione ou eliminar as cópias de segurança ou utilize uma cópia de segurança para restaurar ou clonar um volume.

Este tutorial explica como lista, selecione e eliminar um conjunto de cópia de segurança. Para saber como restaurar o dispositivo de cópia de segurança, aceda à [restaurar o seu dispositivo a partir de um conjunto de cópia de segurança](storsimple-8000-restore-from-backup-set-u2.md). Para saber como clonar um volume, aceda a [clonar um volume StorSimple](storsimple-8000-clone-volume-u2.md).

![Catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

O **catálogo de cópias de segurança** painel fornece uma consulta para restringir a cópia de segurança definir seleção. Pode filtrar os conjuntos de cópia de segurança que são obtidos, com base nos parâmetros seguintes:

* **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.
* **Política de cópia de segurança ou Volume** – a política de cópia de segurança ou volume associados a este conjunto de cópia de segurança.
* **A partir de e para** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.

Os conjuntos de cópia de segurança filtrados, em seguida, são apresentados com base nos seguintes atributos:

* **Nome** – o nome da política de cópia de segurança ou volume associado o conjunto de cópia de segurança.
* **Tamanho** – o tamanho real do conjunto de cópia de segurança.
* **Criado no** – a data e hora quando as cópias de segurança foram criadas. 
* **Tipo** – conjuntos de cópia de segurança podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, enquanto que um instantâneo na nuvem refere-se para a cópia de segurança de dados do volume que residem na nuvem. Instantâneos locais fornecem um acesso mais rápido, enquanto que os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Iniciado pelo** – as cópias de segurança podem ser iniciadas automaticamente por um agendamento ou manualmente por um utilizador. Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar o **efetuar cópia de segurança** opção para efetuar uma cópia de segurança manual.

## <a name="list-backup-sets-for-a-backup-policy"></a>Define a cópia de segurança da lista para uma política de cópia de segurança
Conclua os passos seguintes para listar todas as cópias de segurança para uma política de cópia de segurança.

#### <a name="to-list-backup-sets"></a>Para conjuntos de cópias de segurança de lista
1. Vá para o Gestor de dispositivos do StorSimple de serviço e clique em **catálogo de cópia de segurança**.

2. As seleções de filtro da seguinte forma:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo adequado.
   3. Filtrar por **política de cópia de segurança** para ver as cópias de segurança correspondente.
   3. Na lista pendente, política de cópia de segurança, escolha **todos os** para ver todas as cópias de segurança do dispositivo selecionado.
   4. Clique em **aplicar** para executar esta consulta.
      
      As cópias de segurança associadas à política de cópia de segurança selecionada devem aparecer na lista de conjuntos de cópia de segurança.

      ![Aceda ao catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Selecione um conjunto de cópia de segurança
Conclua os seguintes passos para selecionar uma cópia de segurança definido para um volume ou a política de cópia de segurança.

#### <a name="to-select-a-backup-set"></a>Para selecionar um conjunto de cópia de segurança
1. Vá para o Gestor de dispositivos do StorSimple de serviço e clique em **catálogo de cópia de segurança**.
2. As seleções de filtro da seguinte forma:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo adequado. 
   3. Filtrar pela política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   4. Clique em **aplicar** para executar esta consulta.
      
      As cópias de segurança associado ao volume selecionado ou a política de cópia de segurança deve aparecer na lista de conjuntos de cópia de segurança.

      ![Aceda ao catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecione e expanda um conjunto de cópia de segurança. Agora, pode ver os conjuntos de cópia de segurança, discriminados segundo os volumes nele contidos. O **restaurar** e **eliminar** opções estão disponíveis através do menu de contexto (rato) para o conjunto de cópia de segurança. Pode efetuar qualquer uma destas ações no conjunto de cópia de segurança que selecionou.

    ![Aceda ao catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Eliminar um conjunto de cópia de segurança
Elimine uma cópia de segurança quando que já não pretende manter os dados associados à mesma. Execute os seguintes passos para eliminar um conjunto de cópia de segurança.

#### <a name="to-delete-a-backup-set"></a>Para eliminar um conjunto de cópia de segurança
 Vá para o Gestor de dispositivos do StorSimple de serviço e clique em **catálogo de cópia de segurança**.
2. As seleções de filtro da seguinte forma:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo adequado. 
   3. Filtrar pela política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   4. Clique em **aplicar** para executar esta consulta.
      
      As cópias de segurança associado ao volume selecionado ou a política de cópia de segurança deve aparecer na lista de conjuntos de cópia de segurança.

      ![Aceda ao catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecione e expanda um conjunto de cópia de segurança. Agora, pode ver os conjuntos de cópia de segurança, discriminados segundo os volumes nele contidos. O **restaurar** e **eliminar** opções estão disponíveis através do menu de contexto (rato) para o conjunto de cópia de segurança. Clique com o botão direito do conjunto de cópias de segurança selecionado e no menu de contexto, selecione **eliminar**.

    ![Aceda ao catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. Quando lhe for pedido para confirmação, reveja as informações apresentadas e clique em **eliminar**. A cópia de segurança selecionada é eliminada permanentemente.

    ![Aceda ao catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Será notificado quando a eliminação está em progresso e quando foi concluída com êxito. Depois de terminar a eliminação, atualize a consulta nesta página. O conjunto de cópia de segurança eliminado já não aparecem na lista de conjuntos de cópia de segurança.

    ![Aceda ao catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o catálogo de cópias de segurança para restaurar o seu dispositivo a partir de um conjunto de cópia de segurança](storsimple-8000-restore-from-backup-set-u2.md).
* Saiba como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

