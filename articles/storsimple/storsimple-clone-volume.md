---
title: Clonar o volume do StorSimple | Microsoft Docs
description: "Descreve os tipos de clone diferentes e quando a utilizá-los e explica como pode utilizar uma cópia de segurança definida para clonar um volume individuais."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b5d615f2-02a7-4222-9867-6c0385ce748c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: cf11a369549b887f79a81c19780048d31e56beae
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Utilizar o serviço StorSimple Manager para clonar um volume
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Descrição geral
O serviço StorSimple Manager **catálogo de cópias de segurança** página apresenta todos os conjuntos de cópias de segurança que são criados quando são efetuadas cópias de segurança manuais ou automáticas. Pode utilizar esta página para listar todas as cópias de segurança para uma política de cópia de segurança ou um volume, selecione ou eliminar as cópias de segurança ou utilize uma cópia de segurança para restaurar ou clonar um volume.

![Página do catálogo de cópias de segurança](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Este tutorial descreve como pode utilizar uma cópia de segurança definida para clonar um volume individuais. Também explica a diferença entre *transitório* e *permanente* clones. 

## <a name="create-a-clone-of-a-volume"></a>Crie um clone de um volume
Pode criar um clone no mesmo dispositivo, outro dispositivo ou mesmo uma máquina virtual através da utilização de uma local ou um instantâneo na nuvem.

#### <a name="to-clone-a-volume"></a>A clonagem de um volume
1. Na página de serviço StorSimple Manager, clique o **catálogo de cópia de segurança** separador e selecione um conjunto de cópia de segurança.
2. Expanda a cópia de segurança definida para ver os volumes associados. Clique e selecione um volume do conjunto de cópia de segurança.
   
     ![Clonar um volume](./media/storsimple-clone-volume/HCS_Clone.png) 
3. Clique em **Clone** para começar a clonagem de volume selecionado.
4. No Assistente de clonagem de Volume, em **Especificar nome e localização**:
   
   1. Identifica um dispositivo de destino. Esta é a localização onde será criado o clone. Pode escolher o mesmo dispositivo ou especifique outro dispositivo. Se escolher um volume associado a outros fornecedores de serviços em nuvem (não do Azure), na lista pendente para o dispositivo de destino, só serão apresentados dispositivos físicos. Não é possível clonar um volume associado a outros fornecedores de serviços em nuvem num dispositivo virtual.
      
      > [!NOTE]
      > Certifique-se de que a capacidade necessária para o clone é inferior à capacidade disponível no dispositivo de destino.
      > 
      > 
   2. Especifique um nome exclusivo de volume para o clone. O nome tem de conter entre 3 e 127 carateres.
   3. Clique no ícone de seta ![ícone de seta](./media/storsimple-clone-volume/HCS_ArrowIcon.png) Para continuar para a página seguinte.
5. Em **especificar anfitriões que podem utilizar este volume**:
   
   1. Especifique um registo de controlo de acesso (ACR) para o clone. Pode adicionar um novo ACR ou escolha na lista existente.
   2. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-clone-volume/HCS_CheckIcon.png)para concluir a operação.
6. Uma tarefa de clone será iniciada e será notificado quando foi criado com êxito o clone. Clique em **ver tarefa** para monitorizar a tarefa de clone no **tarefas** página.
7. Depois de concluída a tarefa de clone:
   
   1. Vá para o **dispositivos** página e selecione o **contentores de Volume** separador. 
   2. Selecione o contentor de volume que estão associado com o volume de origem que clonou. Na lista de volumes, deverá ver o clone que acabou de criar.

> [!NOTE]
> Monitorização e predefinido cópia de segurança automaticamente estão desativadas num volume clonado.
> 
> 

Um clone criado desta forma é um clone transitório. Para obter mais informações sobre os tipos de clone, consulte [transitório vs clones permanentes](#transient-vs-permanent-clones).

Este clone agora é um volume normal e todas as operações que é possível num volume estará disponível para o clone. Terá de configurar este volume para quaisquer cópias de segurança.

## <a name="transient-vs-permanent-clones"></a>Transitório vs clones permanentes
Clones transitórios e permanentes são criados apenas quando são clonagem para um dispositivo diferente. Pode clonar um volume específico de uma cópia de segurança definido para um dispositivo diferente. É um clone criado um *transitório* clone. O clone transitório terão as referências ao original volume e irão utilizar esse volume ler ao escrever localmente. 

Depois de tirar um instantâneo de nuvem de um clone transitório, clone resultante será uma *permanente* clone. O clone permanente é independente e não tem quaisquer referências para o volume original que foi clonado a partir do.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para clones transitórios e permanentes
As secções seguintes descrevem situações de exemplo em que podem ser utilizados os clones transitórios e permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação ao nível do item com um clone transitório
Precisa de recuperar um ficheiro de apresentação do Microsoft PowerPoint antigo de ano de um. O administrador de TI identifica a cópia de segurança específica desse período de tempo e, em seguida, filtra o volume. Em seguida, o administrador clones o volume, localiza o ficheiro que está a procurar e fornece-lhe. Neste cenário, é utilizado um clone transitório. 

![Vídeo disponível](./media/storsimple-clone-volume/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como pode utilizar o clone e restaurar as funcionalidades do StorSimple para recuperar ficheiros eliminados, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testar no ambiente de produção com um clone permanente
Tem de validar um erro de teste no ambiente de produção. Pode criar um clone do volume no ambiente de produção ao criar um instantâneo de nuvem deste clone. O volume clonado agora é independente. Neste cenário, é utilizado um clone permanente.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [restaurar um volume StorSimple a partir de um conjunto de cópia de segurança](storsimple-restore-from-backup-set.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

