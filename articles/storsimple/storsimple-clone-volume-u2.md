---
title: "Clonar o volume de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve os tipos de clone diferentes e quando a utilizá-los e explica como pode utilizar uma cópia de segurança definida para clonar um volume individuais."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 070ac53e-7388-4c48-b8a5-8ed7f9108b2c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/26/2017
ms.author: alkohli
ms.openlocfilehash: 2b627250df62bbe3299869ef3812947afbb8f29f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>Utilizar o serviço StorSimple Manager para clonar um volume (atualização 2)
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Descrição geral
O serviço StorSimple Manager **catálogo de cópias de segurança** página apresenta todos os conjuntos de cópias de segurança que são criados quando são efetuadas cópias de segurança manuais ou automáticas. Pode utilizar esta página para listar todas as cópias de segurança para uma política de cópia de segurança ou um volume, selecione ou eliminar as cópias de segurança ou utilize uma cópia de segurança para restaurar ou clonar um volume.

![Página do catálogo de cópias de segurança](./media/storsimple-clone-volume-u2/backupCatalog.png)  

Este tutorial descreve como pode utilizar uma cópia de segurança definida para clonar um volume individuais. Também explica a diferença entre *transitório* e *permanente* clones.

> [!NOTE]
> Um volume localmente afixado será clonado como um volume em camadas. Se precisar de volume clonado para ser afixado localmente, pode converter o clone para um volume localmente afixado, depois da operação de clonagem é concluída com êxito. Para obter informações sobre a conversão de um volume em camadas para um volume localmente afixado, aceda a [alterar o tipo de volume](storsimple-manage-volumes-u2.md#change-the-volume-type).
> 
> Se tentar converter um volume clonado de camadas para afixado localmente imediatamente após a clonagem (quando ainda é um clone transitório), a conversão irá falhar com a seguinte mensagem de erro:
> 
> `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
> 
> Este erro é recebido apenas se estiver a clonagem para um dispositivo diferente. Pode converter com êxito ao volume localmente afixado se converter primeiro o clone transitório para um clone permanente. Para converter o clone transitório para um clone permanente, tire um instantâneo de nuvem do mesmo.
> 
> 

## <a name="create-a-clone-of-a-volume"></a>Crie um clone de um volume
Pode criar um clone no mesmo dispositivo, outro dispositivo ou mesmo uma máquina virtual através da utilização de uma local ou do instantâneo na nuvem.

#### <a name="to-clone-a-volume"></a>A clonagem de um volume
1. Na página de serviço StorSimple Manager, clique o **catálogo de cópia de segurança** separador e selecione um conjunto de cópia de segurança.
2. Expanda a cópia de segurança definida para ver os volumes associados. Clique e selecione um volume do conjunto de cópia de segurança.
   
     ![Clonar um volume](./media/storsimple-clone-volume-u2/CloneVol.png) 
3. Clique em **Clone** para começar a clonagem de volume selecionado.
4. No Assistente de clonagem de Volume, em **Especificar nome e localização**:
   
   1. Identifica um dispositivo de destino. Esta é a localização onde será criado o clone. Pode escolher o mesmo dispositivo ou especifique outro dispositivo. Se escolher um volume associado a outros fornecedores de serviços em nuvem (não do Azure), na lista pendente para o dispositivo de destino, só serão apresentados dispositivos físicos. Não é possível clonar um volume associado a outros fornecedores de serviços em nuvem num dispositivo virtual.
      
      > [!NOTE]
      > Certifique-se de que a capacidade necessária para o clone é inferior à capacidade disponível no dispositivo de destino.
      > 
      > 
   2. Especifique um nome exclusivo de volume para o clone. O nome tem de conter entre 3 e 127 carateres. 
      
      > [!NOTE]
      > O **Clone Volume como** campo será **em camadas** , mesmo que a clonagem de um volume afixado localmente. Não é possível alterar esta definição; No entanto, se precisar de volume clonado para ser afixado localmente bem, pode converter o clone um volume localmente afixado depois de criar com êxito o clone. Para obter informações sobre a conversão de um volume em camadas para um volume localmente afixado, aceda a [alterar o tipo de volume](storsimple-manage-volumes-u2.md#change-the-volume-type).
      > 
      > 
      
        ![Assistente de clonagem 1](./media/storsimple-clone-volume-u2/clone1.png) 
   3. Clique no ícone de seta ![ícone de seta](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) Para continuar para a página seguinte.
5. Em **especificar anfitriões que podem utilizar este volume**:
   
   1. Especifique um registo de controlo de acesso (ACR) para o clone. Pode adicionar um novo ACR ou escolha na lista existente.
      
        ![Assistente de clonagem 2](./media/storsimple-clone-volume-u2/clone2.png) 
   2. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)para concluir a operação.
6. Uma tarefa de clone será iniciada e será notificado quando foi criado com êxito o clone. Clique em **ver tarefa** para monitorizar a tarefa de clone no **tarefas** página. Verá a mensagem seguinte quando estiver concluída a tarefa de clone:
   
    ![Mensagem de clone](./media/storsimple-clone-volume-u2/CloneMsg.png) 
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
Clones transitórios são criados apenas quando são clonagem para um dispositivo diferente. Pode clonar um volume específico de uma cópia de segurança definido para um dispositivo diferente gerido pelo StorSimple Manager. O clone transitório serão referências aos dados no original volume e irá utilizar esses dados para ler e escrever localmente no dispositivo de destino. 

Depois de tirar um instantâneo de nuvem de um clone transitório, clone resultante será uma *permanente* clone. Durante este processo, é criada uma cópia dos dados na nuvem e a hora para copiar dados é determinada pelo tamanho dos dados e as latências do Azure (isto é uma cópia do Azure para o Azure). Este processo pode demorar dias ou semanas. O clone transitório torna-se um clone permanente desta forma e não tem quaisquer referências para os dados do volume original que foi clonado a partir do. 

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para clones transitórios e permanentes
As secções seguintes descrevem situações de exemplo em que podem ser utilizados os clones transitórios e permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação ao nível do item com um clone transitório
Precisa de recuperar um ficheiro de apresentação do Microsoft PowerPoint antigo de ano de um. O administrador de TI identifica a cópia de segurança específica desse período de tempo e, em seguida, filtra o volume. Em seguida, o administrador clones o volume, localiza o ficheiro que está a procurar e fornece-lhe. Neste cenário, é utilizado um clone transitório. 

![Vídeo disponível](./media/storsimple-clone-volume-u2/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como pode utilizar o clone e restaurar as funcionalidades do StorSimple para recuperar ficheiros eliminados, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testar no ambiente de produção com um clone permanente
Tem de validar um erro de teste no ambiente de produção. Crie um clone do volume no ambiente de produção e, em seguida, tirar um instantâneo nuvem este clone para criar um volume clonado independente. Neste cenário, é utilizado um clone permanente.  

## <a name="next-steps"></a>Passos seguintes
* Saiba como [restaurar um volume StorSimple a partir de um conjunto de cópia de segurança](storsimple-restore-from-backup-set-u2.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

