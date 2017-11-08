---
title: Gerir os volumes do StorSimple | Microsoft Docs
description: "Explica como adicionar, modificar, monitorizar e eliminar os volumes do StorSimple e como colocar offline se necessário."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ccabd859-590c-4568-a81d-6ff38f125be3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 8e33f971610b01e9802e8fb12457d9ce88b51813
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-volumes"></a>Utilizar o serviço StorSimple Manager para gerir volumes
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Descrição geral
Este tutorial explica como utilizar o serviço StorSimple Manager para criar e gerir volumes no dispositivo StorSimple e dispositivo virtual StorSimple.

O serviço StorSimple Manager é uma extensão do portal clássico do Azure que permite-lhe gerir solução StorSimple a partir de uma interface web único. Além da gestão de volumes, pode utilizar o serviço StorSimple Manager para criar e gerir serviços StorSimple, ver e gerir dispositivos, ver alertas e ver e gerir políticas de cópia de segurança e o catálogo de cópias de segurança.

> [!NOTE]
> Azure StorSimple pode criar apenas volumes de aprovisionamento dinâmico. Não é possível criar totalmente aprovisionamento totais ou parciais volumes num sistema Azure StorSimple.
> 
> Aprovisionamento dinâmico é uma tecnologia de virtualização em que o armazenamento disponível aparece exceder os recursos físicos. Em vez de reservar antecipadamente armazenamento suficiente, Azure StorSimple utiliza o aprovisionamento dinâmico para alocar espaço apenas suficiente para satisfazer os requisitos atuais. A natureza elástica do armazenamento na nuvem facilita esta abordagem, porque o Azure StorSimple pode aumentar ou diminuir o armazenamento na nuvem para satisfazer os requisitos evolutivos.
> 
> 

## <a name="the-volumes-page"></a>A página de Volumes
O **Volumes** página permite-lhe gerir os volumes de armazenamento que estão aprovisionados no dispositivo Microsoft Azure StorSimple para os iniciadores (servidores). Apresenta a lista de volumes no dispositivo StorSimple.

 ![Página de volumes](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

Um volume é constituída por uma série de atributos:

* **Nome** – um nome descritivo que têm de ser exclusivos e ajuda a identificar o volume. Este nome também é utilizado em relatórios de monitorização ao filtrar num volume específico.
* **Estado** – pode ser online ou offline. Se um volume se offline, não é visível para os iniciadores (servidores) que estão a permissão para aceder ao utilizar o volume.
* **Capacidade** – Especifica a é o volume como grandes, como percetível pelo iniciador (servidor). Capacidade Especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Volumes são com aprovisionamento dinâmico e dados tem eliminação de duplicados. Isto implica que o dispositivo previamente não atribuir capacidade de armazenamento físico internamente ou na nuvem de acordo com a capacidade do volume configurado. A capacidade do volume está atribuída e consumida a pedido.
* **Tipo** – o tipo de volume pode ser arquivo ou em camadas (um subtipo de camadas)
* **Acesso** – Especifica os iniciadores (servidores) que têm permissão de acesso a este volume. Os iniciadores que não sejam membros de registo de controlo de acesso (ACR) que estão associados com o volume não irão ver o volume.
* **Monitorização** – Especifica se pretende ou não está a ser monitorizado um volume. Um volume terão monitorização ativada por predefinição, quando é criado. Monitorização irá, no entanto, ser desativado para um clone de volume. Para ativar a monitorização de um volume, siga as instruções no Monitor de um volume.

As tarefas mais comuns associadas um volume são:

* Adicionar um volume
* Modificar um volume
* Eliminar um volume
* Colocar offline um volume
* Monitorizar um volume

## <a name="add-a-volume"></a>Adicionar um volume
[Criado um volume](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) durante a implementação da solução StorSimple. A adição de um volume é um procedimento semelhante.

### <a name="to-add-a-volume"></a>Para adicionar um volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Selecione um contentor de volume e clique na seta na linha correspondente para aceder aos volumes associados ao contentor.
3. Clique em **adicionar** na parte inferior da página. Adicionar um Assistente de volume é iniciado.
   
     ![Adicionar Assistente de volume definições básicas](./media/storsimple-manage-volumes/AddVolume1.png)
4. No assistente Adicionar um volume, em **Definições Básicas**, faça o seguinte:
   
   1. Forneça um **Nome** para o volume.
   2. Especifique a **Capacidade Aprovisionada** para o volume em GB ou TB. A capacidade tem de ser entre 1 GB e 64 TB para um dispositivo físico. A capacidade máxima que pode ser aprovisionada para um volume num dispositivo virtual StorSimple é 30 TB.
   3. Selecione o **tipo de utilização** para o volume. Se estiver a utilizar o volume em camadas para dados de arquivo, selecionar o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação altera o tamanho de segmentos de eliminação de duplicados para o volume para 512 KB. Se não selecionar esta opção, o volume em camadas correspondente irá utilizar um tamanho de segmentos de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem. (Volumes em camadas chamavam-volumes principais.)
   4. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)para ir para o **definições adicionais** página.
      
        ![Adicionar definições adicionais do Assistente de Volume](./media/storsimple-manage-volumes/AddVolume2.png)
