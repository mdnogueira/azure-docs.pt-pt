---
title: "Restaurar um volume StorSimple a partir de cópia de segurança | Microsoft Docs"
description: "Explica como utilizar a página de catálogo de cópias de segurança do serviço StorSimple Manager para restaurar um volume StorSimple a partir de um conjunto de cópia de segurança."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b979782e-3184-4465-ad5f-e4516a5885d2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 0ed433ba93cb08742e3f0b09cc7acaa2cd62461e
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um volume StorSimple a partir de um conjunto de cópia de segurança
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Descrição geral
O **catálogo de cópias de segurança** página apresenta todos os conjuntos de cópias de segurança que são criados quando são efetuadas cópias de segurança manuais ou automáticas. Pode utilizar esta página para listar todas as cópias de segurança para uma política de cópia de segurança ou um volume, selecione ou eliminar as cópias de segurança ou utilize uma cópia de segurança para restaurar ou clonar um volume.

 ![Página do catálogo de cópias de segurança](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Este tutorial explica como utilizar o **catálogo de cópias de segurança** página para restaurar um volume no seu dispositivo a partir de um conjunto de cópia de segurança.

## <a name="how-to-use-the-backup-catalog"></a>Como utilizar o catálogo de cópias de segurança
O **catálogo de cópias de segurança** página fornece uma consulta que ajuda a restringir a cópia de segurança definir seleção. Pode filtrar os conjuntos cópia de segurança que são obtidos com base nos parâmetros seguintes:

* **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.
* **Política de cópia de segurança** ou **volume** – a política de cópia de segurança ou volume associados a este conjunto de cópia de segurança.
* **De** e **para** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.

Os conjuntos de cópia de segurança filtrados, em seguida, são apresentados com base nos seguintes atributos:

* **Nome** – o nome da política de cópia de segurança ou volume associado o conjunto de cópia de segurança.
* **Tamanho** – o tamanho real do conjunto de cópia de segurança.
* **Criado no** – a data e hora quando as cópias de segurança foram criadas. 
* **Tipo** – conjuntos de cópia de segurança podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, enquanto que um instantâneo na nuvem refere-se para a cópia de segurança de dados do volume que residem na nuvem. Instantâneos locais fornecem um acesso mais rápido, enquanto que os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Iniciado pelo** – as cópias de segurança podem ser iniciadas automaticamente, de acordo com um agendamento ou manualmente por um utilizador. (Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar o **efetuar cópia de segurança** opção para efetuar uma cópia de segurança interativa.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume do StorSimple a partir de uma cópia de segurança
Pode utilizar o **catálogo de cópias de segurança** página para restaurar o volume do StorSimple a partir de uma cópia de segurança específica. 

> [!WARNING]
> Restaurar a partir de uma cópia de segurança irá substituir os volumes existentes da cópia de segurança. Isto pode provocar a perda de quaisquer dados que foi escritos depois de cópia de segurança.
> 
> 

Antes de iniciar um restauro de um volume, certifique-se de que o volume está offline. É necessário colocar offline o volume do anfitrião primeiro e, em seguida, o dispositivo. Siga os passos no [colocar offline um volume](storsimple-manage-volumes.md#take-a-volume-offline). Execute os seguintes passos para restaurar um volume a partir de um conjunto de cópia de segurança.

### <a name="to-restore-from-a-backup-set"></a>Para restaurar a partir de um conjunto de cópia de segurança
1. Na página de serviço StorSimple Manager, clique o **catálogo de cópia de segurança** separador.
   
    ![Catálogo de cópias de segurança](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. Selecione uma cópia de segurança definida da seguinte forma:
   
   1. Selecione o dispositivo adequado.
   2. Na lista pendente, selecione a política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) para executar esta consulta.
      
      As cópias de segurança associado ao volume selecionado ou a política de cópia de segurança deve aparecer na lista de conjuntos de cópia de segurança.
3. Expanda a cópia de segurança definida para ver os volumes associados. Estes volumes devem ser colocados offline no anfitrião e do dispositivo antes de pode restaurá-las. Siga os passos no [colocar offline um volume](storsimple-manage-volumes.md#take-a-volume-offline).
   
   > [!IMPORTANT]
   > Certifique-se de que seguiu os volumes offline no anfitrião em primeiro lugar, antes de colocar offline os volumes no dispositivo. Se não efetuar os volumes offline no anfitrião, pode, potencialmente, levar a danos nos dados.
   > 
   > 
4. Selecione um conjunto de cópia de segurança. Clique em **restaurar** na parte inferior da página.
5. Será solicitado para confirmação. 
   
    ![Página de confirmação](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. Reveja as informações de restauro e clique no ícone de verificação ![ícone de verificação](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Isto iniciará uma tarefa de restauro que pode ver acedendo a **tarefas** página. 
7. Após o restauro está concluído, pode verificar que o conteúdo do seus volumes é substituído pelos volumes a partir da cópia de segurança.

![Vídeo disponível](./media/storsimple-restore-from-backup-set/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como pode utilizar o clone e restaurar as funcionalidades do StorSimple para recuperar ficheiros eliminados, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Passos seguintes
* Saiba como [volumes do StorSimple gerir](storsimple-manage-volumes.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

