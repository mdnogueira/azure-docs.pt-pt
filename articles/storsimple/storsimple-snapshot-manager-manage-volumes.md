---
title: Snapshot Manager do StorSimple e volumes | Microsoft Docs
description: "Descreve como utilizar o snap-in MMC de Snapshot Manager do StorSimple para ver e gerir volumes e configurar cópias de segurança."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 2c0b211bced99d272a73a7b018a22f99d8d58aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Utilize o Snapshot Manager do StorSimple para ver e gerir volumes
## <a name="overview"></a>Descrição geral
Pode utilizar o Snapshot Manager do StorSimple **Volumes** nó (no **âmbito** painel) para selecionar os volumes e ver informações sobre os mesmos. Os volumes são apresentados como unidades que correspondem aos volumes montados pelo anfitrião. O **Volumes** nós mostra volumes locais e tipos de volume que são suportados pelo StorSimple, incluindo volumes detetados através da utilização de iSCSI e um dispositivo. 

Para obter mais informações sobre volumes suportadas, aceda a [suporte para vários tipos de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de volume no painel de resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

O **Volumes** nós também permite-lhe reanalise ou elimine volumes depois Snapshot Manager do StorSimple Deteta-os. 

Este tutorial explica como montar, inicializar e formatar volumes e, em seguida, utilizar o Snapshot Manager do StorSimple para:

* Ver informações sobre volumes 
* Elimine volumes
* Reanalisar volumes 
* Configurar um volume básico e faça uma cópia de
* Configurar um volume espelhado dinâmico e faça uma cópia de

> [!NOTE]
> Todas as **Volume** ações de nó também estão disponíveis no **ações** painel.
> 
> 

## <a name="mount-volumes"></a>Montar os volumes
Utilize o procedimento seguinte para montar, inicializar e formatar volumes do StorSimple. Este procedimento utiliza a gestão de discos, um utilitário de sistema para a gestão de discos rígidos e os volumes de correspondente ou partições. Para obter mais informações sobre a gestão de discos, aceda a [gestão de discos](https://technet.microsoft.com/library/cc770943.aspx) no Web site da Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Montar os volumes
1. No computador anfitrião, inicie o iniciador de iSCSI da Microsoft.
2. Forneça um dos endereços IP da interface como o portal de destino ou endereço IP de deteção e ligar ao dispositivo. Depois do dispositivo estiver ligado, os volumes serão possível aceder ao seu sistema Windows. Para obter mais informações sobre como utilizar o Iniciador do Microsoft iSCSI, consulte a secção "Ligar a um dispositivo de destino iSCSI" na [iniciador iSCSI instalar e configurar o Microsoft][1].
3. Utilize qualquer uma das seguintes opções para iniciar a gestão de discos:
   
   * Escreva Diskmgmt.msc no **executar** caixa.
   * Inicie o Gestor de servidor, expanda o **armazenamento** nó e, em seguida, selecione **gestão de discos**.
   * Iniciar **ferramentas administrativas**, expanda o **gestão de computadores** nó e, em seguida, selecione **gestão de discos**. 
     
     > [!NOTE]
     > Tem de utilizar privilégios de administrador para executar a gestão de discos.
     > 
     > 
4. Colocar online os volumes:
   
   1. Na gestão de discos, clique com botão direito qualquer volume marcado **Offline**.
   2. Clique em **reativar disco**. O disco deve ser marcado como **Online** depois do disco é reativado.
5. Inicializar o volumes:
   
   1. Clique com botão direito os volumes detetados.
   2. No menu, selecione **Inicializar disco**.
   3. No **Inicializar disco** caixa de diálogo, selecione os discos que pretende iniciar e, em seguida, clique em **OK**.
6. Formatar volumes simples:
   
   1. Faça duplo clique num volume que pretende formatar.
   2. No menu, selecione **Novo Volume simples**.
   3. Utilize o Assistente de novo Volume simples para formatar o volume:
      
      * Especifique o tamanho do volume.
      * Forneça uma letra de unidade.
      * Selecione o sistema de ficheiros NTFS.
      * Especifique um tamanho de unidade de alocação de 64 KB.
      * Efetue uma formatação rápida.
7. Formatar partição vários volumes. Para obter instruções, consulte a secção, "Partições e Volumes" [implementar a gestão de discos](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Ver informações sobre os volumes
Utilize o procedimento seguinte para ver informações sobre volumes do StorSimple do Azure e do local.

#### <a name="to-view-volume-information"></a>Para ver informações de volume
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No **âmbito** painel, clique em de **Volumes** nós. É apresentada uma lista de volumes locais e montados, incluindo todos os volumes de Azure StorSimple, no **resultados** painel. As colunas do **resultados** painel são configuráveis. (Clique com botão direito do **Volumes** nó, selecione **vista**e, em seguida, selecione **Adicionar/remover colunas**.)
   
    ![Configurar as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Coluna de resultados | Descrição |
   |:--- |:--- |
   |  Nome |O **nome** coluna contém a letra de unidade atribuída a cada volume detetado. |
   |  Dispositivo |O **dispositivo** coluna contém o endereço IP do dispositivo ligado ao computador anfitrião. |
   |  Nome de Volume do dispositivo |O **nome de Volume do dispositivo** coluna contém o nome do volume de dispositivo a que pertence o volume selecionado. Este é o nome de volume definido no portal do Azure para esse volume específico. |
   |  Caminhos de acesso |O **acesso caminhos** coluna apresenta o caminho de acesso ao volume. Este é o ponto de letra ou um montagem de unidade em que o volume está acessível no computador anfitrião. |

## <a name="delete-a-volume"></a>Eliminar um volume
Utilize o procedimento seguinte para eliminar um volume do Snapshot Manager do StorSimple.

> [!NOTE]
> Não é possível eliminar um volume se faz parte de qualquer grupo de volume. (A opção de eliminação não está disponível para volumes que são membros de um grupo de volume). Tem de eliminar o grupo de volume completo para eliminar o volume.

#### <a name="to-delete-a-volume"></a>Para eliminar um volume
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, clique em de **Volumes** nós. 
3. No **resultados** painel, faça duplo clique o volume que pretende eliminar.
4. No menu, clique em **eliminar**. 
   
    ![Eliminar um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. O **Eliminar Volume** é apresentada a caixa de diálogo. Tipo **confirmar** na caixa de texto e, em seguida, clique em **OK**.
6. Por predefinição, Snapshot Manager do StorSimple efetua cópias de segurança num volume antes de eliminá-lo. Esta precaução pode protegê-lo contra perda de dados se a eliminação não tiver sido planeada. Snapshot Manager do StorSimple apresenta um **instantâneo automática** mensagens de progresso, enquanto que efetua cópias de segurança do volume. 
   
    ![Mensagem de instantâneo automática](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Reanalisar volumes
Utilize o procedimento seguinte para voltar a analisar os volumes ligados para Snapshot Manager do StorSimple.

#### <a name="to-rescan-the-volumes"></a>Para voltar a analisar os volumes
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique **Volumes**e, em seguida, clique em **reanalisar volumes**.
   
    ![Reanalisar volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Este procedimento sincroniza a lista de volume com o Snapshot Manager do StorSimple. Quaisquer alterações, tais como a novos volumes ou volumes eliminados, serão apresentadas nos resultados.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurar e efetuar cópias de segurança de um volume básico
Utilize o procedimento seguinte para configurar uma cópia de segurança de um volume básico e, em seguida, iniciar de imediato uma cópia de segurança ou criar uma política de cópias de segurança agendadas.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o computador de anfitrião e o dispositivo StorSimple estão configurados corretamente. Para obter mais informações, aceda a [implementar o dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).
* Instalar e configurar o Snapshot Manager do StorSimple. Para obter mais informações, aceda a [implementar Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar a cópia de segurança de um volume básico
1. Crie um volume básico no dispositivo StorSimple.
2. Montar, inicializar e formatar o volume, conforme descrito em [montar os volumes](#mount-volumes). 
3. Clique no ícone de Snapshot Manager do StorSimple no seu ambiente de trabalho. É apresentada a janela do Snapshot Manager do StorSimple. 
4. No **âmbito** painel, clique com botão direito do **Volumes** nó e, em seguida, selecione **reanalisar volumes**. Quando a análise estiver concluída, uma lista de volumes deve aparecer no **resultados** painel. 
5. No **resultados** painel, clique com o botão direito do volume e, em seguida, selecione **criar grupo de Volume**. 
   
    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. No **criar grupo de Volume** caixa de diálogo, escreva um nome para o grupo de volume, atribuir volumes ao mesmo e, em seguida, clique em **OK**.
7. No **âmbito** painel, expanda o **Volume grupos** nó. O novo grupo de volume deve aparecer sob o **Volume grupos** nó. 
8. Clique no nome do grupo de volume.
   
   * Para iniciar uma tarefa de cópia de segurança interativa (a pedido), clique em **efetuar cópia de segurança**. 
   * Para agendar uma cópia de segurança automática, clique em **criar política de cópia de segurança**. No **geral** página, selecione um grupo de volume na lista. No **agenda** página, introduza os detalhes de agenda. Quando tiver terminado, clique em **OK**. 
9. Para confirmar que a tarefa de cópia de segurança foi iniciada, expanda o **tarefas** no nó de **âmbito** painel e, em seguida, clique o **em execução** nó. É apresentada a lista de tarefas atualmente em execução no **resultados** painel. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurar e efetuar cópias de segurança de um volume espelhado dinâmico
Conclua os seguintes passos para configurar a cópia de segurança de um volume espelhada dinâmica:

* Passo 1: Utilizar gestão de discos para criar um volume espelhado dinâmico. 
* Passo 2: Utilize Snapshot Manager do StorSimple para configurar a cópia de segurança.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o computador de anfitrião e o dispositivo StorSimple estão configurados corretamente. Para obter mais informações, aceda a [implementar o dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).
* Instalar e configurar o Snapshot Manager do StorSimple. Para obter mais informações, aceda a [implementar Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md).
* Configure dois volumes no dispositivo StorSimple. (Nos exemplos, volumes disponíveis são **disco 1** e **disco 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Passo 1: Gestão de discos de utilização para criar um volume espelhado dinâmico
Gestão de discos é um utilitário de sistema para a gestão de discos rígidos e os volumes ou as partições que contêm. Para obter mais informações sobre a gestão de discos, aceda a [gestão de discos](https://technet.microsoft.com/library/cc770943.aspx) no Web site da Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para criar um volume espelhado dinâmico
1. Utilize qualquer uma das seguintes opções para iniciar a gestão de discos: 
   
   * Abra o **executar** caixa, escreva **Diskmgmt.msc**, e prima Enter.
   * Inicie o Gestor de servidor, expanda o **armazenamento** nó e, em seguida, selecione **gestão de discos**. 
   * Iniciar **ferramentas administrativas**, expanda o **gestão de computadores** nó e, em seguida, selecione **gestão de discos**. 
2. Certifique-se de que tem dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **disco 1** e **disco 2**.) 
3. Na janela Gestão de discos, na coluna da direita do painel inferior, clique com botão direito **disco 1** e selecione **Novo Volume espelhada**. 
   
    ![Novo Volume espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. No **Novo Volume espelhada** página do assistente, clique em **seguinte**.
5. No **selecionar discos** página, selecione **disco 2** no **selecionados** painel, clique em **adicionar**e, em seguida, clique em **seguinte**. 
6. No **atribuir letra da unidade ou caminho** página, aceite as predefinições e, em seguida, clique em **seguinte**. 
7. No **formatação do Volume** na página de **tamanho da unidade de alocação** caixa, selecione **64K**. Selecione o **Efetue uma formatação rápida** caixa de verificação e, em seguida, clique em **seguinte**. 
8. No **concluir o novo Volume espelhada** página, reveja as definições e, em seguida, clique em **concluir**. 
9. É apresentada uma mensagem a indicar que o disco básico será convertido para um disco dinâmico. Clique em **Sim**.
   
    ![Mensagem de conversão do disco dinâmico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Na gestão de discos, certifique-se de que o disco 1 e 2 do disco são apresentadas como volumes espelhados dinâmicos. (**Dinâmica** deve aparecer na coluna Estado e, deve alterar a cor de barras de capacidade para vermelho, que indica que um volume espelhado.) 
    
    ![Discos dinâmicos de gestão espelhado de disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Passo 2: Utilize Snapshot Manager do StorSimple para configurar a cópia de segurança
Utilize o procedimento seguinte para configurar um volume espelhado dinâmico e, em seguida, iniciar de imediato uma cópia de segurança ou criar uma política de cópias de segurança agendadas.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar a cópia de segurança de um volume espelhada dinâmica
1. Clique no ícone de Snapshot Manager do StorSimple no seu ambiente de trabalho. É apresentada a janela do Snapshot Manager do StorSimple. 
2. No **âmbito** painel, clique com botão direito do **Volumes** nó e selecione **reanalisar volumes**. Quando a análise estiver concluída, uma lista de volumes deve aparecer no **resultados** painel. O volume espelhado dinâmico está listado como um único volume. 
3. No **resultados** painel, o volume espelhado dinâmico com o botão direito e, em seguida, clique em **criar grupo de Volume**. 
4. No **criar grupo de Volume** caixa de diálogo, escreva um nome para o grupo de volume, atribua o volume espelhado dinâmico a este grupo e, em seguida, clique em **OK**. 
5. No **âmbito** painel, expanda o **Volume grupos** nó. O novo grupo de volume deve aparecer sob o **Volume grupos** nó. 
6. Clique no nome do grupo de volume. 
   
   * Para iniciar uma tarefa de cópia de segurança interativa (a pedido), clique em **efetuar cópia de segurança**. 
   * Para agendar uma cópia de segurança automática, clique em **criar política de cópia de segurança**. No **geral** página, selecione o grupo de volume na lista. No **agenda** página, introduza os detalhes de agenda. Quando tiver terminado, clique em **OK**. 
7. Pode monitorizar a tarefa de cópia de segurança que é executada. No **âmbito** painel, expanda o **tarefas** nó e, em seguida, clique **executar**, são apresentados os detalhes da tarefa no **resultados** painel. Quando a tarefa de cópia de segurança estiver concluída, os detalhes são transferidos para o **últimas 24** horas lista da tarefa. 

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar Snapshot Manager do StorSimple para criar e gerir grupos de volume](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