5. Em **definições adicionais**, adicione um novo registo de controlo de acesso (ACR):
   
   1. Selecione um registo de controlo de acesso (ACR) na lista pendente. Em alternativa, pode adicionar um novo ACR. ACRs determinar os anfitriões que podem aceder a sua volumes por correspondente anfitrião IQN com que listados no registo.
   2. Recomendamos que ative uma cópia de segurança predefinida selecionando a caixa de verificação **Ativar uma cópia de segurança predefinido para este volume**.
   3. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-volumes/HCS_CheckIcon.png) Para criar o volume com as definições especificadas.

O novo volume agora está pronto a utilizar.

## <a name="modify-a-volume"></a>Modificar um volume
Modificar um volume quando precisa de expandir ou alterar os anfitriões que o volume de acesso.

> [!IMPORTANT]
> * Se modificar o tamanho do volume no dispositivo, tem de ser alterada no anfitrião, bem como o tamanho do volume.
> * Os passos do lado do anfitrião descritos aqui são para o Windows Server 2012 (2012R2). Procedimentos para Linux ou outros sistemas operativos de anfitrião será diferentes. Consulte as instruções de sistema operativo do anfitrião ao modificar o volume de um anfitrião com outro sistema operativo.
> 
> 

### <a name="to-modify-a-volume"></a>Para modificar um volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentor de Volume** separador. Esta página lista, em formato tabular, todos os contentores de volume que estão associados ao dispositivo.
2. Selecione um contentor de volume e clique na mesma para ver a lista de todos os volumes no contentor.
3. No **Volumes** página, selecione um volume e clique em **modificar**.
4. No Assistente de volume modificar, sob **definições básicas**, pode fazer o seguinte:
   
   * Editar o **nome** e **tipo** se pretender modificar um volume em camadas para um volume de arquivo, selecionando o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação Para alterar o tamanho de segmentos de eliminação de duplicados para o volume para 512 KB.
   * Aumentar a **aprovisionado capacidade**. O **capacidade aprovisionada** só pode ser aumentada. Não é possível encolher o volume depois de criado.
     
     > [!NOTE]
     > Não é possível alterar o contentor de volume depois de ser atribuído a um volume.
     > 
     > 
