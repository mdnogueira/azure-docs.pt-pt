---
title: "Clonar uma cópia de segurança de matriz Virtual StorSimple | Microsoft Docs"
description: "Saiba como clonar uma cópia de segurança e recuperar um ficheiro a matriz de Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 768c9a1c906999f4690c9c8f7d075743ab1678ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Clonar a partir de uma cópia de segurança da sua matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve passo a passo como clonar um conjunto de cópias de segurança das suas partilhas ou volumes na sua matriz Virtual do Microsoft Azure StorSimple. A cópia de segurança clonada é usada para recuperar um ficheiro eliminado ou perdido. O artigo também inclui passos detalhados sobre como efetuar uma recuperação ao nível do item na sua matriz de Virtual StorSimple configurado como um servidor de ficheiros.

## <a name="clone-shares-from-a-backup-set"></a>Partilhas de clone a partir de um conjunto de cópia de segurança

**Antes de tentar clonar partilhas, certifique-se de que tem espaço suficiente no dispositivo para concluir esta operação.** A clonagem de uma cópia de segurança, no [portal do Azure](https://portal.azure.com/), execute os seguintes passos.

#### <a name="to-clone-a-share"></a>A clonagem de uma partilha

1. Navegue até à **dispositivos** painel. Selecione e clique em seu dispositivo e, em seguida, clique em **partilhas**. Selecione a partilha que pretende clonar, faça duplo clique a partilha de invocar o menu de contexto. Selecione **Clone**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. No **Clone** painel, clique em **cópia de segurança > selecione** e, em seguida, efetue o seguinte: 
   
   a.    Filtre uma cópia de segurança neste dispositivo com base no intervalo de tempo. Pode escolher entre **nos últimos 7 dias**, **nos últimos 30 dias**, e **passado ano**.
   
   b.    Na lista de cópias de segurança filtradas apresentada, selecione a clonagem de uma cópia de segurança.
   
   c.    Clique em **OK**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. No **Clone** painel, clique em **as definições de destino** e, em seguida, efetue o seguinte:
   
   a.    Forneça um nome de partilha. O nome da partilha tem de conter 3 127 carateres.
   
   b.    Opcionalmente, indique uma descrição para a partilha clonada.
   
   c.    Não é possível alterar o tipo de partilha que está a restaurar. Foi clonada a uma partilha em camadas como um em camadas e uma partilha afixada localmente afixado como localmente.
   
   d.    A capacidade está definida como equivalente ao tamanho da partilha que são a clonagem do.
   
   e.    Atribua os administradores para esta partilha. Será possível modificar as propriedades de partilha através do Explorador de ficheiros após a conclusão da clonagem.
   
   f.    Clique em **OK**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Clique em **Clone** para iniciar uma tarefa de clone. Depois da tarefa estiver concluída, a operação de clonagem é iniciado e será notificado. Para monitorizar o progresso da clonagem, vá para o **tarefas** painel e clique na tarefa para ver os detalhes da tarefa.
5. Depois do clone é criado com êxito, navegue de volta para o **partilhas** painel no seu dispositivo.
6. Agora, pode ver a nova partilha clonada na lista de partilhas no seu dispositivo. Foi clonada a uma partilha em camadas como em camadas e uma partilha localmente afixada como uma partilha afixada localmente.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Volumes de clone de um conjunto de cópia de segurança

A clonagem de uma cópia de segurança, no portal do Azure, tem de efetuar passos semelhantes para aqueles quando a clonagem de uma partilha. A operação clonar clones a cópia de segurança para um novo volume no mesmo dispositivo virtual; Não é possível clonar a um dispositivo diferente.

#### <a name="to-clone-a-volume"></a>A clonagem de um volume

1. Navegue até à **dispositivos** painel. Selecione e clique em seu dispositivo e, em seguida, clique em **Volumes**. Clique com botão direito que tiver o volume que pretende que a clonagem, o volume para invocar o menu de contexto. Selecione **Clone**.
   
   ![Clonar um volume](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. No **Clone** painel, clique em **cópia de segurança** e, em seguida, efetue o seguinte: 
   
   a.    Filtre uma cópia de segurança neste dispositivo com base no intervalo de tempo. Pode escolher entre **nos últimos 7 dias**, **nos últimos 30 dias**, e **passado ano**. 
   
   b.    Na lista de cópias de segurança filtradas apresentada, selecione a clonagem de uma cópia de segurança.
   
   c.    Clique em **OK**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. No **Clone** painel, clique em **definições de volume de destino** e, em seguida, efetue o seguinte:
   
   a. O nome do dispositivo é automaticamente preenchido.
   
   b. Forneça um nome de volume para o **clonado volume**. O nome do volume tem de conter 3 e 127 carateres.
   
   c. O tipo de volume é automaticamente definido para o volume original. Foi clonado a um volume em camadas como em camadas e um volume localmente afixado como localmente afixado.
   
   d. Para o **ligados a anfitriões**, clique em **selecione**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. No **ligados a anfitriões** painel, selecione de um ACR existente ou adicionar um novo ACR. Para adicionar um novo ACR, terá de fornecer um nome ACR e o IQN de anfitrião. Clique em **Selecionar**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Clique em **Clone** para iniciar uma tarefa de clone.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Depois da tarefa de clone é criada, a clonagem irá iniciar. Assim que o clone for criado, é apresentada no painel de Volumes no seu dispositivo. Tenha em atenção que foi clonado a um volume em camadas como em camadas e um volume localmente afixado é clonado como um volume afixado localmente.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Assim que o volume é apresentado online na lista de volumes, o volume está disponível para utilização. No anfitrião de iniciador iSCSI, atualize a lista de destinos na janela de propriedades do iniciador iSCSI. Um novo destino que contém o nome do volume clonado deve aparecer como 'inactive' sob a coluna Estado.
8. Selecione o destino e clique em **Connect**. Depois da sessão do iniciador está ligado ao destino, o estado deverá mudar para **ligado**.
9. No **gestão de discos** janela, os volumes montados aparecem conforme mostrado na ilustração seguinte. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.

> [!IMPORTANT]
> Quando tentar clonar um volume ou uma partilha de um conjunto de cópia de segurança, se a tarefa de clone falhar, um volume de destino ou uma partilha ainda pode ser criada no portal. É importante se eliminar este volume de destino ou partilhar no portal para minimizar os problemas futuros resultantes deste elemento.
> 
> 

## <a name="item-level-recovery-ilr"></a>Recuperação ao nível do item (ILR)

Esta versão apresenta a recuperação ao nível do item (ILR) numa matriz Virtual StorSimple configurado como um servidor de ficheiros. A funcionalidade permite-lhe fazer granular recuperação de ficheiros e pastas a partir da cópia de segurança na nuvem de todas as partilhas no dispositivo StorSimple. Pode obter ficheiros eliminados de cópias de segurança recentes através de um modelo personalizado.

Cada partilha tem um *.backups* pasta que contém as cópias de segurança mais recentes. Pode navegar para a cópia de segurança pretendida, copiar os ficheiros relevantes e pastas da cópia de segurança e restaurá-las. Esta funcionalidade elimina chamadas para os administradores para restaurar os ficheiros de cópias de segurança.

1. Quando efetuar a ILR, pode ver as cópias de segurança através do Explorador de ficheiros. Clique em partilha de específica que pretende ver a cópia de segurança. Verá um *.backups* pasta criada sob a partilha que armazena todas as cópias de segurança. Expanda o *.backups* pasta para ver as cópias de segurança. A pasta mostra a vista de exploded de toda a hierarquia de cópia de segurança. Esta vista é criada a pedido e normalmente demora apenas alguns segundos para criar.
   
   As últimas cinco cópias de segurança são apresentadas desta forma e podem ser utilizadas para efetuar uma recuperação ao nível do item. As cinco cópias de segurança recentes incluem a agendada predefinida e as cópias de segurança manuais.
   
   * **Cópias de segurança agendadas** com o nome como &lt;nome de dispositivo&gt;DailySchedule-AAAAMMDD HHMMSS UTC.
   * **As cópias de segurança manuais** com o nome como Ad-hoc-AAAAMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifica a cópia de segurança que contém a versão mais recente do ficheiro eliminado. Embora o nome da pasta contém um carimbo de UTC em cada um dos cenários anteriores, a hora em que a pasta foi criada é o tempo de dispositivo real quando a cópia de segurança foi iniciada. Utilize o carimbo de pasta para localizar e identificar as cópias de segurança.

3. Localize a pasta ou o ficheiro que pretende restaurar na cópia de segurança que identificou no passo anterior. Tenha em atenção que só pode ver os ficheiros ou pastas que tem permissões para. Se não é possível aceder a determinados ficheiros ou pastas, contacte um administrador de partilha. O administrador pode utilizar o Explorador de ficheiros para editar as permissões de partilha e lhe conceder acesso para a pasta ou ficheiro específico. É uma prática recomendada, que o administrador de partilha é um grupo de utilizadores em vez de um único utilizador.

4. Copie o ficheiro ou pasta para a partilha adequada no seu servidor de ficheiros do StorSimple.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [administrar a matriz de Virtual StorSimple, utilizando a IU da web local](storsimple-ova-web-ui-admin.md).

