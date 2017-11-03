---
title: "Substituir uma unidade de disco num dispositivo série 8000 do StorSimple | Microsoft Docs"
description: Explica como substituir uma unidade de disco num bastidor de principal do StorSimple ou um bastidor EBOD.
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
ms.date: 073/2017
ms.author: alkohli
ms.openlocfilehash: a8616eb51b177a9447a7c466c9d934b9139afedf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Substituir uma unidade de disco no seu dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral
Este tutorial explica como pode remover e substitua malfunctioning ou falha na unidade de disco rígido num dispositivo Microsoft Azure StorSimple. Para substituir uma unidade de disco, tem de:

* Disengage o bloqueio antitamper
* Remova a unidade de disco
* Instalar a unidade de disco de substituição

> [!IMPORTANT]
> Antes de remover e de substituir uma unidade de disco, reveja as informações de segurança no [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Disengage o bloqueio antitamper
Este procedimento explica como os bloqueios antitamper no dispositivo StorSimple podem ser parte ou disengaged quando substituir as unidades de disco. Os bloqueios antitamper são fitted na unidade operadora identificadores e estes são acedidos através de um pequeno aperture na secção do identificador de bloqueio temporário. Unidades são fornecidas com os bloqueios definidos para a posição de bloqueado.

#### <a name="to-unlock-the-antitamper-lock"></a>Para desbloquear o bloqueio antitamper
1. Inserir cuidadosamente a chave de bloqueio (um screwdriver "tamperproof" T10 que fornecidos Microsoft) para o aperture no identificador de e para o socket. 
   
   Se o bloqueio antitamper estiver ativado, o indicador vermelho é visível no aperture.
  
    ![Unidade de disco bloqueada](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Figura 1** bloqueio adulterar anti parte
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Aperture indicador |
   | 2 |Bloqueio antitamper |
2. Roda para a chave numa direção anticlockwise até o indicador vermelho não estiver visível na aperture acima a chave.
3. Remova a chave.
   
    ![Unidade de disco desbloqueada](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Figura 2** Unlocked unidade de disco
4. A unidade de disco pode ser removida.

Siga os passos no inversa para interagir com o bloqueio.

## <a name="remove-the-disk-drive"></a>Remova a unidade de disco
O dispositivo StorSimple suporta uma configuração de espaços de armazenamento como o 10 de RAID. Isto implica que pode funcionar normalmente com um disco com falhas, unidade de estado sólido (SSD), ou unidade de disco rígido (HDD).

> [!IMPORTANT]
> * Se o sistema tem mais do que um disco com falhas, não remova mais do que um SSD ou HDD do sistema em qualquer ponto no tempo. Se o fizer, pode resultar na perda de dados.
> * Certifique-se de que coloque um substituto SSD numa ranhura que continha anteriormente um SSD. Da mesma forma, coloque uma substituição HDD numa ranhura que continha anteriormente um HDD.
> * No portal do Azure, ranhuras são numeradas da 0 – 11. Por conseguinte, se o portal mostra que um disco na ranhura 2 falhou, no dispositivo, procure o disco com falhas na ranhura de terceiro da parte superior esquerda.
> 
> 

Unidades podem ser removidas e substituídas enquanto o sistema está a funcionar.

#### <a name="to-remove-a-drive"></a>Para remover uma unidade
1. Para identificar o disco com falhas, no portal do Azure, aceda ao seu dispositivo **definições > Estado de funcionamento do Hardware**. Porque um disco pode falhar no bastidor primário e/ou um bastidor EBOD (se estiver a utilizar um modelo de 8600), ver o estado dos discos em **partilhado componentes** e, em **EBOD partilhado componentes**. Um disco com falhas em qualquer um dos bastidor será apresentado com um estado vermelho.
2. Localize as unidades no início a inclusão principal ou a inclusão EBOD. 
3. Se o disco está desbloqueado, avance para o passo seguinte. Se o disco estiver bloqueado, desbloqueá-la seguindo o procedimento [Disengage o bloqueio antitamper](#disengage-the-antitamper-lock).
4. Prima o bloqueio temporário preto no módulo de carrier unidade e solicitar o identificador de carrier da unidade terminar e ausente de início do chassis.
   
    ![Libertação do identificador de unidade de disco](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Figura 3** lançar o identificador de unidade
5. Quando o identificador de carrier da unidade totalmente é expandido, deslize a operadora de unidade fora de chassis. 
   
    ![Disco deslizante fora de unidade de disco](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Figura 4** a deslizante a unidade de disco fora operadora

## <a name="install-the-replacement-disk-drive"></a>Instalar a unidade de disco de substituição
Depois de uma unidade falhou no dispositivo StorSimple e ter removido-lo, siga este procedimento para substituí-lo com uma unidade de novo.

#### <a name="to-insert-a-drive"></a>Para inserir uma unidade
1. Certifique-se que o identificador de carrier da unidade totalmente é expandido, conforme mostrado na imagem seguinte.
   
    ![Unidade de disco com o identificador expandido](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Figura 5** unidade com o identificador expandido
2. Deslize operadora unidade até para o chassis.
   
    ![Deslizante disco na unidade de disco operadora](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Figura 6** a deslizante operadora a unidade para o chassis
3. Com a operadora de unidade inserida, feche o identificador de carrier da unidade ao continuar a enviar a operadora de unidade para o chassis, até que ajusta o identificador de carrier da unidade para uma posição bloqueada.
4. Utilize a chave de bloqueio que foi fornecida pela Microsoft (tamperproof Torx screwdriver) para proteger o identificador de carrier no local ao ativar o screw bloqueio ative de um trimestre no sentido horário.
5. Certifique-se de que a substituição foi concluída com êxito e a unidade está operacional. Aceder ao portal do Azure e navegue para **definições do dispositivo** > **estado de funcionamento do Hardware**. Em **partilhado componentes** ou **EBOD partilhado componentes**, o estado da unidade deve estar verde, indicando que está em bom estado.

   
   > [!NOTE]
   > Pode demorar várias horas para que o estado do disco ativar a verde após a substituição.
  
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

