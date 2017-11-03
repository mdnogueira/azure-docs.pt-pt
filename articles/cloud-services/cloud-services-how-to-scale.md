---
title: "Auto dimensionar um serviço em nuvem no portal clássico | Microsoft Docs"
description: "(clássica) Saiba como utilizar o portal clássico para configurar regras de dimensionamento automático para uma função de web do serviço de nuvem ou a função de trabalho no Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 90d55bbac6e113d6add848ace67cf0749e26342b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>Como configurar automática dimensionamento para um serviço em nuvem no portal clássico
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-scale-portal.md)
> * [Portal Clássico do Azure](cloud-services-how-to-scale.md)

Na página de escala do portal clássico do Azure, pode configurar as definições de dimensionamento automático para a função da web ou função de trabalho. Em alternativa, pode configurar o dimensionamento manual em vez de com base em regras de dimensionamento automático.

> [!NOTE]
> Este artigo foca-se as funções web e de trabalho do serviço em nuvem. Quando cria uma máquina virtual (clássica) diretamente, está alojada num serviço em nuvem. Algumas destas informações aplicam-se para estes tipos de máquinas virtuais. Dimensionamento de um conjunto de disponibilidade de máquinas virtuais está apenas a encerrá-los e desativar a com base nas regras de escala que configura. Para obter mais informações sobre as máquinas virtuais e conjuntos de disponibilidade, consulte [gerir a disponibilidade das Virtual Machines](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Deve considerar as seguintes informações antes de configurar o dimensionamento para a sua aplicação:

* Dimensionamento é afetado pela utilização do núcleo.

    Instâncias de função maior utilizem mais núcleos. Pode dimensionar uma aplicação apenas dentro do limite de núcleos para a sua subscrição. Por exemplo, diga a que sua subscrição tem um limite de 20 núcleos. Se executar uma aplicação com dois serviços de cloud média (um total de 4 núcleos), pode apenas Dimensionar outras implementações de serviço em nuvem na sua subscrição, os restantes 16 núcleos. Para obter mais informações acerca dos tamanhos, consulte [tamanhos do serviço em nuvem](cloud-services-sizes-specs.md).

* Tem de criar uma fila e associá-la a uma função antes de a poder dimensionar a uma aplicação com base num limiar da mensagem. Para obter mais informações, consulte [como utilizar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Pode dimensionar recursos que estão ligados ao seu serviço de nuvem. Para obter mais informações sobre ligar os recursos, consulte [como: ligar um recurso a um serviço em nuvem](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

* Para ativar a elevada disponibilidade da aplicação, deve certificar-se de que é implementado com dois ou mais instâncias de função. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="schedule-scaling"></a>Dimensionamento de agenda
Por predefinição, todas as funções não siga uma agenda específica. Por conseguinte, as definições alteradas aplicáveis a todas as horas e todos os dias ao longo do ano. Se quiser, pode configurar o dimensionamento de manual ou automática para um dos seguintes modos de:

* Dias da semana
* Fim de semana
* Nights semana
* Mornings semana
* Datas específicas
* Intervalos de data específica

A definição de agenda está configurada no [portal clássico do Azure](https://manage.windowsazure.com/) no  
**Serviços em nuvem** > **\[seu serviço em nuvem\]** > **escala**  >   **\[Produção ou transição\]**  página.

Clique em de **configurar vezes agenda** botão para cada função que pretende alterar.

![Dimensionamento automático com base numa agenda o serviço em nuvem][scale_schedules]

## <a name="manual-scale"></a>Escala Manual
No **escala** página, pode manualmente aumentar ou reduzir o número de instâncias em execução num serviço em nuvem. Esta definição estiver configurada para cada agenda que criou ou para todos os tempo se não tiver criado uma agenda.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **serviços em nuvem**e, em seguida, clique no nome do serviço de nuvem para abrir o dashboard.
   
   > [!TIP]
   > Se não vir o seu serviço em nuvem, poderá ter de alterar do **produção** para **transição** ou vice-versa.

2. Clique em **escala**.
3. Selecione o agendamento para alterar as opções de dimensionamento para. *Não períodos agendados* é a predefinição se tiver não agendamentos definidos.
4. Localizar o **escala da métrica** secção e selecione **NONE**. Esta definição é a predefinição para todas as funções.
5. Cada função no serviço de nuvem tem um controlo de deslize para alterar o número de instâncias para utilizar.
   
    ![Dimensionar manualmente uma função de serviço em nuvem][manual_scale]
   
    Se precisar de mais instâncias, poderá ter de alterar o [tamanho da máquina virtual de serviço de nuvem](cloud-services-sizes-specs.md).
6. Clique em **Guardar**.  
   Instâncias de função são adicionadas ou removidas consoante as suas seleções.

> [!TIP]
> Sempre que consulte ![][tip_icon] mover o rato ao mesmo e pode obter ajuda sobre o que uma definição específica faz.

## <a name="automatic-scale---cpu"></a>Escala automática - CPU
Este modo dimensiona se a percentagem média de utilização da CPU ficar acima ou abaixo limiares especificados. Instâncias de função são criadas ou eliminadas quando isto acontece.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **serviços em nuvem**e, em seguida, clique no nome do serviço de nuvem para abrir o dashboard.
   
   > [!TIP]
   > Se não vir o seu serviço em nuvem, poderá ter de alterar do **produção** para **transição** ou vice-versa.

2. Clique em **escala**.
3. Selecione o agendamento para alterar as opções de dimensionamento para. *Não períodos agendados* é a predefinição se tiver não agendamentos definidos.
4. Localizar o **escala da métrica** secção e selecione **CPU**.
5. Agora pode configurar um intervalo mínimo e máximo de instâncias de funções, o destino da utilização da CPU (para acionar uma escala cópias de segurança) e quantas instâncias para aumentar vertical ou por.

![Dimensionar uma função de serviço de nuvem por carga de cpu][cpu_scale]

> [!TIP]
> Sempre que consulte ![][tip_icon] mover o rato ao mesmo e pode obter ajuda sobre o que uma definição específica faz.

## <a name="automatic-scale---queue"></a>Escala automática - a fila
Este modo dimensiona automaticamente se o número de mensagens numa fila ficar acima ou abaixo de um limiar especificado. Instâncias de função são criadas ou eliminadas quando isto acontece.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **serviços em nuvem**e, em seguida, clique no nome do serviço de nuvem para abrir o dashboard.
   
   > [!TIP]
   > Se não vir o seu serviço em nuvem, poderá ter de alterar do **produção** para **transição** ou vice-versa.

2. Clique em **escala**.
3. Localizar o **escala da métrica** secção e selecione **fila**.
4. Agora pode configurar um intervalo mínimo e máximo de instâncias de funções, a fila e o número de mensagens de fila para o processo para cada instância e quantas instâncias para aumentar vertical ou pelo.

![Dimensionar uma função de serviço de nuvem por uma fila de mensagens][queue_scale]

> [!TIP]
> Sempre que consulte ![][tip_icon] mover o rato ao mesmo e pode obter ajuda sobre o que uma definição específica faz.

## <a name="scale-linked-resources"></a>Dimensionar recursos ligados
Frequentemente, quando dimensionar uma função, é benéfico dimensionar a base de dados que a aplicação está a utilizar também. Se ligar a base de dados para o serviço em nuvem, pode aceder as definições de dimensionamento para o recurso de clicando na hiperligação adequada.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **serviços em nuvem**e, em seguida, clique no nome do serviço de nuvem para abrir o dashboard.
   
   > [!TIP]
   > Se não vir o seu serviço em nuvem, poderá ter de alterar do **produção** para **transição** ou vice-versa.

2. Clique em **escala**.
3. Localizar o **ligado recursos** secção e clique em **gerir escala desta base de dados**.
   
   > [!NOTE]
   > Se não vir um **ligado recursos** secção, provavelmente, não tem quaisquer recursos ligados.

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
