---
title: Gerir os contentores de volume do StorSimple | Microsoft Docs
description: "Explica como pode utilizar a página de contentores de volume de serviço StorSimple Manager para adicionar, modificar ou eliminar um contentor de volume."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 1c64ce75-1fd3-4d3b-9304-d4dc0fc2b069
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/24/2016
ms.author: v-sharos
ms.openlocfilehash: bb55a7a4bff0fd4319de6f6ce958686ad8a4142b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>Utilizar o serviço StorSimple Manager para gerir contentores de volume do StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como utilizar o serviço StorSimple Manager para criar e gerir contentores de volume do StorSimple.

Um contentor de volume no dispositivo Microsoft Azure StorSimple contém um ou mais volumes que partilham a conta de armazenamento, a encriptação e definições de consumo de largura de banda. Um dispositivo pode ter vários contentores de volume para todos os seus volumes. 

Um contentor de volume tem os seguintes atributos:

* **Volumes** – o camadas ou afixado localmente volumes do StorSimple que estão incluídos no contentor de volume. Um contentor de volume pode conter até 256 volumes do StorSimple.
* **Encriptação** – uma chave de encriptação pode ser definida para cada contentor de volume. Esta chave é utilizada para encriptar os dados que são enviados a partir do dispositivo StorSimple para a nuvem. Uma chave do nível military AES 256 bits é utilizada com a chave de utilizador introduzido. Para proteger os dados, recomendamos que ativar sempre a encriptação de armazenamento de nuvem.
* **Conta de armazenamento** – a conta de armazenamento que está ligada ao seu fornecedor de serviços de armazenamento na nuvem. Todos os volumes que residem num contentor de volume partilham esta conta de armazenamento. Pode escolher uma conta de armazenamento a partir de uma lista existente ou crie uma nova conta ao criar o contentor de volume e, em seguida, especifique as credenciais de acesso para essa conta.
* **Largura de banda da nuvem** – a largura de banda consumida pelo dispositivo quando estão a ser enviados os dados do dispositivo para a nuvem. Pode impor um controlo de largura de banda, especificando um valor entre 1 e 1000 Mbps quando definir neste contentor. Se pretender que o dispositivo consumir largura de banda disponível todos os, defina este campo para ilimitada. Também pode criar e aplicar um modelo de largura de banda de alocar largura de banda com base numa agenda.

![Página de contentores de volume](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Este procedimentos seguintes explicam como utilizar o StorSimple **contentores de Volume** página a concluir as seguintes operações comuns:

* Adicionar um contentor de volume 
* Modificar um contentor de volume 
* Eliminar um contentor de volume 

## <a name="add-a-volume-container"></a>Adicionar um contentor de volume
Execute os seguintes passos para adicionar um contentor de volume.

[!INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificar um contentor de volume
Execute os seguintes passos para modificar um contentor de volume.

[!INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Eliminar um contentor de volume
Um contentor de volume possui volumes dentro do mesmo. Pode ser eliminado apenas se todos os volumes nele contidos são eliminados primeiro. Execute os seguintes passos para eliminar um contentor de volume.

[!INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [gerir volumes do StorSimple](storsimple-manage-volumes.md). 
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

