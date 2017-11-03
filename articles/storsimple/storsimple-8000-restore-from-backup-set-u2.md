---
title: "Restaurar um volume a partir de cópia de segurança numa série 8000 do StorSimple | Microsoft Docs"
description: "Explica como utilizar o serviço do Gestor de dispositivos do StorSimple catálogo de cópias de segurança para restaurar um volume StorSimple a partir de um conjunto de cópia de segurança."
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: aff0710ead4f76bb80c38e2d88fe9cd3ce6a7b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um volume StorSimple a partir de um conjunto de cópia de segurança

## <a name="overview"></a>Descrição geral

Este tutorial descreve a operação de restauro efetuada num dispositivo série 8000 do StorSimple utilizando um conjunto de cópia de segurança existente. Utilize o **catálogo de cópia de segurança** painel para restaurar um volume de um local ou de cópia de segurança de nuvem. O **catálogo de cópia de segurança** painel apresenta todos os conjuntos de cópias de segurança que são criados quando são efetuadas cópias de segurança manuais ou automáticas. A operação de restauro a partir de um conjunto de cópia de segurança repõe o volume online imediatamente enquanto os dados que são transferidos em segundo plano.

É um método alternativo para iniciar o restauro Ir para **dispositivos > [o dispositivo] > Volumes**. No **Volumes** painel, selecione um volume, faça duplo clique para invocar o menu de contexto e, em seguida, selecione **restaurar**.

## <a name="before-you-restore"></a>Antes de restaurar

Antes de iniciar um restauro, reveja os seguintes avisos:

