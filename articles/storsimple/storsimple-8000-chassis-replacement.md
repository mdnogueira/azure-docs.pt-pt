---
title: "Substitua o chassis no dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve como remover e substitua o chassis para o seu inclusão principal do StorSimple ou a inclusão EBOD."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 073fcf0064f1d1482f4683d733f00cf918ff2f38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Substitua o chassis no dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como remover e substituir um chassis de um dispositivo de série 8000 do StorSimple. O modelo do StorSimple 8100 é um dispositivo de inclusão único (chassis), enquanto o 8600 é um dispositivo de inclusão dupla (dois chassis). Para um modelo de 8600, existem dois chassis potencialmente poderá falhar no dispositivo: chassis para a inclusão principal ou o chassis para o bastidor EBOD.

Em ambos os casos, o chassis de substituição que é enviado pela Microsoft está vazio. Sem energia e arrefecimento módulos do (PCMs), módulos de controlador, unidades de disco de estado sólido (SSDs), unidades de disco rígido (HDDs) ou EBOD módulos será incluídos.

> [!IMPORTANT]
> Antes de remover e de substituir o chassis, reveja as informações de segurança no [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Remova o chassis
Execute os seguintes passos para remover o chassis no dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Para remover um chassis
1. Certifique-se de que o dispositivo StorSimple é encerrado e desligado da todas as origens de energia.
2. Remova a rede e cabos SAS, se aplicável.
3. Remova a unidade de bastidor.
4. Remova cada unidades e tenha em atenção os blocos de que são removidos. Para obter mais informações, consulte [remover a unidade de disco](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. Na caixa EBOD (se este for o chassis que falharam), remova os módulos de controlador EBOD. Para obter mais informações, consulte [remover um controlador EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    Na caixa primária (se este for o chassis que falharam), remova os controladores e tenha em atenção os blocos de que são removidos. Para obter mais informações, consulte [remover um controlador](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instalar o chassis
Execute os seguintes passos para instalar o chassis no dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Para instalar um chassis
1. Monte o chassis no bastidor. Para obter mais informações, consulte [montar em Bastidor os cabos do 8100 StorSimple dispositivo](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [montar em Bastidor os cabos do 8600 StorSimple dispositivo](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Depois do chassis está montado num bastidor, instale os módulos de controlador as posições mesmas que tenham sido anteriormente instalados no.
3. Instale as unidades nas posições e ranhuras que tenham sido anteriormente instalados no mesmo.
   
   > [!NOTE]
   > Recomendamos que instale SSD nas ranhuras primeiro e, em seguida, instale os HDDs.
  
4. Com o dispositivo montado em bastidor e os componentes instalados, ligar o seu dispositivo para as fontes de alimentação apropriado e ative o dispositivo. Para obter mais informações, consulte [instalar os cabos do dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [instalar os cabos do dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

