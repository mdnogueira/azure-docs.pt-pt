---
title: "Descrição geral das métricas no Microsoft Azure | Microsoft Docs"
description: "Saiba como personalizar a monitorização gráficos no Azure."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c36031eb-4df5-4cd5-9479-311d493a40d2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: robb
ms.openlocfilehash: 3f9ebb0f5737714dd685f0dcc1ff4b1c0c89528f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Descrição geral das métricas no Microsoft Azure
Todos os serviços do Azure controlam as métricas-chave que permitem-lhe monitorizar o estado de funcionamento, desempenho, disponibilidade e a utilização dos seus serviços. Pode ver estas métricas no portal do Azure e também pode utilizar o [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) ou [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) para aceder ao conjunto completo de métricas programaticamente.

Para alguns serviços, poderá ter de ativar diagnósticos para ver as métricas. Para outros, como as máquinas virtuais, irá obter um conjunto básico de métricas, mas tem de ativar o completo definir métricas de alta frequência. Consulte [ativar a monitorização e diagnóstico](insights-how-to-use-diagnostics.md) para obter mais informações.

## <a name="using-monitoring-charts"></a>Utilizar gráficos de monitorização
Pode gráfico qualquer uma das métricas-los a qualquer período de tempo que escolher.

1. No [Portal do Azure](https://portal.azure.com/), clique em **procurar**, e, em seguida, um recurso está interessado na monitorização.
2. O **monitorização** secção contém as métricas mais importantes de cada recurso do Azure. Por exemplo, uma aplicação web tem **pedidos e erros**, onde como uma máquina virtual teria **percentagem de CPU** e **leitura e escrita em disco**: ![lente monitorização](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Clicar em qualquer gráfico irá mostrar o **métrica** painel. No painel, além de gráfico, é uma tabela que mostra as agregações das métricas (por exemplo, média, mínima e máxima, ao longo do intervalo de tempo que escolheu). Abaixo que são as regras de alerta para o recurso.
    ![Painel métrica](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Para personalizar as linhas que são apresentadas, clique em de **editar** botão no gráfico, em alternativa, o **editar gráfico** comando no painel de métrico.
5. No painel de editar a consulta pode efetuar três ações:
   
   * Alterar o intervalo de tempo
   * Mudar a aparência entre barra e linha
   * Escolha outro metics ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Alterar o intervalo de tempo é tão fácil como selecionar um intervalo diferente (tais como **hora passado**) e clicar em **guardar** na parte inferior do painel. Também pode optar por **personalizada**, que permite-lhe escolher qualquer período de tempo durante as últimos 2 semanas. Por exemplo, pode ver o toda duas semanas ou, apenas 1 hora de ontem. Escreva na caixa de texto para introduzir uma hora diferente.
    ![Intervalo de tempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Abaixo o intervalo de tempo, chan pode escolher qualquer número de métricas para apresentar no gráfico.
8. Ao clicar em guardar as alterações serão guardadas para esse recurso específico. Por exemplo, se tiver duas máquinas virtuais e alterar um gráfico num, este não irá afetar outros.

## <a name="creating-side-by-side-charts"></a>Criação de gráficos de lado a lado
Com a personalização poderosa no portal do pode adicionar tantos gráficos que pretender.

1. No **...**  menu na parte superior do painel, clique em **adicione peças de mosaicos**:  
    ![Menu adicionar](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Em seguida, que pode selecionar selecionar um gráfico do **galeria** no lado direito do ecrã: ![Galeria](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Se não vir a métrica que pretende, sempre que pode adicionar uma das métricas predefinidas, e **editar** o gráfico para mostrar a métrica que precisa.

## <a name="monitoring-usage-quotas"></a>Monitorização de quotas de utilização
A maioria das métricas mostram-lhe tendências ao longo do tempo, mas certos dados, como quotas de utilização, são informações de ponto no tempo com um limiar.

Também pode ver as quotas de utilização no painel de recursos que tenham quotas:

![Utilização](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Tal como com métricas, pode utilizar o [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) ou [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) aceder programaticamente o conjunto completo de quotas de utilização.

## <a name="next-steps"></a>Passos seguintes
* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre uma métrica atravesse um limiar.
* [Ativar a monitorização e diagnóstico](insights-how-to-use-diagnostics.md) para recolher métricas de alta frequência detalhadas do seu serviço.
* [Dimensionar a contagem de instâncias automaticamente](insights-how-to-scale.md) para se certificar de que o serviço está disponível e reativa.
* [Monitorizar o desempenho da aplicação](../application-insights/app-insights-azure-web-apps.md) se pretender compreender exatamente como o código está a executar na nuvem.
* Utilize [Application Insights para aplicações JavaScript e páginas web](../application-insights/app-insights-web-track-usage.md) para obter a análise de cliente sobre os browsers que visitam uma página web.
* [Monitorizar a disponibilidade e capacidade de resposta de qualquer página web](../application-insights/app-insights-monitor-web-app-availability.md) com o Application Insights, pelo que pode descobrir se a página está inativo.