* **O volume tem de colocar offline** – tirar o volume offline no anfitrião e o dispositivo antes de iniciar a operação de restauro. Apesar da operação de restauro repõe automaticamente o volume online no dispositivo, tem de colocar manualmente o dispositivo online no anfitrião. Pode colocar o volume online no anfitrião, assim que o volume está online no dispositivo. (A não ter de aguardar até que a operação de restauro está concluída.) Para obter os procedimentos, vá para [colocar offline um volume](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Tipo de volume após o restauro** – eliminado volumes são restaurados com base no tipo no instantâneo; ou seja, volumes que foram afixados localmente são restaurados como volumes localmente afixados e volumes que foram camadas são restaurados como volumes em camadas.

    Para os volumes existentes, o tipo de utilização atual do volume substitui o tipo que está armazenado no instantâneo. Por exemplo, se restaurar um volume a partir de um instantâneo que foi tomado quando o tipo de volume foi camado e de que o tipo de volume agora é afixado localmente (devido a uma operação de conversão que foi efetuada), em seguida, o volume será restaurado como um volume afixado localmente. Da mesma forma, se um volume localmente afixado existente foi expandido e, subsequentemente, restaurado a partir de um instantâneo mais antigo tomado quando o volume era inferior, o volume restaurado irão manter o tamanho expandido atual.

    Não é possível converter um volume a partir de um volume em camadas para um volume localmente afixado ou de um volume localmente afixado para um volume em camadas enquanto o volume está a ser restaurado. Aguarde até que a operação de restauro está concluída e, em seguida, pode converter o volume de outro tipo. Para obter informações sobre a conversão de um volume, aceda a [alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **O tamanho do volume é refletido no volume restaurado** – esta é uma consideração importante se estiver a restaurar um volume localmente afixado que tenha sido eliminado (porque volumes localmente afixados são totalmente aprovisionados). Certifique-se de que tem espaço suficiente antes de tentar restaurar um volume localmente afixado que tenha sido eliminado anteriormente.

* **Não é possível expandir um volume enquanto esta está a ser restaurada** – Aguarde até que a operação de restauro está concluída antes de tentar expandir o volume. Para informações sobre a expansão de um volume, aceda a [modificar um volume](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Pode efetuar uma cópia de segurança ao restaurar um volume local** – para procedimentos, avance para [utilizar o serviço do Gestor de dispositivos do StorSimple para gerir políticas de cópia de segurança](storsimple-8000-manage-backup-policies-u2.md).

* **Pode cancelar uma operação de restauro** – se cancelar a tarefa de restauro, em seguida, o volume será revertido para o estado que estava em antes de iniciar a operação de restauro. Para obter os procedimentos, vá para [cancelar uma tarefa](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Como restaurar o trabalho

Para dispositivos com a atualização 4 ou posterior, um restauro com base em heatmap é implementado. Como os pedidos de anfitrião para aceder a dados alcancem o dispositivo, estes pedidos são controlados e um heatmap é criado. Taxa de pedidos elevada resulta em segmentos de dados com maior térmico enquanto traduz inferior taxa de pedidos para segmentos com térmico inferior. Tem de aceder a dados, pelo menos, duas vezes para ser marcada como _frequente_. Um ficheiro que é modificado também está marcado como _frequente_. Depois de iniciar o restauro, ocorre com base no heatmap hidratação proativa de dados. Para as versões anteriores ao Update 4, os dados são transferidos durante o restauro com base no acesso apenas.

Os seguintes avisos aplicam-se para restauros heatmap com base em:

* Controlo de Heatmap está ativado apenas para volumes em camadas e volumes localmente afixados não são suportadas.

* Com base em Heatmap restore não é suportada quando a clonagem de um volume para outro dispositivo. 

* Se existir um restauro no local e um instantâneo local para o volume a ser restaurado existe no dispositivo, em seguida, iremos não rehydrate (como os dados já estão disponíveis localmente). 

* Por predefinição, quando restaurar, são iniciadas as tarefas de rehydration que proativamente rehydrate dados com base no heatmap. 

Na atualização 4, os cmdlets do Windows PowerShell pode ser utilizados para consultar tarefas rehydration em execução, cancelar uma tarefa de rehydration e obter o estado da tarefa rehydration.

* `Get-HcsRehydrationJob`-Este cmdlet obtém o estado da tarefa rehydration. É acionada uma tarefa de rehydration único para um volume.

* `Set-HcsRehydrationJob`-Este cmdlet permite-lhe colocar em pausa, parar, retomar a tarefa de rehydration, quando o rehydration está em curso.

Para obter mais informações sobre os cmdlets do rehydration, aceda a [referência de cmdlets do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Normalmente, com rehdyration automática, é esperado um desempenho superior transitório leitura. O magniutde real das melhorias depende de vários fatores, tais como o padrão de acesso, o volume de dados e o tipo de dados. 

Para cancelar uma tarefa de rehydration, pode utilizar o cmdlet do PowerShell. Se pretender desativar permanentemente tarefas rehydration para restauros futuras, [contacte a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Como utilizar o catálogo de cópias de segurança

O **catálogo de cópias de segurança** painel fornece uma consulta que ajuda a restringir a cópia de segurança definir seleção. Pode filtrar os conjuntos cópia de segurança que são obtidos com base nos parâmetros seguintes:

* **Intervalo de tempo** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.
* **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.
* **Política de cópia de segurança** ou **Volume** – a política de cópia de segurança ou volume associados a este conjunto de cópia de segurança.

Os conjuntos de cópia de segurança filtrados, em seguida, são apresentados com base nos seguintes atributos:

* **Nome** – o nome da política de cópia de segurança ou volume associado o conjunto de cópia de segurança.
* **Tipo** – conjuntos de cópia de segurança podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, enquanto que um instantâneo na nuvem refere-se para a cópia de segurança de dados do volume que residem na nuvem. Instantâneos locais fornecem um acesso mais rápido, enquanto que os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Tamanho** – o tamanho real do conjunto de cópia de segurança.
* **Criado no** – a data e hora quando as cópias de segurança foram criadas. 
* **Volumes** -o número de volumes associados ao conjunto de cópia de segurança.
* **Iniciou** – as cópias de segurança podem ser iniciadas automaticamente, de acordo com um agendamento ou manualmente por um utilizador. (Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar o **efetuar cópia de segurança** opção para efetuar uma cópia de segurança interativa ou a pedido.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume do StorSimple a partir de uma cópia de segurança

Pode utilizar o **catálogo de cópias de segurança** painel para restaurar o volume do StorSimple a partir de uma cópia de segurança específica. Tenha em atenção, no entanto, se restaurar um volume irá reverter o volume para o estado que estava quando foi feita a cópia de segurança. Todos os dados que foi adicionados após a operação de cópia de segurança serão perdidos.

> [!WARNING]
> Restaurar a partir de uma cópia de segurança irá substituir os volumes existentes da cópia de segurança. Isto pode provocar a perda de quaisquer dados que foi escritos depois de cópia de segurança.


### <a name="to-restore-your-volume"></a>Para restaurar o volume
1. Aceda ao seu serviço do Gestor de dispositivos do StorSimple e, em seguida, clique em **catálogo de cópia de segurança**.

2. Selecione uma cópia de segurança definida da seguinte forma:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo adequado.
   3. Na lista pendente, selecione a política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   4. Clique em **aplicar** para executar esta consulta.

    As cópias de segurança associado ao volume selecionado ou a política de cópia de segurança deve aparecer na lista de conjuntos de cópia de segurança.
   
    ![Lista de conjunto de cópia de segurança](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expanda a cópia de segurança definida para ver os volumes associados. Estes volumes devem ser colocados offline no anfitrião e do dispositivo antes de pode restaurá-las. Aceder aos volumes no **Volumes** painel do seu dispositivo e, em seguida, siga os passos no [colocar offline um volume](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) para colocá-las offline.
   
   > [!IMPORTANT]
   > Certifique-se de que seguiu os volumes offline no anfitrião em primeiro lugar, antes de colocar offline os volumes no dispositivo. Se não efetuar os volumes offline no anfitrião, pode, potencialmente, levar a danos nos dados.
   
4. Navegue de volta para o **catálogo de cópias de segurança** separador e selecione um conjunto de cópia de segurança. Clique com botão direito e, em seguida, no menu de contexto, selecione **restaurar**.

    ![Lista de conjunto de cópia de segurança](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Será solicitado para confirmação. Reveja as informações de restauro e, em seguida, selecione a caixa de verificação de confirmação.
   
    ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  Clique em **restaurar**. Esta ação inicia uma tarefa de restauro que pode ver acedendo a **tarefas** página.

    ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Após o restauro estiver concluído, certifique-se de que o conteúdo do seus volumes é substituído pelos volumes a partir da cópia de segurança.


## <a name="if-the-restore-fails"></a>Se falhar o restauro

Receberá um alerta se a operação de restauro falhar por algum motivo. Se isto ocorrer, atualize a lista de cópia de segurança para verificar que a cópia de segurança ainda é válida. Se a cópia de segurança é válida e está a restaurar a partir da nuvem, em seguida, problemas de conectividade poderão estar a causar o problema.

Para concluir a operação de restauro, coloque o volume offline no anfitrião e repita a operação de restauro. Tenha em atenção que quaisquer alterações aos dados do volume que foram executadas durante o processo de restauro serão perdidas.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [volumes do StorSimple gerir](storsimple-8000-manage-volumes-u2.md).
* Saiba como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

