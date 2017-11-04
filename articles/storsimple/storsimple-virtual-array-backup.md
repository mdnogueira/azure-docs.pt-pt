---
title: "Tutorial de cópia de segurança de matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs"
description: "Descreve como fazer cópias de segurança de volumes e partilhas de matriz Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c926f0c80ce56cac3106ad97ec3ec2e18a8e2cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Criar cópias de segurança partilhas ou volumes na sua matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral

A matriz de Virtual StorSimple é um híbridos na nuvem no local virtual dispositivo de armazenamento que podem ser configurados como um servidor de ficheiros ou um servidor de iSCSI. A matriz virtual permite ao utilizador criar cópias de segurança agendadas e manuais de todos os volumes ou partilhas no dispositivo. Quando configurado como um servidor de ficheiros, também permite a recuperação ao nível do item. Este tutorial descreve como criar cópias de segurança agendadas e manuais e efetuar a recuperação ao nível do item para restaurar um ficheiro na sua matriz virtual eliminado.

Este tutorial aplica-se a Virtual apenas as matrizes StorSimple. Para obter informações sobre 8000 série, aceda a [criar uma cópia de segurança para o dispositivo da 8000 série](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Cópia de segurança de partilhas e os volumes

As cópias de segurança fornecem proteção de ponto no tempo, melhorar a capacidade de recuperação e minimizar os tempos de restauro de volumes e partilhas. Pode efetuar cópias de segurança de uma partilha ou volume no dispositivo StorSimple de duas formas: **agendada** ou **Manual**. Cada um dos métodos é abordada nas secções seguintes.

## <a name="change-the-backup-start-time"></a>Alterar a hora de início de cópia de segurança

> [!NOTE]
> Nesta versão, as cópias de segurança agendadas são criadas por uma política predefinida que é executada diariamente a uma determinada hora e efetua cópias de segurança de todos os volumes ou partilhas no dispositivo. Não é possível criar políticas personalizadas para cópias de segurança agendadas neste momento.


A matriz de Virtual StorSimple tem uma política de cópia de segurança predefinida que inicia a uma determinada hora do dia (22:30) e cria uma cópia de segurança de todas as partilhas ou volumes no dispositivo uma vez por dia. Pode alterar a hora em que a cópia de segurança começa, mas a frequência e a retenção (que especifica o número de cópias de segurança para manter) não podem ser alteradas. Durante estas cópias de segurança, todo o dispositivo virtual é uma cópia de segurança. Isto foi potencialmente afetar o desempenho do dispositivo e afetam as cargas de trabalho implementadas no dispositivo. Por conseguinte, recomendamos que agende estas cópias de segurança para as horas de ponta.

 Para alterar a hora de início de cópia de segurança de predefinido, execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Para alterar a hora de início para a política de cópia de segurança predefinida

1. Aceda a **dispositivos**. Será apresentada a lista de dispositivos registados com o serviço do Gestor de dispositivos do StorSimple. 
   
    ![Navegue para dispositivos](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Selecione e clique em seu dispositivo. O **definições** é apresentado o painel. Aceda a **gerir > políticas de cópia de segurança**.
   
    ![Selecione o seu dispositivo](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. No **políticas de cópia de segurança** painel, a hora de início predefinido é 22:30. Pode especificar a nova hora de início para a agenda diária no fuso horário do dispositivo.
   
    ![Navegue para políticas de cópia de segurança](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Clique em **Guardar**.

### <a name="take-a-manual-backup"></a>Efetuar uma cópia de segurança manual

Para além das cópias de segurança agendadas, pode tirar uma manual (a pedido) cópia de segurança dos dados de dispositivo em qualquer altura.

#### <a name="to-create-a-manual-backup"></a>Para criar uma cópia de segurança manual

1. Aceda a **dispositivos**. Selecione o seu dispositivo e faça duplo clique **...**  na extremidade direita na linha selecionada. No menu de contexto, selecione **efetuar cópia de segurança**.
   
    ![Navegue para efetuar cópia de segurança](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. No **efetuar cópia de segurança** painel, clique em **efetuar cópia de segurança**. Isto irá criar cópias de segurança todas as partilhas no servidor de ficheiros ou de todos os volumes no seu servidor de iSCSI. 
   
    ![a partir de cópia de segurança](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Inicia uma cópia de segurança a pedido e verá que uma tarefa de cópia de segurança foi iniciada.
   
    ![a partir de cópia de segurança](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Depois da tarefa foi concluída com êxito, será notificado novamente. Em seguida, inicia o processo de cópia de segurança.
   
    ![tarefa de cópia de segurança criada](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Para acompanhar o progresso das cópias de segurança e ver os detalhes da tarefa, clique na notificação. Isto leva-o para **detalhes da tarefa**.
   
     ![Detalhes da tarefa de cópia de segurança](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Depois de concluída a cópia de segurança, aceda a **gestão > catálogo de cópia de segurança**. Verá um instantâneo de nuvem de todas as partilhas (ou volumes) no seu dispositivo.
   
    ![Cópia de segurança concluída](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Ver as cópias de segurança existentes
Para ver as cópias de segurança existentes, execute os seguintes passos no portal do Azure.

#### <a name="to-view-existing-backups"></a>Para ver as cópias de segurança existentes

1. Aceda a **dispositivos** painel. Selecione e clique em seu dispositivo. No **definições** painel, aceda a **gestão > catálogo de cópia de segurança**.
   
    ![Navegue para o catálogo de cópias de segurança](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Especifique os critérios seguintes para ser utilizada para filtragem:
   
    - **Intervalo de tempo** – pode ser **última 1 hora**, **últimas 24 horas**, **nos últimos 7 dias**, **nos últimos 30 dias**, **passado ano**, e **data personalizada**.
    
    - **Dispositivos** – selecione na lista de servidores de ficheiros ou servidores iSCSI registados com o serviço do Gestor de dispositivos do StorSimple.
   
    - **Iniciou** – pode ser automaticamente **agendada** (por uma política de cópia de segurança) ou **manualmente** iniciada (por si).
   
    ![Filtrar as cópias de segurança](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Clique em **Aplicar**. É apresentada a lista filtrada de cópias de segurança no **catálogo de cópia de segurança** painel. Elementos de cópia de segurança de nota 100 só podem ser apresentados num determinado momento.
   
    ![Catálogo de cópias de segurança atualizado](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

