---
title: "Gerir o catálogo de cópias de segurança do StorSimple | Microsoft Docs"
description: "Explica como utilizar a página de catálogo de cópias de segurança do serviço StorSimple Manager para a lista, selecione e eliminar conjuntos de cópias de segurança para um volume."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ad81bee9-fe43-40b3-a384-b15fb274ecd9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 732ae04a8ae5f85ed154370c680d87af2ba5ee39
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Utilizar o serviço StorSimple Manager para gerir o seu catálogo de cópias de segurança
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [utilizar o serviço StorSimple Manager para gerir o seu catálogo de cópias de segurança](storsimple-8000-manage-backup-catalog.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
O serviço StorSimple Manager **catálogo de cópias de segurança** página apresenta todos os conjuntos de cópias de segurança que são criados quando são efetuadas cópias de segurança de manuais ou agendadas. Pode utilizar esta página para listar todas as cópias de segurança para uma política de cópia de segurança ou um volume, selecione ou eliminar as cópias de segurança ou utilize uma cópia de segurança para restaurar ou clonar um volume.

Este tutorial explica como lista, selecione e eliminar um conjunto de cópia de segurança. Para saber como restaurar o dispositivo de cópia de segurança, aceda à [restaurar o seu dispositivo a partir de um conjunto de cópia de segurança](storsimple-restore-from-backup-set.md). Para saber como clonar um volume, aceda a [clonar um volume StorSimple](storsimple-clone-volume.md).

![Catálogo de cópias de segurança](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

O **catálogo de cópias de segurança** página fornece uma consulta para restringir a cópia de segurança definir seleção. Pode filtrar os conjuntos de cópia de segurança que são obtidos, com base nos parâmetros seguintes:

* **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.
* **Volume ou a política de cópia de segurança** – a política de cópia de segurança ou volume associados a este conjunto de cópia de segurança.
* **A partir de e para** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.

Os conjuntos de cópia de segurança filtrados, em seguida, são apresentados com base nos seguintes atributos:

* **Nome** – o nome da política de cópia de segurança ou volume associado o conjunto de cópia de segurança.
* **Tamanho** – o tamanho real do conjunto de cópia de segurança.
* **Criado no** – a data e hora quando as cópias de segurança foram criadas. 
* **Tipo** – conjuntos de cópia de segurança podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, enquanto que um instantâneo na nuvem refere-se para a cópia de segurança de dados do volume que residem na nuvem. Instantâneos locais fornecem um acesso mais rápido, enquanto que os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Iniciado pelo** – as cópias de segurança podem ser iniciadas automaticamente por um agendamento ou manualmente por um utilizador. Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar o **efetuar cópia de segurança** opção para efetuar uma cópia de segurança manual.

## <a name="list-backup-sets-for-a-volume"></a>Define a cópia de segurança da lista para um volume
Conclua os passos seguintes para listar todas as cópias de segurança para um volume.

#### <a name="to-list-backup-sets"></a>Para conjuntos de cópias de segurança de lista
1. Na página de serviço StorSimple Manager, clique o **catálogo de cópia de segurança** separador.
2. As seleções de filtro da seguinte forma:
   
   1. Selecione o dispositivo adequado.
   2. Na lista pendente, escolha um volume para ver as cópias de segurança correspondente.
   3. Especifique o intervalo de tempo.
   4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.
      
      As cópias de segurança associadas ao volume selecionado devem aparecer na lista de conjuntos de cópia de segurança.

## <a name="select-a-backup-set"></a>Selecione um conjunto de cópia de segurança
Conclua os seguintes passos para selecionar uma cópia de segurança definido para um volume ou a política de cópia de segurança.

#### <a name="to-select-a-backup-set"></a>Para selecionar um conjunto de cópia de segurança
1. Na página de serviço StorSimple Manager, clique o **catálogo de cópia de segurança** separador.
2. As seleções de filtro da seguinte forma:
   
   1. Selecione o dispositivo adequado.
   2. Na lista pendente, selecione a política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.
      
      As cópias de segurança associado ao volume selecionado ou a política de cópia de segurança deve aparecer na lista de conjuntos de cópia de segurança.
3. Selecione e expanda um conjunto de cópia de segurança. O **restaurar** e **eliminar** opções são apresentadas na parte inferior da página. Pode efetuar qualquer uma destas ações no conjunto de cópia de segurança que selecionou.

## <a name="delete-a-backup-set"></a>Eliminar um conjunto de cópia de segurança
Elimine uma cópia de segurança quando que já não pretende manter os dados associados à mesma. Execute os seguintes passos para eliminar um conjunto de cópia de segurança.

#### <a name="to-delete-a-backup-set"></a>Para eliminar um conjunto de cópia de segurança
1. Na página de serviço StorSimple Manager, clique o **separador do catálogo de cópia de segurança**.
2. As seleções de filtro da seguinte forma:
   
   1. Selecione o dispositivo adequado.
   2. Na lista pendente, selecione a política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.
      
      As cópias de segurança associado ao volume selecionado ou a política de cópia de segurança deve aparecer na lista de conjuntos de cópia de segurança.
3. Selecione e expanda um conjunto de cópia de segurança. O **restaurar** e **eliminar** opções são apresentadas na parte inferior da página. Clique em **Eliminar**.
4. Será notificado quando a eliminação está em progresso e quando foi concluída com êxito. Depois de terminar a eliminação, atualize a consulta nesta página. O conjunto de cópia de segurança eliminado já não aparecem na lista de conjuntos de cópia de segurança.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o catálogo de cópias de segurança para restaurar o seu dispositivo a partir de um conjunto de cópia de segurança](storsimple-restore-from-backup-set.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

