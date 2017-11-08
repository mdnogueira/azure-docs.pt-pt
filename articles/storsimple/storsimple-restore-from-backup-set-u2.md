---
title: "Restaurar um volume StorSimple a partir de cópia de segurança | Microsoft Docs"
description: "Explica como utilizar a página de catálogo de cópias de segurança do serviço StorSimple Manager para restaurar um volume StorSimple a partir de um conjunto de cópia de segurança."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 76fa3dd8fa2f9775dc166686e699a8dd66399aff
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restaurar um volume StorSimple a partir de um conjunto de cópia de segurança (atualização 2)
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [restaurar um volume StorSimple a partir de um conjunto de cópia de segurança (atualização 2)](storsimple-8000-restore-from-backup-set-u2.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Descrição geral
O **catálogo de cópias de segurança** página apresenta todos os conjuntos de cópias de segurança que são criados quando são efetuadas cópias de segurança manuais ou automáticas. Utilize esta página para listar e gerir cópias de segurança, restaurar a partir de um conjunto de cópia de segurança ou clonar um volume.

 ![Página do catálogo de cópias de segurança](./media/storsimple-restore-from-backup-set-u2/restore.png)

Este tutorial explica como utilizar o **catálogo de cópias de segurança** página para restaurar o seu dispositivo a partir de um conjunto de cópia de segurança.

Pode restaurar uma cópia de volume da local ou na nuvem. Em ambos os casos, a operação de restauro coloca o volume online imediatamente enquanto os dados que são transferidos em segundo plano. 

## <a name="before-you-restore"></a>Antes de restaurar
Antes de iniciar uma operação de restauro, deve ter em consideração os seguintes avisos:

* **Coloque o volume offline** – tirar o volume offline no anfitrião e o dispositivo antes de iniciar a operação de restauro. Apesar da operação de restauro repõe automaticamente o volume online no dispositivo, tem de colocar manualmente o dispositivo online no anfitrião. Quando o volume estiver online no dispositivo que pode colocar o volume online no anfitrião. (A não ter de aguardar até que a operação de restauro está concluída.) Para obter os procedimentos, vá para [colocar offline um volume](storsimple-manage-volumes-u2.md#take-a-volume-offline).
* **Tipo de volume após o restauro** – eliminado volumes são restaurados com base no tipo no instantâneo. Volumes que foram afixados localmente são restaurados como volumes localmente afixados e volumes que foram camadas são restaurados como volumes em camadas.
  
    Para os volumes existentes, o tipo de utilização atual do volume substitui o tipo que está armazenado no instantâneo. Por exemplo, se restaurar um volume a partir de um instantâneo que foi tomado quando o tipo de volume foi camado e de que o tipo de volume agora é afixado localmente (devido a uma operação de conversão), o volume é restaurado como um volume afixado localmente. Da mesma forma, se um volume localmente afixado existente é expandido e, subsequentemente, restaurado a partir de um instantâneo mais antigo tomado quando o volume era inferior, o volume restaurado mantém o tamanho expandido atual.
  
    Não é possível converter um volume a partir de um volume em camadas para um volume localmente afixado ou _vice-versa-se_ enquanto o volume está a ser restaurado. Aguarde até que a operação de restauro está concluída e, em seguida, pode converter o volume de outro tipo. Para obter informações sobre a conversão de um volume, aceda a [alterar o tipo de volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 
* **O tamanho do volume é refletido no volume restaurado** – esta é uma consideração importante se estiver a restaurar um volume localmente afixado que tenha sido eliminado (porque volumes localmente afixados são totalmente aprovisionados). Certifique-se de que tem espaço suficiente antes de tentar restaurar um volume localmente afixado que tenha sido eliminado anteriormente. 
* **Não é possível expandir um volume enquanto esta está a ser restaurada** – Aguarde até que a operação de restauro está concluída antes de tentar expandir o volume. Para informações sobre a expansão de um volume, aceda a [modificar um volume](storsimple-manage-volumes-u2.md#modify-a-volume).
* **Pode efetuar uma cópia de segurança enquanto estiver a restaurar um volume local** – para procedimentos, avance para [utilizar o serviço StorSimple Manager para gerir políticas de cópia de segurança](storsimple-manage-backup-policies.md).
* **Pode cancelar uma operação de restauro** – se cancelar a tarefa de restauro, em seguida, o volume é revertido para o estado que estava em antes de iniciar o restauro. Para obter os procedimentos, vá para [cancelar uma tarefa](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Como restaurar o trabalho
Para dispositivos com a atualização 4 ou posterior, um restauro com base em heatmap é implementado. Como os pedidos de anfitrião para aceder a dados alcancem o dispositivo, estes pedidos são controlados e um heatmap é criado. Taxa de pedidos elevada resulta em segmentos de dados com maior térmico enquanto traduz inferior taxa de pedidos para segmentos com térmico inferior. Tem de aceder a dados, pelo menos, duas vezes para ser marcada como _frequente_. Um ficheiro que é modificado também está marcado como _frequente_. Depois de iniciar o restauro, ocorre com base no heatmap hidratação proativa de dados. Para as versões anteriores ao Update 4, os dados foi transferidos durante com base no acesso só de restauro. 

Controlo baseado em Heatmap está ativado apenas para volumes em camadas e afixado localmente volumes não são suportados. Com base em Heatmap restauro também não é suportado quando a clonagem de um volume para outro dispositivo. Se existir um restauro no local e um instantâneo local para o volume a ser restaurado existe no dispositivo, em seguida, iremos não rehydrate (como os dados já estão disponíveis localmente). Por predefinição, quando restaurar, são iniciadas as tarefas de rehydration que proativamente rehydrate dados com base no heatmap. Na atualização 4, os cmdlets do Windows PowerShell pode ser utilizados para consultar tarefas rehydration em execução, cancelar uma tarefa de rehydration e obter o estado da tarefa rehydration.

* `Get-HcsRehydrationJob`-Este cmdlet obtém o estado da tarefa rehydration. É acionada uma tarefa de rehydration único para um volume.
* `Set-HcsRehydrationJob`-Este cmdlet permite-lhe colocar em pausa, parar, retomar a tarefa de rehydration, quando o rehydration está em curso. 

Para obter mais informações sobre os cmdlets do rehydration, aceda a [referência de cmdlets do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Normalmente, com rehdyration automática, é esperado um desempenho superior transitório leitura. O magniutde real das melhorias depende de vários fatores, tais como o padrão de acesso, o volume de dados e o tipo de dados. Para cancelar uma tarefa de rehydration, pode utilizar o cmdlet do PowerShell. Se pretender desativar permanentemente rehydration as tarefas para todos os restauros futuras, contacte Support da Microsoft.

## <a name="how-to-use-the-backup-catalog"></a>Como utilizar o catálogo de cópias de segurança
O **catálogo de cópias de segurança** página fornece uma consulta que ajuda a restringir a cópia de segurança definir seleção. Pode filtrar os conjuntos cópia de segurança que são obtidos com base nos parâmetros seguintes:

* **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.
* **Política de cópia de segurança** ou **volume** – a política de cópia de segurança ou volume associados a este conjunto de cópia de segurança.
* **De** e **para** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.

Os conjuntos de cópia de segurança filtrados, em seguida, são apresentados com base nos seguintes atributos:

* **Nome** – o nome da política de cópia de segurança ou volume associado o conjunto de cópia de segurança.
* **Tamanho** – o tamanho real do conjunto de cópia de segurança.
* **Criado no** – a data e hora quando as cópias de segurança foram criadas. 
* **Tipo** – conjuntos de cópia de segurança podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo. Um instantâneo na nuvem refere-se para a cópia de segurança de dados do volume que residem na nuvem. Instantâneos locais fornecem um acesso mais rápido, enquanto que os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Iniciado pelo** – as cópias de segurança podem ser iniciadas automaticamente, de acordo com um agendamento ou manualmente por si. (Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar o **efetuar cópia de segurança** opção para efetuar uma cópia de segurança interativa.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume do StorSimple a partir de uma cópia de segurança
Pode utilizar o **catálogo de cópias de segurança** página para restaurar o volume do StorSimple a partir de uma cópia de segurança específica. Tenha em atenção, no entanto, se restaurar um volume reverte o volume para o estado que estava quando foi feita a cópia de segurança. Quaisquer dados que tenha sido adicionados após a operação de cópia de segurança são perdidos.

> [!WARNING]
> Restaurar a partir de uma cópia de segurança substitui os volumes existentes da cópia de segurança. Isto pode provocar a perda de quaisquer dados que foi escritos depois de cópia de segurança.
> 
> 

### <a name="to-restore-your-volume"></a>Para restaurar o volume
1. Na página de serviço StorSimple Manager, clique o **catálogo de cópia de segurança** separador.
   
    ![Catálogo de cópias de segurança](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. Selecione uma cópia de segurança definida da seguinte forma:
   
   1. Selecione o dispositivo adequado.
   2. Na lista pendente, selecione a política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) para executar esta consulta.
      
      As cópias de segurança associado ao volume selecionado ou a política de cópia de segurança deve aparecer na lista de conjuntos de cópia de segurança.
3. Expanda a cópia de segurança definida para ver os volumes associados. Estes volumes devem ser colocados offline no anfitrião e do dispositivo antes de pode restaurá-las. Aceder aos volumes no **contentores de Volume** página e, em seguida, siga os passos em [colocar offline um volume](storsimple-manage-volumes-u2.md#take-a-volume-offline) para colocá-las offline.
   
   > [!IMPORTANT]
   > Certifique-se de que seguiu os volumes offline no anfitrião em primeiro lugar, antes de colocar offline os volumes no dispositivo. Se não efetuar os volumes offline no anfitrião, pode, potencialmente, levar a danos nos dados.
   > 
   > 
4. Navegue de volta para o **catálogo de cópias de segurança** separador e selecione um conjunto de cópia de segurança.
5. Clique em **restaurar** na parte inferior da página.
6. Lhe for pedida a confirmação. Reveja as informações de restauro e, em seguida, selecione a caixa de verificação de confirmação.
   
    ![Página de confirmação](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Inicia uma tarefa de restauro. Pode ver a tarefa acedendo a **tarefas** página. 
8. Após o restauro está concluído, pode verificar que o conteúdo do seus volumes é substituído pelos volumes a partir da cópia de segurança.

![Vídeo disponível](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como pode utilizar o clone e restaurar as funcionalidades do StorSimple para recuperar ficheiros eliminados, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Se falhar o restauro
Recebe um alerta se a operação de restauro falhar por algum motivo. Se isto ocorrer, atualize a lista de cópia de segurança para verificar que a cópia de segurança ainda é válida. Se a cópia de segurança é válida e está a restaurar a partir da nuvem, em seguida, problemas de conectividade poderão estar a causar o problema. 

Para concluir a operação de restauro, coloque o volume offline no anfitrião e repita a operação de restauro. Quaisquer alterações aos dados do volume que foram executadas durante o processo de restauro serão perdidas.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [volumes do StorSimple gerir](storsimple-manage-volumes-u2.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

