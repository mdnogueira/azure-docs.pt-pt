---
title: "Políticas de cópia de segurança do Snapshot Manager do StorSimple | Microsoft Docs"
description: "Descreve como utilizar o snap-in MMC de Snapshot Manager do StorSimple para criar e gerir as políticas de cópia de segurança que controlam as cópias de segurança agendadas."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 218c89e403673c16c72da95aa2c1d685bbed5a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Utilize o Snapshot Manager do StorSimple para criar e gerir políticas de cópia de segurança
## <a name="overview"></a>Descrição geral
Uma política de cópia de segurança cria uma agenda para cópias de segurança dados de volumes localmente ou na nuvem. Quando cria uma política de cópia de segurança, também pode especificar uma política de retenção. (Pode manter um máximo de 64 instantâneos). Para obter mais informações sobre as políticas de cópia de segurança, consulte [tipos de cópia de segurança](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) no [série 8000 do StorSimple: uma solução de nuvem híbrida](storsimple-overview.md).

Este tutorial explica como:

* Criar uma política de cópia de segurança
* Editar uma política de cópia de segurança
* Eliminar uma política de cópia de segurança

## <a name="create-a-backup-policy"></a>Criar uma política de cópia de segurança
Utilize o procedimento seguinte para criar uma nova política de cópia de segurança.

#### <a name="to-create-a-backup-policy"></a>Para criar uma política de cópia de segurança
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique **políticas de cópia de segurança**e clique em **criar política de cópia de segurança**.

    ![Criar uma política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    O **criar uma política** é apresentada a caixa de diálogo.

    ![Criar uma política - separador Geral](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. No **geral** separador, conclua as seguintes informações:

   1. No **nome** caixa de texto, escreva um nome para a política.
   2. No **grupo Volume** caixa de texto, escreva o nome do grupo de volume associadas à política.
   3. Selecione **instantâneo Local** ou **instantâneos de nuvem**.
   4. Selecione o número de instantâneos a manter. Se selecionar **todos os**, 64 instantâneos irão ser retidos (o máximo).
4. Clique em de **agenda** separador.

    ![Criar uma política - separador Agenda](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. No **agenda** separador, conclua as seguintes informações:

   1. Clique em de **ativar** caixa de verificação para agendar a cópia de segurança seguinte.
   2. Em **definições**, selecione **uma vez**, **diária**, **semanal**, ou **mensal**.
   3. No **iniciar** caixa de texto, clique no ícone de calendário e selecione uma data de início.
   4. Em **definições avançadas**, pode definir agendas de repetição opcionais e uma data de fim.
   5. Clique em **OK**.

Depois de criar uma política de cópia de segurança, as seguintes informações aparecem no **resultados** painel:

* **Nome** – o nome da política de cópia de segurança.
* **Tipo** – instantâneo local ou um instantâneo na nuvem.
* **Grupo de volume** – o grupo de volume associado à política.
* **Retenção** – o número de instantâneos retidos; o máximo são 64.
* **Criado** – a data em que esta política foi criada.
* **Ativado** – se a política está atualmente em efeito: **verdadeiro** indica que está em vigor; **Falso** indica que não está a ser aplicadas.

## <a name="edit-a-backup-policy"></a>Editar uma política de cópia de segurança
Utilize o procedimento seguinte para editar uma política de cópia de segurança existente.

#### <a name="to-edit-a-backup-policy"></a>Para editar uma política de cópia de segurança
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, clique no **políticas de cópia de segurança** nós. Todas as políticas de cópia de segurança são apresentados no **resultados** painel.
3. Clique com o botão direito a política que pretende editar e, em seguida, clique em **editar**.

    ![Editar uma política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Quando o **criar uma política** surge a janela, introduza as suas alterações e, em seguida, clique em **OK**.

## <a name="delete-a-backup-policy"></a>Eliminar uma política de cópia de segurança
Utilize o procedimento seguinte para eliminar uma política de cópia de segurança.

#### <a name="to-delete-a-backup-policy"></a>Para eliminar uma política de cópia de segurança
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, clique no **políticas de cópia de segurança** nós. Todas as políticas de cópia de segurança são apresentados no **resultados** painel.
3. Clique com o botão direito a política de cópia de segurança que pretende eliminar e, em seguida, clique em **eliminar**.
4. Quando aparecer a mensagem de confirmação, clique em **Sim**.

    ![Eliminar a confirmação de política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar Snapshot Manager do StorSimple para ver e gerir tarefas de cópia de segurança](storsimple-snapshot-manager-manage-backup-jobs.md).