5. Em **definições adicionais**, pode fazer o seguinte:
   
   * Modificar ACRs, desde que o volume está offline. Se o volume estiver online, terá de levá-los offline pela primeira vez. Consulte os passos descritos para [colocar offline um volume](#take-a-volume-offline) antes de modificar o ACR.
   * Modificar a lista de ACRs depois do volume está offline.
     
     > [!NOTE]
     > Não é possível alterar o **ativar uma cópia de segurança predefinido para este volume** opção para o volume.
     > 
     > 
6. Guardar as alterações ao clicar no ícone de verificação ![ícone de verificação](./media/storsimple-manage-volumes/HCS_CheckIcon.png). Portal clássico do Azure irá apresentar uma mensagem de volume de atualização. Apresentará uma mensagem de êxito quando o volume tiver sido atualizado com êxito.
7. Se está a expandir um volume, conclua os seguintes passos no seu computador de anfitrião do Windows:
   
   1. Aceda a **gestão de computadores** ->**disco gestão**.
   2. Clique com botão direito **gestão de discos** e selecione **reanalisar discos**.
   3. Na lista de discos, selecione o volume que tenha atualizado, rato e, em seguida, selecione **Expandir Volume**. O Assistente de expandir o Volume é iniciado. Clique em **Seguinte**.
   4. Conclua o assistente, aceitando os valores predefinidos. Depois de concluído o assistente, o volume deve mostrar o tamanho maior.

![Vídeo disponível](./media/storsimple-manage-volumes/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como expandir um volume, clique em [aqui](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="take-a-volume-offline"></a>Colocar offline um volume
Poderá ter de colocar offline um volume quando estiver a planear modificá-lo ou eliminá-lo. Quando um volume estiver offline, não está disponível para acesso de leitura e escrita. Terá de colocar offline o volume do anfitrião, bem como no dispositivo. Execute os seguintes passos para colocar offline um volume.

### <a name="to-take-a-volume-offline"></a>Para colocar offline um volume
1. Certifique-se de que o volume em questão não está a ser utilizado antes de colocar-offline.
2. Colocar offline o volume anfitrião primeiro. Isto elimina a qualquer potencial risco de danos nos dados no volume. Para obter passos específicos, consulte as instruções para o seu sistema operativo do anfitrião.
3. Depois do anfitrião estiver offline, colocar o volume no dispositivo offline, efetuando os seguintes passos:
   
   1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador. O **contentores de Volume** separador lista em formato tabular, todos os contentores de volume que estão associados ao dispositivo.
   2. Selecione um contentor de volume e clique na mesma para ver a lista de todos os volumes no contentor.
   3. Selecione um volume e clique em **colocar offline**.
   4. Quando lhe for pedida a confirmação, clique em **Sim**. O volume deve agora estar offline.
      
      Depois de um volume estiver offline, o **colocar Online** opção fica disponível.

> [!NOTE]
> O **Colocar Offline** comando envia um pedido para o dispositivo para colocar offline o volume. Se anfitriões ainda estiver a utilizar o volume, isto resulta em ligações quebradas, mas colocar offline o volume não irá falhar.
> 
> 

## <a name="delete-a-volume"></a>Eliminar um volume
> [!IMPORTANT]
> Pode eliminar um volume apenas se estiver offline.
> 
> 

Conclua os seguintes passos para eliminar um volume.

### <a name="to-delete-a-volume"></a>Para eliminar um volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Selecione o contentor de volume que contém o volume que pretende eliminar. Clique no contentor de volume para aceder a **Volumes** página.
3. Todos os volumes associados a este contentor são apresentados em formato tabular. Verifique o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, coloque-offline em primeiro lugar, seguindo os passos no [colocar offline um volume](#take-a-volume-offline).
4. Depois do volume estiver offline, clique em **eliminar** na parte inferior da página.
5. Quando lhe for pedida a confirmação, clique em **Sim**. O volume agora será eliminado e o **Volumes** página mostrará a lista atualizada de volumes no contentor.

## <a name="monitor-a-volume"></a>Monitorizar um volume
Monitorização de volume permite-lhe recolher estatísticas de I/O-relacionadas com para um volume. Monitorização é ativada por predefinição para os 32 pela primeira vez volumes que criar. Monitorização de volumes adicionais está desativada por predefinição. Monitorização de volumes clonados também é desativado por predefinição.

Execute os seguintes passos para ativar ou desativar a monitorização para um volume.

### <a name="to-enable-or-disable-volume-monitoring"></a>Para ativar ou desativar a monitorização de volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Selecione o contentor de volume em que reside o volume e, em seguida, clique no contentor de volume para aceder a **Volumes** página.
3. Todos os volumes associados a este contentor estão listados na tabela de apresentação. Clique e selecione o volume ou a clonagem de volume.
4. Na parte inferior da página, clique em **modificar**.
5. No assistente modificar Volume, em **definições básicas**, selecione **ativar** ou **desativar** do **monitorização** na lista pendente.
   
    ![Modificar um volume definições básicas](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [clonar um volume StorSimple](storsimple-clone-volume.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

