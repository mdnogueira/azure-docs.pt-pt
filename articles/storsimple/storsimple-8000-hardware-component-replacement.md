---
title: "Substituição de componente de hardware de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve como substituir em segurança o PCMs, bateria, módulos de controlador, EBOD controladores, unidades de disco e chassis de um dispositivo StorSimple."
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
ms.custom: 
ms.openlocfilehash: 6de50c5031db59176bdf17ecc69b934559220f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Substituir um componente de hardware no seu dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral
Os tutoriais de substituição de componente de hardware descrevem os componentes de hardware do dispositivo da série 8000 do StorSimple do Microsoft Azure e os passos necessários para remover e substitua-as. Este artigo descreve os ícones de segurança, disponibiliza apontadores para os tutoriais de detalhado e apresenta os componentes que estão substituível em.

> [!IMPORTANT]
> Antes de tentar remover ou substituir qualquer componente do StorSimple, certifique-se de que revê o [convenções de ícone de segurança](#safety-icon-conventions) e outros [precauções de segurança](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Convenções de ícone de segurança
A tabela seguinte descreve os ícones de segurança utilizados nestes tutoriais. Preste especial atenção para estes ícones de segurança à medida que revê os passos para remover e substitua os componentes do dispositivo.

| Ícone | Texto | Informações adicionais |
|:--- |:--- |:--- |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) |**RISCO!** |Indica uma situação hazardous que, se não evitadas, irá resultar no death ou injury grave. Esta palavra de sinal está limitada às situações mais extremos. |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) |**AVISO!** |Indica uma situação hazardous que, se não evitadas, pode resultar em death ou injury grave. |
| ![Ícone de atenção](./media/storsimple-hardware-component-replacement/Caution.png) |**ATENÇÃO!** |Indica uma situação hazardous que, se não evitadas, pode resultar em injury moderada ou secundária. |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**AVISO:** |Indica informações consideradas importantes, mas não relacionados com hazard. |
| ![Ícone de shock elétrica](./media/storsimple-hardware-component-replacement/Electric.png) |**Hazard Shock elétrica** |Indica tensão elevada. |
| ![Ícone de ponderação pesada](./media/storsimple-hardware-component-replacement/Weight.png) |**Peso pesado** | |
| ![Ícone de partes de uma nenhum utilizador](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Não existem partes de uma de utilizador** |Não acedam a menos que preparada corretamente. |
| ![Ícone de instruções de leitura](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Ler todas as instruções pela primeira vez** | |
| ![Hazard ícone de sugestão](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Sugestão Hazard** | |

### <a name="before-you-begin"></a>Antes de começar
Familiarize-se com as informações de segurança sobre os ícones de dispositivo e de segurança utilizados neste tutorial. Aceda a [possam instalar e operar o dispositivo StorSimple](storsimple-safety.md) para obter informações completas. Certifique-se rever o [precauções de segurança](storsimple-safety.md#handling-precautions) antes de processar o dispositivo StorSimple.

Antes de tentar substituir um componente, considere as seguintes informações.

![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) ![Shock elétrica ícone](./media/storsimple-hardware-component-replacement/Electric.png) **aviso!**

* Fundo sozinho corretamente utilizando um electrostatic discharge ou antistatic mat quando tratamento de módulos e componentes do dispositivo StorSimple.
* Não é touch qualquer circuitry. Utilize o identificadores fornecidos e guias durante o processamento de componentes que podem ter exposto circuitry.

![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) ![Repare ícone](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **aviso:**

Se substituir um módulo, **nunca saem um bay vazio no rear do bastidor**. Obter uma substituição ou módulo em branco antes de remover a parte do problema.

## <a name="hardware-component-replacement-procedures"></a>Procedimentos de substituição de componente de hardware
O dispositivo de série 8000 do StorSimple é composta por vários módulos de plug-in no site primário e/ou inclusões EBOD. O 8100 tem um bastidor primário único, enquanto o 8600 é um dispositivo de inclusão dupla com um bastidor primário e um bastidor EBOD.

Os componentes de hardware principal no seu dispositivo estão resumidos nas tabelas seguintes. Clique na ligação no **procedimento de substituição** coluna para ir para o tutorial associado.

| Componentes | # Presente | Módulo de plug-in? | Procedimento de substituição |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Não |[Substitua o chassis no dispositivo StorSimple](storsimple-8000-chassis-replacement.md) |
| Controladores primários |2 |Sim |[Substituir um módulo de controlador no dispositivo StorSimple](storsimple-8000-controller-replacement.md) |
| 764W energia e arrefecimento módulos (PCMs) |2 |Sim |[Substituir um energia e arrefecimento módulo no dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Cópia de segurança bateria |2 |Sim |[Substitua o módulo de bateria cópia de segurança no dispositivo StorSimple](storsimple-8000-battery-replacement.md) |
| Unidades de disco |12 |Sim |[Substituir uma unidade de disco no dispositivo StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 1** componentes de Hardware na inclusão principal

A inclusão principal e a inclusão EBOD diferem no respetivos módulos de e/s. Além disso, os PCMs tem wattage diferentes. PCMs no bastidor primário são 764 W, enquanto que os o bastidor EBOD são 580 W. PCMs na inclusão principal contém também um módulo de bateria cópia de segurança.

| Componentes | # Presente | Módulo de plug-in? | Procedimento de substituição |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Não |[Substitua o chassis no dispositivo StorSimple](storsimple-8000-chassis-replacement.md) |
| Controladores EBOD |2 |Sim |[Substituir um controlador EBOD no dispositivo StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| 580W energia e arrefecimento módulos (PCMs) |2 |Sim |[Substituir um energia e arrefecimento módulo no dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Unidades de disco |12 |Sim |[Substituir uma unidade de disco no dispositivo StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 2** componentes de Hardware na inclusão EBOD

Os módulos de plug-in do dispositivo são realçados na seguinte frente e diagramas rear. Pode utilizar estes diagramas para determinar a localização dos módulos de plug-in vários se uma substituição não é necessária. O front-diagrama mostra as unidades de disco e os diagramas rear a inclusão EBOD e mostrar a inclusão principal os módulos de plug-in.

![Frontplane do dispositivo com unidades de disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** Front do dispositivo

| Etiqueta | Descrição |
|:--- |:--- |
| 0 - 11 |Unidades de disco (total de 12) |

A inclusão principal e a inclusão EBOD têm módulos de carrier da unidade. O chassis aloja doze 3.5" unidades de disco dispostas num formato 3 a 4.

![Backplane dos módulos de inclusão principal do dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Back of a inclusão principal

| Etiqueta | Descrição |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controlador 0 |
| 4 |Controlador 1 |

![Backplane dos módulos de plug-in de inclusão do dispositivo EBOD](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** Back of a inclusão EBOD

| Etiqueta | Descrição |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD controlador 0 |
| 4 |EBOD controlador 1 |

## <a name="field-replaceable-units"></a>Unidades substituível em campo
As seguintes unidades de substituível em campo (FRUs) estão disponíveis para o dispositivo StorSimple:

* Chassis (incluindo o painel de operações integradas)
* RECOMENDA-W 764 PCM AC
* RECOMENDA-W 580 PCM AC
* Unidade de disco rígido com o módulo de carrier da unidade
* Módulo de controlador
* Módulo de controlador EBOD
* Módulo de cópia de segurança de bateria
* Ao instalar o kit de lado de bastidor

. [Contacte a Microsoft Support](storsimple-8000-contact-microsoft-support.md) ordenar qualquer um destas unidades de substituição.

## <a name="next-steps"></a>Passos seguintes
Reveja todos os [informações de segurança](storsimple-safety.md) antes de tentar a substituir um componente de hardware do StorSimple.

