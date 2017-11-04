---
title: "Catálogo de cópias de segurança do Snapshot Manager do StorSimple | Microsoft Docs"
description: "Descreve como utilizar o snap-in MMC de Snapshot Manager do StorSimple para ver e gerir o catálogo de cópias de segurança."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: b97753e6f1b67e3c8d247281c5e5208033a56eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Utilize o Gestor de instantâneo StorSimple para gerir o catálogo de cópias de segurança

## <a name="overview"></a>Descrição geral
A função principal do Snapshot Manager do StorSimple é permitem-lhe criar cópias de segurança consistentes com aplicações dos volumes do StorSimple sob a forma de instantâneos. Instantâneos, em seguida, são listados no ficheiro XML denominado uma *catálogo de cópias de segurança*. O catálogo de cópias de segurança organiza os instantâneos por grupo de volume e, em seguida, instantâneo local ou um instantâneo na nuvem.

Este tutorial descreve como pode utilizar o **catálogo de cópia de segurança** nós para concluir as seguintes tarefas:

* Restaurar um volume
* Clonar um volume ou o grupo de volume
* Eliminar uma cópia de segurança
* Recuperar um ficheiro
* Restaurar a base de dados do Snapshot Manager do Storsimple

Pode ver o catálogo de cópias de segurança, expandindo o **catálogo de cópias de segurança** no nó de **âmbito** painel e, em seguida, a expansão do grupo de volume.

