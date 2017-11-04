---
title: "Gerir políticas de cópia de segurança de série 8000 do StorSimple | Microsoft Docs"
description: "Explica como pode utilizar o serviço do Gestor de dispositivos do StorSimple para criar e gerir cópias de segurança manuais, as agendas de cópia de segurança e retenção de cópias de segurança num dispositivo série 8000 do StorSimple."
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
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Utilizar o serviço StorSimple Manager de dispositivos no portal do Azure para gerir políticas de cópia de segurança


## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço do Gestor de dispositivos do StorSimple **política de cópia de segurança** painel para controlar os processos de cópia de segurança e retenção de cópias de segurança para os volumes do StorSimple. Também descreve como concluir uma cópia de segurança manual.

Quando a cópia de segurança de um volume, pode optar por criar um instantâneo local ou um instantâneo na nuvem. Se estiver a criar cópias de segurança um volume localmente afixado, recomendamos que especificou um instantâneo na nuvem. Colocar um grande número de instantâneos locais de um volume localmente afixado conjugados com um conjunto de dados que tenha uma grande quantidade de volume de alterações resultará numa situação em que foi rapidamente executar espaço local. Se optar por criar instantâneos locais, recomendamos que criar instantâneos diários menos a cópia de segurança do estado mais recente, mantê-los para um dia e, em seguida, elimine-os.

Ao tirar um instantâneo de nuvem de um volume localmente afixado, copie apenas os dados alterados para a nuvem, onde é eliminação de duplicados e comprimido.

## <a name="the-backup-policy-blade"></a>O painel de política de cópia de segurança

O **política de cópia de segurança** painel para o dispositivo StorSimple permite-lhe gerir políticas de cópia de segurança e agendar locais e instantâneos de nuvem. Políticas de cópia de segurança são utilizadas para configurar as agendas de cópia de segurança e retenção de cópias de segurança para uma coleção de volumes. Políticas de cópia de segurança permitem-lhe tirar um instantâneo de vários volumes em simultâneo. Isto significa que as cópias de segurança criadas por uma política de cópia de segurança serão cópias consistentes com falhas.

A listagem de tabela de políticas de cópia de segurança também permite-lhe filtrar as políticas de cópia de segurança existentes por uma ou mais dos seguintes campos:

* **Nome da política** – o nome associado a política. Os diferentes tipos de políticas incluem:

  * Políticas agendadas, explicitamente são criadas pelo utilizador.
  * Políticas importadas, que foram criadas originalmente no Snapshot Manager do StorSimple. Estes tem uma etiqueta que descreve o anfitrião do Snapshot Manager do StorSimple que as políticas foram importadas a partir.

  > [!NOTE]
  > Políticas de cópia de segurança automática ou predefinição já não estão ativadas no momento da criação de volumes.

* **Última cópia de segurança com êxito** – a data e hora da última bem-sucedida cópia de segurança que foi feita com esta política.

* **Cópia de segurança seguinte** – a data e hora da cópia de segurança de agendada seguinte que será iniciada por esta política.

* **Volumes** – os volumes associados à política. Todos os volumes associados a uma política de cópia de segurança são agrupados quando forem criadas cópias de segurança.

* **As agendas** – o número de agendas associadas à política de cópia de segurança.

As operações utilizadas frequentemente que pode efetuar para políticas de cópia de segurança são:

* Adicionar uma política de cópias de segurança
* Adicionar ou modificar uma agenda
* Adicionar ou remover um volume
* Eliminar uma política de cópia de segurança
* Efetuar uma cópia de segurança manual

## <a name="add-a-backup-policy"></a>Adicionar uma política de cópias de segurança

Adicione uma política de cópia de segurança para agendar automaticamente as cópias de segurança. Quando cria pela primeira vez um volume, não há nenhuma política de cópia de segurança predefinida associada com o volume. Terá de adicionar e atribuir uma política de cópia de segurança para proteger os dados do volume.

Execute os seguintes passos no portal do Azure para adicionar uma política de cópia de segurança para o dispositivo StorSimple. Depois de adicionar a política, pode definir uma agenda (consulte [adicionar ou modificar uma agenda](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Adicionar ou modificar uma agenda

Pode adicionar ou modificar uma agenda que está ligada a uma política de cópia de segurança existente no dispositivo StorSimple. Execute os seguintes passos no portal do Azure para adicionar ou modificar uma agenda.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Adicionar ou remover um volume

Pode adicionar ou remover um volume atribuído a uma política de cópia de segurança no dispositivo StorSimple. Execute os seguintes passos no portal do Azure para adicionar ou remover um volume.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Eliminar uma política de cópia de segurança

Execute os seguintes passos no portal do Azure para eliminar uma política de cópia de segurança no dispositivo StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Efetuar uma cópia de segurança manual

Execute os seguintes passos no portal do Azure para criar uma cópia de segurança a pedido (manual) de um único volume.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [utilizando o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

