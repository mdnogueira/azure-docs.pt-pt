---
title: Grupos de volume do Snapshot Manager do StorSimple | Microsoft Docs
description: Descreve como utilizar o snap-in MMC de Snapshot Manager do StorSimple para criar e gerir grupos de volume.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 6067a88cd42d29c3d2f4b74580095424de77561e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Utilize o Snapshot Manager do StorSimple para criar e gerir grupos de volume
## <a name="overview"></a>Descrição geral
Pode utilizar o **Volume grupos** nó o **âmbito** painel para atribuir volumes a grupos de volume, ver informações sobre um grupo de volume, agendar cópias de segurança e editar os grupos de volume.

Os grupos de volume são conjuntos de volumes relacionados utilizadas para se certificar de que as cópias de segurança consistentes com aplicações. Para obter mais informações, consulte [Volumes e grupos de volume](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [integração com o serviço de cópia de sombra de volumes do Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Todos os volumes de um grupo de volume tem de ser de um fornecedor de serviços de nuvem única.
> * Quando configura grupos de volume, não misture volumes partilhados de cluster (CSVs) e não CSVs no mesmo grupo de volume. Snapshot Manager do StorSimple não suporta uma combinação de csv e não CSVs no mesmo instantâneo.

![Nó de grupos de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nó de grupos de Volume do StorSimple instantâneo Manager** 

Este tutorial explica como pode utilizar o Snapshot Manager do StorSimple para:

* Ver informações sobre os grupos de volume
* Criar um grupo de volume
* Cópia de segurança de um grupo de volume
* Editar um grupo de volume
* Eliminar um grupo de volume

Todas estas ações também estão disponíveis no **ações** painel.

## <a name="view-volume-groups"></a>Grupos de volume de vista
Se clicar no **Volume grupos** nó, o **resultados** painel apresenta as seguintes informações sobre cada grupo de volume, consoante as seleções de coluna se. (As colunas do **resultados** painel são configuráveis. Clique com botão direito do **Volumes** nó, selecione **vista**e, em seguida, selecione **Adicionar/remover colunas**.)

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |O **nome** coluna contém o nome do grupo de volume. |
| Aplicação |O **aplicações** coluna mostra o número de escritores VSS atualmente instalados e em execução no anfitrião do Windows. |
| Selecionado |O **selecionados** coluna mostra o número de volumes que estão contidos no grupo de volume. Um zero (0) não indica que nenhuma aplicação associada com os volumes no grupo de volume. |
| Importados |O **importado** coluna mostra o número de volumes importados. Quando definido como **verdadeiro**, esta coluna indica que um grupo de volume foram importado a partir do portal do Azure e não foi criado no Snapshot Manager do StorSimple. |

> [!NOTE]
> Grupos de volume do Snapshot Manager do StorSimple também são apresentados no **políticas de cópia de segurança** separador no portal do Azure.
> 
> 

## <a name="create-a-volume-group"></a>Criar um grupo de volume
Utilize o procedimento seguinte para criar um grupo de volume.

#### <a name="to-create-a-volume-group"></a>Para criar um grupo de volume
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique **Volume grupos**e, em seguida, clique em **criar grupo de Volume**.
   
    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    O **criar um grupo de volume** é apresentada a caixa de diálogo.
   
    ![Criar uma caixa de diálogo de grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Introduza as seguintes informações:
   
   1. No **nome** caixa, escreva um nome exclusivo para o novo grupo de volume.
   2. No **aplicações** caixa, selecionadas aplicações associadas a volumes que irá adicionar ao grupo de volume.
      
       O **aplicações** listas caixa apenas para as aplicações que utilizam volumes do StorSimple e têm escritores VSS se ativado para os mesmos. Um escritor VSS está ativado apenas se todos os volumes que o escritor está ciente de volumes do StorSimple. Se a caixa de aplicações estiver vazia, não existem aplicações que utilizam volumes do StorSimple do Azure e tem suportada escritores VSS se estão instaladas. (Atualmente, Azure StorSimple suporta o Microsoft Exchange e do SQL Server.) Para obter mais informações sobre os escritores VSS, consulte [integração com o serviço de cópia de sombra de volumes do Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Se selecionar uma aplicação, todos os volumes ele associadas são selecionados automaticamente. Por outro lado, se selecionar volumes associados uma aplicação específica, a aplicação é selecionada automaticamente no **aplicações** caixa. 
   3. No **Volumes** caixa, selecione os volumes do StorSimple para adicionar ao grupo de volume. 
      
      * Pode incluir volumes com únicas ou várias partições. (Vários volumes de partição podem ser discos dinâmicos ou discos básicos com várias partições.) Um volume que contém várias partições é tratado como uma única unidade. Por conseguinte, se apenas uma das partições de adicionar a um grupo de volume, todas as outras partições são adicionadas automaticamente a esse grupo de volume ao mesmo tempo. Depois de adicionar um volume de partição vários para um grupo de volume, o volume de partição vários continua a ser tratado como uma única unidade.
      * Pode criar grupos de volume vazio atribuindo não quaisquer volumes aos mesmos. 
      * Não misture volumes partilhados de cluster (CSVs) e não CSVs no mesmo grupo de volume. Snapshot Manager do StorSimple não suporta uma combinação de volumes CSV e volumes de CSV não no mesmo instantâneo.
4. Clique em **OK** para guardar o grupo de volume.

## <a name="back-up-a-volume-group"></a>Cópia de segurança de um grupo de volume
Utilize o procedimento seguinte para efetuar cópias de segurança de um grupo de volume.

#### <a name="to-back-up-a-volume-group"></a>Fazer cópias de segurança de um grupo de volume
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, expanda o **Volume grupos** nó, clique no nome de um grupo de volume e, em seguida, clique em **efetuar cópia de segurança**.
   
    ![Fazer uma cópia de segurança de grupo de volume imediatamente](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. No **efetuar cópia de segurança** caixa de diálogo, selecione **instantâneo Local** ou **instantâneo na nuvem**e, em seguida, clique em **criar**.
   
    ![Efetuar cópia de segurança caixa de diálogo](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Para confirmar que a cópia de segurança está em execução, expanda o **tarefas** nó e, em seguida, clique em **executar**. A cópia de segurança deve ser apresentada.
5. Para ver o instantâneo foi concluído, expanda o **catálogo de cópia de segurança** nó, expanda o nome do grupo de volume e, em seguida, clique em **instantâneo Local** ou **instantâneo na nuvem**. A cópia de segurança será apresentada se foi concluído com êxito.

## <a name="edit-a-volume-group"></a>Editar um grupo de volume
Utilize o procedimento seguinte para editar um grupo de volume.

#### <a name="to-edit-a-volume-group"></a>Para editar um grupo de volume
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, expanda o **Volume grupos** nó, clique no nome de um grupo de volume e, em seguida, clique em **editar**.
3. O * * criar um grupo de volume * * é apresentada a caixa de diálogo. Pode alterar o **nome**, **aplicações**, e **Volumes** entradas.
4. Clique em **OK** para guardar as alterações.

## <a name="delete-a-volume-group"></a>Eliminar um grupo de volume
Utilize o procedimento seguinte para eliminar um grupo de volume. 

> [!WARNING]
> Isto também elimina todas as cópias de segurança associadas ao grupo de volume.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Para eliminar um grupo de volume
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, expanda o **Volume grupos** nó, clique no nome de um grupo de volume e, em seguida, clique em **eliminar**.
3. O **eliminar grupo de Volume** é apresentada a caixa de diálogo. Tipo **confirmar** na caixa de texto e, em seguida, clique em **OK**.
   
    O grupo de volume eliminado vanishes na lista no **resultados** painel e todas as cópias de segurança que estão associadas esse grupo de volume são eliminadas do catálogo de cópias de segurança.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar Snapshot Manager do StorSimple para criar e gerir políticas de cópia de segurança](storsimple-snapshot-manager-manage-backup-policies.md).

