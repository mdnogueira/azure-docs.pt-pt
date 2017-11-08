---
title: "Gerir as políticas de cópia de segurança do StorSimple | Microsoft Docs"
description: "Explica como pode utilizar o serviço StorSimple Manager para criar e gerir cópias de segurança manuais, as agendas de cópia de segurança e retenção de cópias de segurança."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 4a2db707-bbfc-425c-bfeb-bc5c97781873
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 8bf90b0ca5e488b676b50c37b24202002824c99b
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>Utilizar o serviço StorSimple Manager para gerir políticas de cópia de segurança (atualização 2)
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [utilizar o serviço StorSimple Manager para gerir políticas de cópia de segurança (atualização 2)](storsimple-8000-manage-backup-policies-u2.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Descrição geral
Este tutorial explica como utilizar o serviço StorSimple Manager **políticas de cópia de segurança** página para controlar os processos de cópia de segurança e retenção de cópias de segurança para os volumes do StorSimple. Também descreve como concluir uma cópia de segurança manual.

Quando a cópia de segurança de um volume, pode optar por criar um instantâneo local ou um instantâneo na nuvem. Se estiver a criar cópias de segurança um volume localmente afixado, recomendamos que especificou um instantâneo na nuvem. Colocar um grande número de instantâneos locais de um volume localmente afixado conjugados com um conjunto de dados que tenha uma grande quantidade de volume de alterações resultará numa situação em que foi rapidamente executar espaço local. Se optar por criar instantâneos locais, recomendamos que criar instantâneos diários menos a cópia de segurança do estado mais recente, mantê-los para um dia e, em seguida, elimine-os.

Ao tirar um instantâneo de nuvem de um volume localmente afixado, copie apenas os dados alterados para a nuvem, onde é eliminação de duplicados e comprimido. 

## <a name="the-backup-policies-page"></a>A página de políticas de cópia de segurança
O **políticas de cópia de segurança** página permite-lhe gerir políticas de cópia de segurança e agendar locais e instantâneos de nuvem. (As políticas de cópia de segurança são utilizadas para configurar as agendas de cópia de segurança e retenção de cópias de segurança para uma coleção de volumes). Políticas de cópia de segurança permitem-lhe tirar um instantâneo de vários volumes em simultâneo. Isto significa que as cópias de segurança criadas por uma política de cópia de segurança serão cópias consistentes com falhas. O **políticas de cópia de segurança** página lista as políticas de cópia de segurança, os respetivos tipos, os volumes associados, o número de cópias de segurança de retenção e a opção para ativar estas políticas.

O **políticas de cópia de segurança** página também permite-lhe filtrar as políticas de cópia de segurança existentes por uma ou mais dos seguintes campos:

* **Nome da política** – o nome associado a política. Os diferentes tipos de políticas incluem:
  
  * Políticas agendadas, explicitamente são criadas pelo utilizador.
  * Políticas automáticas, que são criadas quando a cópia de segurança predefinido para esta opção de volume foi ativada no momento da criação de volumes. Estas políticas são designadas como *VolumeName*predefinida onde *VolumeName* refere-se para o nome do volume StorSimple configurado pelo utilizador no portal clássico do Azure. As políticas automáticas resultam em diária instantâneos de nuvem a partir do momento de dispositivo de 22:30.
  * Políticas importadas, que foram criadas originalmente no Snapshot Manager do StorSimple. Estes tem uma etiqueta que descreve o anfitrião do Snapshot Manager do StorSimple que as políticas foram importadas a partir.
* **Volumes** – os volumes associados à política. Todos os volumes associados a uma política de cópia de segurança são agrupados quando forem criadas cópias de segurança.
* **Última cópia de segurança com êxito** – a data e hora da última bem-sucedida cópia de segurança que foi feita com esta política.
* **Cópia de segurança seguinte** – a data e hora da cópia de segurança de agendada seguinte que será iniciada por esta política.
* **As agendas** – o número de agendas associadas à política de cópia de segurança.

As operações utilizadas frequentemente que pode efetuar nesta página são:

* Adicionar uma política de cópias de segurança 
* Adicionar ou modificar uma agenda 
* Eliminar uma política de cópia de segurança 
* Efetuar uma cópia de segurança manual 
* Criar uma política de cópia de segurança personalizada com vários volumes e agendas 

## <a name="add-a-backup-policy"></a>Adicionar uma política de cópias de segurança
Adicione uma política de cópia de segurança para agendar automaticamente as cópias de segurança. Execute os seguintes passos no portal clássico do Azure para adicionar uma política de cópia de segurança para o dispositivo StorSimple. Depois de adicionar a política, pode definir uma agenda (consulte [adicionar ou modificar uma agenda](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Vídeo disponível](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como criar uma local ou na nuvem a política de cópia de segurança, clique em [aqui](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).

## <a name="add-or-modify-a-schedule"></a>Adicionar ou modificar uma agenda
Pode adicionar ou modificar uma agenda que está ligada a uma política de cópia de segurança existente no dispositivo StorSimple. Execute os seguintes passos no portal clássico do Azure para adicionar ou modificar uma agenda.

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>Eliminar uma política de cópia de segurança
Execute os seguintes passos no portal clássico do Azure para eliminar uma política de cópia de segurança no dispositivo StorSimple.

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Efetuar uma cópia de segurança manual
Execute os seguintes passos no portal clássico do Azure para criar uma cópia de segurança (manual) de um único volume a pedido.

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Criar uma política de cópia de segurança personalizada com vários volumes e agendas
Execute os seguintes passos no portal clássico do Azure para criar uma política de cópia de segurança personalizada que tenha vários volumes e agendamentos.

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

