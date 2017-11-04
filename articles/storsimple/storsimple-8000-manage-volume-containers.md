---
title: "Gerir os contentores de volume do StorSimple no dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: "Explica como pode utilizar a página de contentores de volume de serviço do Gestor de dispositivos do StorSimple para adicionar, modificar ou eliminar um contentor de volume."
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
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 0f8e00d6d07224f56625482f339e612e68914be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para gerir contentores de volume do StorSimple

## <a name="overview"></a>Descrição geral
Este tutorial explica como utilizar o serviço do Gestor de dispositivos do StorSimple para criar e gerir contentores de volume do StorSimple.

Um contentor de volume no dispositivo Microsoft Azure StorSimple contém um ou mais volumes que partilham a conta de armazenamento, a encriptação e definições de consumo de largura de banda. Um dispositivo pode ter vários contentores de volume para todos os seus volumes. 

Um contentor de volume tem os seguintes atributos:

* **Volumes** – o camadas ou afixado localmente volumes do StorSimple que estão incluídos no contentor de volume. 
* **Encriptação** – uma chave de encriptação pode ser definida para cada contentor de volume. Esta chave é utilizada para encriptar os dados que são enviados a partir do dispositivo StorSimple para a nuvem. Uma chave do nível military AES 256 bits é utilizada com a chave de utilizador introduzido. Para proteger os dados, recomendamos que ativar sempre a encriptação de armazenamento de nuvem.
* **Conta de armazenamento** – conta do storage do Azure que é utilizada para armazenar os dados. Todos os volumes que residem num contentor de volume partilham esta conta de armazenamento. Pode escolher uma conta de armazenamento a partir de uma lista existente ou crie uma nova conta ao criar o contentor de volume e, em seguida, especifique as credenciais de acesso para essa conta.
* **Largura de banda da nuvem** – a largura de banda consumida pelo dispositivo quando estão a ser enviados os dados do dispositivo para a nuvem. Pode impor um controlo de largura de banda, especificando um valor entre 1 e 1000 Mbps quando cria este contentor. Se pretender que o dispositivo consumir largura de banda disponível, defina este campo para **ilimitada**. Também pode criar e aplicar um modelo de largura de banda de alocar largura de banda com base numa agenda.

Os procedimentos seguintes explicam como utilizar o StorSimple **contentores de Volume** painel para concluir as seguintes operações comuns:

* Adicionar um contentor de volume
* Modificar um contentor de volume
* Eliminar um contentor de volume

## <a name="add-a-volume-container"></a>Adicionar um contentor de volume
Execute os seguintes passos para adicionar um contentor de volume.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificar um contentor de volume
Execute os seguintes passos para modificar um contentor de volume.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Eliminar um contentor de volume
Um contentor de volume possui volumes dentro do mesmo. Pode ser eliminado apenas se todos os volumes nele contidos são eliminados primeiro. Execute os seguintes passos para eliminar um contentor de volume.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [gerir volumes do StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Saiba mais sobre [utilizando o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

