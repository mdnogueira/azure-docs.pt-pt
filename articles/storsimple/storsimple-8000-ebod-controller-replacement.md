---
title: Substituir um controlador de StorSimple 8600 EBOD | Microsoft Docs
description: Explica como remover e substitua um ou ambos os controladores EBOD num dispositivo StorSimple 8600.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 45699c267d1009c4884dd164fd3f2950d6d5f555
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Substituir um controlador EBOD no dispositivo StorSimple

## <a name="overview"></a>Descrição geral
Este tutorial explica como substituir um módulo de controlador EBOD defeituoso no seu dispositivo do Microsoft Azure StorSimple. Para substituir um módulo de controlador EBOD, tem de:

* Remover o controlador EBOD defeituoso
* Instalar um novo controlador EBOD

Antes de começar, considere as seguintes informações:

* Módulos EBOD em branco tem de ser inseridos em todas as ranhuras de não utilizadas. A inclusão não será esporádico corretamente se uma ranhura é deixada aberta.
* O controlador EBOD é frequente-swappable e pode ser removido ou substituído. Não remova um módulo falhado até ter uma substituição. Quando inicia o processo de substituição, deve terminar dentro de 10 minutos.

> [!IMPORTANT]
> Antes de tentar remover ou substituir qualquer componente do StorSimple, certifique-se de que revê o [convenções de ícone de segurança](storsimple-safety.md#safety-icon-conventions) e outros [precauções de segurança](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Remover um controlador EBOD
Antes de substituir o módulo de controlador EBOD falhou no dispositivo StorSimple, certifique-se de que o módulo de controlador outros EBOD está ativa e em execução. O procedimento e tabela seguintes explicam como remover o módulo de controlador EBOD.

#### <a name="to-remove-an-ebod-module"></a>Para remover um módulo EBOD
1. Abra o portal do Azure.
2. Aceda ao seu dispositivo e navegue para **definições** > **estado de funcionamento do Hardware**e certifique-se de que o estado de LED para o módulo de controlador do Active Directory EBOD for verde, não sendo LED para o módulo de controlador EBOD falhado vermelho.
3. Localize o módulo de controlador EBOD falhado at the back of o dispositivo.
4. Remova os cabos de ligar o módulo de controlador EBOD ao controlador de antes de tirar o módulo EBOD fora do sistema.
5. Tome nota da porta SAS exata do módulo de controlador EBOD que foi ligado ao controlador. Será necessário para restaurar o sistema para esta configuração depois de as substituir o módulo EBOD.
   
   > [!NOTE]
   > Normalmente, esta será a porta A, que é denominada como **alojar na** no diagrama seguinte.
   
    ![Controlador Backplane de EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Figura 1** módulo do Back EBOD
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Falhas LED |
   | 2 |Energia LED |
   | 3 |Conectores SAS |
   | 4 |SAS LEDs |
   | 5 |Portas série fábrica apenas para utilização |
   | 6 |Porta (anfitrião do) |
   | 7 |Porta B (anfitrião de saída) |
   | 8 |Porta C (apenas para utilização de fábrica) |

## <a name="install-a-new-ebod-controller"></a>Instalar um novo controlador EBOD
O procedimento e tabela seguintes explicam como instalar um módulo de controlador EBOD no dispositivo StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Para instalar um controlador EBOD
1. Verifique o dispositivo EBOD para danos, especialmente para o conector de interface. Não instale o novo controlador EBOD se qualquer pins são bent.
2. Com os bloqueios simultâneos na posição aberta, deslize o módulo a inclusão até os bloqueios simultâneos envolvimento.
   
    ![Instalar o controlador de EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Figura 2** instalar o módulo de controlador EBOD
3. Feche o bloqueio temporário. Deve ouvir um clique, envolva o bloqueio temporário.
   
    ![Ao libertar o bloqueio temporário EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Figura 3** fechar o bloqueio de temporário do módulo EBOD
4. Voltar a ligar os cabos. Utilize a configuração exata que estava presente antes da substituição. Consulte o seguinte diagrama e tabela para obter detalhes sobre como ligar os cabos.
   
    ![Instalar os cabos do dispositivo 4U de energia](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Figura 4**. Cabos de restabelecer a ligação
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Inclusão principal |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Controlador 0 |
   | 5 |Controlador 1 |
   | 6 |Controlador EBOD 0 |
   | 7 |Controlador EBOD 1 |
   | 8 |Inclusão EBOD |
   | 9 |Unidades de distribuição de energia |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

