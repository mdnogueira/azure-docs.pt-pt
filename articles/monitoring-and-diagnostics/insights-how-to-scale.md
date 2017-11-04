---
title: "Dimensionar a contagem de instâncias manual ou com dimensionamento automático com o Portal do Azure | Microsoft Docs"
description: "Saiba como dimensionar os serviços do Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: ancav
ms.openlocfilehash: d171538ea57839eccddcc74ca099a39aee34ea10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-instance-count-manually-or-automatically"></a>Dimensionar a contagem de instâncias manual ou automaticamente
No [Portal do Azure](https://portal.azure.com/), pode configurar manualmente a contagem de instâncias do seu serviço, ou, pode definir parâmetros para o automaticamente com base no pedido de dimensionamento. Isto é normalmente denominado *aumentar horizontalmente* ou *dimensionar de*.

Antes de dimensionamento com base na contagem de instâncias, deve considerar que o dimensionamento é afetado por **escalão de preço** para além de contagem de instâncias. Diferentes escalões de preço podem ter memória e números diferentes de núcleos e, por isso, terá um melhor desempenho para o mesmo número de instâncias (que é *aumentar verticalmente* ou *reduzir verticalmente*). Este artigo aborda especificamente *dimensionar de* e *saída*.

Pode aumentar no portal, e também pode utilizar o [REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx) ou [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) para ajustar a escala de forma manual ou automaticamente.

> [!NOTE]
> Este artigo descreve como criar uma definição de dimensionamento automático no portal em [http://portal.azure.com](http://portal.azure.com). As definições de dimensionamento automático criadas neste portal não não possível editá-lo de o portal clássico ([http://manage.windowsazure.com](http://manage.windowsazure.com)).
> 
> 

## <a name="scaling-manually"></a>Dimensionar Manualmente
1. No [Portal do Azure](https://portal.azure.com/), clique em **procurar**, em seguida, navegue para o recurso que pretende dimensionar, tal como um **plano do App Service**.
2. Clique em **definições > aumentar horizontalmente (plano do App Service).**
3. Na parte superior do **escala** painel pode ver um histórico das ações de dimensionamento automático do serviço.
   
    ![Painel de escala](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
   
   > [!NOTE]
   > Apenas as ações efetuadas pelo dimensionamento automático irão mostrar neste gráfico. Se ajustar a contagem de instâncias manualmente, a alteração não será refletida neste gráfico.
   > 
   > 
4. Manualmente, pode ajustar o número **instâncias** com controlo de deslize.
5. Clique em de **guardar** comandos e irá ser ampliada para esse número de instâncias de quase imediatamente.

## <a name="scaling-based-on-a-pre-set-metric"></a>Dimensionamento com base numa métrica de pré-configurada
Se pretender que o número de instâncias para ajustar automaticamente com base numa métrica, selecione a métrica que pretende incluir o **Dimensionar por** pendente. Por exemplo, para um **plano do App Service** pode dimensionar por **percentagem de CPU**.

1. Quando seleciona uma métrica irá obter um controlo de deslize, e/ou, as caixas de texto para introduzir o número de instâncias que pretende dimensionar entre:
   
    ![Painel de escala com percentagem de CPU](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
   
    Dimensionamento automático nunca irá demorar o seu serviço abaixo ou acima os limites que definir, independentemente da sua carga.
2. Segundo, escolha o intervalo de destino para a métrica. Por exemplo, se tiver escolhido **percentagem de CPU**, pode definir um destino para a CPU média em todas as instâncias no seu serviço. Uma ampliação irá acontecer quando a CPU média excede o máximo que define, da mesma forma, uma escala em acontecerá sempre que a média da CPU descerem abaixo do mínimo.
3. Clique em de **guardar** comando. Dimensionamento automático irá verificar cada alguns minutos para se certificar de que estão no intervalo de instância e o destino para a métrica. Quando o seu serviço recebe tráfego adicional, irá receber mais instâncias sem fazer nada.

## <a name="scale-based-on-other-metrics"></a>Com base nas outras métricas de escala
Pode dimensionar com base nas métricas que não sejam de predefinições que aparecem no **Dimensionar por** lista pendente e pode ainda tem um conjunto complexo de escalamento horizontal e dimensionar de regras.

### <a name="adding-or-changing-a-rule"></a>Adicionar ou alterar uma regra
1. Escolha o **regras de desempenho e a agenda** no **Dimensionar por** pendente: ![as regras de desempenho](./media/insights-how-to-scale/Insights_PerformanceRules.png)
2. Se tinha anteriormente o dimensionamento automático, no, verá uma vista das regras exatas que tiver.
3. Com base no outro clique métrica de dimensionamento de **Adicionar regra** linha. Também pode clicar das linhas existentes para alterar a métrica que tinha anteriormente para a métrica que pretende dimensionar por.
   ![Adicionar regra](./media/insights-how-to-scale/Insights_AddRule.png)
4. Agora tem de selecionar qual métrica que pretende dimensionar por. Ao escolher uma métrica existem alguns aspetos a considerar:
   
   * O *recursos* vêm a métrica. Normalmente, este será o mesmo que o recurso que são dimensionamento. No entanto, se pretender dimensionar pela profundidade de fila de armazenamento, o recurso é a fila que pretende dimensionar por.
   * O *o nome da métrica* próprio.
   * O *tempo agregação* da métrica. Esta é a forma como os dados combinação através de *duração*.
5. Depois de escolher a métrica que escolher o limiar para a métrica e o operador. Por exemplo, pode dizer **maior** **80%**.
6. Em seguida, escolha a ação que pretende efetuar. Existem alguns tipo diferente de ações:
   
   * Aumentar ou diminuir por - Isto irá adicionar ou remover o **valor** número de instâncias, definir
   * Aumentar ou diminuir percent - Isto vai alterar a contagem de instâncias, uma percentagem. Por exemplo, pode colocar 25 **valor** campo, e se tiver atualmente 8 instâncias, 2 seria adicionado.
   * Aumentar ou diminuir a – isto define a contagem de instâncias para a **valor** define.
7. Por fim, pode escolher útil para baixo - quanto esta regra deverá aguardar após a ação de dimensionamento anterior de dimensionamento novamente.
8. Depois de configurar a regra de acessos **OK**.
9. Assim que tiver configurado a todas as regras que pretende, lembre-se de que atingiu o **guardar** comando.

### <a name="scaling-with-multiple-steps"></a>Dimensionamento com vários passos
Os exemplos acima são pretty básicos. No entanto, se pretender ser mais agressiva sobre dimensionamento (ou reduzir verticalmente), até adicionar várias regras de dimensionamento para a métrica do mesma. Por exemplo, pode definir duas regras de dimensionamento em percentagem de CPU:

1. Aumentar horizontalmente por 1 instância se a percentagem de CPU for superior a 60%
2. Aumentar horizontalmente por 3 instâncias se a percentagem de CPU está acima 85%

![Várias regras de dimensionamento](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Com esta regra adicional, se a carga excede 85% antes de uma ação de dimensionamento, obterá duas instâncias adicionais em vez de um.

## <a name="scale-based-on-a-schedule"></a>Escala com base numa agenda
Por predefinição, quando criar uma regra de escala, este irá sempre aplicam-se. Pode ver que, ao clicar no cabeçalho do perfil:

![Perfil](./media/insights-how-to-scale/Insights_Profile.png)

No entanto, poderá ter mais agressiva dimensionamento durante o dia ou a semana, que no fim de semana. Foi mesmo encerrar o serviço completamente fora do horário de trabalho.

1. Para tal, no perfil tiver, selecione **periodicidade** em vez de **sempre,** e escolha o número de vezes que pretende que o perfil a aplicar.
2. Por exemplo, para ter um perfil que aplica-se durante a semana, no **dias** pendente desmarque **Sábado** e **Domingo**.
3. Para ter um perfil que aplica-se durante a daytime, defina o **hora de início** para a hora do dia que pretende iniciar.
   
    ![Periodicidade predefinido](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)
4. Clique em **OK**.
5. Em seguida, terá de adicionar o perfil que pretende aplicar em outros momentos. Clique em de **adicionar perfil** linha.
    ![Trabalho](./media/insights-how-to-scale/Insights_ProfileOffWork.png)
6. Nome do novo, segundo, o perfil, por exemplo, pode chamar **desativado trabalho**.
7. Em seguida, selecione **periodicidade** novamente e escolha o intervalo de contagem de instância que pretende durante este período.
8. Como com o perfil predefinido, escolha o **dias** pretende que este perfil seja aplicado e o **hora de início** durante o dia.
   
   > [!NOTE]
   > Dimensionamento automático irá utilizar as regras de poupança de Verão para que **fuso horário** que selecionar. No entanto, durante a hora de Verão o Desvio UTC irá mostrar o desvio de fuso horário base, não o deslocamento de reduções UTC de Verão.
   > 
   > 
9. Clique em **OK**.
10. Agora, terá de adicionar qualquer regras deverá ser aplicada durante o segundo perfil. Clique em **Adicionar regra**, e, em seguida, foi possível construir a mesma regra tiver durante o perfil predefinido.
    
    ![Adicionar regra de trabalho de desativado](./media/insights-how-to-scale/Insights_RuleOffWork.png)
11. Certifique-se criar ambas as uma regra de escalamento horizontal e escala no or else durante o perfil a contagem de instâncias só irá aumentar (ou diminuir).
12. Por fim, clique em **guardar**.

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar as métricas de serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e reativa.
* [Ativar a monitorização e diagnóstico](insights-how-to-use-diagnostics.md) para recolher métricas de alta frequência detalhadas do seu serviço.
* [Receber notificações de alertas](insights-receive-alert-notifications.md) sempre que ocorrem eventos operacionais ou quando as métricas ultrapassam um determinado limiar.
* [Monitorizar o desempenho da aplicação](../application-insights/app-insights-azure-web-apps.md) se pretender compreender exatamente como o código está a executar na nuvem.
* [Ver eventos e registo de atividade](insights-debugging-with-events.md) para saber tudo o que tiver ocorrido no seu serviço.
* [Monitorizar a disponibilidade e capacidade de resposta de qualquer página web](../application-insights/app-insights-monitor-web-app-availability.md) com o Application Insights, pelo que pode descobrir se a página está inativo.