* Se clicar em nome do grupo de volume, o **resultados** painel mostra o número de instantâneos locais e instantâneos de nuvem disponível para o grupo de volume. 
* Se clicar em **instantâneo Local** ou **instantâneo na nuvem**, a **resultados** painel apresenta as seguintes informações sobre cada instantâneo de cópia de segurança (dependendo do seu **vista** definições):
  
  * **Nome** – o tempo que o instantâneo foi tirado.
  * **Tipo** – se este é um instantâneo local ou um instantâneo na nuvem.
  * **Proprietário** – o proprietário do conteúdo. 
  * **Disponível** – se o instantâneo está atualmente disponível. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado; **Falso** indica que o instantâneo já não está disponível. 
  * **Importar** – se a cópia de segurança foi importada. **Verdadeiro** indica que a cópia de segurança foram importada a partir do serviço do Gestor de dispositivos do StorSimple no momento, o dispositivo foi configurado no Snapshot Manager do StorSimple; **Falso** indica que não foi importado, mas que foi criado pelo Snapshot Manager do StorSimple. (Possa identificar facilmente um grupo de volume importada porque é adicionado um sufixo que identifica o dispositivo a partir do qual o grupo de volume foram importado.)
    
    ![Catálogo de cópias de segurança](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Se expandir **instantâneo Local** ou **instantâneo na nuvem**e, em seguida, clique num nome de instantâneo individuais, o **resultados** painel apresenta as seguintes informações sobre o instantâneo selecionado:
  
  * **Nome** – volume identificado por uma letra de unidade. 
  * **Nome local** – o nome local da unidade (se disponível). 
  * **Dispositivo** – o nome do dispositivo no qual reside o volume. 
  * **Disponível** – se o instantâneo está atualmente disponível. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado; **Falso** indica que o instantâneo já não está disponível. 

## <a name="restore-a-volume"></a>Restaurar um volume
Utilize o procedimento seguinte para restaurar um volume a partir de cópia de segurança.

#### <a name="prerequisites"></a>Pré-requisitos
Se ainda não o tiver feito, crie um volume e o grupo de volume e, em seguida, elimine o volume. Por predefinição, Snapshot Manager do StorSimple efetua cópias de segurança num volume antes que permite que a eliminação. Esta precaução pode impedir a perda de dados se o volume for eliminado acidentalmente ou se os dados precisam de ser recuperados por qualquer motivo. 

Snapshot Manager do StorSimple apresenta a seguinte mensagem enquanto cria a cópia de segurança precaução.

![Mensagem de instantâneo automática](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Não é possível eliminar um volume que faz parte de um grupo de volume. A opção de eliminação não está disponível. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Para restaurar um volume
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No **âmbito** painel, expanda o **catálogo de cópia de segurança** nós, expandir um grupo de volume e, em seguida, clique em **instantâneos locais** ou **instantâneos de nuvem**. É apresentada uma lista de instantâneos de cópia de segurança no **resultados** painel.
3. Localizar a cópia de segurança que pretende restaurar, rato e, em seguida, clique em **restaurar**.
   
    ![Restaurar o catálogo de cópias de segurança](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na página de confirmação, reveja os detalhes do utilizador, tipo **confirmar**e, em seguida, clique em **OK**. Snapshot Manager do StorSimple utiliza a cópia de segurança para restaurar o volume.
   
    ![Restaurar a mensagem de confirmação](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Pode monitorizar a acção de restauro enquanto é executada. No **âmbito** painel, expanda o **tarefas** nó e, em seguida, clique em **executar**. Os detalhes da tarefa são apresentados no **resultados** painel. Quando a tarefa de restauro está concluída, os detalhes da tarefa são transferidos para o **últimas 24 horas** lista.

## <a name="clone-a-volume-or-volume-group"></a>Clonar um volume ou o grupo de volume
Utilize o procedimento seguinte para criar um duplicado (clone) de um volume ou o grupo de volume.

#### <a name="to-clone-a-volume-or-volume-group"></a>A clonagem de um volume ou o grupo de volume
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, expanda o **catálogo de cópia de segurança** nós, expandir um grupo de volume e, em seguida, clique em **instantâneos de nuvem**. É apresentada uma lista de cópias de segurança no **resultados** painel.
3. Localizar o volume ou o grupo de volume que pretende clonar, faça duplo clique o volume ou o nome do grupo de volume e, em **Clone**. O **instantâneo na nuvem de Clone** é apresentada a caixa de diálogo.
   
    ![Clonar um instantâneo na nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Concluir o **instantâneo na nuvem de Clone** caixa de diálogo da seguinte forma: 
   
   1. No **nome** caixa de texto, escreva um nome para o volume clonado. Este nome será apresentado no **Volumes** nós. 
   2. (Opcional) selecione **unidade**e, em seguida, selecione uma letra de unidade na lista pendente.
   3. (Opcional) selecione **pasta (NTFS)**, escreva um caminho de pasta ou clique em Procurar e selecione uma localização para a pasta. 
   4. Clique em **Criar**.
5. Quando o processo de clonagem é concluído, o volume clonado tem de ser inicializado. Inicie o Gestor de servidor e, em seguida, inicie a gestão de discos. Para obter instruções detalhadas, consulte [montar os volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois de ser inicializada, o volume será listado sob o **Volumes** no nó de **âmbito** painel. Se não vir o volume listado, atualize a lista de volumes (com o botão direito do **Volumes** nó e, em seguida, clique em **atualizar**).

## <a name="delete-a-backup"></a>Eliminar uma cópia de segurança
Utilize o procedimento seguinte para eliminar um instantâneo a partir do catálogo de cópias de segurança. 

> [!NOTE]
> Eliminar um instantâneo elimina os dados de cópias de segurança associados com o instantâneo. No entanto, o processo de limpeza dos dados da nuvem pode demorar algum tempo.<br>


#### <a name="to-delete-a-backup"></a>Para eliminar uma cópia de segurança
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, expanda o **catálogo de cópia de segurança** nós, expandir um grupo de volume e, em seguida, clique em **instantâneos locais** ou **instantâneos de nuvem**. É apresentada uma lista de instantâneos no **resultados** painel.
3. O instantâneo de que pretende eliminar e, em seguida, clique com o botão direito **eliminar**.
4. Quando aparecer a mensagem de confirmação, clique em **OK**.

## <a name="recover-a-file"></a>Recuperar um ficheiro
Se um ficheiro for eliminado acidentalmente um volume, pode recuperar o ficheiro de recuperar um instantâneo de pré-as datas de eliminação, utilizando o instantâneo para criar um clone do volume e, em seguida, copiar o ficheiro do volume clonado ao original volume.

#### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem uma cópia de segurança atual do grupo de volume. Em seguida, elimine um ficheiro armazenado dos volumes nesse grupo de volume. Por fim, utilize os seguintes passos para restaurar o ficheiro eliminado da sua cópia de segurança. 

#### <a name="to-recover-a-deleted-file"></a>Para recuperar um ficheiro eliminado
1. Clique no ícone de Snapshot Manager do StorSimple no seu ambiente de trabalho. É apresentada a janela de consola Snapshot Manager do StorSimple. 
2. No **âmbito** painel, expanda o **catálogo de cópia de segurança** nós e navegue para um instantâneo que contém o ficheiro foi eliminado. Normalmente, deve selecionar um instantâneo de que foi criado imediatamente antes da eliminação.
3. Localizar o volume que pretende que a clonagem, rato e clique em **Clone**. O **instantâneo na nuvem de Clone** é apresentada a caixa de diálogo.
   
    ![Clonar um instantâneo na nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Concluir o **instantâneo na nuvem de Clone** caixa de diálogo da seguinte forma: 
   
   1. No **nome** caixa de texto, escreva um nome para o volume clonado. Este nome será apresentado no **Volumes** nós. 
   2. (Opcional) Selecione **unidade**e, em seguida, selecione uma letra de unidade na lista pendente. 
   3. (Opcional) Selecione **pasta (NTFS)**e escreva um caminho de pasta ou clique em **procurar** e selecione uma localização para a pasta. 
   4. Clique em **Criar**. 
5. Quando o processo de clonagem é concluído, o volume clonado tem de ser inicializado. Inicie o Gestor de servidor e, em seguida, inicie a gestão de discos. Para obter instruções detalhadas, consulte [montar os volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois de ser inicializada, o volume será listado sob o **Volumes** no nó de **âmbito** painel. 
   
    Se não vir o volume listado, atualize a lista de volumes (com o botão direito do **Volumes** nó e, em seguida, clique em **atualizar**).
6. Abra a pasta NTFS que contém o volume clonado, expanda o **Volumes** nó e, em seguida, abra o volume clonado. Localizar o ficheiro que pretende recuperar e copie-a para o volume primário.
7. Depois de restaurar o ficheiro, pode eliminar a pasta NTFS que contém o volume clonado.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurar a base de dados do Snapshot Manager do StorSimple
Regularmente deve criar cópias de segurança da base de dados do Snapshot Manager do StorSimple no computador anfitrião. Se ocorrer um desastre ou o computador anfitrião falha por alguma razão, em seguida, pode restaurá-lo da cópia de segurança. Criar a cópia de segurança da base de dados é um processo manual.

#### <a name="to-back-up-and-restore-the-database"></a>Para criar cópias de segurança e restaurar a base de dados
1. Pare o serviço de gestão do StorSimple da Microsoft:
   
   1. Inicie o Gestor de servidor.
   2. No dashboard do Gestor de servidores, no **ferramentas** menu, selecione **serviços**.
   3. No **serviços** janela, selecione o **serviço de gestão da Microsoft StorSimple**.
   4. No painel direito, em **serviço de gestão da Microsoft StorSimple**, clique em **parar o serviço**.
2. No computador anfitrião, navegue até à C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
   > 
   > 
3. Localizar o ficheiro XML de catálogo, copie o ficheiro e armazenar a cópia numa localização segura ou na nuvem. Se o anfitrião falhar, pode utilizar este ficheiro de cópia de segurança para ajudar a recuperar as políticas de cópia de segurança que criou no Snapshot Manager do StorSimple.
   
    ![Ficheiro de catálogo de cópias de segurança do Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Reinicie o serviço de gestão do StorSimple da Microsoft: 
   
   1. No dashboard do Gestor de servidores, no **ferramentas** menu, selecione **serviços**.
   2. No **serviços** janela, selecione o **serviço de gestão da Microsoft StorSimple**.
   3. No painel direito, em **serviço de gestão da Microsoft StorSimple**, clique em **reinicie o serviço**.
5. No computador anfitrião, navegue até à C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Elimine o ficheiro XML de catálogo e substitua-o com a versão de cópia de segurança que criou. 
7. Clique no ícone de Snapshot Manager do StorSimple ambiente de trabalho para iniciar o Snapshot Manager do StorSimple. 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [utilizando Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba mais sobre [tarefas Snapshot Manager do StorSimple e fluxos de trabalho](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

