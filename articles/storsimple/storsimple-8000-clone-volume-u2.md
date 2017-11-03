---
title: "Clonar um volume num série 8000 do StorSimple | Microsoft Docs"
description: "Descreve os tipos de clone diferentes e a utilização e explica como pode utilizar uma cópia de segurança definida para clonar um volume num dispositivo série 8000 do StorSimple individuais."
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
ms.date: 07/26/2017
ms.author: alkohli
ms.openlocfilehash: 70c85bcb2c26d2ad3d0515d24e028f84495634c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Utilizar o serviço StorSimple Manager de dispositivos no portal do Azure para clonar um volume

## <a name="overview"></a>Descrição geral

Este tutorial descreve como pode utilizar uma cópia de segurança definida para clonar um volume individuais através do **catálogo de cópia de segurança** painel. Também explica a diferença entre *transitório* e *permanente* clones. As orientações neste tutorial aplica-se a todos os dispositivos de série 8000 do StorSimple executar a atualização 3 ou posterior.

O serviço do Gestor de dispositivos do StorSimple **catálogo de cópia de segurança** painel apresenta todos os conjuntos de cópias de segurança que são criados quando são efetuadas cópias de segurança manuais ou automáticas. Em seguida, pode selecionar um volume numa cópia de segurança definida para clonar.

 ![Lista de conjunto de cópia de segurança](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Considerações para a clonagem de um volume

Quando a clonagem de um volume, considere as seguintes informações.

- Um clone comporta-se da mesma forma que um volume normal. Todas as operações que é possível num volume estão disponível para o clone.

- Monitorização e predefinido cópia de segurança automaticamente estão desativadas num volume clonado. Terá de configurar um volume clonado para quaisquer cópias de segurança.

- Um volume localmente afixado é clonado como um volume em camadas. Se precisar de volume clonado para ser afixado localmente, pode converter o clone para um volume localmente afixado, depois da operação de clonagem é concluída com êxito. Para obter informações sobre a conversão de um volume em camadas para um volume localmente afixado, aceda a [alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Se tentar converter um volume clonado de camadas para afixado localmente imediatamente após a clonagem (quando ainda é um clone transitório), a conversão irá falhar com a seguinte mensagem de erro:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Este erro é recebido apenas se estiver a clonagem para um dispositivo diferente. Pode converter com êxito ao volume localmente afixado se converter primeiro o clone transitório para um clone permanente. Tire um instantâneo de nuvem do clone transitório convertê-lo para um clone permanente.

## <a name="create-a-clone-of-a-volume"></a>Crie um clone de um volume

Pode criar um clone no mesmo dispositivo, outro dispositivo ou mesmo uma aplicação de nuvem através da utilização de uma local ou do instantâneo na nuvem.

O procedimento abaixo descreve como criar um clone a partir do catálogo de cópias de segurança.  É um método alternativo para iniciar clone Ir para **Volumes**, selecione um volume, em seguida, faça duplo clique para invocar o menu de contexto e selecione **Clone**.

Execute os seguintes passos para criar um clone do seu volume a partir do catálogo de cópias de segurança.

#### <a name="to-clone-a-volume"></a>A clonagem de um volume

1. Aceda ao seu serviço do Gestor de dispositivos do StorSimple e, em seguida, clique em **catálogo de cópia de segurança**.

2. Selecione uma cópia de segurança definida da seguinte forma:
   
   1. Selecione o dispositivo adequado.
   2. Na lista pendente, selecione a política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique em **aplicar** para executar esta consulta.

    As cópias de segurança associado ao volume selecionado ou a política de cópia de segurança deve aparecer na lista de conjuntos de cópia de segurança.
   
    ![Lista de conjunto de cópia de segurança](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expanda a cópia de segurança definida para ver os volumes associados. Estes volumes devem ser colocados offline no anfitrião e do dispositivo antes de pode restaurá-las. Aceder aos volumes no **Volumes** painel do seu dispositivo e, em seguida, siga os passos no [colocar offline um volume](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) para colocá-las offline.
   
   > [!IMPORTANT]
   > Certifique-se de que seguiu os volumes offline no anfitrião em primeiro lugar, antes de colocar offline os volumes no dispositivo. Se não efetuar os volumes offline no anfitrião, pode, potencialmente, levar a danos nos dados.
   
4. Navegue de volta para o **catálogo de cópia de segurança** e selecione um volume num conjunto de cópia de segurança. Clique com botão direito e, em seguida, no menu de contexto, selecione **Clone**.

   ![Lista de conjunto de cópia de segurança](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. No **Clone** painel, efetue os seguintes passos:
   
    1. Identifica um dispositivo de destino. Esta é a localização onde será criado o clone. Pode escolher o mesmo dispositivo ou especifique outro dispositivo.

      > [!NOTE]
      > Certifique-se de que a capacidade necessária para o clone é inferior à capacidade disponível no dispositivo de destino.
       
    2. Especifique um nome exclusivo de volume para o clone. O nome tem de conter entre 3 e 127 carateres.
      
        > [!NOTE]
        > O **Clone Volume como** campo será **em camadas** , mesmo que a clonagem de um volume afixado localmente. Não é possível alterar esta definição; No entanto, se precisar de volume clonado para ser afixado localmente bem, pode converter o clone um volume localmente afixado depois de criar com êxito o clone. Para obter informações sobre a conversão de um volume em camadas para um volume localmente afixado, aceda a [alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
    3. Em **ligados a anfitriões**, especifique um registo de controlo de acesso (ACR) para o clone. Pode adicionar um novo ACR ou escolha na lista existente. O ACR irá determinar os anfitriões que podem aceder a este clone.
      
        ![Lista de conjunto de cópia de segurança](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

    4. Clique em **Clone** para concluir a operação.

4. Uma tarefa de clone é iniciada e será notificado quando foi criado com êxito o clone. Clique na notificação da tarefa ou vá para **tarefas** painel para monitorizar a tarefa de clone.

    ![Lista de conjunto de cópia de segurança](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Depois de concluída a tarefa de clone, aceda ao seu dispositivo e, em seguida, clique em **Volumes**. Na lista de volumes, deverá ver o clone que acabou de ser criado no mesmo contentor de volume que tem o volume de origem.

    ![Lista de conjunto de cópia de segurança](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Um clone criado desta forma é um clone transitório. Para obter mais informações sobre os tipos de clone, consulte [transitório vs clones permanentes](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Transitório vs clones permanentes
Clones transitórios são criados apenas quando clonar a outro dispositivo. Pode clonar um volume específico de uma cópia de segurança definido para um dispositivo diferente gerido pelo Gestor de dispositivo StorSimple. O clone transitório tem referências aos dados no original volume e utiliza os dados para ler e escrever localmente no dispositivo de destino.

Depois de tomar um instantâneo de nuvem de um clone transitório, clone resultante é um *permanente* clone. Durante este processo, é criada uma cópia dos dados na nuvem e a hora para copiar dados é determinada pelo tamanho dos dados e as latências do Azure (isto é uma cópia do Azure para o Azure). Este processo pode demorar dias ou semanas. O clone transitório torna-se de um clone permanente e não tem quaisquer referências para os dados do volume original que foi clonado a partir do.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para clones transitórios e permanentes
As secções seguintes descrevem situações de exemplo em que podem ser utilizados os clones transitórios e permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação ao nível do item com um clone transitório
Precisa de recuperar um ficheiro de apresentação do Microsoft PowerPoint antigo de ano de um. O administrador de TI identifica a cópia de segurança específica de tempo e, em seguida, filtra o volume. Em seguida, o administrador clones o volume, localiza o ficheiro que está a procurar e fornece-lhe. Neste cenário, é utilizado um clone transitório.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testar no ambiente de produção com um clone permanente
Tem de validar um erro de teste no ambiente de produção. Crie um clone do volume no ambiente de produção e, em seguida, tirar um instantâneo nuvem este clone para criar um volume clonado independente. Neste cenário, é utilizado um clone permanente.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [restaurar um volume StorSimple a partir de um conjunto de cópia de segurança](storsimple-8000-restore-from-backup-set-u2.md).
* Saiba como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

