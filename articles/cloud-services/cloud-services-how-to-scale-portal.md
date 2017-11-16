---
title: "Auto dimensionar um serviço em nuvem no portal | Microsoft Docs"
description: "Saiba como utilizar o portal para configurar regras de dimensionamento automático para uma função de web do serviço de nuvem ou a função de trabalho no Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 708b35252429e30b44226030d3d83b928f2a9520
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Como configurar automática dimensionamento para um serviço em nuvem no portal

Condições podem ser definidas para uma função de trabalho do serviço de nuvem que acionam uma escala de entrada ou saída da operação. Podem basear-se as condições para a função na CPU, disco ou de carga na rede da função. Também pode definir uma condição com base na fila de mensagens ou a métrica de alguns recursos do Azure associado à sua subscrição.

> [!NOTE]
> Este artigo foca-se as funções web e de trabalho do serviço em nuvem. Quando cria uma máquina virtual (clássica) diretamente, está alojada num serviço em nuvem. Pode dimensionar uma máquina virtual standard associando-o com um [conjunto de disponibilidade](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) e ativá-los manualmente ou desativar.

## <a name="considerations"></a>Considerações
Deve considerar as seguintes informações antes de configurar o dimensionamento para a sua aplicação:

* Dimensionamento é afetado pela utilização do núcleo.

    Instâncias de função maior utilizem mais núcleos. Pode dimensionar uma aplicação apenas dentro do limite de núcleos para a sua subscrição. Por exemplo, diga a que sua subscrição tem um limite de 20 núcleos. Se executar uma aplicação com dois serviços de cloud média (um total de 4 núcleos), pode apenas Dimensionar outras implementações de serviço em nuvem na sua subscrição, os restantes 16 núcleos. Para obter mais informações acerca dos tamanhos, consulte [tamanhos do serviço em nuvem](cloud-services-sizes-specs.md).

* Pode dimensionar com base num limiar de mensagem de fila. Para mais informações sobre como utilizar as filas, consulte [como utilizar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Também pode escalar a outros recursos associados à subscrição.

* Para ativar a elevada disponibilidade da aplicação, deve certificar-se de que é implementado com dois ou mais instâncias de função. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).


## <a name="where-scale-is-located"></a>Onde está localizada a escala
Depois de selecionar o seu serviço em nuvem, deve ter o painel do serviço de nuvem visível.

1. No painel de serviço em nuvem, no **funções e instâncias** mosaico, selecione o nome do serviço de nuvem.   
   **IMPORTANTE**: certificar-se de que clique na função de serviço de nuvem, não a instância de função que é inferior a função.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Selecione o **escala** mosaico.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Escala automática
Pode configurar as definições de dimensionamento para uma função com qualquer um dos dois modos **manual** ou **automática**. Manual como seria de esperar, defina o número absoluto de instâncias. No entanto, automático permite que um conjunto de regras que regem como e por como muito deve dimensionar.

Definir o **Dimensionar por** opção para **regras de desempenho e a agenda**.

![Definições de dimensionamento de serviços em nuvem com o perfil e a regra](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Um perfil existente.
2. Adicione uma regra para o perfil de principal.
3. Adicione outro perfil.

Selecione **adicionar perfil**. O perfil determina o modo de que pretende utilizar para a escala: **sempre**, **periodicidade**, **fixo data**.

Depois de ter configurado o perfil e as regras, selecione o **guardar** na parte superior.

#### <a name="profile"></a>Perfil
O perfil define instâncias mínimas e máximos para a escala, e também quando está ativo neste intervalo de dimensionamento.

* **Sempre**

    Manter sempre este intervalo de instâncias disponíveis.  

    ![Serviço em nuvem que sempre Dimensionar](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Periodicidade**

    Escolha um conjunto de dias da semana de dimensionar.

    ![Escala de serviço da nuvem com uma agenda de periodicidade](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Data fixa**

    Um intervalo de datas fixas ao dimensionar a função.

    ![Escala de serviço da nuvem com uma data fixa](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Depois de ter configurado o perfil, selecione o **OK** na parte inferior do painel de perfil.

#### <a name="rule"></a>Regra
As regras são adicionadas a um perfil e representam uma condição que aciona a escala.

O acionador de regra baseiam-se uma métrica do serviço de nuvem (utilização da CPU, atividade do disco ou atividade de rede) ao qual pode adicionar um valor condicional. Além disso pode ter o acionador com base na fila de mensagens ou a métrica de alguns recursos do Azure associado à sua subscrição.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Depois de configurar a regra, selecione o **OK** na parte inferior do painel de regra.

## <a name="back-to-manual-scale"></a>Voltar à escala manual
Navegue para o [definições de dimensionamento](#where-scale-is-located) e defina o **Dimensionar por** opção para **uma contagem de instâncias que introduzo manualmente**.

![Definições de dimensionamento de serviços em nuvem com o perfil e a regra](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Esta definição remove o dimensionamento automático da função e, em seguida, pode definir a contagem de instâncias diretamente.

1. A opção de (manual ou automática) de escala.
2. Um função instância controlo de deslize para definir as instâncias de dimensionar.
3. Instâncias da função de dimensionar.

Depois de ter configurado as definições de dimensionamento, selecione o **guardar** na parte superior.
